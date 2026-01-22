---
description: Create named recovery checkpoint
argument-hint: [name]
allowed-tools: Bash(but:*), Bash(git:*)
---

# GitButler Checkpoint

Create a named snapshot for easy recovery. Checkpoints are saved in GitButler's operation log and can be restored at any time.

**Checkpoint name**: $ARGUMENTS

## Pre-flight Check

First, verify this is a GitButler workspace:

```
Current branch: !`git branch --show-current`
```

If the branch is NOT `gitbutler/workspace`, STOP and inform the user:

> "This directory is not a GitButler workspace. Please run `but` to initialize GitButler first, or use standard git commands."

## Current Status

Show workspace state that will be checkpointed:

```
!`but status 2>&1 || echo "GitButler not available"`
```

## Checkpoint Workflow

1. **Determine checkpoint name**:

   - If name provided ($ARGUMENTS), use that
   - If no name provided, generate a descriptive name like "before-refactor" or "working-state"

2. **Create snapshot**: Run `but oplog snapshot -m "<name>"`

3. **Confirm creation**: Show the oplog entry for reference

4. **Provide recovery info**: Tell user how to restore this checkpoint later

## Command Syntax

```bash
but oplog snapshot -m "checkpoint-name"
```

## Viewing Checkpoints

To see available checkpoints:

```bash
but oplog
```

## Restoring a Checkpoint

To restore to a checkpoint:

```bash
but restore <sha> --force
```

## When to Create Checkpoints

- Before attempting risky operations (squash, rebase, large refactors)
- After completing a logical piece of work
- Before experimenting with different approaches
- When you have a "known good state" you might want to return to

## Tips

- Use descriptive names: "before-auth-refactor", "working-login-flow"
- Checkpoints are cheap - create them liberally
- Use `/gitbutler:undo` for simple single-step recovery
- Use checkpoint restore for multi-step recovery

## See Also

- `/gitbutler:undo` - Quick single-step undo
- `/gitbutler:squash` - Operation that benefits from checkpoints
- `/gitbutler:move` - Operation that benefits from checkpoints
