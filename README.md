# Git Worktree Skill for Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

A Claude Code plugin that bundles an Agent Skill for safely managing Git worktrees during development.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview) v1.0.33+
- Git 2.5+ (worktree support)

## Installation

### Option A: Interactive install

```text
# 1. Start Claude Code
claude

# 2. Add this repository as a marketplace
/plugin marketplace add kndoshn/git-worktree-skill

# 3. Install the plugin
/plugin install git-worktree-skill@kndoshn-git-worktree-skill
```

### Option B: Repository configuration (for teams)

Add to `.claude/settings.json`:

```json
{
  "enabledPlugins": {
    "git-worktree-skill@kndoshn-git-worktree-skill": true
  },
  "extraKnownMarketplaces": {
    "kndoshn-git-worktree-skill": {
      "source": "github",
      "repo": "kndoshn/git-worktree-skill"
    }
  }
}
```

Collaborators will be prompted to install the marketplace and enable the plugin.

### Local development

```bash
git clone https://github.com/kndoshn/git-worktree-skill
claude --plugin-dir ./git-worktree-skill
```

## What the Skill covers

The Skill is model-invoked: it activates automatically when the conversation involves `git worktree`, parallel branch work, multiple working directories, or common worktree errors.

- Adding worktrees for new or existing branches
- Handling branch collisions ("already checked out")
- Safe cleanup (remove, prune, lock/unlock) with a preference for non-destructive inspection first

## Usage examples

- "Create a worktree for a hotfix while keeping my current changes intact."
- "This branch is already checked out in another worktree. What is the safest approach?"
- "List my worktrees and propose a safe cleanup plan."

## Repository structure

```text
git-worktree-skill/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── managing-git-worktrees/
        └── SKILL.md
```

## Troubleshooting

If `/plugin` is not recognized, ensure you have Claude Code v1.0.33 or later:

```bash
claude --version
```

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

## License

MIT License. See `LICENSE`.
