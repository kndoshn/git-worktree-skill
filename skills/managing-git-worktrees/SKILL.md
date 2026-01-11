---
name: managing-git-worktrees
description: 'Creates, inspects, and cleans up Git worktrees safely (add, list, lock/unlock, remove, prune) and troubleshoots common worktree errors. Use when the user mentions git worktree, parallel branch work, multiple working directories, or errors like "already checked out", "worktree", "prunable", or "locked".'
---

# Managing Git Worktrees

## Scope

This Skill guides safe and efficient use of `git worktree` for parallel development without extra clones.

It focuses on:
- Adding worktrees for existing or new branches
- Avoiding branch collisions across worktrees
- Cleaning up worktrees safely (including `prune` and `lock`)
- Handling common errors and recovery paths

Assume the user already understands Git fundamentals.

## Operating rules

### Safety rules (must follow)

1. **Never discard work without explicit user intent.**
   - Before any destructive operation, check for uncommitted changes.
   - If a worktree is not clean, ask whether to commit, stash, move changes, or discard.

2. **Prefer non-destructive inspection first.**
   - Use `git worktree list --porcelain` to understand current state.
   - For cleanup, run `git worktree prune --dry-run` before `git worktree prune`.

3. **Avoid forcing branch reuse across worktrees by default.**
   - If a branch is already checked out in another worktree, prefer:
     - Using the existing worktree
     - Creating a new branch off the target branch
     - Using a detached worktree at a specific commit
   - Treat `--ignore-other-worktrees` as a last resort and require explicit confirmation.

4. **Keep commands copy-pasteable and path-safe.**
   - Use forward slashes in paths.
   - Quote paths that may include spaces.

### Degree of freedom

- **Low freedom** for destructive tasks: removal, pruning, forced operations.
- **Medium freedom** for creation tasks: branch naming and path layout can vary by repo conventions.

## Quick command patterns

### Inspect

```bash
git worktree list
git worktree list --porcelain
```

### Add a worktree

Existing local branch:

```bash
git worktree add <path> <branch>
```

Create a new branch and add a worktree:

```bash
git worktree add -b <new-branch> <path> <start-point>
```

Detached worktree for quick experiments:

```bash
git worktree add --detach <path> <commit-ish>
```

### Remove and prune

Safely remove a worktree (clean only):

```bash
git worktree remove <path>
```

Cleanup orphaned metadata:

```bash
git worktree prune --dry-run
git worktree prune
```

### Lock and unlock

```bash
git worktree lock <path>
git worktree unlock <path>
```

## Workflows

### Workflow 1: Add a parallel worktree for a new task (recommended)

Checklist:
- [ ] Pick a deterministic path name (for example: `../wt/<branch>`).
- [ ] Decide a start point (for example: `main`, `develop`, or `origin/main`).
- [ ] Create and verify the worktree.

Commands:

```bash
git worktree add -b <new-branch> <path> <start-point>
git -C <path> status
```

If the start point is ambiguous (multiple remotes), inspect available remote refs first:

```bash
git remote
git show-ref --heads --remotes | grep -E "refs/remotes/.*/<start-branch>$"
```

### Workflow 2: Add a worktree for an existing branch

Checklist:
- [ ] Confirm the branch exists locally.
- [ ] Confirm it is not already checked out elsewhere.
- [ ] Add the worktree.

Commands:

```bash
git branch --list <branch>
git worktree list --porcelain
git worktree add <path> <branch>
```

### Workflow 3: Resolve "is already checked out" (branch collision)

1. Find where the branch is currently checked out:

```bash
git worktree list --porcelain
```

2. Choose the safest option:

- **Option A (best):** Use the existing worktree path.
- **Option B:** Create a new branch and add a new worktree:

```bash
git worktree add -b <new-branch> <path> <branch>
```

- **Option C:** Detached worktree at the same commit:

```bash
git rev-parse <branch>
git worktree add --detach <path> <commit-sha>
```

- **Option D (last resort):** Force checkout ignoring other worktrees.
  - Explain risk and require explicit confirmation.
  - Command (choose one based on user preference):

```bash
git switch --ignore-other-worktrees <branch>
# or
git checkout --ignore-other-worktrees <branch>
```

### Workflow 4: Clean up worktrees safely

Checklist:
- [ ] List worktrees
- [ ] For each candidate path, check cleanliness
- [ ] Remove clean worktrees
- [ ] Dry-run prune
- [ ] Prune

Commands:

```bash
git worktree list --porcelain
git -C <path> status --porcelain
git worktree remove <path>
git worktree prune --dry-run
git worktree prune
```

If a worktree is locked:
- Prefer `git worktree unlock <path>` first.
- Removing a locked worktree requires `--force` twice. Only do this after explicit confirmation:

```bash
git worktree remove --force --force <path>
```

## Response format

When responding to the user, provide:

1. A brief recommendation (1 to 3 sentences)
2. A copy-paste command block
3. A safety note if anything is destructive
4. A fallback path if the first attempt fails (for example: collision or non-clean worktree)

## Evaluation scenarios

Use these scenarios to test whether this Skill activates and behaves correctly:

1. Parallel hotfix request:
   - Prompt: "I am mid-feature, but need a hotfix. Use git worktree."
   - Expected: Proposes `git worktree add -b <hotfix-branch> <path> <start-point>`, uses a separate path, and does not require stashing.

2. Branch collision:
   - Prompt: "git worktree add failed: branch is already checked out. What now?"
   - Expected: Finds the existing worktree, recommends Option A or B, and treats `--ignore-other-worktrees` as last resort.

3. Cleanup request:
   - Prompt: "Please clean up old worktrees and prune."
   - Expected: Uses `list --porcelain`, checks status per worktree, runs `prune --dry-run`, and avoids `--force` without confirmation.

