# secure-modern-frontend

A Claude Code plugin with one workflow that designs, builds, and reviews web UI
with the least code that meets the quality bar.

**Not affiliated with Anthropic, Cloudflare, or Google.** This plugin bundles
their open-source skills under the original licenses. See [NOTICE](NOTICE) and
[THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).

## Before you install

- **Read the skills first.** A skill is a set of instructions that your agent
  follows with your permissions. Read every `SKILL.md` under `skills/` before
  you install the plugin.
- **Audit only authorized code.** Run the security audit only on code that you
  own or have written permission to test.

## Skills

| Skill | Source | What it does |
| :- | :- | :- |
| `secure-modern-frontend` | ours | Runs the workflow below |
| `frontend-design` | Anthropic, unmodified | Sets the visual direction: plan, review, then code |
| `modern-web-guidance` | Google Chrome, modified (see NOTICE) | Looks up current web platform APIs through its CLI |
| `minimal-code` | ours | Keeps the change as small as the quality bar allows |
| `security-audit` | Cloudflare, unmodified | Audits a codebase for vulnerabilities, on request |

## Workflow

1. Write a design plan with `frontend-design`: plan, review, then code.
2. Map each feature to platform APIs with `modern-web-guidance search` and
   `retrieve`.
3. Build under the `minimal-code` rules.
4. Run `modern-web-guidance legacy-scan .` and fix the findings.
5. Run `security-audit` only on an explicit request or a pre-release review.
   It is expensive and spawns sub-agents. Its output stays outside the repo,
   in `~/security-audit-skill/<repo-name>/run-<N>`.
6. Fix findings with minimal patches. Flag any fix that changes a design
   decision.

When sources disagree, the order is: your brief, then `frontend-design` (look),
`modern-web-guidance` (APIs), `minimal-code` (code volume), and
`security-audit` (reports, does not redesign).

## Install

```sh
claude plugin marketplace add urmiljp/secure-modern-frontend
claude plugin install secure-modern-frontend@secure-modern-frontend
```

To try it without installing, run `claude --plugin-dir <path-to-this-repo>`.

Then ask for a UI task, or run `/secure-modern-frontend:secure-modern-frontend`.

## Requirements

- Node.js 22 or later, with network access for `npx` to fetch
  `modern-web-guidance@1.0.1`.
- For the audit, a platform that can delegate to sub-agents.

## Decisions

- **CLI pinned to `modern-web-guidance@1.0.1`.** `search`, `retrieve`, and
  `legacy-scan` were verified on this version. Nothing in the plugin uses
  `@latest`. To upgrade, change the version in
  `skills/modern-web-guidance/SKILL.md` and
  `skills/secure-modern-frontend/SKILL.md`, then update NOTICE.
- **One upstream file is modified.** `skills/modern-web-guidance/SKILL.md`
  changes the version pin, rewrites the frontmatter description, and adds a
  one-line change notice. All other vendored files are byte-identical to the
  commits recorded in NOTICE.
- **The `modern-web-guidance` description is rewritten.** The upstream text
  asks to run first on every HTML and CSS task. In testing it triggered in
  place of the workflow, so the task got no design plan and no net-lines
  report. The new description limits it to API lookups and scans.
- **No polyfills by default.** The workflow uses Baseline Newly available
  features with feature detection and graceful degradation, and adds no
  polyfill. Upstream guidance asks for a fallback, often a polyfill, for
  features that are not Baseline Widely available. State a different browser
  support policy in your brief to override this.
- **Guides are not vendored.** The pinned CLI serves them at runtime, so a
  copy in this repo could drift from the CLI. This also keeps the upstream
  CC-BY 4.0 guide content out of the repo.
- **The workflow always runs `legacy-scan` after a build.** The upstream
  `modern-web-guidance` skill says to skip the scan for single-feature tasks.
  The orchestrator overrides that on purpose.
- **Each upstream LICENSE sits in its skill folder.** Two of them were copied
  there from the upstream repository root.
- **`minimal-code` has a fallback for non-git directories.** It counts changed
  lines by hand when `git diff` is not available.
- **Line endings are pinned to LF** in `.gitattributes`, so vendored files
  stay byte-identical to upstream on every OS.
- **No icon.** `claude plugin validate` warns about it. It matters only for a
  submission to Anthropic's plugin directory.

## License

Files written for this plugin are licensed under [Apache-2.0](LICENSE).
Vendored skills keep their own licenses, which ship in each skill folder.
