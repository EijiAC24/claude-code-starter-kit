# Git Workflow Guidelines

> Essential rules for commits and branches. For detailed commands, use the git-reference skill.

## Branch Naming

Format: `type/short-description`

| Type | Purpose |
|------|---------|
| `feature/` | New functionality |
| `fix/` | Bug fixes |
| `refactor/` | Code improvements |
| `docs/` | Documentation |
| `hotfix/` | Critical production fix |
| `chore/` | Maintenance |

Rules: lowercase, kebab-case, 3-5 words, include ticket if applicable (`feature/ABC-123-user-auth`)

---

## Conventional Commits

### Format

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Types

| Type | Description |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, no code change |
| `refactor` | Code change, no feature/fix |
| `perf` | Performance improvement |
| `test` | Adding/fixing tests |
| `build` | Build system changes |
| `ci` | CI configuration |
| `chore` | Other changes |
| `revert` | Revert previous commit |

### Breaking Changes

```bash
feat!: change API response format

# Or with footer
feat: change API response format

BREAKING CHANGE: description of breaking change
```

---

## Commit Best Practices

- Subject line: max 50 characters
- Use imperative mood ("add" not "added")
- Don't end subject with period
- Each commit = one logical unit
- Each commit should compile and pass tests

---

## PR Best Practices

- Keep PRs small (< 400 lines)
- One logical change per PR
- Title follows conventional commits format
- Respond to feedback promptly

---

## Push Timing

Push at natural breakpoints:
- Feature completion
- Bug fix completion
- End of work session

Pre-push checklist:
1. Tests pass
2. Build succeeds
3. No lint errors

---

## MUST DO

- Update docs when direction changes
- Keep CLAUDE.md in sync with project state
- Push after significant implementations

## MUST NOT

- Push broken code
- Force push to main/master
- Accumulate commits locally too long
