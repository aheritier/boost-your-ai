---
description: Edit a commit message
argument-hint: [sha] [new-message]
allowed-tools: Bash(but:*), Bash(git:*)
---

# GitButler Fix Message

Edit a commit message. GitButler handles the rebase automatically.

**Arguments**: $ARGUMENTS

- First argument ($1): Commit SHA to edit
- Remaining arguments: New commit message (wrap in quotes if it contains spaces)

## Pre-flight Check

First, verify this is a GitButler workspace:

```
Current branch: !`git branch --show-current`
```

If the branch is NOT `gitbutler/workspace`, STOP and inform the user:

> "This directory is not a GitButler workspace. Please run `but` to initialize GitButler first, or use standard git commands."

## Current Commits

Show recent commits to help identify the target:

```
!`but status 2>&1 || echo "GitButler not available"`
```

## Fix Message Workflow

1. **Identify target commit**:

   - If SHA provided ($1), use that commit
   - If no SHA provided, show the commit list and ask user which commit to edit

2. **Get new message**:

   - If message provided in arguments, use that
   - If no message provided, ask user for the new message
   - Suggest improvements if the current message is unclear

3. **Execute change**: Run `but reword <sha> -m "<new-message>"`

4. **Confirm result**: Show the updated commit with new message

## Command Syntax

```bash
but reword <sha> -m "New commit message"
```

## Tips

- Good commit messages explain WHY, not just WHAT
- Use conventional commit format if the project follows it: `type(scope): description`
- Keep the first line under 72 characters
- Use `/gitbutler:undo` if you need to revert the message change

## See Also

- `/gitbutler:squash` - Combine commits (then fix the message)
- `/gitbutler:undo` - Revert message change if needed
