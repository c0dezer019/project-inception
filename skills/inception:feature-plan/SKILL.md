---
name: inception:feature-plan
description: >
  This skill should be used when the user wants to plan a new feature for an existing
  project that was previously designed with the project-inception plugin. Trigger phrases
  include: "plan a new feature", "add X to the project", "feature planning", "integrate X",
  "I want to add X", "what would it take to add X", "feature inception", "plan feature
  integration". Also triggers when the user describes a new capability they want to add
  to an existing codebase. Expects an existing project — run inception:discovery and
  inception:design first for new projects.
metadata:
  version: "0.1.0"
  phase: "3 of 3 (reentrant)"
  previous: "inception:design"
---

# Project Inception — Phase 3: Feature Planning

Discover the existing project's architecture from the codebase, then plan a new feature
integration as a delta — what changes, what's added, and what risks exist. Reentrant:
can be run multiple times on the same project as it grows.

## Tool Constraint

Requires filesystem read access. Use Read, Glob, and Bash tools to inspect the codebase.
Do not write or modify any files — this is a planning-only skill.

## Feature Plan Context Object

```
feature_plan = {
  project_name,
  discovered_stack,      // {} what was found in the codebase
  existing_features,     // [] inferred from code structure
  folder_pattern,        // detected pattern
  existing_schema,       // [] entities found in schema/models
  claude_md_present,     // boolean
  new_feature,           // what the user wants to add
  new_feature_stories,   // [] user stories for the new feature
  impact,                // {} what changes across the codebase
  new_entities,          // [] new DB entities required
  new_routes,            // [] new API routes/pages required
  risks,                 // [] identified integration risks
  delta_plan,            // phased implementation plan for the feature
}
```

---

## Step 0 — Codebase Discovery

Read `references/codebase-discovery.md` before running any file reads.

Follow the discovery protocol in that reference. Priority order:

1. **Check for CLAUDE.md** — if present, treat as source of truth for stack,
   structure, and conventions. Extract all relevant context.

2. **Check for README.md** — secondary source. Extract tech stack mentions,
   architecture notes, setup instructions.

3. **Inspect package.json / pyproject.toml / go.mod** — identify runtime,
   framework, and key dependencies.

4. **Inspect folder structure** — infer folder pattern (FBO, DDD, Atomic,
   Layered, or hybrid) from directory layout.

5. **Find schema files** — look for Prisma schema, Drizzle schema, SQLAlchemy
   models, Go structs, or migration files. Extract entity names and relationships.

6. **Sample a feature directory** — read one existing feature to understand
   conventions (naming, file structure, import patterns).

Produce a discovery summary before proceeding:

```
CODEBASE DISCOVERY
─────────────────────────────────────────
Source:          [CLAUDE.md / README / package.json / inferred]
Stack:           [detected technologies]
Folder Pattern:  [detected pattern]
Existing Entities: [list from schema]
Existing Features: [inferred from folder structure]
Conventions:     [naming, file structure, import style observed]
─────────────────────────────────────────
```

Ask: "Does this match your project? Correct anything before we plan the feature."

---

## Step 1 — Feature Definition

Ask: "What feature do you want to add?"

Accept free-text. Record as `new_feature`.

Then ask:
"Describe what this feature should do from the user's perspective. Who uses it
and what does it enable them to do?"

Accept free-text. Use this to generate user stories.

Generate 3–7 user stories for the new feature in free-form style (see
inception:discovery's user-story-formats.md for guidance if needed).

Present stories and ask: "Do these capture what you're building? Add, remove,
or edit anything."

---

## Step 2 — Impact Analysis

Read `references/delta-planning.md` before this step.

Analyze the new feature against the discovered codebase. Produce an impact map:

```
IMPACT ANALYSIS — [new_feature]
─────────────────────────────────────────
Database:
  New entities:    [list]
  Modified entities: [list + what changes]
  New relationships: [list]

API / Backend:
  New routes:      [list]
  Modified routes: [list + why]
  New services:    [list]
  New middleware:  [list, if any]

Frontend:
  New pages/screens: [list]
  New components:  [list]
  Modified components: [list + why]
  State changes:   [if state complexity affected]

Auth / Permissions:
  New roles or permissions required: [list or "none"]
  Existing permission changes: [list or "none"]

Third-party integrations:
  New services needed: [list or "none"]
  New environment variables: [list]

Testing:
  New unit tests:        [areas to cover]
  New integration tests: [areas to cover]
─────────────────────────────────────────
```

---

## Step 3 — ERD Delta

If new entities or relationships are required, generate a Mermaid ERD showing
only the delta — new entities and modified entities with their changes highlighted.

Do not regenerate the full ERD unless the user asks.

````
**New / Modified Entities:**
```mermaid
erDiagram
  [new and modified entities only]
```

**Changes from existing schema:**
- [Entity]: [what was added or changed]
````

---

## Step 4 — Risk Assessment

Identify integration risks based on the impact analysis:

```
INTEGRATION RISKS
─────────────────────────────────────────
[HIGH / MEDIUM / LOW] [Risk description]
  Mitigation: [specific recommendation]

[repeat for each risk]
─────────────────────────────────────────
```

Common risk patterns to check (from `references/delta-planning.md`):
- Breaking changes to existing API routes
- Schema migrations on tables with existing data
- Auth/permission model changes affecting existing users
- New third-party dependencies introducing cost or rate limits
- Performance impact on existing queries (N+1, missing indexes)
- State management changes that affect existing components

---

## Step 5 — Delta Implementation Plan

Produce a phased plan scoped to this feature only:

```
FEATURE DELTA PLAN — [new_feature]
─────────────────────────────────────────
Phase 0 — Schema & Migration
  - [specific migration tasks]
  - [new Prisma/Drizzle/SQLAlchemy models]

Phase 1 — Backend
  - [new services]
  - [new routes]
  - [new middleware if needed]
  - [environment variables to add]

Phase 2 — Frontend
  - [new pages/screens]
  - [new components]
  - [modified components]

Phase 3 — Integration & Testing
  - [wire frontend to backend]
  - [unit tests to write]
  - [integration tests to write]
  - [manual QA checklist]

Phase 4 — Deployment Considerations
  - [migration strategy for existing data]
  - [feature flag recommendation if risky]
  - [rollback plan if needed]
─────────────────────────────────────────
Estimated complexity: [Low / Medium / High]
Rationale: [1-2 sentences]
```

---

## Final Output

After the user confirms the delta plan, produce a final summary:

```
FEATURE PLAN COMPLETE — [new_feature]
─────────────────────────────────────────
Project:        [project_name]
Feature:        [new_feature]
Complexity:     [Low / Medium / High]
New Entities:   [count]
New Routes:     [count]
New Screens:    [count]
Risks:          [count] identified
─────────────────────────────────────────
Artifacts produced:
  ✓ Feature user stories
  ✓ Impact analysis
  ✓ ERD delta (if schema changes)
  ✓ Risk assessment
  ✓ Phased delta implementation plan
─────────────────────────────────────────
```

Then prompt:

> "Feature plan complete. Development is in your hands from here.
> Run **inception:feature-plan** again whenever you're ready to plan
> the next feature."
