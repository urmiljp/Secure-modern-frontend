---
name: minimal-code
description: Code-volume rules for writing or changing code - smallest correct change, reuse before new code, no speculative abstraction, and a net-lines report. Use when implementing, fixing, or patching code, including as step 3 of the secure-modern-frontend workflow. Not for choosing a visual direction, looking up web platform APIs, or auditing security.
---

# Minimal Code

Ship the least code that meets the quality bar.

## Before coding

State the smallest design that satisfies the request in 2 lines: what changes,
and what is reused. Then write the smallest correct change.

## Order of preference

1. Delete or reuse existing code.
2. Use a native platform feature.
3. Use a dependency the project already has.
4. Write new code.

Add a new dependency only if it removes more code than it adds.

## Do not add

Abstractions, options, wrappers, or config that the request did not ask for.

## Quality floor

Never trade these for fewer lines:

- input validation at boundaries
- error handling
- types
- accessibility
- security
- tests for behavior

Readable beats short. No code golf.

## After writing

1. Run `git diff --stat` and `git status --short`. Untracked files do not
   appear in the diff, so count them from the status output. If the directory
   is not a git repo, list the changed files and count their lines by hand.
2. Do one pass and cut anything the request does not require.
3. Report net lines added and removed.
