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

### Example Prompts

- "Squash the last 3 commits into one"
- "Fix the typo in my previous commit message"
- "Undo my last git operation"
- "Help me work on two features at the same time"

## Documentation

- **[SKILL.md](skills/gitbutler/SKILL.md)** - Main skill documentation with command reference
- **[Cheatsheet](skills/gitbutler/references/cheatsheet.md)** - Quick Git-to-But command mapping
- **[Tutorial](skills/gitbutler/references/tutorial.md)** - Step-by-step learning guide
- **[Hooks Guide](skills/gitbutler/references/hooks.md)** - Claude Code hooks integration

## Author

[Arnaud Héritier](https://github.com/aheritier)

## License

MIT
