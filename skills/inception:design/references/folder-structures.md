# Folder Structure Guide

Reference for inception:design when recommending a project folder structure.
Recommend one pattern based on project signals — do not always present all options.

---

## Decision Matrix

Use these signals to select the right pattern:

| Signal | Lean Toward |
|---|---|
| Small project, beginner team, personal/hobby | FBO |
| Clear domain boundaries, medium-large team | DDD |
| React-heavy frontend, component library focus | Atomic |
| Full-stack SaaS, large feature set, advanced team | DDD + FBO Hybrid |
| API-only backend | Layered (Routes/Services/Models) |
| Rapid MVP, small team, time-to-market priority | FBO |
| Enterprise, compliance, complex business logic | DDD |
| Design system needed, multiple product teams | Atomic + FBO Hybrid |

---

## Pattern 1: Feature-Based Organization (FBO)

**Best for:** Most web and full-stack projects. Small to medium teams. Startup/MVP
through Production scale. Beginner to Intermediate teams.

**Philosophy:** Organize by feature/domain first. Each feature owns its own
components, hooks, services, and types. Easy to navigate, easy to delete a feature.

```
src/
├── features/
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── types.ts
│   │   └── index.ts
│   ├── dashboard/
│   │   ├── components/
│   │   ├── hooks/
│   │   └── index.ts
│   └── [feature-name]/
├── shared/
│   ├── components/     ← UI primitives used across features
│   ├── hooks/          ← shared hooks
│   ├── utils/          ← shared utilities
│   └── types/          ← global types
├── lib/                ← third-party client setup (db, auth, stripe)
├── app/ or pages/      ← routing layer (framework-specific)
└── styles/             ← global styles, theme tokens
```

**Strengths:**
- Easy to find everything related to a feature in one place
- Features can be added or removed cleanly
- Works well with Next.js App Router co-location

**Weaknesses:**
- Shared code can become a dumping ground if not maintained
- Doesn't enforce strict domain boundaries (business logic can leak into components)

---

## Pattern 2: Domain-Driven Design (DDD)

**Best for:** Complex business domains, enterprise projects, Advanced teams,
Production and Enterprise scale. When business logic is the core of the product.

**Philosophy:** Model the software after the business domain. Separate domain logic
from infrastructure. Bounded contexts define clear ownership boundaries.

```
src/
├── domain/
│   ├── [entity]/
│   │   ├── [Entity].ts         ← domain model (pure business logic)
│   │   ├── [Entity]Repository  ← interface (abstract)
│   │   └── [Entity]Service.ts  ← domain service
├── application/
│   ├── [use-case]/
│   │   ├── [UseCase]Handler.ts
│   │   └── [UseCase]Dto.ts
├── infrastructure/
│   ├── db/
│   │   ├── [Entity]RepositoryImpl.ts  ← concrete DB implementation
│   │   └── schema/
│   ├── auth/
│   ├── email/
│   └── storage/
├── interfaces/
│   ├── http/
│   │   ├── routes/
│   │   ├── middleware/
│   │   └── controllers/
│   └── [other-interface]/     ← CLI, WebSocket, etc.
└── shared/
    ├── errors/
    ├── events/
    └── types/
```

**Strengths:**
- Business logic is protected from infrastructure details
- Highly testable — domain layer has zero external dependencies
- Scales well to large codebases and large teams
- Clear ownership boundaries

**Weaknesses:**
- Significant upfront overhead — not appropriate for MVPs
- Steeper learning curve for Beginner/Intermediate teams
- Can feel over-engineered for simple CRUD applications

---

## Pattern 3: Atomic Design

**Best for:** Component-library-heavy frontends, design system projects, teams with
a dedicated design/UI focus. Works best as a frontend-only pattern.

**Philosophy:** UI components are atoms → molecules → organisms → templates → pages.
Enforces strict component composition hierarchy.

```
src/
├── components/
│   ├── atoms/          ← smallest UI units (Button, Input, Label)
│   ├── molecules/      ← composed atoms (FormField, SearchBar)
│   ├── organisms/      ← composed molecules (Header, ProductCard, Form)
│   ├── templates/      ← page layouts (no data, just structure)
│   └── pages/          ← templates + real data
├── hooks/
├── services/
├── utils/
└── styles/
```

**Strengths:**
- Excellent for building and documenting a design system
- Forces intentional component decomposition
- Works well with Storybook

**Weaknesses:**
- Categorizing components is subjective and causes ongoing debates
- Doesn't address backend, services, or feature organization
- Overkill for most product codebases unless a design system is the product

---

## Pattern 4: DDD + FBO Hybrid

**Best for:** Large full-stack SaaS products, Advanced teams, Production+ scale.
When you need both clean domain separation AND practical feature organization.

**Philosophy:** Use DDD for the backend/domain layer, FBO for the frontend/UI layer.
Each layer has the organization pattern that suits it best.

```
src/
├── domain/             ← DDD: pure business entities and logic
├── application/        ← DDD: use cases and orchestration
├── infrastructure/     ← DDD: DB, auth, email, storage implementations
├── api/                ← HTTP interface layer (routes, controllers, middleware)
│
├── features/           ← FBO: frontend feature modules
│   ├── auth/
│   ├── dashboard/
│   └── [feature]/
├── shared/             ← FBO: shared UI components, hooks, utils
└── app/ or pages/      ← routing layer
```

**Strengths:**
- Best of both patterns for full-stack applications
- Backend is testable and domain-pure
- Frontend is navigable and feature-organized

**Weaknesses:**
- Most complex to set up and explain to new team members
- Requires team buy-in and documentation to maintain discipline

---

## Pattern 5: Layered (API/Backend Only)

**Best for:** API-only backends, microservices, or when there is no frontend in scope.

```
src/
├── routes/             ← HTTP route definitions
├── controllers/        ← request/response handling
├── services/           ← business logic
├── models/ or db/      ← data models, schema, repository layer
├── middleware/         ← auth, logging, error handling
├── utils/              ← shared utilities
└── types/              ← TypeScript types
```

**Strengths:**
- Simple and familiar to most backend developers
- Easy to onboard new developers
- Works well for REST and GraphQL APIs

**Weaknesses:**
- Business logic can become scattered across services without discipline
- Doesn't scale as cleanly as DDD for complex domains

---

## Folder Structure Output Template

When presenting a recommendation:

```
RECOMMENDED STRUCTURE: [Pattern Name]
([one-sentence rationale for why this fits the project])

[project_name]/
├── [full directory tree based on the pattern]

Why this pattern:
[2-3 sentences connecting pattern choice to project_type, team_experience,
scale, and feature set]

Alternatives considered:
- [Pattern]: [one sentence on why it was not recommended]
- [Pattern]: [one sentence on why it was not recommended]
```
