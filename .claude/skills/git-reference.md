# Git Reference Guide

> Detailed git commands, hooks, and troubleshooting.

## Trigger Keywords

- git コマンド, git commands
- rebase, cherry-pick, bisect, reflog
- git hooks, husky, commitlint
- gitignore, git設定
- conflict, コンフリクト解決
- release, リリース, タグ

---

## Common Git Commands

### Daily Workflow

```bash
# Start new feature
git checkout main
git pull origin main
git checkout -b feature/my-feature

# Save progress
git add .
git commit -m "feat: implement feature"

# Update branch with latest main
git fetch origin
git rebase origin/main

# Push changes
git push -u origin feature/my-feature
```

### Useful Commands

```bash
# View commit history (graphical)
git log --oneline --graph --all

# Amend last commit (before push only!)
git commit --amend -m "feat: corrected message"

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Stash changes temporarily
git stash
git stash pop

# Interactive rebase (clean up commits)
git rebase -i HEAD~3

# Cherry-pick specific commit
git cherry-pick <commit-hash>

# Find commit that introduced bug
git bisect start
git bisect bad HEAD
git bisect good <known-good-commit>
```

### Dangerous Commands (Use with Caution)

```bash
# Force push (only on feature branches, never main!)
git push --force-with-lease  # Safer than --force

# Hard reset (loses uncommitted changes!)
git reset --hard HEAD~1

# Clean untracked files
git clean -fd
```

---

## Merge Strategies

| Strategy | Use Case | Result |
|----------|----------|--------|
| **Squash** | Feature branches | Single clean commit |
| **Rebase** | Keep history linear | Replayed commits |
| **Merge** | Release branches | Merge commit |

### Recommended Workflow

```bash
# Keep branch updated
git fetch origin
git rebase origin/main

# Resolve conflicts
git add .
git rebase --continue

# Force push after rebase (feature branches only!)
git push --force-with-lease
```

---

## Troubleshooting

### Undo Mistakes

```bash
# Undo staged changes
git reset HEAD <file>

# Discard local changes
git checkout -- <file>

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Recover deleted branch
git reflog
git checkout -b recovered-branch <commit-hash>
```

### Fix Merge Conflicts

```bash
# During rebase
git rebase origin/main
# Fix conflicts in files
git add .
git rebase --continue

# Abort if needed
git rebase --abort
```

---

## Git Hooks

### Pre-commit (Recommended)

```bash
# .husky/pre-commit
#!/bin/sh
npm run lint-staged
npm run typecheck
```

### Commit-msg (Conventional Commits)

```bash
# .husky/commit-msg
#!/bin/sh
npx commitlint --edit $1
```

### commitlint.config.js

```javascript
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [
      2,
      'always',
      ['feat', 'fix', 'docs', 'style', 'refactor', 'perf', 'test', 'build', 'ci', 'chore', 'revert'],
    ],
    'subject-max-length': [2, 'always', 72],
  },
};
```

---

## Protected Branch Rules

### Main/Master Branch

- Require pull request reviews (1-2 approvers)
- Require status checks to pass
- No direct pushes
- No force pushes
- No deletions

### Branch Protection Settings (GitHub)

```yaml
main:
  required_pull_request_reviews:
    required_approving_review_count: 1
    dismiss_stale_reviews: true
  required_status_checks:
    strict: true
    contexts:
      - "ci/test"
      - "ci/lint"
```

---

## .gitignore Essentials

```gitignore
# Dependencies
node_modules/
vendor/

# Build output
dist/
build/
.next/

# Environment & Secrets
.env
.env.*
!.env.example
*.pem
*.key

# IDE
.idea/
.vscode/

# OS
.DS_Store
Thumbs.db

# Logs & Cache
*.log
.cache/
coverage/

# Claude Code
.claude/settings.local.json
CLAUDE.local.md
```

---

## Release Workflow

### Semantic Versioning

```
MAJOR.MINOR.PATCH

MAJOR: Breaking changes
MINOR: New features (backward compatible)
PATCH: Bug fixes (backward compatible)
```

### Creating a Release

```bash
npm version patch  # or minor, major
git push origin main --tags
gh release create v1.2.3 --generate-notes
```

### Changelog Generation

```bash
npx conventional-changelog -p angular -i CHANGELOG.md -s
```

---

## PR Description Template

```markdown
## Summary
Brief description of changes (1-3 sentences)

## Changes
- Added X
- Fixed Y
- Updated Z

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests added/updated
- [ ] Manual testing completed

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
```
