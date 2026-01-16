# GitButler CLI (but) Tutorial

A hands-on guide to manipulating git history with `but` - safer, faster, and more intuitive than raw git commands.

## Table of Contents

1. [What is GitButler?](#what-is-gitbutler)
2. [Setup](#setup)
3. [Understanding the Status Output](#understanding-the-status-output)
4. [The Operation Log (oplog)](#the-operation-log-oplog)
5. [Basic Workflow](#basic-workflow)
6. [Fixing Commit Messages](#fixing-commit-messages)
7. [Squashing Commits](#squashing-commits)
8. [Auto-Amending with Absorb](#auto-amending-with-absorb)
9. [Undo and Recovery](#undo-and-recovery)
10. [Virtual Branches](#virtual-branches)
11. [Tips and Gotchas](#tips-and-gotchas)

---

## What is GitButler?

GitButler is a modern Git client that provides:

- **Virtual Branches**: Work on multiple branches simultaneously
- **Safe History Manipulation**: Full undo/restore with operation history
- **Intelligent Change Assignment**: Auto-detect where changes belong
- **Simplified Commands**: One `rub` command for squash, amend, move, and assign

It's fully Git-compatible - your repository is still a regular Git repo.

---

## Setup

### Prerequisites

- Git repository with a remote
- GitButler installed (`but --version` to check)

### Initialize GitButler

```bash
# In your git repository
git fetch origin
but init
```

GitButler needs a remote to set up its base branch (typically `origin/main`).

---

## Understanding the Status Output

Run `but status` to see your workspace:

```
╭┄00 [Unassigned Changes]
┊   g0 M calculator.py 🔒 8ebedce
┊   h0 A new-file.py
┊
┊╭┄al [calculator-feature]
┊●   859393e Add main entry point
┊●   bac83b0 Add complete test suite
┊●   8ebedce Add divide function
├╯
┊
┊╭┄ut [utilities-feature] (no commits)
├╯
┊
┴ 6e7da9e (common base) [origin/main] Initial commit
```

### Key Elements:

| Element         | Meaning                                 |
| --------------- | --------------------------------------- |
| `g0`, `h0`      | File reference IDs (use with `but rub`) |
| `al`, `ut`      | Branch reference IDs                    |
| `M`, `A`        | Modified, Added                         |
| `🔒 8ebedce`    | File has dependency to that commit      |
| `●`             | A commit in the branch                  |
| `(common base)` | Where branches diverge from remote      |

---

## The Operation Log (oplog)

GitButler tracks every operation, allowing time-travel:

```bash
$ but oplog
Operations History
──────────────────────────────────────────────────
7f8e652791ca 2026-01-10 13:57:31 [OTHER] OnDemandSnapshot
06aff309c0b9 2026-01-10 13:57:22 [CREATE] CreateCommit
0e6d12fe32df 2026-01-10 13:55:45 [MOVE_HUNK] MoveHunk
```

**This is more powerful than `git reflog`** because it captures:

- Uncommitted changes
- File assignments
- Branch modifications
- Everything!

Use any SHA with `but restore <sha>` to go back in time.

---

## Basic Workflow

### 1. Create a Branch

```bash
but branch new my-feature
```

### 2. Make Changes

Edit your files normally.

### 3. Check Status

```bash
$ but status
╭┄00 [Unassigned Changes]
┊   g0 M calculator.py
```

### 4. Assign to Branch

```bash
but rub g0 al  # Assign file g0 to branch al
```

### 5. Commit

```bash
but commit al -m "Add feature"
```

**Important**: `but commit` by default commits ALL uncommitted changes. Use `--only` to commit just assigned changes:

```bash
but commit al -m "Add feature" --only
```

---

## Fixing Commit Messages

### The Problem

```
●   8b8568c Add basic calculator fnctions  ← typo!
```

### The Solution

```bash
but reword 8b8568c -m "Add basic calculator functions"
```

### What Happens

```
Updated commit message for 8b8568c (now 2182f39)
```

GitButler:

1. Rewrites the commit with the new message
2. **Automatically rebases** all dependent commits
3. All commits get new SHAs (this is normal)

**In git, this would require**: `git rebase -i`, editing the commit, resolving potential conflicts.

---

## Squashing Commits

### The Problem

```
●   69a69ef fix: add test for divide      ← Should be one commit
●   5bd62a2 fix: add test for multiply    ←
●   e37dcb6 Add tests for add and subtract ←
```

### The Solution

Use `but rub <source> <target>` to squash:

```bash
# Squash "fix: multiply" into main test commit
but rub 5bd62a2 e37dcb6

# Squash "fix: divide" into main test commit (get new ID first!)
but status  # Get the new commit ID
but rub f08d1a9 6e1cf1f
```

### Result

```
●   3eae31c Add tests for add and subtract  ← Now contains all tests
```

Update the message if needed:

```bash
but reword 3eae31c -m "Add complete test suite for calculator"
```

**In git, this would require**: `git rebase -i` with pick/squash commands.

---

## Auto-Amending with Absorb

This is GitButler's killer feature!

### The Scenario

You fix a bug in a function, but the fix should go into the original commit that added that function.

### Make Your Change

```bash
# Edit calculator.py to add error handling to divide()
```

### Check Status

```bash
$ but status
╭┄00 [Unassigned Changes]
┊   g0 M calculator.py 🔒 b7e8472   ← GitButler detected the dependency!
```

The `🔒 b7e8472` shows GitButler knows this change belongs to the "Add divide function" commit!

### Absorb It

```bash
but absorb
# Output: Absorbed changes into commit b7e8472
```

The change is now part of the original commit, as if it was always there.

**In git, this would require**:

1. `git stash`
2. `git rebase -i` (mark commit for edit)
3. `git stash pop`
4. `git add` + `git commit --amend`
5. `git rebase --continue`

With GitButler: Just `but absorb`!

---

## Undo and Recovery

### Quick Undo

```bash
but undo  # Reverts to previous operation
```

Each undo goes back one operation (commit, assignment, etc.)

### Create Safety Snapshots

Before risky operations:

```bash
but oplog snapshot -m "Before major refactor"
# Output: Snapshot ID: 7f8e652
```

### Restore to Any Point

```bash
# View available snapshots
but oplog

# Restore to specific point
but restore 7f8e652 --force
```

### Even Undos Are Tracked!

If you undo something and change your mind, the undo itself is in the oplog:

```
b0f544a12c4b [RESTORE] Restored from snapshot
```

You can restore to before the undo!

---

## Virtual Branches

Work on multiple features simultaneously without switching:

### Create Multiple Branches

```bash
but branch new feature-a
but branch new feature-b
```

### Assign Changes to Different Branches

```bash
# File edits show up in Unassigned Changes
$ but status
╭┄00 [Unassigned Changes]
┊   g0 M file-a.py
┊   h0 M file-b.py

# Assign to respective branches
but rub g0 al  # file-a to feature-a
but rub h0 ut  # file-b to feature-b
```

### Commit to Each Branch

```bash
but commit al -m "Feature A changes"
but commit ut -m "Feature B changes"
```

Both branches are active - no `git checkout` needed!

---

## Tips and Gotchas

### 1. Always Refresh IDs

Reference IDs (`g0`, `h0`, etc.) change after operations. Always run `but status` to get current IDs.

### 2. GitButler Uses Its Own Branch

Your working tree is on `gitbutler/workspace`, not `main`. This is normal.

### 3. Don't Mix Git and But Commits

If you use `git commit` directly, GitButler may get confused. Stick to `but commit`.

### 4. The Workspace Commit

You'll see a "GitButler Workspace Commit" in `git log`. This is a placeholder - don't worry about it.

### 5. Pushing Changes

```bash
but push <branch-name>
```

This pushes the virtual branch to the remote as a real git branch.

### 6. When Things Go Wrong

```bash
# View what you can restore to
but oplog

# Go back to a known good state
but restore <sha> --force
```

---

## Summary: Why Use But?

| Task                      | Git Complexity                              | But Simplicity                   |
| ------------------------- | ------------------------------------------- | -------------------------------- |
| Fix old commit message    | `rebase -i`, edit, continue                 | `but reword`                     |
| Squash commits            | `rebase -i`, pick/squash, resolve conflicts | `but rub`                        |
| Amend to old commit       | stash, rebase, edit, pop, amend, continue   | `but absorb`                     |
| Undo anything             | Complex/impossible depending on situation   | `but undo` or `but restore`      |
| Work on multiple features | Stash, checkout, work, checkout, pop        | Virtual branches - no switching! |

GitButler makes git history manipulation **safe, simple, and reversible**.

---

## Further Resources

- [GitButler Documentation](https://docs.gitbutler.com)
- [GitButler CLI Overview](https://docs.gitbutler.com/cli-overview)
- [Stacked Branches Guide](https://docs.gitbutler.com/features/branch-management/stacked-branches)
