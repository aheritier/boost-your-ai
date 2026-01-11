---
description: Auto-amend changes into correct commits
allowed-tools: Bash(but:*), Bash(git:*)
---

# GitButler Absorb

Auto-amend uncommitted changes into the correct historical commits. GitButler intelligently detects which commit introduced the lines you modified and amends your changes into that commit.

## Pre-flight Check

First, verify this is a GitButler workspace:

```
Current branch: !`git branch --show-current`
```

If the branch is NOT `gitbutler/workspace`, STOP and inform the user:
> "This directory is not a GitButler workspace. Please run `but` to initialize GitButler first, or use standard git commands."

## Current Status

Show the current workspace state:

```
!`but status 2>&1 || echo "GitButler not available"`
```

## Absorb Workflow

1. **Analyze changes**: Review the uncommitted modifications shown above
2. **Run absorb**: Execute `but absorb` to auto-amend changes into their originating commits
3. **Report results**: Show which changes were absorbed into which commits
4. **Handle remainders**: If any changes couldn't be absorbed (new code, not modifications), inform the user they need to commit these separately

## Important Notes

- Absorb only works for **modifications to existing lines** - it traces back which commit added those lines
- New code (additions that don't modify existing lines) cannot be absorbed and must be committed normally
- If absorb fails, suggest using `/gitbutler:commit` for the remaining changes

## See Also

- `/gitbutler:commit` - Commit remaining changes after absorb
- `/gitbutler:checkpoint` - Create a safety checkpoint before absorb
- `/gitbutler:undo` - Undo if absorb results aren't what you expected
