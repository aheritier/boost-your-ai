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
- Git

### Setup

1. Fork and clone the repository:

   ```bash
   git clone https://github.com/YOUR-USERNAME/boost-your-ai.git
   cd boost-your-ai
   ```

2. Install the marketplace locally for testing:
   ```bash
   claude mcp add-plugin /path/to/boost-your-ai
   ```

## Development Workflow

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
| `feat`     | New feature        | `feat(jj-lsp): add hover documentation`        |
| `fix`      | Bug fix            | `fix(commands): correct argument parsing`       |
| `docs`     | Documentation      | `docs: update installation guide`               |
| `style`    | Formatting         | `style: fix markdown linting issues`            |
| `refactor` | Code restructuring | `refactor(skills): simplify SKILL.md structure` |
| `perf`     | Performance        | `perf(commands): reduce status check calls`     |
| `test`     | Testing            | `test(jj-lsp): add startup timeout tests`      |
| `chore`    | Maintenance        | `chore: update dependencies`                    |

### Scopes

Use the plugin name or component:

- `<plugin-name>` - Plugin changes (e.g. `jj-lsp`)
- `commands` - Command file changes
- `skills` - Skill file changes
- `docs` - Documentation changes

### Examples

**Simple feature:**

```
feat(jj-lsp): add hover documentation support
```

**Bug fix with details:**

```
fix(jj-lsp): correct startup timeout configuration

The startupTimeout value was being ignored on slow machines.
Now correctly waits for the language server to initialize.

Fixes #42
```

**Breaking change:**

```
feat(jj-lsp)!: rename startup-timeout to startupTimeout

BREAKING CHANGE: The startup-timeout config key has been
renamed to startupTimeout for consistency.
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
