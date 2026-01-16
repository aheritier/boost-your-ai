# Boost Your AI - Project Instructions

A Claude Code plugin marketplace for boosting AI-assisted development workflows.

## Project Overview

This repository is a collection of Claude Code plugins that extend Claude's capabilities with specialized skills, commands, and workflows. Each plugin is self-contained and can be installed independently.

## Directory Structure

```
boost-your-ai/
├── .claude-plugin/          # Root plugin manifest (marketplace itself)
│   └── plugin.json
├── plugins/                 # Individual plugins
│   └── <plugin-name>/
│       ├── .claude-plugin/
│       │   └── plugin.json  # Plugin manifest (required)
│       ├── README.md        # Plugin documentation
│       ├── commands/        # Slash commands (optional)
│       │   └── <cmd>.md
│       └── skills/          # Knowledge/guidance skills (optional)
│           └── <skill>/
│               ├── SKILL.md
│               └── references/
├── README.md               # Marketplace documentation
└── CLAUDE.md               # This file
```

## Plugin Development Guidelines

### Creating a New Plugin

1. Create directory: `plugins/<plugin-name>/`
2. Add manifest: `plugins/<plugin-name>/.claude-plugin/plugin.json`
3. Add README: `plugins/<plugin-name>/README.md`
4. Add skills and/or commands as needed

### Plugin Manifest (plugin.json)

```json
{
  "name": "plugin-name",
  "description": "Brief description (shown in plugin list)",
  "version": "0.0.1",
  "author": {
    "name": "Author Name",
    "url": "https://github.com/username"
  }
}
```

### Command Files

Commands are markdown files in `commands/` with YAML frontmatter:

```markdown
---
description: Short description (max 60 chars)
argument-hint: [arg1] [arg2]
allowed-tools: Bash(tool:*), Read, Write
---

# Command Title

Instructions for Claude to execute this command...
```

**Key principles:**

- Commands are instructions FOR Claude, not messages TO users
- Use `!`backticks`` for inline bash execution to gather context
- Use `$ARGUMENTS`, `$1`, `$2` for argument interpolation
- Keep `allowed-tools` as restrictive as possible

### Skill Files

Skills are knowledge documents in `skills/<name>/SKILL.md`:

```markdown
---
name: skill-name
description: When to use this skill
---

# Skill Content

Reference documentation, workflows, best practices...
```

**Key principles:**

- Skills provide knowledge/guidance, not executable actions
- Use `references/` subdirectory for detailed documentation
- Include practical examples and common patterns

## Code Style

### Markdown Files

- Use ATX headers (`#`, `##`, `###`)
- Use fenced code blocks with language identifiers
- Keep lines under 100 characters when practical
- Use tables for structured data

### YAML Frontmatter

- Keep descriptions concise (under 60 characters)
- Use lowercase for field names
- Quote strings containing special characters

## Git Workflow

This project uses GitButler for version control. When in a `gitbutler/workspace` branch:

- Use `but status` instead of `git status`
- Use `but commit` instead of `git commit`
- Use `but absorb` to auto-amend changes
- Use `but rub` to move files/commits between branches
- Create checkpoints before risky operations: `but oplog snapshot -m "name"`

## Commit Convention

This project follows the [Conventional Commits](https://www.conventionalcommits.org/) standard.

### Commit Message Format

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Types

| Type       | Description                                             |
| ---------- | ------------------------------------------------------- |
| `feat`     | New feature or capability                               |
| `fix`      | Bug fix                                                 |
| `docs`     | Documentation changes only                              |
| `style`    | Formatting, missing semicolons, etc. (no code change)   |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf`     | Performance improvement                                 |
| `test`     | Adding or updating tests                                |
| `chore`    | Build process, dependencies, or tooling changes         |

### Scopes

For this project, use these scopes:

| Scope           | When to use                     |
| --------------- | ------------------------------- |
| `gitbutler`     | Changes to the gitbutler plugin |
| `<plugin-name>` | Changes to a specific plugin    |
| `commands`      | Changes to command files        |
| `skills`        | Changes to skill files          |
| `docs`          | Documentation changes           |

### Examples

```
feat(gitbutler): add squash command for combining commits
fix(gitbutler): correct branch detection in absorb command
docs: update README with installation instructions
chore: add .gitignore for build artifacts
refactor(commands): extract common pre-flight check pattern
```

### Breaking Changes

For breaking changes, add `!` after the type/scope or add `BREAKING CHANGE:` in the footer:

```
feat(gitbutler)!: rename /commit to /smart-commit

BREAKING CHANGE: The /gitbutler:commit command has been renamed to /gitbutler:smart-commit
```

## Testing Plugins

1. Install the plugin locally:

   ```bash
   claude mcp add-plugin /path/to/plugins/<plugin-name>
   ```

2. Restart Claude Code to load changes

3. Test commands: `/<plugin-name>:<command>`

4. Verify skill auto-detection by describing relevant scenarios

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md) for detailed guidelines.

**Quick start:**

1. Create a new virtual branch: `but branch new feature-name`
2. Make changes and assign to branch
3. Commit using conventional commits format (see above)
4. Push and create PR

## Available Plugins

### GitButler (`gitbutler`)

Safe git history manipulation using GitButler CLI.

**Commands:**

- `/gitbutler:absorb` - Auto-amend changes into correct commits
- `/gitbutler:commit` - Commit to virtual branch
- `/gitbutler:squash` - Combine commits
- `/gitbutler:fix-msg` - Edit commit message
- `/gitbutler:branch` - Manage virtual branches
- `/gitbutler:move` - Move files/commits between branches
- `/gitbutler:checkpoint` - Create recovery checkpoint
- `/gitbutler:undo` - Undo last operation
