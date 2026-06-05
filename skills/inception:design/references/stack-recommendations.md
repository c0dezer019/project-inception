# Stack Recommendation Matrix

Use this reference when executing Step 14 of the project wizard. Map the collected
`answers` object to specific technology choices using the decision trees below.

---

## Primary Stack Selection

### Web / Full-stack SaaS

**Default for all web projects**: Next.js App Router + TypeScript

Exceptions:
- User selected Python runtime → Django (full-stack) or FastAPI + separate React/Next frontend
- Advanced team + Performance priority + SPA target → Remix or Vite + React (skip SSR overhead)
- Very Complex state + offline-first → Vite + React + TanStack Query + service worker (SPA)

### Mobile

- React Native / Expo (JS ecosystem) → Expo SDK + TypeScript + Expo Router
- Flutter → Flutter 3.x + Dart + GoRouter
- iOS native → Swift + SwiftUI + Swift Package Manager
- Android native → Kotlin + Jetpack Compose + Gradle

### API / Backend only

| Runtime | Beginner/Intermediate | Advanced |
|---|---|---|
| Node.js | Express + TypeScript | Fastify + TypeScript |
| Python | FastAPI + Pydantic | FastAPI + Pydantic (same — FastAPI is already excellent) |
| Go | Gin or Echo | Chi or stdlib net/http |
| Rust | Axum | Axum or Actix-web |
| Java | Spring Boot | Spring Boot |
| .NET | ASP.NET Core minimal API | ASP.NET Core |

### CLI

| Runtime | Library |
|---|---|
| Node.js | oclif (complex CLIs) or commander.js (simple) |
| Python | Typer (type-annotated) or Click (classic) |
| Go | cobra + viper |
| Rust | clap |

### Desktop

| Framework | When |
|---|---|
| Electron | Team knows web, needs cross-platform, size not critical |
| Tauri | Performance/size matters, team can handle Rust backend |
| Swift/SwiftUI | macOS-only, native feel essential |

---

## Database Selection

| Data Complexity | Scale | Recommended DB |
|---|---|---|
| Simple / Relational | Any | PostgreSQL (always Postgres over MySQL for new projects) |
| Simple | Personal/Hobby | SQLite (via Prisma or Drizzle — zero ops) |
| Complex (reporting) | Any | PostgreSQL + consider read replica or materialized views |
| High-Volume (time-series) | Production+ | PostgreSQL + TimescaleDB extension OR ClickHouse (analytics) |
| Graph/Network | Any | PostgreSQL with recursive CTEs (simple) OR Neo4j (complex graphs) |
| Document-heavy | Startup+ | PostgreSQL JSONB (preferred) OR MongoDB (only if team already uses it) |

**Never recommend MongoDB for new projects unless user explicitly asks** — Postgres JSONB
handles document storage with full relational capability.

### ORM / Query Tool

| Runtime | Beginner/Intermediate | Advanced |
|---|---|---|
| TypeScript | Prisma (great DX, migration tooling) | Drizzle (type-safe, lighter, more control) |
| Python | SQLAlchemy 2.x ORM | SQLAlchemy Core or raw asyncpg |
| Go | GORM (beginner) | sqlx + raw SQL or sqlc (generates type-safe code) |
| Rust | Diesel or SeaORM | sqlx (async, type-checked queries) |

---

## Authentication

| Context | Beginner/Intermediate | Advanced / Enterprise |
|---|---|---|
| Next.js | Clerk (DX-optimized, free tier) | Auth.js / NextAuth (open-source, no vendor lock-in) |
| Next.js + Enterprise | Auth0 or AWS Cognito | Auth0 with custom rules |
| API (Node) | JWT + bcrypt (DIY) or Supabase Auth | Keycloak (self-hosted) or Auth0 |
| API (Python) | python-jose + passlib | Same + Keycloak |
| Mobile | Clerk (Expo SDK) or Firebase Auth | Auth0 |
| Full-stack SaaS + Payments | Clerk (handles user management elegantly with Stripe) | Auth.js + custom billing |

**Clerk** — recommend when: Startup, MVP, Time to Market priority, or Beginner team.
**Auth.js** — recommend when: Advanced team, Cost Efficiency priority, or dislike vendor lock-in.
**Auth0 / Cognito** — recommend when: Enterprise, Compliance priority, SSO/SAML needed.

---

## API Layer Implementation

| Style | Implementation |
|---|---|
| REST | Next.js API routes (App Router Route Handlers) OR Fastify/Express routes |
| GraphQL | Apollo Server 4 + Apollo Client (or urql for lighter bundle) |
| tRPC | @trpc/server + @trpc/react-query (TypeScript only, both ends) |
| gRPC | @grpc/grpc-js + protobufjs (Node) OR google.golang.org/grpc (Go) |

**tRPC only when**: TypeScript on both frontend AND backend, team is Intermediate+.
**gRPC only when**: service-to-service communication, Performance is a top priority, team is Advanced.
**REST default**: mixed teams, public APIs, cross-language backends, any Beginner team.

---

## State Management

> State management library selection is deferred to the development phase.
> The discovery and design phases flag state complexity level only.
> Revisit this decision before wiring up components in Phase 2 of development.

Guidelines for the dev phase (not surfaced to user during inception):

| State Complexity | Recommended |
|---|---|
| Low | React built-ins only (useState, useContext, useReducer) |
| Medium | TanStack Query (server state) + useState/useReducer (local) |
| High | TanStack Query + Zustand (client state) |
| Very High | TanStack Query + Zustand + Jotai (atomic state) |

For offline-first + Very High: add Replicache or ElectricSQL for local-first sync.

**No Redux** unless: team has existing Redux expertise, or migrating a Redux codebase.
Redux Toolkit is acceptable if Redux is chosen — never bare Redux.

---

## Feature-Specific Recommendations

### Payments
- **Always Stripe** — Stripe Checkout for simple, Stripe Elements for custom UI
- Package: `stripe` (server) + `@stripe/stripe-js` + `@stripe/react-stripe-js` (client)
- Add `stripe-webhooks` handler for subscription lifecycle events

### File Storage
| Scale | Solution |
|---|---|
| Personal/Hobby | Cloudflare R2 (free tier, S3-compatible) |
| Small/Startup | Cloudflare R2 or AWS S3 |
| Production+ | AWS S3 + CloudFront CDN |
| With transforms | Cloudinary (images) or Mux (video) |

### Real-time
| Scale | Solution |
|---|---|
| Simple / Hobby | Server-Sent Events (no extra deps) |
| Small / Startup | Supabase Realtime (if using Supabase) or Pusher (managed) |
| Production | Ably (managed, reliable) or self-hosted Socket.io |
| Enterprise | AWS AppSync or Ably |

### Email
| Volume | Solution |
|---|---|
| Low (transactional) | Resend (excellent DX, generous free tier) |
| High / Marketing | Resend + React Email for templates |
| Enterprise | SendGrid or AWS SES |

### Search
| Complexity | Solution |
|---|---|
| Simple | PostgreSQL full-text search (tsvector — no extra infra) |
| Faceted / Relevance | Meilisearch (self-hosted) or Algolia (managed) |
| Enterprise | Elasticsearch or OpenSearch |

### Push Notifications
- Web: Web Push API (vanilla, no cost) + `web-push` npm package
- Mobile (Expo): Expo Notifications SDK + Expo Push Service
- Cross-platform: Firebase Cloud Messaging (FCM)

---

## Hosting-Specific Configuration

### Vercel
- Works best with: Next.js, SvelteKit, Nuxt, Astro
- Database: Neon Postgres (serverless, Vercel integration) or PlanetScale
- Storage: Vercel Blob or Cloudflare R2
- Note: no persistent server — use serverless functions or edge runtime

### Railway / Render
- Works well with: any Node/Python/Go/Rust/Docker project
- Best when: need a persistent server (WebSockets, background jobs)
- Database: managed Postgres included
- Note: has free tier sleep (Render free) — use paid for production

### AWS
- EC2/ECS for persistent servers; Lambda for serverless
- RDS Postgres for database; ElastiCache for Redis; S3 for storage
- Recommend: ECS Fargate (containers without managing instances)
- Note: complex setup — only if team has ops experience or Advanced

### GCP
- Cloud Run (serverless containers, great for Next.js/FastAPI)
- Cloud SQL Postgres for database
- Firebase for mobile auth + Firestore if document DB is acceptable
- Note: Cloud Run is the "Railway of GCP" — easiest entry point

### Self-hosted / VPS
- Docker Compose for multi-service setups
- Traefik or Caddy for reverse proxy + auto-SSL
- Postgres via Docker or managed (e.g. Supabase self-hosted)
- Recommend Coolify or Dokku for app-platform-like experience on VPS

---

## Opinionated Defaults by Persona

### Beginner + Personal/Hobby
→ Next.js + TypeScript + Prisma + SQLite → Postgres later + Auth.js + Vercel
→ Keep it simple. One repo, one service, zero ops.

### Intermediate + Startup/MVP + Time to Market
→ Next.js + TypeScript + Prisma + Neon Postgres + Clerk + Stripe + Vercel
→ Batteries included. Ship in days, not weeks.

### Advanced + Production + Performance
→ Next.js (or Remix if SPA-leaning) + Drizzle + Postgres + Auth.js + tRPC + Vercel/Railway
→ Lean, type-safe, high control.

### Advanced + Enterprise + Compliance
→ Next.js or standalone API (Fastify/Go) + Prisma/GORM + Postgres (RDS) + Auth0 + AWS
→ Auditability, RBAC, compliance baked in from the start.

### API-only + Go + Performance
→ Echo or Chi + sqlc + Postgres + JWT auth + Railway or GCP Cloud Run
→ Simple, fast, easy to deploy.

### Full-stack SaaS + Mixed Team
→ Next.js + TypeScript + Prisma + Neon + Clerk + Stripe + Resend + Vercel
→ The T3-adjacent stack. Most common for SaaS MVPs. Great docs everywhere.

### Mobile + Beginner + Personal/Hobby (local-first)
→ Expo (React Native) + TypeScript + Expo Router + expo-sqlite + Drizzle (local DB)
→ No backend needed for local-only apps. EAS Build for distribution. Zero ops.

### Mobile + Intermediate/Advanced + Needs Backend
→ Expo + TypeScript + Expo Router + TanStack Query + your backend (Next.js or Fastify)
→ Clerk (Expo SDK) or Supabase Auth. Supabase Realtime if real-time needed.
→ "Hosting" = EAS Build for the app + Railway/Vercel for the backend API.

### Mobile + Payments
→ Expo + Stripe (via webview or React Native Stripe SDK) + backend for webhook handling
→ App Store rules: in-app purchases for digital goods require Apple/Google IAP, not Stripe.

---

## Dependency Conflict Check (Silent — Not User-Facing)

Run this check silently before outputting the stack recommendation. If any conflict
is detected, surface it to the user as a warning before finalizing. Do not present
this list to the user as a question.

**Conflicts to detect:**
- tRPC + GraphQL selected together → redundant API layers, pick one
- Clerk + Auth.js selected together → redundant auth solutions, pick one
- Redux + Zustand both implied → redundant client state, pick one
- MongoDB suggested for relational data → redirect to Postgres
- Next.js pages router implied → always recommend App Router for new projects
- gRPC for a beginner team → warn that gRPC requires significant expertise

**Conflict output format (only shown if conflict detected):**
```
⚠ Conflict Detected: [brief description]
Recommendation: [specific resolution]
```

---

## Rationale Template

When writing the rationale (2-3 sentences), connect:
1. Stack choice → user's stated priorities
2. A specific tradeoff acknowledged (what was sacrificed for the win)
3. What makes this stack particularly suited to their team experience level
