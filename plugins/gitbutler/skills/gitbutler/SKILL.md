---
name: gitbutler
description: GitButler CLI (but) for safe git history manipulation. Use when user wants to edit commits, squash, rebase, fix commit messages, undo operations, or work with virtual branches using GitButler instead of raw git commands.
---

# GitButler CLI Guide

GitButler (`but`) is a modern Git client that makes history manipulation safe and reversible.

## ⚠️ IMPORTANT: Detect GitButler Workspace

**Before running any git commands, check if GitButler is active:**

```bash
git branch --show-current
```

**If the result is `gitbutler/workspace`:**
- ✅ USE `but` commands (this skill)
- ❌ AVOID raw `git commit`, `git rebase`, `git reset`, `git checkout`
- ℹ️ Read-only git commands are OK: `git log`, `git diff`, `git show`, `git status`

**Why?** GitButler manages virtual branches through its workspace. Using raw git commands can:
- Create commits outside GitButler's tracking
- Corrupt the virtual branch state
- Require manual recovery with `git reset --soft`

**Quick detection pattern:**
```bash
# At start of git-related tasks, check:
if [[ $(git branch --show-current) == "gitbutler/workspace" ]]; then
    # Use 'but' commands instead of 'git'
fi
```

---

## Key Advantages Over Git

| Feature | Git | GitButler |
|---------|-----|-----------|
| Undo operations | Complex reflog | `but undo` |
| Time travel | Risky reset | `but restore <sha>` |
| Squash commits | `rebase -i` + editor | `but rub <src> <target>` |
| Fix old commit | stash → rebase → amend | `but absorb` |
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
┊   g0 M calculator.py 🔒 8ebedce   ← File ID, status, dependency
┊
┊╭┄al [calculator-feature]          ← Branch ID
┊●   abc1234 Commit message         ← Commit
├╯
┊
┴ 6e7da9e (common base) [origin/main]
```

- `g0`, `h0`: File/change IDs (use with `but rub`)
- `al`, `ut`: Branch IDs
- `🔒 <sha>`: GitButler detected this change belongs to that commit
- `M`, `A`, `D`: Modified, Added, Deleted

---

### The `rub` Multi-Tool

The `rub` command performs different operations based on source/target types:

| Source | Target | Operation | Example |
|--------|--------|-----------|---------|
| File | Branch | **Assign** | `but rub g0 al` |
| File | Commit | **Amend** | `but rub g0 abc123` |
| Commit | Commit | **Squash** | `but rub abc123 def456` |
| Commit | Branch | **Move** | `but rub abc123 ut` |

**Squash workflow:**
```bash
but status                        # Get current SHAs
but rub <source-sha> <target-sha> # Squash source INTO target
but describe <new-sha> -m "Combined message"  # Update message
```

---

### Smart Amending with `absorb`

When you modify code, GitButler detects which commit introduced those lines:

```bash
# Edit a file, then check status
but status
# Shows: g0 M file.py 🔒 abc123  ← Detected dependency!

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
but describe <sha> -m "new message"  # Edit commit message (auto-rebases)
but absorb                            # Auto-amend changes to correct commits
but absorb <file-id>                  # Absorb specific file only
```

---

### Undo & Recovery (Time Travel)

```bash
but undo                      # Undo last operation (one step back)
but snapshot -m "checkpoint"  # Create named checkpoint
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

⚠️ **Important**: Without `--only`, `but commit` includes ALL uncommitted changes!

---

## Quick Reference: Git → But

| Git Command | But Command |
|-------------|-------------|
| `git status` | `but status` |
| `git reflog` | `but oplog` |
| `git checkout -b` | `but branch new` |
| `git add` | `but rub <file> <branch>` |
| `git commit` | `but commit <branch> -m` |
| `git commit --amend` | `but rub <file> <commit>` or `but absorb` |
| `git rebase -i` (squash) | `but rub <commit> <commit>` |
| `git rebase -i` (reword) | `but describe <sha> -m` |
| `git reset --hard` | `but restore <sha> --force` |
| N/A | `but undo` |
| N/A | `but snapshot` |
| N/A | `but absorb` |

---

## Common Workflows

### Fix typo in old commit message
```bash
but status                           # Find the commit SHA
but describe <sha> -m "Fixed message"
```

### Squash multiple commits
```bash
but status                           # Get SHAs
but rub <source> <target>            # Squash
but describe <new-sha> -m "Combined" # Update message
```

### Amend change to old commit
```bash
# Edit the file, then:
but status                           # Check for 🔒 dependency
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

## Claude Code Hooks Integration

GitButler provides hooks that integrate with Claude Code to automatically manage commits during AI-assisted development sessions.

### Available Hooks

| Hook | Trigger | Command | Purpose |
|------|---------|---------|---------|
| **PreToolUse** | Before Edit/MultiEdit/Write | `but claude pre-tool` | Prepare context |
| **PostToolUse** | After code modifications | `but claude post-tool` | Auto-assign changes |
| **Stop** | When agent session ends | `but claude stop` | Commit session work |

### Configuration

Add hooks to your Claude settings file:

**User-level** (`~/.claude/settings.json`):
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|MultiEdit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "but claude pre-tool"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Edit|MultiEdit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "but claude post-tool"
          }
        ]
      }
    ],
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "but claude stop"
          }
        ]
      }
    ]
  }
}
```

**Project-level** (`.claude/settings.json` or `.claude/settings.local.json` for uncommitted)

### Benefits

- **Auto-assigns changes**: Changes from concurrent Claude sessions go to appropriate branches
- **Generates commit messages**: Uses user prompts for meaningful commit messages
- **Eliminates manual git**: No need for manual `but commit` during sessions
- **Works with virtual branches**: Seamlessly integrates with stacked/virtual branch workflows

### How It Works

1. **PreToolUse**: Before Claude edits a file, GitButler captures context
2. **PostToolUse**: After modifications, changes are automatically assigned to the current branch
3. **Stop**: When the session ends, GitButler commits all pending changes with a generated message

For detailed setup instructions, see `references/hooks.md`.

---

## Reference Files

For detailed documentation, see:
- `references/cheatsheet.md` - Quick command reference with git → but mappings
- `references/tutorial.md` - Comprehensive step-by-step workflows
- `references/hooks.md` - Claude Code hooks setup guide
