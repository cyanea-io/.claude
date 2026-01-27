# Cyanea Claude Configurations

> Shared Claude Code configurations for the cyanea-io organization

This repository contains Claude Code context files, rules, and configurations that can be shared across cyanea-io projects.

## Purpose

- **Standardize** AI-assisted development practices across projects
- **Share** common rules, conventions, and context
- **Version control** Claude configurations alongside code
- **Template** for new projects in the organization

---

## Repository Structure

```
.claude/
├── .claude/
│   ├── CLAUDE.md          # This file - repository context
│   ├── ROADMAP.md         # Configuration roadmap
│   └── rules/             # Reusable rule files
│       ├── elixir.md      # Elixir development rules
│       ├── phoenix.md     # Phoenix framework rules
│       ├── testing.md     # Testing conventions
│       ├── git.md         # Git workflow rules
│       └── security.md    # Security guidelines
├── templates/             # Project-specific templates (future)
└── README.md              # Public documentation
```

---

## Usage

### Option 1: Git Submodule

Add this repository as a submodule in your project:

```bash
git submodule add git@github.com:cyanea-io/.claude.git .claude-shared
```

Then symlink or copy the rules you need into your project's `.claude/` directory.

### Option 2: Direct Copy

Copy the relevant files from this repository into your project's `.claude/` directory and customize as needed.

### Option 3: Reference

Use this repository as a reference when setting up Claude configurations for new projects.

---

## Rules Overview

| Rule File | Purpose |
|-----------|---------|
| `elixir.md` | Elixir idioms, OTP patterns, formatting |
| `phoenix.md` | Phoenix/LiveView conventions, contexts |
| `testing.md` | Test organization, coverage, mocking |
| `git.md` | Branch naming, commit messages, PR workflow |
| `security.md` | OWASP guidelines, secrets management |

---

## Organization Standards

### Elixir/Phoenix Projects

All Elixir projects in cyanea-io should follow:

- Elixir 1.17+ with latest Phoenix
- Standard `mix format` configuration
- Credo for linting with `--strict` mode
- Dialyzer for type checking
- ExCoveralls for test coverage (target: 80%+)

### Code Review

- All PRs require at least one review
- CI must pass before merge
- Squash commits on merge to main

### Documentation

- Public modules must have `@moduledoc`
- Public functions must have `@doc` and typespecs
- Complex logic should have inline comments

---

## Contributing

1. Create a branch for your changes
2. Update relevant rule files
3. Test changes in a real project
4. Submit PR with description of changes

---

## Related Resources

- [Cyanea Main Repository](../cyanea/) - The main application
- [Elixir Style Guide](https://github.com/christopheradams/elixir_style_guide)
- [Phoenix Best Practices](https://hexdocs.pm/phoenix/overview.html)
