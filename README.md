# Boost Your AI

A Claude Code skill marketplace for boosting AI-assisted development workflows.

## Installation

Add this marketplace to Claude Code:

```bash
/plugin add https://github.com/aheritier/boost-your-ai
```

Then restart Claude Code to load the skills.

## Available Skills

### GitButler (`gitbutler`)

Safe git history manipulation using the GitButler CLI (`but`).

**Features:**
- 🔄 **Squash commits** - `but rub <source> <target>`
- ✏️ **Edit commit messages** - `but describe <sha> -m "new message"`
- 🎯 **Auto-amend to correct commit** - `but absorb`
- ⏪ **Undo any operation** - `but undo` / `but restore`
- 🌿 **Virtual branches** - Work on multiple features simultaneously

**Slash Commands:**

| Command | Description |
|---------|-------------|
| `/gitbutler:absorb` | Auto-amend changes into correct commits |
| `/gitbutler:commit` | Commit changes to virtual branch |
| `/gitbutler:squash` | Squash commits together |
| `/gitbutler:fix-msg` | Edit a commit message |
| `/gitbutler:branch` | Manage virtual branches |
| `/gitbutler:move` | Move file/commit to another branch |
| `/gitbutler:checkpoint` | Create named recovery checkpoint |
| `/gitbutler:undo` | Undo last GitButler operation |

**Auto-detection:** When on a `gitbutler/workspace` branch, Claude will automatically suggest `but` commands instead of raw git.

[View GitButler Skill Documentation](./plugins/gitbutler/skills/gitbutler/SKILL.md)

## Usage

Once installed, Claude will automatically use these skills when relevant. For example:

- "Help me squash these commits" → Uses GitButler skill
- "Fix my commit message" → Suggests `but describe`
- "Undo my last operation" → Recommends `but undo`

## Contributing

### Adding a New Plugin

1. Create a new directory under `plugins/`
2. Add `.claude-plugin/plugin.json` manifest:
   ```json
   {
     "name": "your-plugin",
     "description": "Brief description of your plugin",
     "version": "1.0.0"
   }
   ```
3. Add skills under `skills/<skill-name>/SKILL.md`
4. Submit a pull request

### Plugin Structure

```
plugins/your-plugin/
├── .claude-plugin/
│   └── plugin.json       # Required: Plugin manifest
├── README.md             # Recommended: Installation guide
└── skills/
    └── your-skill/
        ├── SKILL.md      # Required: Skill instructions
        └── references/   # Optional: Detailed documentation
```

## License

Apache 2.0 - See [LICENSE](./LICENSE)

## Author

[Arnaud Héritier](https://github.com/aheritier)
