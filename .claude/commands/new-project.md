# New Project Setup Wizard

You are starting a new project setup wizard. Guide the user through project initialization by asking questions and gathering requirements before writing any code.

## Phase 1: Gather Information

First, ask the user how they want to start:

```
What are you building?

How would you like to start?

1. I have a spec document
   → Share file path or paste text

2. Let's figure it out together (recommended for new ideas)
   → I'll guide you through key questions with best practices

3. Quick start - just describe briefly
   → For simple projects or prototypes

※ Long specs are fine - just give me the path and I'll read it all
```

---

### Option 1: User has a spec

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

**Then, ONLY ask about missing information:**

- If tech stack is clear from spec → Don't ask
- If scope is mentioned → Don't ask
- If resources are listed → Don't ask

**Example:** If user provides a spec that says "Flutter app with Supabase backend for MVP launch", you already know:
- Tech: Flutter + Supabase ✓
- Scope: MVP ✓
- Only ask: "Do you have any design files or other resources?"

→ Skip to **Phase 2: Confirm Understanding**

---

### Option 2: Build spec together (Interactive Mode)

Guide the user through structured questions. Ask ONE question at a time, wait for response, then proceed.

#### Step 1: The Idea
```
Let's start with the basics.

What's your project idea?
- What problem does it solve?
- Who is it for?

Just describe it in your own words - doesn't need to be polished.
```

#### Step 2: Core Features
```
What are the MUST-HAVE features? (The app doesn't work without these)

List 2-5 core features. For example:
- "Users can create an account"
- "Search for nearby restaurants"
- "Export data to CSV"

Keep it focused - we can add more later.
```

**Best Practice Tip:** Share with user:
> Tip: For MVP, aim for 3-5 core features max. The #1 reason projects fail is scope creep. What's the ONE thing this app must do well?

#### Step 3: What NOT to Build
```
What's explicitly OUT OF SCOPE for now?

Examples:
- "No social features for v1"
- "No mobile app yet, web only"
- "No payment integration initially"

This helps keep the project focused.
```

#### Step 4: Tech Stack
```
What tech stack do you want to use?

**Web App:**
- Frontend: React/Next.js, Vue/Nuxt, Svelte
- Backend: Node.js, Python (FastAPI/Django), Go
- Database: PostgreSQL, MongoDB, SQLite

**Mobile App:**
- Cross-platform: Flutter, React Native
- Native: Swift (iOS), Kotlin (Android)

**Game:**
- Godot, Unity, Unreal

**Backend-as-a-Service (recommended for solo devs):**
- Supabase (Postgres + Auth + Storage + Realtime)
- Firebase (NoSQL + Auth + Hosting)

Not sure? Tell me your constraints (budget, timeline, experience) and I'll recommend.
```

**Best Practice Tips to share based on user's situation:**

| Situation | Recommendation |
|-----------|----------------|
| Solo dev, MVP | BaaS (Supabase/Firebase) - less ops overhead |
| Learning new stack | Pick ONE new thing, keep rest familiar |
| Need real-time | Supabase Realtime or Firebase |
| Complex queries | PostgreSQL (Supabase) over NoSQL |
| Offline-first mobile | Flutter + local DB (Hive/Isar) |
| Quick prototype | Next.js + Vercel (instant deploys) |

#### Step 5: Project Scope
```
What's the scope of this project?

1. Quick prototype (1-2 weeks, speed > quality)
   → Skip tests, simple UI, prove the concept

2. MVP for launch (1-2 months)
   → Core features polished, basic tests, production-ready

3. Production app (ongoing)
   → Full test coverage, CI/CD, monitoring, scalability

Which fits your goal?
```

#### Step 6: Existing Resources
```
Do you have any existing resources?

- UI designs / mockups / sketches
- Reference apps to emulate
- API documentation (if integrating with services)
- Brand guidelines

If you have image files, share the path and I'll organize them.
```

#### Step 7: Generate Spec

After gathering all information, generate a spec document:

```markdown
# [Project Name] Specification

## Overview
[One paragraph description]

## Problem & Target Users
- Problem: [What problem does this solve]
- Target: [Who is this for]

## Core Features (MVP)
1. [Feature 1]
2. [Feature 2]
3. [Feature 3]

## Out of Scope (v1)
- [What we're NOT building]

## Tech Stack
| Layer | Technology |
|-------|------------|
| Frontend | [Choice] |
| Backend | [Choice] |
| Database | [Choice] |
| Hosting | [Choice] |

## User Stories
| As a... | I want to... | So that... |
|---------|--------------|------------|
| [User type] | [Action] | [Benefit] |

## Project Scope
- Type: [Prototype/MVP/Production]
- Timeline: [User's timeline if mentioned]

## Resources
- [List any provided resources]

---
Generated: [Date]
```

Save this to `docs/spec.md` and show the user for confirmation.

---

### Option 3: Quick Start

For simple projects or when user just wants to describe briefly:

```
Got it! Just tell me:
1. What you're building (one sentence)
2. Tech stack (or "recommend something")
3. Prototype or MVP?
```

Then extract what you can and fill gaps with sensible defaults.

---

### Questions to ask ONLY if not covered:

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
  - Flutter: Keep dart-flutter, testing, security, git-workflow, design-patterns, frontend-design
  - Godot: Keep godot, security, git-workflow, design-patterns, frontend-design
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

### Step 5: Git Setup & Initial Push

Ask the user:

```
Do you want to set up Git and push to a remote repository?

1. Yes, I have a GitHub/GitLab URL ready
2. Yes, create a new private repository on GitHub (requires gh CLI)
3. No, skip for now
```

**Option 1: User has a URL**
- Ask for the repository URL
- Run:
  ```bash
  git init
  git add .
  git commit -m "Initial commit: project setup"
  git remote add origin <URL>
  git branch -M main
  git push -u origin main
  ```

**Option 2: Create new private repo**
- Check if `gh` CLI is available (`gh --version`)
- Generate a repository name from the project name (kebab-case, lowercase)
  - Example: "Task Management App" → `task-management-app`
- Generate a short description from the project overview (1 line, ~100 chars max)
  - Example: "A mobile app for managing daily tasks with reminders and categories"
- Show the proposed name and description, ask for confirmation:
  ```
  I'll create a private repository:
  - Name: task-management-app
  - Description: A mobile app for managing daily tasks with reminders and categories

  Is this OK? (or let me know what to change)
  ```
- Run:
  ```bash
  git init
  git add .
  git commit -m "Initial commit: project setup"
  gh repo create <repo-name> --private --description "<description>" --source=. --remote=origin --push
  ```

**Option 3: Skip**
- Just run `git init` for local version control
- Inform user they can push later

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
