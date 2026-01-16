---
name: gitbutler
description: This skill should be used when the user asks about GitButler, "but" commands (but status, but absorb, but rub, but commit, but undo, but oplog snapshot), working in a gitbutler/workspace branch, safe git history manipulation, editing commits without rebase -i, squashing commits, fixing commit messages, undoing git operations, or using virtual branches. Use GitButler CLI instead of raw git commands when gitbutler/workspace is detected.
---

# GitButler CLI Guide

GitButler (`but`) is a modern Git client that makes history manipulation safe and reversible.

## [IMPORTANT] Detect GitButler Workspace

**Before running any git commands, check if GitButler is active:**

```bash
git branch --show-current
```

**If the result is `gitbutler/workspace`:**

- [PREFER] `but` commands when equivalent exists (commit, squash, undo, amend)
- [OK] Native git for operations `but` doesn't cover (cherry-pick, stash, tag, revert, blame)
- [CAUTION] Some git commands can corrupt virtual branch state (see below)

**Git Command Guidelines:**

| Prefer `but`                   | Use `git` freely          | Use `git` with caution |
| ------------------------------ | ------------------------- | ---------------------- |
| `but commit` over `git commit` | `git cherry-pick`         | `git reset --hard`     |
| `but rub` over `git rebase -i` | `git stash`               | `git push --force`     |
| `but undo` over `git reset`    | `git tag`                 | `git rebase`           |
| `but absorb` for amending      | `git revert`              | `git clean`            |
| `but reword` for messages      | `git blame`, `git bisect` | `git checkout <file>`  |

**Why prefer `but` when available?** GitButler manages virtual branches through its workspace:

- `but` commands maintain virtual branch tracking
- `but undo` and oplog provide safer recovery
- Some raw git commands can corrupt the virtual branch state

**Quick detection pattern:**

```bash
# At start of git-related tasks, check:
if [[ $(git branch --show-current) == "gitbutler/workspace" ]]; then
    # Use 'but' commands instead of 'git'
fi
```

---

## Key Advantages Over Git

| Feature           | Git                        | GitButler                       |
| ----------------- | -------------------------- | ------------------------------- |
| Undo operations   | Complex reflog             | `but undo`                      |
| Time travel       | Risky reset                | `but restore <sha>`             |
| Squash commits    | `rebase -i` + editor       | `but rub <src> <target>`        |
| Fix old commit    | stash → rebase → amend     | `but absorb`                    |
| Multiple features | Switch branches constantly | Virtual branches (simultaneous) |

---

## Essential Commands

### Inspection (Always Start Here!)

```bash
but status              # View workspace state (branches, commits, changes)
but oplog               # View operation history (time-travel checkpoints)
```

**Status output explained:**

```
╭┄00 [Unassigned Changes]
┊   g0 M calculator.py [LOCKED] 8ebedce   ← File ID, status, dependency
┊
┊╭┄al [calculator-feature]                ← Branch ID
┊●   abc1234 Commit message               ← Commit
├╯
┊
┴ 6e7da9e (common base) [origin/main]
```

- `g0`, `h0`: File/change IDs (use with `but rub`)
- `al`, `ut`: Branch IDs
- `[LOCKED] <sha>`: GitButler detected this change belongs to that commit
- `M`, `A`, `D`: Modified, Added, Deleted

---

### The `rub` Multi-Tool

The `rub` command performs different operations based on source/target types:

| Source | Target | Operation  | Example                 |
| ------ | ------ | ---------- | ----------------------- |
| File   | Branch | **Assign** | `but rub g0 al`         |
| File   | Commit | **Amend**  | `but rub g0 abc123`     |
| Commit | Commit | **Squash** | `but rub abc123 def456` |
| Commit | Branch | **Move**   | `but rub abc123 ut`     |

**Squash workflow:**

```bash
but status                        # Get current SHAs
but rub <source-sha> <target-sha> # Squash source INTO target
but reword <new-sha> -m "Combined message"  # Update message
```

---

### Smart Amending with `absorb`

When you modify code, GitButler detects which commit introduced those lines:

```bash
# Edit a file, then check status
but status
# Shows: g0 M file.py [LOCKED] abc123  ← Detected dependency!

# Auto-amend to the correct commit
but absorb
```

GitButler automatically:

1. Analyzes which lines changed
2. Finds the commit that introduced them
3. Amends the change into that commit
4. Rebases all dependent commits

---

### Commit Editing

```bash
but reword <sha> -m "new message"  # Edit commit message (auto-rebases)
but absorb                            # Auto-amend changes to correct commits
but absorb <file-id>                  # Absorb specific file only
```

---

### Undo & Recovery (Time Travel)

```bash
but undo                      # Undo last operation (one step back)
but oplog snapshot -m "checkpoint"  # Create named checkpoint
but restore <sha> --force     # Restore to any oplog snapshot
```

**The oplog is your time machine:**

```bash
but oplog                     # See all operations
# Output:
# 7f8e652 [SQUASH] SquashCommit
# abc1234 [CREATE] CreateCommit
# def5678 [MOVE_HUNK] MoveHunk

but restore 7f8e652 --force   # Go back to that point
```

Even undos are tracked! You can undo an undo.

---

### Virtual Branches (Work on Multiple Features)

**Create and manage branches:**

```bash
but branch new <name>         # Create virtual branch
but branch list               # List all branches
but branch unapply <id>       # Hide branch temporarily
but branch apply <id>         # Show branch again
```

**Work on multiple features simultaneously:**

```bash
# Edit files for different features
vim feature-a.py
vim feature-b.py

# Check status - assign to different branches
but status
# g0 M feature-a.py
# h0 M feature-b.py

but rub g0 al                 # Assign to branch 'al'
but rub h0 ut                 # Assign to branch 'ut'

# Commit to each branch
but commit al -m "Feature A" --only
but commit ut -m "Feature B" --only
```

**No context switching!** Both branches are active simultaneously.

---

### Committing

```bash
but commit <branch> -m "message"        # Commit ALL uncommitted changes
but commit <branch> -m "message" --only # Commit ONLY assigned changes
but commit -c -m "message"              # Create new branch and commit
```

[CAUTION] **Important**: Without `--only`, `but commit` includes ALL uncommitted changes!

---

## Quick Reference: Git → But

| Git Command              | But Command                               |
| ------------------------ | ----------------------------------------- |
| `git status`             | `but status`                              |
| `git reflog`             | `but oplog`                               |
| `git checkout -b`        | `but branch new`                          |
| `git add`                | `but rub <file> <branch>`                 |
| `git commit`             | `but commit <branch> -m`                  |
| `git commit --amend`     | `but rub <file> <commit>` or `but absorb` |
| `git rebase -i` (squash) | `but rub <commit> <commit>`               |
| `git rebase -i` (reword) | `but reword <sha> -m`                     |
| `git reset --hard`       | `but restore <sha> --force`               |
| N/A                      | `but undo`                                |
| N/A                      | `but oplog snapshot`                      |
| N/A                      | `but absorb`                              |

---

## Common Workflows

### Fix typo in old commit message

```bash
but status                           # Find the commit SHA
but reword <sha> -m "Fixed message"
```

### Squash multiple commits

```bash
but status                           # Get SHAs
but rub <source> <target>            # Squash
but reword <new-sha> -m "Combined" # Update message
```

### Amend change to old commit

```bash
# Edit the file, then:
but status                           # Check for [LOCKED] dependency
but absorb                           # Auto-amend
```

### Recover from mistake

```bash
but undo                             # Quick: undo last operation
# OR
but oplog                            # Find the right snapshot
but restore <sha> --force            # Go back in time
```

---

## Error Handling

### Common Errors and Recovery

| Error                 | Cause                            | Recovery                                                     |
| --------------------- | -------------------------------- | ------------------------------------------------------------ |
| `but status` fails    | GitButler not initialized        | Run `but` to initialize, or check `.git/gitbutler` directory |
| "Branch not found"    | Virtual branch deleted/renamed   | Run `but status` to list available branches                  |
| "Conflict detected"   | Merge conflict during operation  | Resolve conflicts in files, then `but commit`                |
| "Uncommitted changes" | Operation blocked by dirty state | Commit or stash changes first                                |
| Command hangs         | Large repo or network issue      | Wait, or Ctrl+C and retry                                    |

### Recovery Commands

```bash
but undo                    # Undo last operation (safe, always works)
but oplog                   # View all operations for recovery points
but restore <sha> --force   # Restore to any previous state
but oplog snapshot list           # List named checkpoints
```

### When to Use `undo` vs `restore`

- **`but undo`**: Quick single-step rollback. Use when the last operation went wrong.
- **`but restore`**: Time-travel to any point. Use when you need to go back multiple operations or to a named checkpoint.

[INFO] The oplog tracks everything, including undos. You can always recover!

---

## Claude Code Hooks Integration

GitButler integrates with Claude Code through hooks (`but claude pre-tool`, `but claude post-tool`, `but claude stop`) that automatically manage commits during AI-assisted development sessions.

**Key benefits:**

- Auto-assigns changes to appropriate branches
- Generates commit messages from user prompts
- Eliminates manual `but commit` during sessions

For detailed configuration and setup instructions, see `references/hooks.md`.

---

## Reference Files

For detailed documentation, see:

- `references/cheatsheet.md` - Quick command reference with git → but mappings
- `references/tutorial.md` - Comprehensive step-by-step workflows
- `references/hooks.md` - Claude Code hooks setup guide
