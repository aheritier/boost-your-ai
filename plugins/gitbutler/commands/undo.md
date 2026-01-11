---
description: Undo last GitButler operation
allowed-tools: Bash(but:*), Bash(git:*)
---

# GitButler Undo

Undo the last GitButler operation. This is a safe, reversible way to recover from mistakes.

## Pre-flight Check

First, verify this is a GitButler workspace:

```
Current branch: !`git branch --show-current`
```

If the branch is NOT `gitbutler/workspace`, STOP and inform the user:
> "This directory is not a GitButler workspace. Please run `but` to initialize GitButler first, or use standard git commands."

## Recent Operations

Show recent operations from the oplog:

```
!`but oplog 2>&1 | head -20 || echo "GitButler not available"`
```

## Undo Workflow

1. **Review last operation**: Show what will be undone from the oplog above
2. **Confirm with user**: Ask for confirmation before proceeding
   > "This will undo: [operation description]. Proceed?"
3. **Execute undo**: Run `but undo`
4. **Show result**: Display the restored state

## Command Syntax

```bash
# Undo last operation
but undo

# View operation history
but oplog

# Restore to specific point (for multi-step undo)
but restore <sha> --force
```

## What Can Be Undone

GitButler tracks all operations in its oplog:
- Commits
- Squashes
- Branch operations
- File movements
- Message edits
- And more...

## Multi-Step Undo

For undoing multiple operations:
1. Run `but oplog` to see history
2. Find the SHA of the state you want to restore
3. Run `but restore <sha> --force`

Or use `/gitbutler:checkpoint` proactively to create named restore points.

## Tips

- Undo is safe - the operation log preserves everything
- For multiple undos, consider using `but restore` to a specific checkpoint
- When in doubt, create a checkpoint before risky operations
- Use `/gitbutler:checkpoint` to create named save points

## See Also

- `/gitbutler:checkpoint` - Create named restore points proactively
- `/gitbutler:squash` - Common operation you might want to undo
- `/gitbutler:move` - Common operation you might want to undo
