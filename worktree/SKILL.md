---
name: worktree
description: >
  Git worktree and task-branch workflow. MUST be loaded before creating or entering a dedicated git worktree or `worktree/*` task branch, and before committing or finishing work inside one.
when_to_use: >
  Invoke as soon as a task is going to run in a dedicated worktree — when asked to work in a worktree, when using an enter-worktree tool, or when the current checkout is already on a task branch. Load it before the first commit, not after.
---

# Worktree workflow

Only applies inside a dedicated worktree. Outside one, Git stays read-only unless explicitly requested.

## Branches

- Use a temporary `worktree/<task-name>` branch, where `<task-name>` is short descriptive `kebab-case`, e.g. `new-card-style`, `fix-client-list`.
- If the current worktree is already on a task/non-default branch, reuse the current worktree and branch. Do not create a nested task branch unless I explicitly ask you to.
- Do not use prefixes such as `feat/`, `fix/`, `chore/`, or usernames unless explicitly requested.
- Treat the corresponding `<task-name>` branch as the final task branch.

## Finishing

- Commit completed work to the worktree branch.
- After verification, create or fast-forward the final task branch to the completed worktree branch without checking it out or modifying the primary working tree.
- Never overwrite or rewrite divergent history on the final task branch.
- Keep commits local; do not push to remotes.
- Leave the worktree clean before finishing.
