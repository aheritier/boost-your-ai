---
description: Move file/commit to another branch
argument-hint: [source-id] [target-branch]
allowed-tools: Bash(but:*), Bash(git:*)
---

# GitButler Move

Move files or commits between virtual branches using the `rub` multi-tool.

**Arguments**: $ARGUMENTS
- First argument ($1): Source ID (file ID like `g0`, `g1` or commit SHA)
- Second argument ($2): Target branch name

## Pre-flight Check

First, verify this is a GitButler workspace:

```
Current branch: !`git branch --show-current`
```

If the branch is NOT `gitbutler/workspace`, STOP and inform the user:
> "This directory is not a GitButler workspace. Please run `but` to initialize GitButler first, or use standard git commands."

## Safety Reminder

**Before moving commits**, consider creating a checkpoint:
```bash
but snapshot -m "before-move"
```
Or use `/gitbutler:checkpoint before-move` to create a named restore point.

## Current Status

Show workspace with file IDs and branch names:

```
!`but status 2>&1 || echo "GitButler not available"`
```

## Understanding IDs

In the status output above:
- **File IDs**: Short identifiers like `g0`, `g1`, `g2` represent uncommitted file changes
- **Commit SHAs**: Full or abbreviated commit hashes
- **Branch names**: The virtual branch identifiers (often shown as 2-letter codes like `al`, `ut`)

## Move Workflow

1. **Identify source and target**:
   - If both provided ($1 and $2), proceed
   - If missing, show the status and ask user to specify

2. **Determine move type**:
   - File → Branch: Reassign uncommitted file to different branch
   - Commit → Branch: Move entire commit to different branch

3. **Execute move**: Run `but rub <source> <target>`

4. **Confirm result**: Show updated status

## Command Examples

```bash
# Move file g0 to branch "al"
but rub g0 al

# Move commit abc123 to branch "ut"
but rub abc123 ut
```

## The `rub` Multi-Tool

`rub` behavior depends on source and target types:

| Source | Target | Result |
|--------|--------|--------|
| File ID | Branch | Reassign file to branch |
| File ID | Commit | Amend file into commit |
| Commit | Commit | Squash commits |
| Commit | Branch | Move commit to branch |

## Tips

- Use this to separate mixed changes into appropriate branches
- Moving commits preserves their content and authorship
- Use `/gitbutler:undo` if the move wasn't what you intended

## See Also

- `/gitbutler:branch` - Create new branches to move items to
- `/gitbutler:checkpoint` - Create safety checkpoint before moving
- `/gitbutler:undo` - Undo the move if needed
