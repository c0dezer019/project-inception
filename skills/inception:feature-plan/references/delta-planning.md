# Delta Planning Guide

Reference for inception:feature-plan. Use this guide when analyzing the impact
of a new feature and producing the phased delta implementation plan.

---

## What is a Delta Plan?

A delta plan describes only what changes — the difference between the current
codebase state and the post-feature state. It does not re-document existing code.

A good delta plan answers:
1. What new code needs to be written?
2. What existing code needs to change, and why?
3. What are the risks of this change to the existing system?
4. In what order should the work happen?

---

## Impact Analysis Framework

### Database Layer

**New entities required when:**
- The feature introduces a new noun (e.g., "Comments" → `COMMENT` entity)
- The feature requires tracking a new relationship (e.g., "User follows Topic" → `FOLLOW` join table)
- The feature needs to store new state (e.g., "Notification preferences" → `NOTIFICATION_SETTING`)

**Modified entities required when:**
- The feature adds new fields to an existing entity
- The feature changes the cardinality of an existing relationship
- The feature adds soft-delete, versioning, or audit trails to an existing entity

**Migration considerations:**
- Adding nullable columns to existing tables = low risk
- Adding non-nullable columns without defaults = HIGH risk (requires backfill)
- Renaming or removing columns = HIGH risk (requires coordination with code changes)
- Adding indexes = low risk, but may lock table briefly on large datasets
- Changing column types = HIGH risk (requires careful migration strategy)

### API / Backend Layer

**New routes required when:**
- The feature introduces new CRUD operations
- The feature exposes new data to the frontend
- The feature needs to handle new webhooks or external callbacks

**Modified routes required when:**
- The feature changes the response shape of an existing route
- The feature adds new query parameters to an existing route
- The feature changes authorization requirements on an existing route

**Breaking change signals:**
- Removing a field from an existing response → any consumer will break
- Changing a field type → any consumer will break
- Changing route path → any hardcoded client call will break
- Adding required request fields → any caller not sending them will break

### Frontend Layer

**New screens/pages required when:**
- The feature has its own primary view (e.g., a Notifications page)
- The feature requires a dedicated settings view
- The feature has a multi-step onboarding or setup flow

**New components required when:**
- The feature introduces new UI patterns not in the existing component library
- The feature requires a complex widget (e.g., a rich text editor, a calendar picker)
- The feature needs a new card, list item, or modal type

**Modified components required when:**
- The feature adds a new action to an existing component (e.g., adding "Share" to a card)
- The feature adds new states to an existing component (e.g., a loading state that didn't exist)
- The feature changes the data shape a component depends on

### Auth & Permissions

**New permissions required when:**
- The feature is role-gated (only admins, only subscribers, etc.)
- The feature introduces resource ownership (users can only edit their own items)
- The feature adds organization-level access control

**Watch for:**
- Features that accidentally expose data across users (missing ownership checks)
- Features that elevate a user's capabilities without explicit permission grant
- Features that bypass existing rate limits

### Third-Party Integrations

**New integrations required when:**
- The feature needs email sending (add Resend/SendGrid if not present)
- The feature needs file storage (add S3/R2 if not present)
- The feature needs a payment provider (add Stripe if not present)
- The feature needs push notifications (add FCM/Expo Notifications)
- The feature needs a search provider (add Meilisearch/Algolia)

**Cost implications to flag:**
- Adding a managed service introduces ongoing cost — note estimated tiers
- Adding a per-request API (OpenAI, Twilio) — flag that usage scales with traffic

---

## Risk Register

For each risk, assess: **severity** (High/Medium/Low) and **likelihood** (High/Medium/Low).
Surface all High severity risks regardless of likelihood.

### Common Risk Patterns

**Data Migration Risks**
- Non-nullable column addition on a large table
  - Mitigation: add as nullable first, backfill, then add constraint
- Changing a column type (e.g., int → uuid)
  - Mitigation: add new column, dual-write, migrate, remove old column
- Deleting a column that's still referenced in code
  - Mitigation: deploy code change first, then schema migration

**Breaking API Changes**
- Removing or renaming a response field
  - Mitigation: deprecate with warning, support both for one release cycle
- Changing route paths
  - Mitigation: support old path with redirect for a transition period

**Performance Risks**
- New query that could cause N+1 (e.g., fetching related data in a loop)
  - Mitigation: use joins or eager loading, add query analysis in dev
- Missing index on a new foreign key or commonly filtered column
  - Mitigation: add index in the migration alongside the column
- New feature increases page payload significantly
  - Mitigation: code-split the feature, lazy-load heavy components

**Security Risks**
- New user input that isn't validated or sanitized
  - Mitigation: validate at API boundary, sanitize before DB writes
- New file upload without type/size validation
  - Mitigation: enforce allowed MIME types and max file size
- New admin feature without proper role check
  - Mitigation: always gate admin routes with middleware, not just UI

**Integration Risks**
- New third-party service that has rate limits
  - Mitigation: add retry logic with exponential backoff, queue heavy operations
- New webhook endpoint without signature verification
  - Mitigation: verify webhook signatures before processing

---

## Delta Implementation Plan Phases

Always structure the delta plan in this order. Skip phases that don't apply.

### Phase 0 — Schema & Migration
Write and apply DB migrations before any other code changes. Schema is the foundation.
- Create new entity models/schema definitions
- Write migration files
- Add new indexes
- Seed any required reference data

### Phase 1 — Backend
Build the server-side before wiring up the frontend.
- New service functions (business logic)
- New repository methods (data access)
- New API routes / route handlers
- New middleware (auth guards, validators)
- Update `.env.example` with new variables
- Unit tests for service functions

### Phase 2 — Frontend
Build UI against the new API.
- New pages/screens
- New components
- Modify existing components
- Wire up data fetching (React Query, SWR, etc.)
- Handle loading, empty, and error states

### Phase 3 — Integration & Testing
Validate end-to-end before shipping.
- Integration tests for new API routes
- End-to-end tests for critical user flows
- Manual QA checklist execution
- Performance check on new queries (EXPLAIN ANALYZE)

### Phase 4 — Deployment Considerations
Plan the rollout, especially for risky migrations.
- Feature flag strategy (if the feature is risky or incremental)
- Migration execution order (schema first, then code deploy)
- Rollback plan (what to do if something goes wrong)
- Monitoring: what metrics to watch after deploy

---

## Complexity Estimation

| Signals | Estimated Complexity |
|---|---|
| New UI only, no schema changes, no new routes | Low |
| 1-2 new entities, new routes, new screens | Medium |
| 3+ new entities, breaking API changes, new third-party | High |
| Auth/permission model changes, large data migration | High |
| Cross-cutting concern (affects many existing components) | High |

Always explain the complexity rating in 1-2 sentences referencing specific signals.
