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

<!-- One row per tool/library, each with a docs URL. /build agents reference these URLs. Add/remove rows as needed. -->

## Runtime & Deployment

<!-- How the app runs in production: hosting target, env/secrets, build & deploy pipeline, IaC or manual setup, scaling if applicable. -->

## Architecture Overview

<!-- High-level system architecture, followed by a diagram. -->

```
<!-- ASCII architecture diagram: major components and relationships -->
```

### Data Flow

<!-- How data moves through the system for the most important operations. -->

```
<!-- ASCII data flow diagram, e.g. User Action → Frontend → API → Database → Response -->
```

<!-- For each critical flow: trigger, components involved, data transformed, where persisted. -->

<!-- == Component Sections == -->
<!-- One ## per architectural component (e.g. ## Frontend, ## API, ## Database). These headings become addressable references for /sprint item spec citations. Use ### subsections for specific pieces (e.g. ## API > ### GET /api/items). -->
<!-- Each component section: open with "Implements `prd.md > [Epic Name]`"; describe responsibilities, interfaces, contracts; include data shapes, API signatures, or schemas where applicable. -->

## File Structure

```
<!-- Annotated file tree of every file/dir, with brief descriptions on key files. Example:
src/
  app/
    layout.tsx          # Root layout with providers
    page.tsx            # Landing page
  components/
    SearchBar.tsx       # Main search input with autocomplete
  lib/
    db.ts               # Database connection singleton
-->
```

## Testing Strategy

### Unit Tests
<!-- What is tested at unit level, framework, coverage expectations -->

### Integration Tests
<!-- API route, database, and component integration tests -->

### End-to-End Tests
<!-- Critical user flows, framework (e.g., Playwright, Cypress) -->

### Coverage Expectations
<!-- Target coverage; what must vs. should be covered -->

## Key Technical Decisions

| Decision | Why | Tradeoff |
|----------|-----|----------|
| <!-- e.g., SQLite over PostgreSQL --> | <!-- e.g., Single-file deploy, no server --> | <!-- e.g., No concurrent write scaling --> |

## Open Issues

<!-- Unresolved technical questions surfaced during spec writing; feed back into docs/open-concerns.md. Bullet list: brief description + which phase resolves it. These are in-spec questions for the author, NOT deferred-to-backlog items (the conditional backlog pointer below is the deferred-items hook). -->

## Out of Scope

<!-- Conditional pointer block. Emit only when docs/backlog.md exists with ≥1 entry (see references/backlog.md > Parser note); body is exactly the line below, verbatim. Otherwise omit heading and body. Don't inline-list deferred items. -->

Deferred items: see `docs/backlog.md`.
