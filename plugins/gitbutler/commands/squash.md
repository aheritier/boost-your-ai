---
description: Squash commits together
argument-hint: [source-sha] [target-sha]
allowed-tools: Bash(but:*), Bash(git:*)
---

# GitButler Squash

Squash (combine) multiple commits into one. The source commit is merged INTO the target commit.

**Arguments**: $ARGUMENTS

- First argument ($1): Source commit SHA (the commit to squash)
- Second argument ($2): Target commit SHA (the commit to squash into)

## Pre-flight Check

First, verify this is a GitButler workspace:

```
Current branch: !`git branch --show-current`
```

If the branch is NOT `gitbutler/workspace`, STOP and inform the user:

> "This directory is not a GitButler workspace. Please run `but` to initialize GitButler first, or use standard git commands."

## Safety Reminder

**Before squashing**, consider creating a checkpoint:

```bash
but oplog snapshot -m "before-squash"
```

Or use `/gitbutler:checkpoint before-squash` to create a named restore point.

## Current Commits

Show recent commits to help identify SHAs:

```
!`but status 2>&1 || echo "GitButler not available"`
```

## Squash Workflow

1. **Identify commits**:

   - If both SHAs provided ($1 and $2), proceed with those
   - If only one SHA provided, ask user for the second SHA
   - If no SHAs provided, show the commit list above and ask user to specify which commits to squash

2. **Explain the operation**:

   > "This will squash commit `<source>` INTO commit `<target>`. The source commit will be removed and its changes merged into the target."

3. **Execute squash**: Run `but rub <source-sha> <target-sha>`

4. **Confirm result**: Show the updated commit history

## Understanding `but rub` for Squashing

The `rub` command is GitButler's multi-tool. When both source and target are commits:

- `but rub abc123 def456` → Squash commit abc123 INTO def456
- The source commit disappears, target commit now contains both changes
- Commit message of target is preserved (can be edited with `/gitbutler:fix-msg`)

## Tips

- Squash related commits to keep history clean
- Squash "fixup" commits into their parent feature commit
- After squashing, consider using `/gitbutler:fix-msg` to update the commit message
- Use `/gitbutler:undo` if the squash result isn't what you expected

## See Also

- `/gitbutler:fix-msg` - Update the combined commit's message
- `/gitbutler:checkpoint` - Create safety checkpoint before squashing
- `/gitbutler:undo` - Undo the squash if needed
