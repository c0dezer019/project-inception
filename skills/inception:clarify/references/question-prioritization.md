# Assumption Refinement Guide

Reference for inception:clarify. Use when updating, challenging, or replacing
inferences Jordan made during discovery.

---

## Downstream Impact Map

When an assumption changes, use this map to identify what artifacts need updating.

| Assumption type | Downstream impact |
|---|---|
| Audience or problem statement | User stories, README, SRS sections 2.1–2.3 |
| Core feature added/removed | ERD, folder structure, stack rec, SRS section 5 |
| Data model change | ERD, SRS section 3, API routes, folder structure |
| Auth/permissions change | ERD, stack rec, API design, SRS section 6.2 |
| Scale change | Stack rec, hosting, caching strategy, SRS section 6.1 |
| Caching change | Stack rec, data flow, SRS section 4.4 |
| Third-party integration | Stack rec, ERD (if data stored), SRS section 4.3 |
| Compliance requirement | SRS sections 6.2, 7.1, may affect stack + hosting |

Always surface downstream impact after any update. Never silently change one
thing when others are affected.

---

## Inference Confidence Levels (internal use only — never shown to user)

Use these internally to prioritize which assumptions are most likely to need
revisiting as the project evolves. Do not surface confidence levels in output.

**Strong inference** — well-established pattern, unlikely to need revisiting:
- "Auth means email + password" for a standard SaaS
- "Payments means Stripe" for a US-based product with no stated alternative
- "File upload needs size + type validation" — universal requirement

**Reasonable inference** — common pattern but context-dependent:
- "Refund flow needed" for a product with subscriptions
- "Admin needs data export" for a B2B product
- "Mobile-first" for a consumer-facing product

**Weak inference** — significant uncertainty, most likely to need refinement:
- Any inference from "faster/easier/better" without clear context
- Feature scope inferred from a single vague user story
- Scale inferred from audience description without explicit signal

When doing a full review (Mode 4), start with weak inferences first.

---

## Inference Rewriting Rules

When a vague update comes in during refinement, apply the same inference
heuristics Jordan uses:

| Vague update | Concrete interpretation |
|---|---|
| "make it more flexible" | Add configuration options for [relevant feature]; avoid hardcoded values |
| "users want more control" | Expose user preferences for [relevant feature]; add per-user settings |
| "it should be faster" | Identify primary data fetch bottlenecks; add caching or pagination |
| "too complicated" | Reduce steps in primary flow; apply progressive disclosure |
| "not what they expected" | Align with UX conventions for [platform/audience type] |
| "they want to see more data" | Add [relevant metrics/fields] to primary view; consider dashboard |
| "they don't use [feature]" | Deprioritize or remove [feature]; log as explicit exclusion |

---

## Living Spec Version Guidelines

| Change type | Version bump |
|---|---|
| Wording clarification, no behavior change | 0.1 → 0.1.1 |
| Single assumption updated | 0.1 → 0.2 |
| Multiple assumptions updated in one session | 0.1 → 0.2 |
| Feature added or removed | 0.1 → 0.2 |
| Major scope change (audience, problem statement) | 0.1 → 1.0 |
| Post-prototype full revision | [N].x → [N+1].0 |

Always add a change log entry. Never overwrite history — keep previous
assumptions in the log with strikethrough or "Previous:" notation.

---

## Prototype Feedback → Assumption Delta

When a prototype reveals something unexpected, map observations to assumptions:

**User didn't understand the feature** →
Inference: UX pattern doesn't match audience expectations
Update: Add explicit onboarding or change interaction model assumption

**User ignored the feature** →
Inference: Feature doesn't address actual pain point
Update: Revisit problem statement and related user stories

**User wanted to do something the prototype didn't support** →
Inference: Missing feature or flow
Update: Add to feature set, run gap-filling pass on new feature

**User complained about speed** →
Inference: Caching or data fetching assumption needs revisiting
Update: Revisit caching_needs, flag for Riley to address in design

**User data looked different from expected** →
Inference: ERD or data model assumptions need revisiting
Update: Flag schema changes, suggest re-running inception:design for data layer
