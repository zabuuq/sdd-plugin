# [Project Name] — Technical Specification

## Stack

| Layer | Choice | Reference Docs |
|-------|--------|----------------|
| Platform | <!-- e.g., Web, iOS, Desktop --> | <!-- URL --> |
| Language | <!-- e.g., TypeScript, Python --> | <!-- URL --> |
| Runtime | <!-- e.g., Node.js 20, Python 3.12 --> | <!-- URL --> |
| Framework | <!-- e.g., Next.js 14, FastAPI --> | <!-- URL --> |
| Database | <!-- e.g., PostgreSQL 16, SQLite --> | <!-- URL --> |
| ORM / Query | <!-- e.g., Prisma, SQLAlchemy --> | <!-- URL --> |
| Auth | <!-- e.g., NextAuth, Supabase Auth --> | <!-- URL --> |
| Styling | <!-- e.g., Tailwind CSS, CSS Modules --> | <!-- URL --> |
| Testing | <!-- e.g., Vitest, pytest --> | <!-- URL --> |
| CI/CD | <!-- e.g., GitHub Actions --> | <!-- URL --> |

<!-- Add or remove rows as needed. Every tool and library in the stack gets a row with a link to its current docs. These URLs are what /build agents will reference. -->

## Runtime & Deployment

<!-- How the application runs in production. Cover: -->
<!-- - Hosting target (e.g., Vercel, AWS, self-hosted) -->
<!-- - Environment requirements (env vars, secrets management) -->
<!-- - Build and deploy pipeline -->
<!-- - Infrastructure-as-code or manual setup -->
<!-- - Scaling considerations if applicable -->

## Architecture Overview

<!-- High-level description of the system architecture. Follow with a diagram. -->

```
<!-- ASCII architecture diagram showing major components and their relationships -->
```

### Data Flow

<!-- How data moves through the system for the most important operations. -->
<!-- Include at least one diagram for the primary data path. -->

```
<!-- ASCII data flow diagram -->
<!-- Example: User Action → Frontend → API → Database → Response path -->
```

<!-- For each critical data flow, describe: -->
<!-- - What triggers it -->
<!-- - What components are involved -->
<!-- - What data is transformed and how -->
<!-- - Where data is persisted -->

<!-- == Component Sections == -->
<!-- One top-level heading (##) per architectural component. -->
<!-- These headings become addressable references for /sprint item spec citations. -->
<!-- Example components: ## Frontend, ## API, ## Database, ## Authentication, ## Background Jobs -->
<!-- Each component gets granular subsections (###) for specific pieces. -->
<!-- Example: ## Frontend > ### Search Component, ## API > ### GET /api/items, ## Data Model > ### Users Table -->

<!-- For each component section: -->
<!-- - Open with "Implements `prd.md > [Epic Name]`" cross-reference -->
<!-- - Describe responsibilities, interfaces, and contracts -->
<!-- - Include data shapes, API signatures, or schema definitions where applicable -->

## File Structure

```
<!-- Annotated file tree showing every file and directory the project will contain -->
<!-- Annotate key files with a brief description -->
<!-- Example: -->
<!-- src/ -->
<!--   app/ -->
<!--     layout.tsx          # Root layout with providers -->
<!--     page.tsx            # Landing page -->
<!--   components/ -->
<!--     SearchBar.tsx       # Main search input with autocomplete -->
<!--   lib/ -->
<!--     db.ts               # Database connection singleton -->
```

## Testing Strategy

### Unit Tests
<!-- What is tested at the unit level, which framework, coverage expectations -->

### Integration Tests
<!-- API route tests, database integration, component integration -->

### End-to-End Tests
<!-- Critical user flows tested e2e, framework choice (e.g., Playwright, Cypress) -->

### Coverage Expectations
<!-- Target coverage percentage, what must vs. should be covered -->

## Key Technical Decisions

<!-- For each significant technical choice, document the decision, rationale, and tradeoff. -->

| Decision | Why | Tradeoff |
|----------|-----|----------|
| <!-- e.g., SQLite over PostgreSQL --> | <!-- e.g., Single-file deployment, no server needed --> | <!-- e.g., No concurrent write scaling --> |

## Open Issues

<!-- Unresolved technical questions that surfaced during spec writing. -->
<!-- These feed back into docs/open-concerns.md. -->
<!-- Format: bullet list with brief description and which phase should resolve it. -->
<!-- Open Issues is NOT a deferred-items section — entries here are in-spec unresolved questions for the spec author's attention, not items punted to the backlog. The conditional backlog pointer below is the deferred-items hook for this template. -->

## Out of Scope

<!-- Conditional pointer block. Emitted only when docs/backlog.md exists and contains at least one entry (per skills/sdd-guide/references/backlog.md > Parser note). When emitted, the section body is exactly the single line below, verbatim. When the backlog is absent or empty, omit the entire section heading and body — do not leave an empty section behind. Do not inline-list deferred items here; the backlog is the single source of truth. -->

Deferred items: see `docs/backlog.md`.
