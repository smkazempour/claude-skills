# Claude Skills

Custom skills and slash commands for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

This repo mirrors the layout of your `~/.claude/` directory:

- **`commands/`** — slash commands (single `.md` files, invoked as `/name`).
- **`skills/`** — skills (each a folder containing a `SKILL.md`).

## Installation

Copy each group into the matching user-level directory:

```bash
cp -r commands/* ~/.claude/commands/
cp -r skills/*   ~/.claude/skills/
```

Or symlink an individual item, e.g.:

```bash
ln -s /path/to/claude-skills/commands/learn-repo.md ~/.claude/commands/learn-repo.md
ln -s /path/to/claude-skills/skills/make-discussion-slides ~/.claude/skills/make-discussion-slides
```

Restart Claude Code (or start a new session) so it rescans those directories.

## Slash commands

| Command | Invocation | Description |
|---------|------------|-------------|
| [critique.md](commands/critique.md) | `/critique [file-or-description]` | Spawns parallel reviewer agents that examine work from different angles, then synthesizes findings and applies revisions. |
| [learn-repo.md](commands/learn-repo.md) | `/learn-repo owner/repo` | Reads a GitHub repo's README, structure, config, and source files, then produces a coding style guide so the AI can write code matching the repo's conventions. |
| [push.md](commands/push.md) | `/push [message] [--update-docs] [--file path]` | Stages, commits, and pushes changes to remote. Optionally updates README.md and CLAUDE.md to reflect latest changes before pushing. |

## Skills

| Skill | Description |
|-------|-------------|
| [make-discussion-slides](skills/make-discussion-slides/SKILL.md) | Creates a Beamer discussion slide deck for a conference paper. Use when preparing discussant slides for a paper presentation. |
