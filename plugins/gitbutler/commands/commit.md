---
description: Commit changes to virtual branch
argument-hint: [message]
allowed-tools: Bash(but:*), Bash(git:*)
---

# GitButler Smart Commit

Commit changes to a virtual branch with an optional message. This command provides a smart workflow that can optionally absorb changes first.

**Provided message**: $ARGUMENTS

## Pre-flight Check

First, verify this is a GitButler workspace:

```
Current branch: !`git branch --show-current`
```

If the branch is NOT `gitbutler/workspace`, STOP and inform the user:
> "This directory is not a GitButler workspace. Please run `but` to initialize GitButler first, or use standard git commands."

## Current Status

Show the current workspace state with branches and uncommitted changes:

```
!`but status 2>&1 || echo "GitButler not available"`
```

## Commit Workflow

1. **Review status**: Analyze the uncommitted changes shown above
2. **Identify target branch**: Determine which virtual branch should receive the commit
   - If only one branch exists, use that branch
   - If multiple branches exist, ask the user which branch to commit to
3. **Consider absorb first**: If changes appear to be modifications to existing code (not new features), suggest running absorb first
4. **Commit changes**:
   - If a message was provided (`$ARGUMENTS`), use: `but commit <branch-name> -m "<message>"`
   - If no message provided, generate an appropriate commit message based on the changes
5. **Confirm result**: Show the new commit and updated status

## Command Variations

- `but commit <branch> -m "message"` - Commit all uncommitted changes on the branch
- `but commit <branch> -m "message" --only` - Commit only changes assigned to that branch (useful with multiple branches)

## Tips

- Use descriptive commit messages that explain WHY, not just WHAT
- If you have changes spanning multiple features, consider using `/gitbutler:branch` to create separate branches first

## See Also

- `/gitbutler:absorb` - Auto-amend changes into existing commits first
- `/gitbutler:branch` - Create branches to organize unrelated changes
- `/gitbutler:fix-msg` - Edit commit message after committing
