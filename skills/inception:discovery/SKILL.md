---
name: inception:discovery
description: >
  This skill should be used when the user wants to start a new project from scratch,
  plan a project, or kick off a development effort. Trigger phrases include: "start a
  new project", "let's plan X", "I want to build X", "project inception", "discovery
  phase", "help me scope a project", "I have an idea for X". Also trigger proactively
  when the user describes a project idea but hasn't established any scope, audience,
  or requirements yet. Run this skill before inception:design.
metadata:
  version: "0.3.0"
  phase: "1 of 3"
  next: "inception:design"
---

# Project Inception — Phase 1: Discovery

Facilitate a structured discovery session to capture scope, audience, user stories,
core features (derived from stories), caching needs, and state complexity. Produce
a README draft and optionally an SRS document. Hand off a clean `discovery` context
object to Phase 2 (inception:design).

**Flow order:** Project shape → Audience & Problem → User Stories → Features (story-driven)
→ Caching → State complexity flag → SRS check → Review → Output generation.

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

## Discovery Context Object

Maintain a running object throughout the session:

```
discovery = {
  project_name,
  project_type,        // web, api, mobile, cli, desktop, full-stack saas
  scale,               // personal, small-team, startup, production, enterprise
  team_experience,     // beginner, intermediate, advanced, mixed
  audience,            // who the end users are (free text)
  problem_statement,   // what problem this solves (free text)
  core_features,       // [] from feature rounds
  custom_features,     // [] free-text additions
  user_stories,        // [] generated or gathered
  caching_needs,       // none, basic, aggressive, offline-first, is-a-caching-tool
  state_complexity,    // none, low, medium, high — flagged only, not prescribed
  needs_srs,           // boolean
  team_or_company      // boolean — determines SRS necessity
}
```

Also maintain `inferred = {}` — keys populated from Step 0.

---

## Step 0 — Pre-parse (always run first, never shown to user)

Scan the user's initial message and populate `inferred` with any answers extractable
at high or medium confidence. Do not ask what the user already told you.

### Confidence levels

**High confidence** (record directly, confirm silently at review):
- Explicit project type: "Next.js app", "REST API", "mobile app", "CLI tool"
- Explicit scale signals: "enterprise", "just for me", "startup MVP", "for my company"
- Explicit audience: "for my team", "for customers", "for internal use"
- Explicit features: "needs auth", "payments", "real-time", "file uploads"
- Startup/MVP language: "launch fast", "MVP", "building a product" → Startup/MVP
- Personal language: "side project", "for fun", "hobby", "just me" → Personal/Hobby
- Work language: "at work", "internal tool", "for my team" → Small Team

**Medium confidence** (pre-select, confirm before proceeding):
- Implicit scale from goal: "launch an MVP" → Startup/MVP
- Implicit audience from context: "SaaS product" → paying customers
- Implicit features from product examples: "like Notion" → auth, real-time, file upload

**Product example → feature mapping**:

| "like ___" | Pre-infer features |
|---|---|
| Linear, Jira, Trello | Auth, Real-time, Admin Dashboard + issue tracking, kanban |
| Slack, Discord | Auth, Real-time, File Upload, Push Notifications |
| Notion | Auth, Real-time, File Upload + rich text editor, databases |
| Stripe, Paddle | Payments |
| Airbnb, Uber | Auth, Payments, File Upload, Search, Real-time, geolocation |
| Shopify | Auth, Payments, File Upload, Search, Admin Dashboard, Email |

Show pre-inferred features before Round 1: "Based on '[example]', I've pre-selected:
[list]. Does that cover it, or do you want to add/remove anything?"

---

## Group 1 — Project Shape

Ask all non-inferred questions together (one `AskUserQuestion` call in Cowork; one batched message in Claude Code):

**Project Type** (multiSelect) — "What type of application are you building?"
- Web Application (browser-based frontend)
- API / Backend Service (server-side, no UI)
- Mobile App (iOS, Android, or cross-platform)
- Full-stack SaaS (web + backend + auth + billing)

If Other → follow up: CLI Tool / Desktop App / free-text.

**Scale** (single) — "What's the expected scale and team context?"
- Personal/Hobby (solo, <100 users, no SLA)
- Small Team (1–10 people, internal tool or small product)
- Startup/MVP (launch fast, iterate, potential to scale)
- Production (hundreds to thousands of users, reliability matters)
- Enterprise (thousands+ users, compliance, SLAs, multiple teams)

**Team Experience** (single) — "What's the team's experience level?"
- Beginner — learning the stack, needs clear patterns
- Intermediate — comfortable with the domain
- Advanced — can handle complex architectural tradeoffs
- Mixed — varies across team

**Team/Company context** (single) — "Is this for a team or company?"
- Yes — record `team_or_company: true`, flag SRS as likely needed
- No — personal or solo project

---

## Group 2 — Audience & Problem

Ask as a single prompt (one `AskUserQuestion` call in Cowork; one conversational message in Claude Code):

"Who are the end users of this project, and what problem does it solve for them?"

Accept free-text. Record as `audience` and `problem_statement`. This feeds directly
into the README and SRS.

---

## Group 3 — User Stories

Read `references/user-story-formats.md` before this step.

User stories define what users need — they drive feature selection, not the other
way around. Gather stories immediately after establishing audience and problem,
before presenting any feature checklist.

"Would you like to provide user stories, or should I generate them from what
we've discussed so far?"
- Generate them for me
- I'll provide them
- Skip for now

If generating: produce 5–10 user stories in free-form style based on `audience`
and `problem_statement`. Prefer free-form narrative over rigid "As a [role]"
format — see `references/user-story-formats.md` for guidance. Do NOT reference
features yet — derive stories purely from who the users are and what they need.

If user provides: accept free-text and record as-is.

Show generated stories and ask: "Do these look right? Add, remove, or edit anything?"

Record confirmed stories as `user_stories`.

---

## Group 4 — Feature Set

Always its own dedicated batch. Never merged with other groups.

Features are derived from user stories first. Scan `user_stories` and extract any
features clearly implied before presenting the checklist. Pre-select those and show
them to the user:

> "Based on your user stories, these features seem necessary: [list]. Does that
> cover it, or do you want to add/remove anything?"
Give options: "Looks good", "Add more", "Change something."

If `inferred.core_features` is also non-empty (from Step 0 product example), merge
with story-derived features before presenting.

After confirming story-derived features, run a "did we miss anything?" pass:

**Round 1** — "Any of these also needed?"
- Authentication & User Accounts
- Payments / Billing (subscriptions, one-time)
- Real-time (live updates, chat, WebSockets)
- File Upload / Storage

**Round 2** — "Any of these too?"
- Push Notifications (mobile / browser)
- Email (transactional, newsletters)
- Search (full-text, faceted filters)
- Admin Dashboard / CMS

Skip any feature already captured from stories. After both rounds, ask:
"Any custom features not listed?" — record free-text as `custom_features`.

---

## Group 5 — Caching

Always ask. Never skip. Caching strategy has architectural implications that must
be surfaced in discovery before design locks anything in.

"What are your caching and offline requirements?"
- None — fresh data always, simplicity preferred
- Basic — standard HTTP / CDN cache headers
- Aggressive — background refetch, stale-while-revalidate (React Query / SWR style)
- Offline-first — works without network, local sync
- Caching IS the product — this tool is primarily a caching layer

Record as `caching_needs`. If "Caching IS the product", note this prominently in
the discovery summary — it will heavily shape the design phase.

---

## Group 6 — State Complexity Flag

Only ask if one or more of the following signals are present in `user_stories`,
`core_features`, or `custom_features`:
- Real-time collaboration
- Offline-first sync
- Multi-step complex flows
- Collaborative editing
- Complex multi-user interactions

If signals present, ask:
"Your stories and feature set suggest potentially complex client state. How would
you describe the expected UI state complexity?"
- Low — mostly server data, minimal UI state
- Medium — forms, modals, some shared state
- High — real-time sync, optimistic updates, collaborative editing

Record as `state_complexity`. Do NOT recommend a solution here — flag only.
If no signals, set `state_complexity: none` and skip.

---

## Group 7 — SRS Check

If `team_or_company` is true OR scale is Production/Enterprise, ask:
"Does your team or company require a Software Requirements Specification (SRS)?"
- Yes — I'll generate an IEEE 830-format SRS
- No — README only is fine

Record as `needs_srs`.

If `team_or_company` is false and scale is Personal/Hobby or Startup/MVP, skip
this question and set `needs_srs: false`.

---

## Step — Review & Confirm

Display a clean summary before generating outputs:

```
DISCOVERY SUMMARY
─────────────────────────────────────────
Project:        [project_name]
Type:           [project_type]
Scale:          [scale]
Team Level:     [team_experience]
Audience:       [audience]
Problem:        [problem_statement]
Core Features:  [core_features + custom_features]
Caching:        [caching_needs]
State:          [state_complexity — "flagged: high" or "not flagged"]
User Stories:   [count] captured
SRS Required:   [yes/no]
─────────────────────────────────────────
```

Ask: "Does this look right? Confirm to generate your discovery documents, or tell
me what to change."

Once confirmed, ask for the project name if not already captured:
"What would you like to name this project? (kebab-case, e.g. `my-project-name`)"

---

## Output Generation

### Always generate: README draft

Produce a `README.md` skeleton:

```markdown
# [Project Name]

## Overview
[1-2 sentences from problem_statement]

## Who It's For
[audience]

## Core Features
[bulleted list from core_features + custom_features]

## Tech Stack
> To be determined in the Design & Architecture phase.

## Getting Started
> To be completed after scaffolding.

## Contributing
> To be completed by the team.
```

### If needs_srs is true: SRS document

Read `references/srs-template.md` and produce a populated IEEE 830-format SRS
using all collected discovery data. See the template for section structure.

### State complexity notice

If `state_complexity` is medium or high, append this notice to the discovery
output (not in the README):

```
⚠ State Complexity Flag
Your feature set suggests [medium/high] client-side state complexity.
Revisit state management strategy at the start of the development phase.
This will be surfaced again in the Design & Architecture phase if relevant.
```

---

## Handoff to Phase 2

After outputs are generated, prompt:

> "Discovery complete. Ready to move into Design & Architecture?
> Run **inception:design** when you're ready — your discovery context will carry over."

Pass the full `discovery` object forward. inception:design expects it.
