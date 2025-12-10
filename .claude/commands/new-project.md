# New Project Setup Wizard

You are starting a new project setup wizard. Guide the user through project initialization by asking questions and gathering requirements before writing any code.

## Phase 1: Gather Information

First, ask the user for their spec document or project description:

```
What are you building?

Please share your spec (any of these works):

1. File path (recommended)
   e.g., C:\Users\you\Documents\spec.md
   e.g., /Users/you/Documents/spec.md

2. Paste text directly

3. Describe briefly in your own words

※ Long specs are fine - just give me the path and I'll read it all
※ I'll create a summary version if needed
```

### After receiving the spec:

**If file path** → Use Read tool to load it

**Keep full spec (no summarizing):**
- Copy to `docs/spec.md` as-is
- Do NOT summarize (information loss)
- Always reference spec when implementing

**Extract the following:**

1. **Project Overview** - What the app/project does
2. **Tech Stack** - Frontend, backend, database, etc.
3. **Project Scope** - Prototype, MVP, or production
4. **Existing Resources** - Design files, APIs, references

### Then, ONLY ask about missing information:

- If tech stack is clear from spec → Don't ask
- If scope is mentioned → Don't ask
- If resources are listed → Don't ask

**Example:** If user provides a spec that says "Flutter app with Supabase backend for MVP launch", you already know:
- Tech: Flutter + Supabase ✓
- Scope: MVP ✓
- Only ask: "Do you have any design files or other resources?"

### Questions to ask ONLY if not in spec:

**Tech Stack (if unclear):**
```
What's your tech stack?

Examples:
- Frontend: React/Next.js, Vue, Flutter, Godot
- Backend: Node.js, Python, Supabase, Firebase
- Database: PostgreSQL, MongoDB, SQLite
```

**Project Scope (if unclear):**
```
What's the scope of this project?

1. Quick prototype / hackathon (speed over quality)
2. Side project / MVP (balanced)
3. Production app (quality, tests, CI/CD)
```

**Existing Resources (if not mentioned):**
```
Do you have any existing resources?

- UI designs / prototype images (screenshots, Figma, even hand-drawn sketches)
- Wireframes / screen flow diagrams
- API documentation
- Reference apps or websites
- Brand guidelines (colors, fonts, etc.)

※ If you have images, please provide the file path. I'll place them in docs/designs/.
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
- `docs/` for documentation
  - `docs/spec.md` - specification document
  - `docs/designs/` - UI images, wireframes, prototype images

**If UI images are provided:**
- Copy user's images to `docs/designs/`
- Add reference in CLAUDE.md: `@docs/designs/`
- Reference these images when implementing UI

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
