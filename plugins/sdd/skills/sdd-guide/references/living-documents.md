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

## project-state.json Schema and Reset

`docs/project-state.json` carries cross-cycle state. This is its canonical field set:

| Field | Type | Owner / notes |
|---|---|---|
| `version` | integer | Schema version (`2`). Not cycle state. |
| `cycleNumber` | integer (top-level) | The current working cycle. **Sole owner: `/sdd:archive`.** No other command reads or writes it. **Absent → assume `1`.** Single writer ⇒ no double-increment, no stale-number race. |
| `smallProject` | boolean / null | Right-sizing judgment for the current cycle. Stays top-level: machine-owned, AI-derived — not a user setting. |
| `lastCommand` | string | Most recent command to run (see `## State Tracking` in `SKILL.md`). |
| `settings` | object | **Persisted user choices.** Seeded `{ "prototypeFidelity": null }`; `prototypeFidelity` is `"hifi"`, `"lofi"`, or `null` (not yet asked). Written by the command that asks the choice; carried across cycles. |
| `commandExplanationsShown` | object | Per-command booleans gating the once-per-project explanation blurb (see `## Command Explanations` in `SKILL.md`). Keys are the v6 command set: `discovery`, `refine`, `validate`, `prototype`, `build`, `retro`, `checkpoint`, `resolve-pr`, `onboard`, `pause`, `unpause`, `feedback`, `archive`. |
| `notes` | string | Free-text seed line naming the cycle. |

Schema v2 (v6) **dropped** the v1 fields `currentSprint` and `buildMode` — both were sprint-machinery state; v6 has no sprint documents and build is always autonomous. Maturity of `docs/plan.md` lives only in that document's in-file version line, never duplicated into state.

### cycleNumber resolution and bump

`/sdd:archive` resolves the target archive directory from `cycleNumber`: read `cycleNumber` (absent → assume `1`); start at `docs/archive/v{cycleNumber}/`; if that directory already exists, advance to the next unused `v{N}`. The resolved `N` is authoritative for the directory name, the branch name, and the `cycleNumber` bump. After archiving into `v{N}/`, the reset sets live `cycleNumber = N + 1`.

### Reset map

`/sdd:archive` applies the following to the **live** `project-state.json` **after** the byte-identical snapshot is written into the archive directory:

| Field | Reset behavior | Rationale |
|---|---|---|
| `version` | Carry (`2`) | Schema version, not cycle state. |
| `cycleNumber` | Bump to `N+1` | Resolved N + 1; sole owner. |
| `smallProject` | Reset to `null` | New cycle re-judges from scratch. |
| `lastCommand` | Reset to `"archive"` | Truthful; the next cycle's first command overwrites on startup. |
| `settings` | **Carry** | Sticky user choices (e.g. `prototypeFidelity`) survive cycles. |
| `commandExplanationsShown` | Rekey to the v6 set + flip to `false` (except `archive`) | See normalization below. |
| `notes` | Rewrite to a fresh seed line | Names the new cycle and where the prior cycle archived. |

The reset must leave `project-state.json` a valid, parseable JSON file. The dropped v1 fields (`currentSprint`, `buildMode`) are removed outright if a pre-v2 file still carries them.

### commandExplanationsShown normalization

The reset normalizes the object to the current command set:

- **Drop stale legacy keys** (any key not in the v6 command set — e.g. `scope`, `prd`, `spec`, `plan`, `polish`, `sprint`, `iterate`, `reflect`).
- **Ensure every v6 key exists:** `discovery`, `refine`, `validate`, `prototype`, `build`, `retro`, `checkpoint`, `resolve-pr`, `onboard`, `pause`, `unpause`, `feedback`, `archive`.
- Flip every key to `false` **except `archive`**, whose value is preserved (the archive blurb is once-per-project, not once-per-cycle).

### Never-swept carry-forward set

The following are permanent cross-cycle stores. `/sdd:archive` snapshots the three files but never moves or empties any of these; `docs/learnings/` is never even snapshotted — it simply persists:

- `docs/backlog.md`
- `docs/sdd-feedback.md`
- `docs/project-state.json`
- `docs/learnings/`

### Snapshot-first / startup-overwrite exemption

`/sdd:archive` is **exempt** from the `## State Tracking` rule that every command overwrites `lastCommand` on startup. It captures the existing `lastCommand` first (for the snapshot and `INDEX.md`), snapshots `project-state.json` untouched, and sets `lastCommand: "archive"` only later, in the reset. This preserves the cycle's true final command in the snapshot.

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
   - **Split or merge:** new ACs get fresh 4-char IDs and start unchecked; old IDs are removed (not recycled).
   Show the user exactly what changed and get confirmation before proceeding.
2. **`docs/spec.md`** — Update technical specifications to match the PRD changes. Show the user the changes and get confirmation.
3. **Open sprint files (if affected)** — when the PRD edit touched an AC referenced in a `[PRD: ...]` tag inside an **open** sprint file, offer to update those refs in lockstep. An open sprint file is one without a `[close-sprint-manifest]` block. **Closed sprint files are never touched.** Show and confirm.
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
