# Cyanea Claude Configurations

Shared [Claude Code](https://docs.anthropic.com/en/docs/claude-code) configurations for the cyanea-io organization.

## What's Inside

```
.claude/
├── CLAUDE.md      # Repository context and overview
├── ROADMAP.md     # Configuration development roadmap
└── rules/         # Reusable rule files
    ├── elixir.md  # Elixir development conventions
    ├── phoenix.md # Phoenix framework patterns
    ├── testing.md # Testing guidelines
    ├── git.md     # Git workflow rules
    └── security.md# Security best practices
```

## Usage

### For New Projects

Copy the `.claude` directory into your project root:

```bash
cp -r path/to/.claude/.claude your-project/.claude
```

### As a Submodule

```bash
git submodule add git@github.com:cyanea-io/.claude.git .claude-shared
ln -s .claude-shared/.claude .claude
```

### Reference Only

Browse the rule files for guidance when setting up your own configurations.

## Rules Overview

| Rule | Purpose |
|------|---------|
| **elixir.md** | Pattern matching, pipe operators, documentation, OTP |
| **phoenix.md** | Contexts, LiveView, components, routing, Ecto |
| **testing.md** | Test organization, factories, coverage, mocking |
| **git.md** | Branch naming, commits, PRs, releases |
| **security.md** | OWASP prevention, secrets, validation, headers |

## Contributing

1. Fork this repository
2. Create a branch for your changes
3. Update or add rule files
4. Submit a pull request

## License

MIT
