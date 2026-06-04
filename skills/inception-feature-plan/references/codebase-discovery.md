# Codebase Discovery Protocol

Reference for inception:feature-plan. Follow this protocol to understand an
existing project before planning a new feature integration.

---

## Discovery Priority Order

Always follow this order. Stop and summarize after each source if it provides
sufficient context. Do not over-read — the goal is enough context to plan the
feature, not a full codebase audit.

---

## Source 1: CLAUDE.md (Highest Priority)

**Check:** Look for `CLAUDE.md` in the project root and `~/.claude/CLAUDE.md`.

```bash
# Check project root
ls CLAUDE.md 2>/dev/null

# Check parent directories up to repo root
ls ../CLAUDE.md 2>/dev/null
ls ../../CLAUDE.md 2>/dev/null
```

**If found:** Read it in full. Extract:
- Project name and purpose
- Tech stack (frontend, backend, database, ORM, auth)
- Folder structure pattern
- Coding conventions (naming, file structure, import style)
- Known constraints or decisions
- Any existing feature documentation

**Treat CLAUDE.md as the authoritative source.** If it conflicts with what you
find in the code, prefer CLAUDE.md and note the discrepancy to the user.

---

## Source 2: README.md

**Check:** Project root `README.md`.

```bash
cat README.md 2>/dev/null | head -200
```

**Extract:**
- Project description and purpose
- Tech stack mentions
- Setup/installation instructions (reveals runtime, DB, env vars)
- Architecture notes if present

---

## Source 3: Package / Dependency Files

**Check based on likely runtime:**

```bash
# Node.js / TypeScript
cat package.json 2>/dev/null

# Python
cat pyproject.toml 2>/dev/null
cat requirements.txt 2>/dev/null

# Go
cat go.mod 2>/dev/null

# Rust
cat Cargo.toml 2>/dev/null
```

**From package.json, extract:**
- `dependencies` — identify framework, ORM, auth, payments, etc.
- `devDependencies` — identify testing tools, linters, build tools
- `scripts` — reveal dev, build, test commands (shows project conventions)

**Dependency → technology mapping:**

| Dependency | Implies |
|---|---|
| `next` | Next.js (check version for App Router vs Pages) |
| `react`, `vite` | React SPA |
| `prisma` | Prisma ORM + PostgreSQL or SQLite |
| `drizzle-orm` | Drizzle ORM |
| `next-auth` or `@auth/core` | Auth.js authentication |
| `@clerk/nextjs` | Clerk authentication |
| `stripe` | Stripe payments |
| `socket.io` | WebSocket real-time |
| `@supabase/supabase-js` | Supabase (auth, DB, or real-time) |
| `fastify` | Fastify backend |
| `express` | Express backend |
| `@tanstack/react-query` | TanStack Query (server state) |
| `zustand` | Zustand (client state) |
| `resend` | Resend email |
| `trpc` | tRPC API layer |

---

## Source 4: Folder Structure

**Check:**

```bash
# Top-level structure
ls -la

# src directory if present
ls src/ 2>/dev/null

# One level deeper
find . -maxdepth 3 -type d | grep -v node_modules | grep -v .git | grep -v .next | sort
```

**Infer folder pattern:**

| Signals | Pattern |
|---|---|
| `src/features/` or `src/modules/` subdirectories | Feature-Based Organization (FBO) |
| `src/domain/`, `src/application/`, `src/infrastructure/` | Domain-Driven Design (DDD) |
| `src/components/atoms/`, `molecules/`, `organisms/` | Atomic Design |
| `src/domain/` + `src/features/` | DDD + FBO Hybrid |
| `src/routes/`, `src/controllers/`, `src/services/`, `src/models/` | Layered (API-only) |
| Flat `src/components/`, `src/pages/`, `src/utils/` | Informal / Mixed |

---

## Source 5: Schema Files

**Check for schema definitions:**

```bash
# Prisma
cat prisma/schema.prisma 2>/dev/null

# Drizzle (common locations)
cat src/db/schema.ts 2>/dev/null
cat src/lib/db/schema.ts 2>/dev/null
cat drizzle/schema.ts 2>/dev/null

# SQLAlchemy (Python)
find . -name "models.py" | grep -v node_modules | head -5

# Go structs
find . -name "*.go" | xargs grep -l "type .* struct" 2>/dev/null | head -5

# Migrations directory
ls migrations/ 2>/dev/null || ls db/migrations/ 2>/dev/null
```

**Extract:**
- Entity/model names
- Key fields and types
- Relationships (foreign keys, relations)
- Enums and their values

---

## Source 6: Feature Sampling

Pick one existing feature directory and read its structure to understand conventions.

```bash
# FBO — sample one feature
ls src/features/ 2>/dev/null | head -5
# Then read the first feature's files

# DDD — sample one domain entity
ls src/domain/ 2>/dev/null | head -5

# Layered — sample routes and services
ls src/routes/ 2>/dev/null | head -10
```

**Look for:**
- File naming conventions (camelCase, kebab-case, PascalCase)
- How components/services are structured
- Import patterns (barrel exports, direct imports)
- Test file co-location or separate test directory

---

## Discovery Output Format

After completing discovery, present this summary before proceeding:

```
CODEBASE DISCOVERY
─────────────────────────────────────────
Source:            [CLAUDE.md / README / package.json / inferred]
Project:           [name if found]
Runtime:           [Node.js / Python / Go / etc.]
Framework:         [Next.js / FastAPI / Express / etc.]
Database:          [PostgreSQL / SQLite / MongoDB / etc.]
ORM:               [Prisma / Drizzle / SQLAlchemy / etc.]
Auth:              [Clerk / Auth.js / JWT / Supabase / etc.]
Folder Pattern:    [FBO / DDD / Atomic / Layered / Hybrid / Mixed]
Existing Entities: [list from schema — or "not found"]
Existing Features: [inferred from folder structure]
Conventions:       [naming style, co-location, barrel exports, etc.]
─────────────────────────────────────────
CLAUDE.md present: [yes / no]
```

---

## When Discovery Is Incomplete

If key information can't be found from files, ask the user directly:

> "I couldn't determine [X] from the codebase. Can you tell me:
> - What database and ORM are you using?
> - What's the main framework?
> - [Any other missing key fact]"

Do not proceed with impact analysis until the stack is known — the analysis
depends on it.

---

## CLAUDE.md Encouragement Note

If `CLAUDE.md` is NOT present, append this note to the discovery summary:

> 💡 Tip: A `CLAUDE.md` file in your project root makes future feature planning
> significantly faster. It acts as a persistent source of truth for your stack,
> conventions, and architecture decisions. Consider creating one after this session.
