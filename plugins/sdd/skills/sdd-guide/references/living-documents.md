# Living Documents

**Used by:** `/sdd:refine`, `/sdd:build`, `/sdd:polish`

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

When `/sdd:refine` processes changes, update documents in this order. **Each step is shown to the user and confirmed before proceeding to the next.** Do not batch updates or apply them silently.

1. **`docs/prd.md`** — Update stories, move items from unvetted to approved, or modify existing acceptance criteria. AC editing semantics:
   - **Pure reword (same intent):** the AC's checked/unchecked state survives; confirm with the user that the intent did not shift.
   - **Semantic change:** un-check the AC. Surface any work already shipped against the old wording as an iteration candidate — log it to the current open sprint's process notes if one exists, otherwise flag it to the user.
   - **Split or merge:** new ACs get fresh 4-char IDs and start unchecked; old IDs are removed (not recycled). See `references/sprint-tags.md` for AC ID rules.
   Show the user exactly what changed and get confirmation before proceeding.
2. **`docs/spec.md`** — Update technical specifications to match the PRD changes. Show the user the changes and get confirmation.
3. **Open sprint files (if affected)** — when the PRD edit touched an AC referenced in a `[PRD: ...]` tag inside an **open** sprint file, offer to update those refs in lockstep. An open sprint file is one without a `[close-sprint-manifest]` block (see `references/sprint-tags.md` for the parser). **Closed sprint files are never touched.** Show and confirm.
4. **`AGENTS.md`** — Update agent instructions if the changes affect how work is structured or delegated. Show and confirm.
5. **`CLAUDE.md`** — Update project conventions if the changes affect coding standards, patterns, or project-wide rules. Show and confirm.

## Exclusions from the Living-Documents Chain

The following documents are **never updated by `/sdd:refine`**:

- **`docs/v2-verification.md`** — manual audit trail, not updated by `/refine`.
- **`docs/backlog.md`** — append-only deferral log written during planning, never edited by `/refine`.

## Re-Scoping Trigger

When accumulated changes — across multiple `/sdd:refine` cycles or through organic discovery during build — fundamentally shift the project's direction, scope, or complexity:

- Recognize the shift explicitly. Name what has changed and why it constitutes a fundamental shift rather than incremental refinement.
- Suggest re-scoping: going back to `/sdd:scope` to re-evaluate the project boundaries.
- Frame this as a deliberate, weighty decision. Re-scoping is not routine maintenance. It means the project has evolved beyond what the original scope anticipated, and the team should consciously decide whether to embrace the new direction or cut back to the original vision.

## PRD Health Monitoring

Monitor the PRD for signs of scope creep or unwieldy complexity:

- **10+ epics:** `/sdd:prd` emits a phase-split recommendation. **This does not block PRD generation** — the user may elect to keep one phase. The recommendation suggests breaking the project into distinct phases with clear boundaries, each with its own sprint cycle.
- **5+ unvetted items:** Flag scope creep. The rate of new ideas is outpacing the rate of deliberate planning. The user should either run `/sdd:refine` to process the backlog or consciously defer items to a future project.

Surface these warnings during any command that loads the PRD, not just during `/sdd:refine`.
