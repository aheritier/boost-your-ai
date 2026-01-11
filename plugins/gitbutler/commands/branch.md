---
description: Manage virtual branches
argument-hint: [action] [name]
allowed-tools: Bash(but:*), Bash(git:*)
---

# GitButler Branch Management

Create, list, switch, or manage virtual branches. Virtual branches allow you to work on multiple features simultaneously without switching contexts.

**Arguments**: $ARGUMENTS
- First argument ($1): Action (new, list, delete, apply, unapply)
- Second argument ($2): Branch name (for new, delete, apply, unapply)

## Pre-flight Check

First, verify this is a GitButler workspace:

```
Current branch: !`git branch --show-current`
```

If the branch is NOT `gitbutler/workspace`, STOP and inform the user:
> "This directory is not a GitButler workspace. Please run `but` to initialize GitButler first, or use standard git commands."

## Current Status

Show existing branches and their state:

```
!`but status 2>&1 || echo "GitButler not available"`
```

## Branch Actions

Based on the action provided ($1), execute the appropriate command:

### `new` - Create a new virtual branch
```bash
but branch new <name>
```
Creates a new virtual branch. Uncommitted changes can then be assigned to it.

### `list` - List all branches
```bash
but branch list
```
Shows all virtual branches with their status.

### `delete` - Delete a branch
```bash
but branch delete <name>
```
Removes a virtual branch. Changes in the branch should be committed or moved first.

### `apply` - Show a branch (make it active)
```bash
but branch apply <name>
```
Makes a branch visible in the working directory.

### `unapply` - Hide a branch (make it inactive)
```bash
but branch unapply <name>
```
Temporarily hides a branch from the working directory without deleting it.

## Workflow

1. **Parse action**: Determine which action was requested
   - If no action provided, show the list of branches and available actions
2. **Validate branch name**: For actions requiring a name, ensure it's provided
3. **Execute action**: Run the appropriate `but branch` command
4. **Show result**: Display updated branch status

## Virtual Branches Concept

Unlike traditional git branches, GitButler's virtual branches:
- Can all be "checked out" simultaneously
- Allow changes to be moved between branches freely
- Don't require stashing when switching contexts
- Make it easy to separate unrelated changes

## Tips

- Create separate branches for unrelated changes
- Use `/gitbutler:move` to reassign files between branches
- Unapply branches you're not actively working on to reduce noise

## See Also

- `/gitbutler:move` - Move files or commits between branches
- `/gitbutler:commit` - Commit changes to a specific branch
