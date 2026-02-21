# Claude Skills

Custom slash commands for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Installation

Copy the `.md` files into your user-level commands directory:

```bash
cp *.md ~/.claude/commands/
```

Or symlink individual skills:

```bash
ln -s /path/to/claude-skills/learn-repo.md ~/.claude/commands/learn-repo.md
```

## Available Skills

| Skill | Command | Description |
|-------|---------|-------------|
| [learn-repo.md](learn-repo.md) | `/learn-repo owner/repo` | Reads a GitHub repo's README, structure, config, and source files, then produces a coding style guide so the AI can write code matching the repo's conventions. |
