# project-inception

A Claude Code plugin that guides you through project discovery, design, and architecture
— producing a README, optional SRS, stack recommendation, ERD, folder structure, and
UI/UX design handoff prompt. Reentrant for feature integration planning on existing projects.

## Skills

### `inception:discovery` — Phase 1

Facilitates a structured discovery session to establish scope, audience, core features,
caching strategy, and user stories.

**Produces:**
- README draft
- User stories (free-form preferred)
- Optional SRS document (IEEE 830 format)
- State complexity flag (if warranted)

**Trigger:** "start a new project", "let's plan X", "I want to build X", "project inception"

---

### `inception:design` — Phase 2

Transforms discovery findings into concrete architectural decisions.

**Produces:**
- Stack recommendation (agnostic, opinionated by context)
- Folder structure recommendation (FBO, DDD, Atomic, or hybrid)
- ERD in Mermaid format
- Data flow overview
- UI/UX design handoff prompt

**Trigger:** "design the architecture", "pick a stack", "design phase" — or runs
automatically after inception:discovery

---

### `inception:feature-plan` — Phase 3 (Reentrant)

Plans a new feature integration against an existing codebase. Discovers the project
from the filesystem — reads CLAUDE.md, package.json, schema files, and folder structure.

**Produces:**
- Feature user stories
- Impact analysis (DB, API, frontend, auth, third-party)
- ERD delta (new/modified entities only)
- Risk assessment
- Phased delta implementation plan

**Trigger:** "plan a new feature", "add X to the project", "I want to add X"

---

## Usage

### New Project

Run in order:

1. Start discovery: "Let's plan a new project" or "I want to build [X]"
2. Move to design: "Design the architecture" or "inception design"
3. Develop — the plugin hands off here
4. Plan new features: "Plan a new feature" or "I want to add [X]"

### Existing Project (Feature Planning Only)

Run inception:feature-plan directly from your project directory:

```
"Plan a new feature for this project"
"I want to add [feature] to this codebase"
```

The skill will discover your project from the filesystem automatically.

---

## Tips

- **Keep a CLAUDE.md** in your project root. inception:feature-plan reads it as the
  primary source of truth — a well-maintained CLAUDE.md makes feature planning
  significantly faster and more accurate.

- **The plugin is agnostic** — it recommends the right tool for your context, not a
  fixed stack. It works for any language, framework, or hosting platform.

- **UI/UX is intentionally out of scope** — the design phase produces a structured
  handoff prompt you can take to Canva, Figma AI, or a new Claude conversation.

- **State management is deferred** — the plugin flags complexity during design but
  leaves library selection to the development phase, where framework patterns and
  third-party choices may have already narrowed the options.

---

## Installation (Claude Code)

```bash
# Load for the current session
claude --plugin-dir /path/to/project-inception

# Or install from a .plugin file
# Drop project-inception.plugin into your Claude Code plugins directory
```

---

## Based On

Adapted from the `project-wizard` skill by Brian Blankenship, with significant
extensions for document generation, ERD output, feature delta planning, and
codebase discovery.
