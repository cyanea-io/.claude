# Git Workflow Rules

## Branch Naming

```
feature/short-description    # New features
fix/issue-number-description # Bug fixes
chore/description           # Maintenance tasks
docs/description            # Documentation only
refactor/description        # Code refactoring
```

Examples:
- `feature/user-authentication`
- `fix/123-login-timeout`
- `chore/update-dependencies`
- `docs/api-documentation`

## Commit Messages

Follow conventional commits format:

```
type(scope): short description

Longer description if needed. Explain the "why" not the "what".

Closes #123
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Formatting, no code change
- `refactor`: Code restructuring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

Examples:
```
feat(auth): add ORCID OAuth login

Integrates ORCID as an authentication provider for researcher identity
verification. Uses OAuth 2.0 flow with PKCE.

Closes #45
```

```
fix(upload): handle large file timeouts

Increases timeout for S3 uploads from 30s to 5min for files over 100MB.
Adds progress tracking to prevent false timeout errors.

Fixes #89
```

## Pull Request Workflow

### Creating PRs

1. Create a feature branch from `main`
2. Make commits following the conventions above
3. Push branch and create PR
4. Fill in PR template with:
   - Summary of changes
   - Testing performed
   - Screenshots (if UI changes)

### PR Title Format

Same as commit messages:
```
feat(scope): short description
```

### PR Description Template

```markdown
## Summary
Brief description of changes.

## Changes
- Change 1
- Change 2

## Testing
- [ ] Unit tests added/updated
- [ ] Manual testing performed
- [ ] Tested on mobile (if applicable)

## Screenshots
(If applicable)
```

### Review Process

1. All PRs require at least one approval
2. CI must pass before merge
3. Address review comments or explain disagreement
4. Squash commits on merge to main

## Protected Branches

- `main` is protected
- No direct pushes to `main`
- All changes via PR
- Require passing CI
- Require up-to-date branch

## Merge Strategy

- Use "Squash and merge" for feature branches
- Use "Merge commit" for release branches
- Delete branch after merge

## Release Process

1. Create release branch: `release/v1.2.0`
2. Run final tests
3. Update CHANGELOG.md
4. Tag release: `git tag -a v1.2.0 -m "Release v1.2.0"`
5. Merge to main
6. Deploy

## Avoiding Common Issues

- Pull latest main before creating branches
- Rebase feature branches regularly
- Don't commit secrets or credentials
- Don't commit large binary files
- Use `.gitignore` appropriately
