# GitButler Claude Code Hooks Setup Guide

This guide covers how to configure GitButler hooks for automatic code management during Claude Code sessions.

## Overview

GitButler's Claude Code hooks automate the workflow of committing changes during AI-assisted development. Instead of manually running `but commit` after each change, hooks handle this automatically.

## Prerequisites

- GitButler CLI (`but`) installed and in PATH
- Claude Code installed
- A repository with GitButler initialized (`gitbutler/workspace` branch active)

## Configuration Files

Hooks can be configured at different levels:

| Location | Scope | Committed |
|----------|-------|-----------|
| `~/.claude/settings.json` | All projects | N/A (user home) |
| `.claude/settings.json` | This project | ✅ Yes (team shares) |
| `.claude/settings.local.json` | This project | ❌ No (personal) |

## Full Configuration

Add this to your chosen settings file:

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

## Hook Details

### PreToolUse Hook

**Trigger**: Before Claude executes Edit, MultiEdit, or Write tools

**What it does**:
- Captures the current workspace state
- Records which files are about to be modified
- Stores context for commit message generation

**Command**: `but claude pre-tool`

### PostToolUse Hook

**Trigger**: After Claude completes Edit, MultiEdit, or Write tools

**What it does**:
- Detects which files were modified
- Auto-assigns changes to the current virtual branch
- Prepares changes for eventual commit

**Command**: `but claude post-tool`

### Stop Hook

**Trigger**: When a Claude session/agent stops

**What it does**:
- Reviews all pending changes from the session
- Generates commit message from user prompts and context
- Commits changes with descriptive message
- Ensures no work is lost when session ends

**Command**: `but claude stop`

## Step-by-Step Setup

### 1. Verify GitButler is Active

```bash
git branch --show-current
# Should output: gitbutler/workspace
```

### 2. Verify `but` CLI is Available

```bash
but --version
# Should show version number
```

### 3. Create/Update Settings File

For user-level (all projects):
```bash
# Create directory if needed
mkdir -p ~/.claude

# Edit settings
nano ~/.claude/settings.json
```

For project-level:
```bash
# Create directory if needed
mkdir -p .claude

# Edit settings (committed to repo)
nano .claude/settings.json

# OR for personal settings (not committed)
nano .claude/settings.local.json
```

### 4. Restart Claude Code

After adding hooks, restart Claude Code to load the new configuration.

### 5. Test the Integration

1. Open a Claude Code session
2. Ask Claude to make a code change
3. Watch for GitButler automatically handling the commit
4. Check `but status` to see the result

## Troubleshooting

### Hook Not Triggering

1. **Check settings file location**: Ensure file is in correct location
2. **Verify JSON syntax**: Use a JSON validator
3. **Check matcher pattern**: `Edit|MultiEdit|Write` must match exactly
4. **Restart Claude Code**: Settings require restart to take effect

### `but` Command Not Found

```bash
# Check if but is in PATH
which but

# If not found, add GitButler to PATH
export PATH="$PATH:/path/to/gitbutler/bin"
```

### Not on GitButler Workspace

Hooks require an active GitButler workspace:

```bash
# Check current branch
git branch --show-current

# If not gitbutler/workspace, initialize GitButler in the repo
```

### Changes Not Being Committed

1. Check `but status` for pending changes
2. Verify the Stop hook is configured
3. Check for error messages in Claude Code output

## Best Practices

### 1. Use Project-Level Settings for Team Projects

If your team uses GitButler, add `.claude/settings.json` to the repository so everyone benefits from automatic commits.

### 2. Use Local Settings for Personal Preferences

If you want hooks but your team doesn't use GitButler, use `.claude/settings.local.json` and add it to `.gitignore`.

### 3. Create Snapshots Before Experiments

```bash
but snapshot -m "Before experimental changes"
```

Then if hooks create unwanted commits, you can restore:

```bash
but restore <snapshot-sha> --force
```

### 4. Monitor with `but oplog`

Keep an eye on what hooks are doing:

```bash
but oplog
```

This shows all operations including hook-triggered commits.

## Advanced Configuration

### Conditional Hooks (by file pattern)

The matcher supports regex patterns:

```json
{
  "matcher": "Edit|MultiEdit|Write",
  "hooks": [...]
}
```

### Multiple Hooks Per Event

You can chain multiple commands:

```json
{
  "PostToolUse": [
    {
      "matcher": "Edit|MultiEdit|Write",
      "hooks": [
        {
          "type": "command",
          "command": "but claude post-tool"
        },
        {
          "type": "command",
          "command": "echo 'Changes recorded'"
        }
      ]
    }
  ]
}
```

## Reference

- [GitButler Claude Code Hooks Documentation](https://docs.gitbutler.com/features/ai-integration/claude-code-hooks)
- [Claude Code Hooks Documentation](https://docs.anthropic.com/en/docs/claude-code/hooks)
- [GitButler CLI Reference](https://docs.gitbutler.com/cli)
