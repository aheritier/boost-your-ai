# Contributing to Boost Your AI

Thank you for your interest in contributing! This guide will help you get started.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Commit Convention](#commit-convention)
- [Pull Request Process](#pull-request-process)
- [Plugin Development](#plugin-development)

## Code of Conduct

Be respectful, inclusive, and constructive. We're all here to learn and build great tools together.

## Getting Started

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed
- [GitButler CLI](https://docs.gitbutler.com/cli) (recommended for development)
- Git

### Setup

1. Fork and clone the repository:

   ```bash
   git clone https://github.com/YOUR-USERNAME/boost-your-ai.git
   cd boost-your-ai
   ```

2. Initialize GitButler (optional but recommended):

   ```bash
   but
   ```

3. Install the marketplace locally for testing:
   ```bash
   claude mcp add-plugin /path/to/boost-your-ai
   ```

## Development Workflow

### Using GitButler (Recommended)

GitButler enables working on multiple features simultaneously:

```bash
# Create a new virtual branch
but branch new my-feature

# Check status
but status

# Assign files to your branch
but rub <file-id> my-feature

# Commit changes
but commit my-feature -m "feat(plugin): add new feature"

# Create checkpoint before risky operations
but oplog snapshot -m "before-refactor"

# Undo if something goes wrong
but undo
```

### Using Standard Git

```bash
git checkout -b my-feature
git add .
git commit -m "feat(plugin): add new feature"
git push origin my-feature
```

## Commit Convention

We follow [Conventional Commits](https://www.conventionalcommits.org/) for clear, automated changelogs.

### Format

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Types

| Type       | Description        | Example                                         |
| ---------- | ------------------ | ----------------------------------------------- |
| `feat`     | New feature        | `feat(gitbutler): add checkpoint command`       |
| `fix`      | Bug fix            | `fix(commands): correct argument parsing`       |
| `docs`     | Documentation      | `docs: update installation guide`               |
| `style`    | Formatting         | `style: fix markdown linting issues`            |
| `refactor` | Code restructuring | `refactor(skills): simplify SKILL.md structure` |
| `perf`     | Performance        | `perf(commands): reduce status check calls`     |
| `test`     | Testing            | `test(gitbutler): add absorb command tests`     |
| `chore`    | Maintenance        | `chore: update dependencies`                    |

### Scopes

Use the plugin name or component:

- `gitbutler` - GitButler plugin changes
- `commands` - Command file changes
- `skills` - Skill file changes
- `docs` - Documentation changes
- `<plugin-name>` - Other plugin changes

### Examples

**Simple feature:**

```
feat(gitbutler): add branch management command
```

**Bug fix with details:**

```
fix(gitbutler): correct workspace detection

The pre-flight check was failing on Windows due to
path separator differences. Now uses platform-agnostic
path handling.

Fixes #42
```

**Breaking change:**

```
feat(gitbutler)!: rename absorb to smart-amend

BREAKING CHANGE: The /gitbutler:absorb command has been
renamed to /gitbutler:smart-amend for clarity.
```

## Pull Request Process

### Before Submitting

1. **Test your changes** - Verify commands work as expected
2. **Follow commit convention** - Use conventional commit format
3. **Update documentation** - Update README if adding features
4. **Keep PRs focused** - One feature/fix per PR

### PR Template

When creating a PR, include:

```markdown
## Summary

Brief description of changes

## Type of Change

- [ ] New plugin
- [ ] New command
- [ ] New skill
- [ ] Bug fix
- [ ] Documentation
- [ ] Other (describe)

## Testing

How did you test these changes?

## Checklist

- [ ] Follows conventional commits
- [ ] Documentation updated
- [ ] Commands tested locally
```

### Review Process

1. Submit PR against `main` branch
2. Maintainers will review within a few days
3. Address any feedback
4. Once approved, maintainers will merge

## Plugin Development

### Creating a New Plugin

1. **Create directory structure:**

   ```
   plugins/my-plugin/
   ├── .claude-plugin/
   │   └── plugin.json
   ├── README.md
   ├── commands/        # Optional
   └── skills/          # Optional
   ```

2. **Add plugin manifest** (`plugin.json`):

   ```json
   {
     "name": "my-plugin",
     "description": "Brief description of your plugin",
     "version": "0.0.1",
     "author": {
       "name": "Your Name",
       "url": "https://github.com/username"
     }
   }
   ```

3. **Add README** with installation and usage instructions

4. **Add commands and/or skills** as needed

### Command Guidelines

- Keep descriptions under 60 characters
- Include pre-flight checks for safety
- Add "See Also" sections linking related commands
- Use safety reminders for destructive operations

### Skill Guidelines

- Provide practical examples
- Include reference documentation in `references/`
- Focus on knowledge/guidance, not executable actions

## Questions?

- Open an issue for bugs or feature requests
- Start a discussion for questions or ideas

Thank you for contributing! 🚀
