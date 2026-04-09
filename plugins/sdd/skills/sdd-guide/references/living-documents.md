# Living Documents

**Used by:** `/sdd:refine`, `/sdd:build`, `/sdd:iterate`

This reference defines how the SDD plugin handles updates to project documents after their initial creation.

## Updatable Documents

The following documents can be modified after initial creation:

- `docs/prd.md`
- `docs/spec.md`
- `CLAUDE.md`
- `AGENTS.md`

All other documents (scope, process notes, sprint files) are append-only or immutable once created.

## Default Stance

**Resist changes to scope, PRD, and spec.** These documents represent deliberate decisions made during planning. Changes should not happen casually.

When new ideas, features, or requirements surface during build or iteration:
- Default action: Add them to the **backlog** or **unvetted section** of the PRD.
- Do not modify the spec or approved PRD stories without going through `/sdd:refine`.
- Push back on scope changes. Make the user justify why this can't wait for the next sprint or a future project.

## Update Ordering for `/sdd:refine`

When `/sdd:refine` processes changes, update documents in this order:

1. **`docs/prd.md`** — Update stories, move items from unvetted to approved, or modify existing stories. Show the user exactly what changed and get confirmation before proceeding.
2. **`docs/spec.md`** — Update technical specifications to match the PRD changes. Show the user the changes and get confirmation.
3. **`AGENTS.md`** — Update agent instructions if the changes affect how work is structured or delegated. Show and confirm.
4. **`CLAUDE.md`** — Update project conventions if the changes affect coding standards, patterns, or project-wide rules. Show and confirm.

Each update is shown to the user and confirmed before moving to the next document. Do not batch updates or apply them silently.

## Re-Scoping Trigger

When accumulated changes — across multiple `/sdd:refine` cycles or through organic discovery during build — fundamentally shift the project's direction, scope, or complexity:

- Recognize the shift explicitly. Name what has changed and why it constitutes a fundamental shift rather than incremental refinement.
- Suggest re-scoping: going back to `/sdd:scope` to re-evaluate the project boundaries.
- Frame this as a deliberate, weighty decision. Re-scoping is not routine maintenance. It means the project has evolved beyond what the original scope anticipated, and the team should consciously decide whether to embrace the new direction or cut back to the original vision.

## PRD Health Monitoring

Monitor the PRD for signs of scope creep or unwieldy complexity:

- **10+ epics:** Recommend phase-splitting. The project is likely too large for a single development cycle. Suggest breaking it into distinct phases with clear boundaries, each with its own sprint cycle.
- **5+ unvetted items:** Flag scope creep. The rate of new ideas is outpacing the rate of deliberate planning. The user should either run `/sdd:refine` to process the backlog or consciously defer items to a future project.

Surface these warnings during any command that loads the PRD, not just during `/sdd:refine`.
