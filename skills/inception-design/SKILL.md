---
name: inception-design
description: >
  This skill should be used after project discovery is complete and the user is ready
  to design the architecture. Trigger phrases include: "design the architecture",
  "pick a stack", "design phase", "what folder structure should I use", "help me
  design the database", "inception design", "architecture phase". Also triggers
  automatically after inception-discovery completes. Expects a discovery context
  object — if one is not present, prompt the user to run inception-discovery first
  or provide project details manually.
metadata:
  version: "0.6.0"
  phase: "2 of 3"
  previous: "inception-discovery"
  next: "inception-feature-plan"
---

# Project Inception — Phase 2: Design & Architecture

Transform discovery findings into concrete architectural decisions. Produce a stack
recommendation, ERD (Mermaid), folder structure recommendation, data flow overview,
and a UI/UX design handoff prompt.

## Environment Detection

At the start of the session, silently check whether `AskUserQuestion` is available
as a callable tool in the current environment.

- **If available (Cowork):** use `AskUserQuestion` for all question groups. Max 4
  options per call. Never include "None" or "Other" as explicit options — these are
  automatic. Follow up if "Other" is selected.
- **If not available (Claude Code or other):** present all questions as plain
  conversational markdown. Format options as a numbered or bulleted list and accept
  free-text input. Apply the same batching logic — ask grouped questions together
  in one message rather than one at a time.

All question groups below support both modes. The content and logic are identical
regardless of environment — only the rendering differs.

## Design Context Object

Extend the incoming `discovery` object with:

```
design = {
  ...discovery,
  hosting,             // vercel, railway, aws, gcp, azure, self-hosted
  target_platform,     // rendering/runtime approach
  api_style,           // rest, graphql, trpc, grpc, none
  data_complexity,     // simple, relational, complex, high-volume, graph
  data_access,         // [] via-api, direct-db, realtime-subs, third-party
  priorities,          // [] up to 3
  state_complexity,    // carried from discovery or refined here
  stack,               // {} finalized stack recommendation
  folder_structure,    // recommended pattern + rationale
  erd,                 // mermaid ERD string
  data_flow,           // prose description of data flow
}
```

---

## Step 0 — Context Check

Check if `discovery` object is present from inception-discovery.

If present: greet the user with a brief summary of what was captured in discovery
and confirm before proceeding:
> "Based on discovery, here's what I know: [2-3 sentence summary]. Ready to
> design the architecture?"

If not present: ask the user to describe the project. Collect the minimum needed:
project type, scale, team experience, core features, and caching needs. Reconstruct
a partial `discovery` object and proceed.

---

## Group 1 — Hosting & Platform

Ask all non-inferred questions together (one `AskUserQuestion` call in Cowork; one batched message in Claude Code):

**Hosting** (single) — "Where will this be hosted?"
- Vercel — serverless, edge-optimized, first-class Next.js
- Railway / Render — simple deploys, affordable, no DevOps overhead
- AWS — full ecosystem, maximum control
- GCP / Google Cloud — Cloud Run, Firebase, GKE
- Azure — Microsoft ecosystem, enterprise compliance
- Self-hosted / VPS — full control (DigitalOcean, Hetzner, etc.)

**Target Platform** (single) — adapt options based on `project_type`:

*Web / Full-stack SaaS:*
- Server-rendered (Next.js/Remix — SEO-critical, fast first paint)
- Single Page App (React/Vue — client-heavy, dynamic UX)
- Hybrid (SSR + rich client interactivity)
- Progressive Web App (offline-capable, installable)

*Mobile:*
- iOS only
- Android only
- Both — React Native / Expo
- Both — Flutter

*API / Backend:*
- Node.js / TypeScript
- Python
- Go
- Other (prompt follow-up)

*CLI:*
- Node.js
- Python
- Go
- Other

*Desktop:*
- Electron
- Tauri
- Swift / SwiftUI
- Other

---

## Group 2 — Data Model

Ask together (one `AskUserQuestion` call in Cowork; one batched message in Claude Code):

**Data Complexity** (single) — "How complex is your data model?"
- Simple — flat records, basic CRUD (users, settings, posts)
- Relational — multiple entities with relationships
- Complex — reporting, aggregations, full-text search
- High-Volume — time-series, event streams, millions of rows
- Graph / Network — social graphs, deeply connected data

**Data Access Patterns** (multiSelect) — "How does your app access data?"
- Via your own API (frontend calls your backend)
- Direct DB queries (server-side only)
- Real-time subscriptions (live push)
- Third-party APIs (Stripe, Twilio, OpenAI, etc.)

---

## Group 3 — API Style & Priorities

Ask together (one `AskUserQuestion` call in Cowork; one batched message in Claude Code).
Skip API Style if `data_access` does not include "Via your own API".

**API Style** (single) — "What's your preferred API style?"
- REST — familiar, widely supported, easy to test
- GraphQL — flexible queries, great for complex relationships
- tRPC — type-safe end-to-end, TypeScript-first
- gRPC — high-performance, best for service-to-service

**Priorities Round 1** (multiSelect, max 3 total across both rounds):
- Developer Experience
- Time to Market
- Performance
- Type Safety

**Priorities Round 2** — "Any of these rank in your top 3?"
- Scalability
- Security
- Cost Efficiency
- Maintainability

If total > 3, ask user to narrow to exactly 3.

---

## Group 4 — State Complexity Refinement

Only ask if `state_complexity` was flagged as medium or high in discovery, OR if
new signals emerged (real-time, collaborative features confirmed in discovery).

"Your feature set suggests potentially complex state. Can you confirm the expected
level of UI state complexity?"
- Low — mostly server data, minimal UI state
- Medium — forms, modals, some shared client state
- High — real-time sync, optimistic updates, collaborative editing

Record refined value. Still do NOT prescribe a solution — flag for dev phase with
a specific note appended to design output.

---

## Step — Stack Recommendation

Read `references/stack-recommendations.md` in full before generating recommendations.

Apply the decision matrix to `design` context. Run silent dependency conflict check
against the conflicts list — surface any conflicts to the user before finalizing.

Output in this format:

```
RECOMMENDED STACK
─────────────────────────────────────────
Frontend:      [framework + 1-line reason]
Backend:       [runtime/framework + 1-line reason]
Database:      [DB + ORM/query tool + 1-line reason]
Auth:          [solution + 1-line reason]
Payments:      [solution, if needed]
File Storage:  [solution, if needed]
Realtime:      [solution, if needed]
API Layer:     [style + implementation + 1-line reason]
Hosting:       [platform + key config notes]
─────────────────────────────────────────
Rationale: [2-3 sentences connecting stack to priorities and constraints]

⚠ State Note: [only if state_complexity is medium/high]
State complexity is flagged as [level]. Revisit state management library
selection at the start of the development phase before wiring up components.
```

Ask: "Does this stack look right? Confirm or tell me what to change."

---

## Step — Folder Structure

Read `references/folder-structures.md` before generating a recommendation.

Based on `project_type`, `core_features`, `data_complexity`, and `team_experience`,
recommend a folder structure pattern. Do not always present all options — recommend
one and explain why it fits, then offer alternatives if the user wants to explore.

Output format:

```
RECOMMENDED STRUCTURE: [Pattern Name] ([rationale in one sentence])

[project_name]/
├── [directory tree]

Why this pattern:
[2-3 sentences explaining the fit]

Alternatives considered:
- [Pattern]: [why it was not recommended for this project]
```

Ask: "Does this structure work for you, or would you like to explore alternatives?"

---

## Step — ERD

Read `references/erd-guide.md` before generating the ERD.

Derive entities from `core_features`, `custom_features`, and `data_complexity`.
Generate a Mermaid ERD covering all primary entities and their relationships.

Output:

````
```mermaid
erDiagram
  [entities and relationships]
```
````

Follow the ERD with a brief legend explaining any non-obvious relationships or
design decisions (e.g., soft deletes, polymorphic associations).

Ask: "Does this ERD capture your data model? Let me know what to add, remove,
or adjust."

---

## Step — Data Flow

Produce a concise prose description of how data moves through the system:

```
DATA FLOW OVERVIEW
─────────────────────────────────────────
[1. Client/Entry point]
  → [What the client sends / triggers]

[2. API / Backend layer]
  → [How requests are processed]

[3. Data layer]
  → [How data is read/written]

[4. Response path]
  → [How data returns to the client]

[5. Async/background processes — if applicable]
  → [Jobs, webhooks, events]
─────────────────────────────────────────
```

---

## Step — UI/UX Handoff Prompt

Read `references/uiux-handoff-prompt.md` for the prompt template.

Generate a structured design handoff prompt the user can take to a design tool
(Canva, Figma, Claude, etc.). Populate it using all collected context.

Present it in a copyable code block with instructions:
> "Here's your UI/UX design handoff prompt. Copy this into your design tool
> of choice (Canva, Figma AI, or a new Claude conversation) to start the
> visual design phase."

---

## Final Output Summary

After all steps are confirmed, produce a final design summary:

```
DESIGN & ARCHITECTURE SUMMARY
─────────────────────────────────────────
Project:         [project_name]
Stack:           [key technologies, comma-separated]
Folder Pattern:  [pattern name]
Database:        [DB + ORM]
API Style:       [style]
Hosting:         [platform]
─────────────────────────────────────────
Artifacts produced:
  ✓ Stack recommendation
  ✓ Folder structure
  ✓ ERD (Mermaid)
  ✓ Data flow overview
  ✓ UI/UX handoff prompt
─────────────────────────────────────────
```

Then prompt:

> "Design phase complete. From here, development is in your hands.
> When you're ready to plan a new feature integration, run **inception-feature-plan**."
