---
name: secure-modern-frontend
description: End-to-end workflow for web UI work. Use first for any request to add, build, change, or review a web page, component, or UI feature, such as "add a popover menu to this page". It sequences the design plan, platform API lookup, minimal build, legacy scan, and an opt-in security audit. Not for a single step on its own - a look-only brief, an API lookup, or a security-only review each have their own skill.
---

# Secure Modern Frontend

Run the steps in order. Each step names the skill it uses. If a skill does not
load by name, read its file at the path given and follow it.

## Precedence

When two sources disagree, the earlier one wins:

1. The user's brief.
2. frontend-design: how it looks.
3. modern-web-guidance: which platform APIs to use.
4. minimal-code: how much code to write.
5. security-audit: reports findings. It does not redesign.

## 1. Design plan

Use the `frontend-design` skill
(`${CLAUDE_PLUGIN_ROOT}/skills/frontend-design/SKILL.md`).
Write the design plan, review it against the brief, and revise it. When the
task adds to an existing page, keep that page's look and plan only the new part.

Gate: do not call Edit or Write until you have sent the user a message that
starts with the heading `## Design plan` and contains the plan (color, type,
layout, principles, and what the review changed). A plan that exists only in
your reasoning does not count.

## 2. Map features to platform APIs

Use the `modern-web-guidance` skill
(`${CLAUDE_PLUGIN_ROOT}/skills/modern-web-guidance/SKILL.md`).
For each feature in the plan, search with a 2-5 word query, then retrieve the
guides that match:

```sh
npx -y modern-web-guidance@1.0.1 search "<query>"
npx -y modern-web-guidance@1.0.1 retrieve "<id>[,<id>]"
```

On Windows outside Git Bash, use `npx.cmd`. If `retrieve` saves its output to a
file, read that file in full. Keep the CLI version pinned; never use `@latest`.

Browser support policy for this workflow, unless the user's brief sets another:
use Baseline Newly available features with feature detection and graceful
degradation. Do not add polyfills or other new dependencies. Use a feature that
is not yet Baseline only if the page still works without it and it needs no
fallback code.

## 3. Build

Use the `minimal-code` skill
(`${CLAUDE_PLUGIN_ROOT}/skills/minimal-code/SKILL.md`).
State the smallest design in 2 lines in a message, then build it. Prefer the native APIs
from step 2 over new dependencies or hand-written JavaScript.

## 4. Legacy scan

```sh
npx -y modern-web-guidance@1.0.1 legacy-scan . --json
```

This workflow always runs the scan after a build, which overrides the
modern-web-guidance skill's advice to skip it for single-feature tasks. Check
each finding against its `keepWhen` / `dropWhen` rubric and drop false
positives. Retrieve the guide for each remaining finding, fix it, and scan
again. Do not edit safe code to force zero findings.

## 5. Security audit (opt-in)

Run the `security-audit` skill
(`${CLAUDE_PLUGIN_ROOT}/skills/security-audit/SKILL.md`) only when the user
explicitly asks for an audit or a pre-release review. It is expensive and
spawns sub-agents, so never start it as part of a normal UI task. When it is
skipped, say so in the report.

Audit only code the user is authorized to audit. Keep all audit output outside
the repository, in the skill's default `~/security-audit-skill/<repo-name>/run-<N>`.

## 6. Fix findings

Fix scan and audit findings with minimal patches under the `minimal-code`
rules. If a fix would change a design decision from step 1, flag it and ask
before applying it.

## Report

End with:

- the design plan that was built, written out again in full
- the guides retrieved and the platform APIs used
- dependencies added (expected: none)
- legacy-scan result
- whether the security audit ran
- net lines added and removed
