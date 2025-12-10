# Git Workflow Guidelines

## Branch Naming

| Type | Format | Example |
|------|--------|---------|
| Feature | `feature/description` | `feature/user-authentication` |
| Bug fix | `fix/description` | `fix/login-validation` |
| Refactor | `refactor/description` | `refactor/user-service` |
| Documentation | `docs/description` | `docs/api-readme` |
| Hotfix | `hotfix/description` | `hotfix/critical-bug` |
| Chore | `chore/description` | `chore/update-dependencies` |

## Commit Message Format

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

### Types

| Type | Description |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, no code change |
| `refactor` | Code change without feature/fix |
| `test` | Adding or fixing tests |
| `chore` | Maintenance tasks |
| `perf` | Performance improvement |

### Examples

```bash
# Feature
feat(auth): add OAuth2 login support

# Bug fix
fix(api): handle null response from user endpoint

# With body
feat(dashboard): add real-time notifications

Implement WebSocket connection for live updates.
Notifications appear in the header bell icon.

Closes #123

# Breaking change
feat(api)!: change response format for user endpoint

BREAKING CHANGE: The user endpoint now returns camelCase keys
instead of snake_case.
```

## Commit Best Practices

- Commit early, commit often
- Each commit should be a logical unit
- Write in present tense ("add feature" not "added feature")
- Keep subject line under 50 characters
- Wrap body at 72 characters
- Reference issues/tickets when relevant

## Pull Request Guidelines

### PR Title
Same format as commit messages:
```
feat(auth): add OAuth2 login support
```

### PR Description Template

```markdown
## Summary
Brief description of changes

## Changes
- Added X functionality
- Fixed Y bug
- Updated Z documentation

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing completed

## Screenshots (if applicable)
[Add screenshots for UI changes]

## Related Issues
Closes #123
```

## Merge Strategy

- Use **squash merge** for feature branches
- Use **rebase** to keep branch up-to-date with main
- Use **merge commit** only for release branches

```bash
# Keep branch updated
git fetch origin
git rebase origin/main

# Resolve conflicts if any
git add .
git rebase --continue
```

## Protected Branch Rules

For `main`/`master` branch:
- Require pull request reviews
- Require status checks to pass
- Require linear history (optional)
- No force pushes

## Common Commands

```bash
# Start new feature
git checkout main
git pull origin main
git checkout -b feature/my-feature

# Save work in progress
git stash
git stash pop

# Amend last commit (before push)
git commit --amend

# Interactive rebase (clean up commits)
git rebase -i HEAD~3

# Undo last commit (keep changes)
git reset --soft HEAD~1

# View commit history
git log --oneline --graph
```

## .gitignore Essentials

```gitignore
# Dependencies
node_modules/
vendor/

# Environment
.env
.env.*
!.env.example

# Build output
dist/
build/
.next/

# IDE
.idea/
.vscode/
*.swp

# OS
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Claude Code local settings
.claude/settings.local.json
CLAUDE.local.md
```
