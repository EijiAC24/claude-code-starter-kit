# New Project Setup Wizard

You are starting a new project setup wizard. Guide the user through project initialization by asking questions and gathering requirements before writing any code.

## Phase 1: Gather Information

Ask the user these questions ONE AT A TIME. Wait for their response before asking the next question.

### Question 1: Project Overview
```
What are you building?

Please share:
- A brief description of the project
- Your spec document (paste text, file path, or URL)
```

### Question 2: Tech Stack
```
What's your tech stack?

Examples:
- Frontend: React/Next.js, Vue, Flutter, Godot
- Backend: Node.js, Python, Supabase, Firebase
- Database: PostgreSQL, MongoDB, SQLite
- Other: AI/ML, Mobile, Game, CLI tool
```

### Question 3: Project Scope
```
What's the scope of this project?

1. Quick prototype / hackathon (speed over quality)
2. Side project / MVP (balanced)
3. Production app (quality, tests, CI/CD)
```

### Question 4: Existing Resources
```
Do you have any existing resources?

- Design files (Figma, etc.)
- API documentation
- Reference projects
- Brand guidelines
```

## Phase 2: Confirm Understanding

After gathering all information, summarize what you understood:

```
## Project Summary

**Project:** [Name/Description]
**Type:** [Web App / Mobile App / Game / API / etc.]
**Stack:** [Technologies]
**Scope:** [Prototype / MVP / Production]

**Key Features:**
1. [Feature 1]
2. [Feature 2]
3. [Feature 3]

**Resources:**
- [List any provided resources]

Is this correct? Should I proceed with setup?
```

## Phase 3: Project Setup

Once confirmed, execute these steps:

### Step 1: Configure Starter Kit
- Copy `.claude/` folder structure
- Remove unnecessary rules based on tech stack:
  - Web (React/Next.js): Keep code-style, testing, security, git-workflow, design-patterns, frontend-design
  - Flutter: Keep dart-flutter, testing, security, git-workflow, design-patterns
  - Godot: Keep godot, security, git-workflow, design-patterns
  - Node.js API: Keep code-style, testing, security, git-workflow, design-patterns

### Step 2: Create CLAUDE.md
Configure with:
- Project-specific commands
- Correct project structure
- Tech stack notes
- Link to spec document

### Step 3: Create Directory Structure
Based on tech stack, create appropriate folders:
- `src/` or `lib/` for source code
- `tests/` for test files
- `docs/` for documentation (place spec here)

### Step 4: Initialize Project
- Run appropriate init commands (npm init, flutter create, etc.)
- Install core dependencies
- Setup configuration files

## Phase 4: Planning

After setup is complete, ask:

```
Setup complete! Now let's plan the implementation.

Would you like me to:
1. Create a detailed MVP task list
2. Design the data models / schemas first
3. Start with a specific feature
4. Something else?
```

## Important Rules

- NEVER skip the question phase
- ALWAYS wait for user responses
- CONFIRM understanding before making changes
- ASK if unsure about any technical decisions
- KEEP the user informed of what you're doing
