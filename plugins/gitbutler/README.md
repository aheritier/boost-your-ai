# GitButler Skill for Claude Code

A Claude Code skill that provides guidance for using [GitButler CLI](https://docs.gitbutler.com/cli) (`but`) for safe git history manipulation.

## Overview

This skill teaches Claude how to use GitButler instead of raw git commands when working in a GitButler-managed repository. GitButler provides:

- **Safe undo/redo** - Full operation history with time-travel capabilities
- **Smart amending** - Auto-detect which commit a change belongs to (`but absorb`)
- **Easy squashing** - Combine commits without interactive rebase
- **Virtual branches** - Work on multiple features simultaneously without switching

## Prerequisites

- [GitButler CLI](https://docs.gitbutler.com/cli) installed (`but` command available)
- A repository initialized with GitButler (check: `git branch --show-current` returns `gitbutler/workspace`)

## Installation

Add this plugin to your Claude Code configuration:

```bash
# From your project directory
claude mcp add-plugin /path/to/plugins/gitbutler
```

Or add to your settings manually:

```json
{
  "plugins": [
    "/path/to/plugins/gitbutler"
  ]
}
```

## Usage

Once installed, Claude will automatically use GitButler commands when:

- You ask to edit, squash, or rebase commits
- You want to fix commit messages
- You need to undo git operations
- You're working with virtual branches

### Slash Commands

The plugin provides these commands for common workflows:

| Command | Description | Example |
|---------|-------------|---------|
| `/gitbutler:absorb` | Auto-amend changes into correct commits | `/gitbutler:absorb` |
| `/gitbutler:commit` | Commit changes to virtual branch | `/gitbutler:commit fix login validation` |
| `/gitbutler:squash` | Squash commits together | `/gitbutler:squash abc123 def456` |
| `/gitbutler:fix-msg` | Edit a commit message | `/gitbutler:fix-msg abc123 "Better message"` |
| `/gitbutler:branch` | Manage virtual branches | `/gitbutler:branch new feature-auth` |
| `/gitbutler:move` | Move file/commit to another branch | `/gitbutler:move g0 feature-auth` |
| `/gitbutler:checkpoint` | Create named recovery checkpoint | `/gitbutler:checkpoint before-refactor` |
| `/gitbutler:undo` | Undo last GitButler operation | `/gitbutler:undo` |

### Example Prompts

- "Squash the last 3 commits into one"
- "Fix the typo in my previous commit message"
- "Undo my last git operation"
- "Help me work on two features at the same time"

### Git Assistant Agent

The plugin includes an intelligent agent that handles git-related conversations naturally. Unlike slash commands (which you invoke explicitly), the agent activates automatically when you discuss git operations in a GitButler workspace.

**Automatic Activation:**
The agent triggers automatically when you're on a `gitbutler/workspace` branch and discuss:
- Committing changes
- Cleaning up commit history
- Undoing mistakes
- Organizing work across branches

**Example Conversations:**
- "I've made some changes, help me commit them properly"
- "Clean up my commit history - I have too many small commits"
- "Undo my last change, I made a mistake"
- "I'm working on two features at once, help me organize"

**Behavior:**
The agent follows a "suggest and confirm" pattern:
1. Analyzes your current GitButler state
2. Suggests appropriate actions with explanations
3. Waits for your confirmation before executing
4. Reports results and suggests next steps

**Note:** In non-GitButler repositories, the agent must be invoked manually.

## Documentation

- **[SKILL.md](skills/gitbutler/SKILL.md)** - Main skill documentation with command reference
- **[Cheatsheet](skills/gitbutler/references/cheatsheet.md)** - Quick Git-to-But command mapping
- **[Tutorial](skills/gitbutler/references/tutorial.md)** - Step-by-step learning guide
- **[Hooks Guide](skills/gitbutler/references/hooks.md)** - Claude Code hooks integration

## Author

[Arnaud Héritier](https://github.com/aheritier)

## License

MIT
