---
name: execute
description: >-
  Implements a backlog item end-to-end in an isolated worktree: scope,
  decompose, verify, commit. Use when an order's execute stage is dispatched.
schedule: "Primary stage. When backlog items are ready for implementation."
---

# Execute

## Scope

Read the order's `prompt`/`extra_prompt` and any linked plan first. Establish
what changes and what stays untouched before writing anything. If the task
is bigger than a focused change, split it into independently completable
units rather than doing it all in one sweep.

## Worktree discipline

Never edit files on `main`/`master` directly.

1. `noodle worktree create <name>` before making any change.
2. Make commits inside that worktree only.
3. `noodle worktree merge <name>` once verification passes.

If a worktree already exists for this order (resumed session), reuse it
instead of creating a new one.

## Verification

Before committing, run whatever this project actually has configured —
check for a test runner, linter, type checker, or build script (e.g.
`package.json` scripts, a `Makefile`, CI config) and run the matching
commands. Never commit on top of a failing check. If no verification
tooling exists yet for the area you touched, say so explicitly in the
commit message instead of skipping silently.

## Commits

Conventional commits: `<type>(<scope>): <description>`. Reference the
backlog item id when there is one (e.g. `fixes #12`).

## Scope discipline

Only touch what's in scope for this order. If you notice unrelated issues
while working, leave a note for the `quality`/`reflect` stage instead of
fixing them inline — out-of-scope changes make orders harder to review and
revert.

## Finishing

Signal completion so the pipeline can advance and no work is lost if the
session is interrupted mid-task.
