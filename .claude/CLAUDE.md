# Project Configuration Template

> This is a universal template for Claude Code. Customize for your specific project.

## Quick Start

```bash
# Common commands (customize for your project)
npm run dev          # Start development server
npm run build        # Build for production
npm run test         # Run test suite
npm run lint         # Lint code
npm run format       # Format code
```

## Project Structure

```
your-project/
├── src/              # Source code
├── tests/            # Test files
├── docs/             # Documentation
├── public/           # Static assets
└── config/           # Configuration files
```

## Core Files

- `package.json` - Dependencies and scripts
- `tsconfig.json` - TypeScript configuration
- See @README.md for project overview

## Code Quality Standards

### General Principles
- Write self-documenting code with clear variable names
- Keep functions small and focused (single responsibility)
- Prefer composition over inheritance
- Handle errors explicitly, never silently ignore them

### Language-Specific Rules
- Detailed rules are in `.claude/rules/` directory
- See @.claude/rules/code-style.md for formatting standards
- See @.claude/rules/testing.md for test requirements

### Architecture & Design
- See @.claude/rules/design-patterns.md for GoF pattern reference
- See @.claude/rules/frontend-design.md for UI/UX guidelines

## Git Workflow

- Branch naming: `feature/`, `fix/`, `refactor/`, `docs/`
- Commit messages: Use conventional commits format
- See @.claude/rules/git-workflow.md for details

## Security

- NEVER commit secrets or credentials
- Validate all external input
- See @.claude/rules/security.md for security checklist

## Testing Requirements

- Write tests for all new features
- Maintain minimum 80% code coverage
- Run tests before committing
- See @.claude/rules/testing.md for conventions

## Environment Setup

```bash
# Environment variables (create .env.local)
DATABASE_URL=        # Database connection string
API_KEY=             # API key (never commit!)
```

## Important Notes

<!-- Add project-specific warnings and gotchas here -->
- [Add any unexpected behaviors or warnings]
- [Add any project-specific conventions]

---

## How to Customize This Template

1. Replace placeholder commands with your actual project commands
2. Update the project structure diagram
3. Add project-specific notes and warnings
4. Remove unused sections
5. Use `#` shortcut during sessions to add frequently needed instructions
