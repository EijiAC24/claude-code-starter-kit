# Git Workflow Guidelines

> Based on Conventional Commits, Trunk-Based Development, and industry best practices for 2025.

## Workflow Strategy Selection

| Strategy | Best For | Key Characteristics |
|----------|----------|---------------------|
| **Trunk-Based** | CI/CD, small teams | Short-lived branches, frequent merges |
| **GitHub Flow** | Continuous deployment | Feature branches, PR reviews |
| **GitFlow** | Scheduled releases | Long-lived branches, version management |

**Recommendation**: Use Trunk-Based or GitHub Flow for most modern projects.

---

## Branch Naming

### Format: `type/short-description`

| Type | Purpose | Example |
|------|---------|---------|
| `feature/` | New functionality | `feature/user-authentication` |
| `fix/` | Bug fixes | `fix/login-validation-error` |
| `refactor/` | Code improvements | `refactor/extract-user-service` |
| `docs/` | Documentation | `docs/api-endpoints` |
| `hotfix/` | Critical production fix | `hotfix/security-vulnerability` |
| `chore/` | Maintenance | `chore/update-dependencies` |
| `test/` | Test additions | `test/user-service-coverage` |
| `perf/` | Performance | `perf/optimize-queries` |

### Rules
- Use lowercase with hyphens (kebab-case)
- Keep names short but descriptive (3-5 words)
- Include ticket number if applicable: `feature/ABC-123-user-auth`

---

## Conventional Commits

### Format
```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Types

| Type | Description | SemVer | Example |
|------|-------------|--------|---------|
| `feat` | New feature | MINOR | `feat: add user registration` |
| `fix` | Bug fix | PATCH | `fix: resolve login timeout` |
| `docs` | Documentation only | - | `docs: update API documentation` |
| `style` | Formatting, no code change | - | `style: fix indentation` |
| `refactor` | Code change, no feature/fix | - | `refactor: extract validation logic` |
| `perf` | Performance improvement | PATCH | `perf: optimize database queries` |
| `test` | Adding/fixing tests | - | `test: add user service tests` |
| `build` | Build system changes | - | `build: update webpack config` |
| `ci` | CI configuration | - | `ci: add GitHub Actions workflow` |
| `chore` | Other changes | - | `chore: update dependencies` |
| `revert` | Revert previous commit | - | `revert: feat: add user registration` |

### Breaking Changes

```bash
# Method 1: Add ! after type
feat!: change API response format

# Method 2: Footer notation
feat: change API response format

BREAKING CHANGE: The user endpoint now returns camelCase keys
instead of snake_case. Update all API consumers.
```

### Scope (Optional)
```bash
feat(auth): add OAuth2 support
fix(api): handle null response
docs(readme): update installation steps
refactor(user-service): extract validation
```

### Examples

```bash
# Simple feature
feat: add dark mode toggle

# Feature with scope
feat(dashboard): add real-time notifications

# Bug fix with body
fix(auth): prevent session timeout during active use

The session was timing out even when users were actively
interacting with the application. Now activity resets
the timeout timer.

Closes #234

# Breaking change
feat(api)!: change response pagination format

BREAKING CHANGE: Pagination now uses cursor-based approach
instead of offset. Update client implementations.

Migration guide: https://docs.example.com/migration
```

---

## Commit Best Practices

### Message Guidelines
- Subject line: max 50 characters
- Use imperative mood ("add" not "added" or "adds")
- Don't end subject with period
- Wrap body at 72 characters
- Explain what and why, not how

### Atomic Commits
```bash
# GOOD: Each commit is a logical unit
git commit -m "feat(auth): add password validation"
git commit -m "feat(auth): add email verification"
git commit -m "test(auth): add registration tests"

# BAD: Multiple unrelated changes
git commit -m "add auth, fix bug, update docs"
```

### Commit Frequency
- Commit early, commit often
- Each commit should compile and pass tests
- Squash WIP commits before merging

---

## Pull Request Guidelines

### PR Title
Same format as commit messages:
```
feat(auth): add OAuth2 login support
```

### PR Description Template

```markdown
## Summary
Brief description of changes (1-3 sentences)

## Changes
- Added OAuth2 authentication flow
- Created OAuthService class
- Updated login page UI

## Type of Change
- [ ] Bug fix (non-breaking change fixing an issue)
- [ ] New feature (non-breaking change adding functionality)
- [ ] Breaking change (fix or feature causing existing functionality to change)
- [ ] Documentation update

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing completed

## Screenshots (if applicable)
[Add screenshots for UI changes]

## Checklist
- [ ] Code follows project style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex logic
- [ ] Documentation updated
- [ ] No new warnings introduced

## Related Issues
Closes #123
Related to #456
```

### PR Best Practices
- Keep PRs small (< 400 lines ideally)
- One logical change per PR
- Request reviews from relevant team members
- Respond to feedback promptly
- Update PR based on review comments

---

## Merge Strategies

### When to Use Each

| Strategy | Use Case | Result |
|----------|----------|--------|
| **Squash** | Feature branches | Single clean commit |
| **Rebase** | Keep history linear | Replayed commits |
| **Merge** | Release branches, preserving history | Merge commit |

### Recommended Workflow
```bash
# Keep branch updated (use rebase to avoid merge commits)
git fetch origin
git rebase origin/main

# Resolve conflicts if any
git add .
git rebase --continue

# Force push after rebase (only on feature branches!)
git push --force-with-lease
```

### Squash Merge (Recommended for Features)
```bash
# Via GitHub UI: "Squash and merge"
# Final commit message should follow conventional commits
```

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

## Protected Branch Rules

### Main/Master Branch
- ✅ Require pull request reviews (1-2 approvers)
- ✅ Require status checks to pass
- ✅ Require linear history (optional)
- ✅ Require signed commits (optional)
- ❌ No direct pushes
- ❌ No force pushes
- ❌ No deletions

### Branch Protection Settings (GitHub)
```yaml
# .github/branch-protection.yml (example config)
main:
  required_pull_request_reviews:
    required_approving_review_count: 1
    dismiss_stale_reviews: true
  required_status_checks:
    strict: true
    contexts:
      - "ci/test"
      - "ci/lint"
  enforce_admins: true
  restrictions: null
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

## .gitignore Essentials

```gitignore
# Dependencies
node_modules/
vendor/
.pnp/
.pnp.js

# Build output
dist/
build/
out/
.next/
.nuxt/

# Environment & Secrets
.env
.env.*
!.env.example
*.pem
*.key

# IDE
.idea/
.vscode/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
logs/
*.log
npm-debug.log*

# Test & Coverage
coverage/
.nyc_output/

# Cache
.cache/
.eslintcache
*.tsbuildinfo

# Claude Code local settings
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
# Update version
npm version patch  # or minor, major

# Push with tags
git push origin main --tags

# Create GitHub release
gh release create v1.2.3 --generate-notes
```

### Changelog Generation
```bash
# Using conventional-changelog
npx conventional-changelog -p angular -i CHANGELOG.md -s
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

## Spec Changes → Update Documentation

When requirements or direction changes during development, **always update project documentation**.

### What Triggers an Update

| Change Type | Update Required |
|-------------|-----------------|
| New feature added to scope | CLAUDE.md, spec doc |
| Feature removed or deprioritized | CLAUDE.md, spec doc |
| Tech stack change | CLAUDE.md |
| API contract change | spec doc, API docs |
| Data model change | spec doc, schema docs |
| UI/UX direction change | spec doc, design docs |
| Third-party service swap | CLAUDE.md |
| Performance requirements change | spec doc |

### What to Update

**CLAUDE.md** - Update when:
- Tech stack changes (new library, service, etc.)
- Project structure changes
- New commands needed
- New conventions established

```markdown
## Project-Specific Notes

<!-- Add change log here -->
- [2025-01] Switched from REST to GraphQL
- [2025-01] Added Redis for caching
- [2025-02] Changed auth from JWT to session-based
```

**Spec Document** - Update when:
- Features added/removed/changed
- User flows modified
- Business logic updated
- Data models changed

### Update Workflow

```bash
# 1. When spec changes during conversation
"仕様が変わったので、CLAUDE.md と spec.md を更新して"

# 2. Include in commit
git add .claude/CLAUDE.md docs/spec.md
git commit -m "docs: update spec for [change description]"
```

### Keep History

Add a changelog section to track major changes:

```markdown
## Changelog

| Date | Change | Reason |
|------|--------|--------|
| 2025-01-15 | Added offline mode | User feedback |
| 2025-01-20 | Removed social login | Scope reduction |
| 2025-02-01 | Switched to GraphQL | Performance |
```

### Rule: No Undocumented Changes

**MUST DO:**
- Update docs when direction changes
- Note the reason for the change
- Keep CLAUDE.md in sync with actual project state

**MUST NOT:**
- Leave outdated information in CLAUDE.md
- Implement changed specs without updating docs
- Let documentation drift from reality
