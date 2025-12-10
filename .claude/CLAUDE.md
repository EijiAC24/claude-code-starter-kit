# Project Configuration

> Keep this file minimal. Include ONLY what's needed in EVERY session.
> Store detailed documentation in `docs/` and reference with `@docs/filename.md`.

## Commands

```bash
# Development (customize for your stack)
npm run dev          # Start dev server
npm run build        # Production build
npm run test         # Run tests
npm run lint         # Lint & fix
npm run typecheck    # Type check (if TS)

# Flutter/Dart projects
flutter run          # Run app
flutter test         # Run tests
flutter analyze      # Lint

# Godot projects
# Use Godot editor or CLI
```

## Project Structure

```
project/
├── src/              # Source code
│   ├── components/   # UI components
│   ├── services/     # Business logic
│   ├── utils/        # Utilities
│   └── types/        # Type definitions
├── tests/            # Test files
├── docs/             # Documentation (reference with @docs/*)
└── public/           # Static assets
```

## Key Files

- @README.md - Project overview
- @package.json - Available npm scripts

## Rules

Modular rules auto-load based on file paths:

| Rule | Applies To |
|------|------------|
| @.claude/rules/code-style.md | `src/**/*.{ts,tsx,js,jsx}` |
| @.claude/rules/testing.md | `tests/**`, `*.test.*` |
| @.claude/rules/security.md | All source files |
| @.claude/rules/git-workflow.md | All files |
| @.claude/rules/design-patterns.md | `src/**/*` |
| @.claude/rules/frontend-design.md | `*.tsx`, `*.css` |
| @.claude/rules/dart-flutter.md | `**/*.dart` |
| @.claude/rules/godot.md | `**/*.gd`, `*.tscn` |

## Workflow

1. **Explore** - Understand before changing
2. **Plan** - Use `think` for complex tasks
3. **Implement** - Small, focused changes
4. **Test** - Verify before committing
5. **Commit** - Conventional commits format

## Critical Rules

### MUST DO
- Read files before modifying
- Run tests after changes
- Use explicit types (avoid `any`)
- Handle errors with context
- Validate external input

### MUST NOT
- Commit secrets/credentials
- Ignore errors silently
- Skip tests for new features
- Use `rm -rf` without confirmation
- Force push to main/master

## Project-Specific Notes

<!--
Add your project-specific instructions below.
Use `#` shortcut during sessions to add frequently needed instructions.
Examples:
- "This project uses Prisma for database"
- "API base URL is in NEXT_PUBLIC_API_URL"
- "Component tests use data-testid attributes"
-->

---

## Template Usage

1. **Customize commands** - Replace with your actual scripts
2. **Update structure** - Match your project layout
3. **Add notes** - Document gotchas and conventions
4. **Remove unused rules** - Delete irrelevant rule files
5. **Keep minimal** - Move detailed docs to `docs/` folder
