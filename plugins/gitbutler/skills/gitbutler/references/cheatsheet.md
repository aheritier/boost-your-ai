# But (GitButler CLI) Cheat Sheet

## ⚠️ GitButler Detection

```bash
# Check if GitButler is active
git branch --show-current
# If result is "gitbutler/workspace" → Use 'but' commands!
```

| Command Type | On gitbutler/workspace |
|--------------|------------------------|
| Read-only (`git log`, `git diff`) | ✅ Safe |
| `git commit`, `git rebase` | ❌ Use `but` instead |
| `git reset`, `git checkout` | ❌ Use `but` instead |

---

## Quick Reference: Git → But

| Git Command | But Command | Description |
|------------|-------------|-------------|
| `git status` | `but status` | View workspace state |
| `git log` | `but status` | See branch commits (visual) |
| `git reflog` | `but oplog` | Operation history (more powerful) |
| `git checkout -b` | `but branch new <name>` | Create new branch |
| `git add <file>` | `but rub <file> <branch>` | Assign file to branch |
| `git commit -m` | `but commit <branch> -m` | Commit to branch |
| `git commit --amend` | `but rub <file> <commit>` | Amend file to commit |
| `git rebase -i` (squash) | `but rub <commit1> <commit2>` | Squash commits |
| `git rebase -i` (reword) | `but describe <commit> -m` | Edit commit message |
| `git reset --hard` | `but restore <sha>` | Restore to snapshot |
| N/A | `but undo` | Undo last operation |
| N/A | `but snapshot -m` | Create named checkpoint |
| N/A | `but absorb` | Auto-amend to correct commit |

---

## Inspection Commands

```bash
# View workspace state (branches, commits, changes)
but status

# View operation history (time-travel checkpoints)
but oplog

# View operations since specific point
but oplog --since <sha>
```

---

## Branch Operations

```bash
# Create new branch
but branch new <name>

# List branches
but branch list

# Delete branch
but branch delete <name>

# Apply/unapply branch to workspace
but branch apply <name>
but branch unapply <name>
```

---

## Committing

```bash
# Commit all changes to branch
but commit <branch> -m "message"

# Commit only assigned changes
but commit <branch> -m "message" --only

# Create new branch and commit
but commit -c -m "message"

# Insert blank commit
but new <position>
```

---

## The Rub Command (Multi-Tool)

The `rub` command combines two entities based on their types:

| Source | Target | Operation |
|--------|--------|-----------|
| File | Branch | **Assign** file to branch |
| File | Commit | **Amend** file into commit |
| Commit | Commit | **Squash** commits together |
| Commit | Branch | **Move** commit to branch |

```bash
# Assign file to branch
but rub <file-id> <branch-id>

# Amend file into existing commit
but rub <file-id> <commit-sha>

# Squash two commits (source into target)
but rub <source-sha> <target-sha>

# Move commit to different branch
but rub <commit-sha> <branch-id>
```

---

## Commit Editing

```bash
# Edit commit message
but describe <commit-sha> -m "new message"

# Rename branch
but describe <branch-id> -m "new-branch-name"

# Auto-amend changes to correct commits
but absorb

# Absorb specific file
but absorb <file-id>
```

---

## Undo & Recovery

```bash
# Undo last operation
but undo

# Create named snapshot/checkpoint
but snapshot -m "description"

# Restore to specific snapshot
but restore <oplog-sha>

# Force restore (overwrite changes)
but restore <oplog-sha> --force
```

---

## File/Change Reference IDs

When you run `but status`, changes show IDs like `g0`, `h0`, `al`, `ut`:

- **Lowercase letters + numbers** (`g0`, `h0`): File/change IDs
- **Two letters** (`al`, `ut`): Branch IDs
- Use these IDs with `but rub`, `but absorb`, etc.

Example:
```
╭┄00 [Unassigned Changes]
┊   g0 M calculator.py    ← Use 'g0' to reference this file
┊
┊╭┄al [calculator-feature] ← Use 'al' to reference this branch
┊●   abc123 Commit message
```

---

## Status Symbols

| Symbol | Meaning |
|--------|---------|
| `A` | Added (new file) |
| `M` | Modified |
| `D` | Deleted |
| `🔒` | File has dependency to specific commit |
| `●` | Commit |

---

## Virtual Branches Workflow

Work on multiple features simultaneously without switching branches:

```bash
# Create branches
but branch new feature-a
but branch new feature-b

# Edit files, check status
but status
# g0 M file-a.py
# h0 M file-b.py

# Assign to different branches
but rub g0 al    # file-a to branch 'al'
but rub h0 ut    # file-b to branch 'ut'

# Commit to each
but commit al -m "Feature A" --only
but commit ut -m "Feature B" --only
```

---

## Common Workflows

### Squash Commits
```bash
but status                        # Get SHAs
but rub <source> <target>         # Squash
but describe <new-sha> -m "Msg"   # Update message
```

### Auto-Amend (absorb)
```bash
# Edit file, then:
but status                        # Look for 🔒 indicator
but absorb                        # Auto-amend to correct commit
```

### Time Travel
```bash
but oplog                         # See all snapshots
but restore <sha> --force         # Go back
```

---

## Tips

1. **Always check `but status` first** to get current IDs
2. **IDs change after operations** - refresh with `but status`
3. **Create snapshots before risky operations**: `but snapshot -m "before cleanup"`
4. **Use `but oplog`** to see what you can restore to
5. **Virtual branches work simultaneously** - no need to switch!
6. **Use `--only` flag** when committing to avoid including unassigned changes
7. **Look for `🔒`** - GitButler detected which commit owns those lines
