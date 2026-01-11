# Git Worktree Skill for Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

This repository provides a Claude Code plugin that bundles an Agent Skill for safely managing Git worktrees during development.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview) CLI
- Git 2.5+ (worktree support)

The Skill is model-invoked: it activates automatically when the conversation involves `git worktree`, parallel branch work, multiple working directories, or common worktree errors.

## What the Skill covers

- Adding worktrees for new or existing branches
- Handling branch collisions ("already checked out")
- Safe cleanup (remove, prune, lock/unlock) with a preference for non-destructive inspection first

## Install and test locally

Clone this repository, then run:

```bash
claude --plugin-dir ./git-worktree-skill
```

Restart Claude Code after changes so it reloads the plugin.

## Install from GitHub

```bash
git clone https://github.com/kndoshn/git-worktree-skill
claude --plugin-dir ./git-worktree-skill
```

## How to use

Ask Claude Code questions like:

- "Create a worktree for a hotfix while keeping my current changes intact."
- "This branch is already checked out in another worktree. What is the safest approach?"
- "List my worktrees and propose a safe cleanup plan."

## Sharing with others

For community distribution, Claude Code recommends:
1. Including a README with installation and usage instructions
2. Using semantic versioning in `.claude-plugin/plugin.json`
3. Distributing via a plugin marketplace

## Plugin metadata

Metadata lives in `.claude-plugin/plugin.json` and is used by Claude Code to discover the bundled Skill and version.

## Repository structure

```text
git-worktree-skill/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── managing-git-worktrees/
        └── SKILL.md
```

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

## License

MIT License. See `LICENSE`.
