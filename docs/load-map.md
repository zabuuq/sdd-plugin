<!-- docs/load-map.md — v3 per-command reference-load map. Driver artifact for the input-footprint trims (Item 4) and home of the rule-inventory audit trail (Items 4 and 8). -->

# Load Map

Per-command map of which `sdd-guide` reference files each `/sdd:*` command loads at
startup, with an over-inclusion flag on each. Built second in Sprint 1 (after the
technique inventory), from a sweep of every command `SKILL.md` startup-load list plus
the `Per-Command Loading Rules` table in
`plugins/sdd/skills/sdd-guide/references/context-loading.md`.

## What this is for

- **Driver for Item 4 (reference-stack trim).** Every reference flagged **over-included**
  is a trim target — Item 4 removes that load from the command's `SKILL.md` startup list.
- **Resolution of the per-command over-inclusion open concern** (AC `lpmb`). The flag on
  each reference *is* the resolution: each load is judged load-justified or over-included
  against the command's documented behavior.
- **Worksheet + audit-trail home.** The over-inclusion summary below is Item 4's worklist;
  the `## Rule-inventory audit trail` section (stubbed) is where Items 4 and 8 record that
  no behavioral rule was dropped by a trim.

## Method and scope

- **Ground truth is the command `SKILL.md`.** Each command's `## Loading` /
  `## Startup Sequence` / top-of-file `Read ...` instructions are authoritative for what is
  loaded at startup. The `context-loading.md` table is cross-checked but does not override a
  `SKILL.md`; discrepancies are recorded below.
- **`sdd-guide/SKILL.md` (the core) is the always-loaded baseline.** Every one of the 13
  commands loads it first. It is *not* an over-includable reference and is not flagged or
  counted in this map.
- **Scope of "reference" = the 9 files in `sdd-guide/references/`:** `open-concerns`,
  `backlog`, `context-loading`, `context-management`, `deepening-rounds`,
  `living-documents`, `pause-resume`, `right-sizing`, `sprint-tags`. Templates, project
  docs (`docs/*.md`), `docs/project-state.json`, the user profile, and `CLAUDE.md`/`AGENTS.md`
  are out of scope here — this map is only about the reference stack.
- **Startup-load vs. read-on-trigger.** Several references are *not* named in a command's
  startup list but are pointed at inline, deep in a behavior step, to be consulted only when
  a specific trigger fires (e.g. a deferral happens, a tag must be parsed). Those are noted
  as **read-on-trigger (not a startup load)** and are explicitly *not* over-inclusion
  targets — there is no startup load to trim. Item 4 acts only on startup loads.

## Reference key

`oc` open-concerns · `bk` backlog · `cl` context-loading · `cm` context-management ·
`dr` deepening-rounds · `ld` living-documents · `pr` pause-resume · `rs` right-sizing ·
`st` sprint-tags

## At-a-glance matrix (startup loads only)

`L` = loaded at startup & load-justified · `O` = loaded at startup but over-included ·
`·` = not loaded at startup (may still be read-on-trigger; see per-command notes).
The always-loaded `sdd-guide` core is omitted (baseline).

| Command | oc | bk | cl | cm | dr | ld | pr | rs | st |
|---|---|---|---|---|---|---|---|---|---|
| `/sdd:onboard`   | · | · | · | · | · | · | · | · | · |
| `/sdd:discovery` | * | L | · | L | L | · | · | * | · |
| `/sdd:scope`     | * | L | · | L | L | · | · | * | · |
| `/sdd:prd`       | * | L | · | L | L | · | · | * | · |
| `/sdd:spec`      | L | L | · | L | L | · | · | * | · |
| `/sdd:plan`      | L | L | **O** | L | L | · | · | * | · |
| `/sdd:build`     | * | · | L | · | · | L | · | · | * |
| `/sdd:polish`    | * | · | · | L | · | L | · | * | * |
| `/sdd:refine`    | * | · | · | L | · | L | · | * | * |
| `/sdd:retro`     | L | · | · | · | · | · | · | · | · |
| `/sdd:pause`     | · | · | · | · | · | · | L | · | · |
| `/sdd:unpause`   | · | · | · | · | · | · | L | · | · |
| `/sdd:feedback`  | · | · | · | · | · | · | · | · | · |

`*` = read-on-trigger / via core protocol, not an explicit startup reference load — see the
per-command note. These are not over-inclusion targets.

## Per-command detail

### `/sdd:onboard`
- **Startup loads:** none. `## Loading` says "Load: sdd-guide core only. Skip: everything
  else." No reference files.
- No reference is over-included (none loaded). Correct — onboard touches no project context.

### `/sdd:discovery`
- **Startup loads (explicit, ordered list):** `deepening-rounds`, `context-management`,
  `backlog`.
- `deepening-rounds` — **load-justified.** Phase 2 deepening rounds protocol is the spine of
  the interview.
- `context-management` — **load-justified.** Emits the three-tier between-rounds recommendation
  after each round.
- `backlog` — **load-justified.** The `## Deferrals` step routes mid-interview defers through
  the backlog write-trigger and entry schema.
- `right-sizing` — **read-on-trigger, not a startup load.** Discovery reads the `smallProject`
  *value* and points at `right-sizing.md` sections in prose, but its `## Loading` list does not
  load the file, and discovery is explicitly excluded from the authoring lifecycle. Not a trim
  target.
- `open-concerns` — handled via the sdd-guide core protocol; not in discovery's startup list.

### `/sdd:scope`
- **Startup loads:** `deepening-rounds`, `context-management`, `backlog`.
- `deepening-rounds` — **load-justified.** Phase 2 deepening rounds.
- `context-management` — **load-justified.** Between-rounds recommendation.
- `backlog` — **load-justified.** Project-size-split deferrals and ad-hoc deferrals both route
  through the backlog write-trigger.
- `right-sizing` — **read-on-trigger.** Scope is the authoritative writer of `smallProject` and
  references the file heavily, but the startup list does not name it as a loaded reference. (Borderline:
  scope arguably uses right-sizing enough at startup that an explicit load could be justified; it
  is currently consulted by section pointer rather than full-file load, so it is not counted as a
  startup over-inclusion either way.)

### `/sdd:prd`
- **Startup loads:** `deepening-rounds`, `context-management`, `backlog`.
- `deepening-rounds` — **load-justified.** Phase 2 deepening rounds.
- `context-management` — **load-justified.** Between-rounds recommendation.
- `backlog` — **load-justified.** `### Deferrals` routes out-of-scope items through the backlog
  write-trigger.
- `living-documents` — **read-on-trigger, not a startup load.** The Phase-Split Recommendation
  sources its epic threshold from `living-documents.md > ## PRD Health Monitoring` by pointer; the
  file is not in prd's startup load list. Not a trim target.
- `right-sizing` — **read-on-trigger.** Re-evaluates `smallProject` via section pointers; not a
  startup load.

### `/sdd:spec`
- **Startup loads:** `deepening-rounds`, `context-management`, `backlog`, plus `open-concerns`
  is loaded indirectly via `docs/open-concerns.md` (the project file, not the reference) — note
  spec's `## Loading` lists `backlog` twice (once in the top `Read ...` lines, once in the
  `## Loading` block); that is a self-duplication, not a second reference.
- `deepening-rounds` — **load-justified.** Phase 2 deepening rounds.
- `context-management` — **load-justified.** Between-rounds recommendation.
- `backlog` — **load-justified.** Step 11a deferrals route through the backlog write-trigger.
- `right-sizing` — **read-on-trigger.** Re-evaluates `smallProject` via section pointers; not a
  startup load.

### `/sdd:plan`
- **Startup loads (explicit, ordered list):** `deepening-rounds`, `context-management`,
  `context-loading`, `backlog`.
- `deepening-rounds` — **load-justified.** Step 6 optional deepening rounds.
- `context-management` — **load-justified.** Between-rounds recommendation in Step 6.
- `context-loading` — **OVER-INCLUDED.** Plan loads the full `context-loading.md` at startup
  (item 4 of its `## Loading` list), but its only use of it is a single pointer in
  `## Prior-Sprint Process Notes` ("see `references/context-loading.md` for the per-command
  loading rule") — a one-line cross-reference, not exercise of the file's content. Plan does no
  surgical per-item loading (that is `/sdd:build`'s job); the full loading-rules table and the
  build re-entry order are not used by plan's steps. **This is the headline trim target for Item 4.**
- `backlog` — **load-justified.** Step 4a deferrals route through the backlog write-trigger.
- `right-sizing` — **read-on-trigger.** Re-evaluates `smallProject` via section pointers; not a
  startup load.

### `/sdd:build`
- **Startup loads (explicit, ordered list):** `context-loading`, `living-documents`.
- `context-loading` — **load-justified.** Surgical per-item spec/PRD loading and the `/clear`
  re-entry order are the heart of build; this is the command the reference was written for.
- `living-documents` — **load-justified.** Steps 8 / 3e update `CLAUDE.md` / `AGENTS.md` per the
  living-documents protocol; the default-stance ("resist scope/PRD/spec edits") governs build.
- `sprint-tags` — **read-on-trigger, not a startup load.** Wrap-up steps (W1, W6, W9, W10) parse
  `[iteration-candidate]`, `[PRD: ...]`, `[FB: ...]`, and the `[close-sprint-manifest]` block by
  pointer to `sprint-tags.md`. Heavily used, but consulted at the wrap-up trigger, not loaded at
  startup. Not a startup-list trim target. (Borderline: because wrap-up always runs when a sprint
  closes, an explicit startup load could be defended; the SKILL.md currently keeps it as a per-step
  pointer, which is the more economical posture and consistent with build's surgical-loading ethos.)
- `open-concerns` — via the core protocol; not a build-specific reference load.

### `/sdd:polish`
- **Startup loads (explicit):** `living-documents`, `context-management`.
- `living-documents` — **load-justified.** Step 5 follows the default-stance (route changes to
  backlog/unvetted, resist direct PRD/spec edits) during the iteration interview.
- `context-management` — **load-justified, borderline.** Polish runs no formal deepening rounds;
  the reference is used only *discretionarily* at the Step 5 / Step 11 transitions. The SKILL.md
  explicitly frames this as optional ("discretionary for polish, not round-by-round mandatory").
  The content is genuinely exercised when a transition warrants a recommendation, so it is
  load-justified rather than over-included — but it is the weakest of polish's loads and worth a
  second look in Item 4 if input-footprint pressure is high.
- `sprint-tags` — **read-on-trigger.** Step 7 (re-open detection) and the Re-Open Flow parse the
  `[close-sprint-manifest]` block and `[sprint-reopened]` marker by pointer; not a startup load.
- `right-sizing` — **read-on-trigger.** Permissive `smallProject` re-write via section pointer;
  not a startup load.

### `/sdd:refine`
- **Startup loads (explicit):** `living-documents`, `context-management`.
- `living-documents` — **load-justified.** Refine is the gatekeeper for deliberate document
  changes; Step 6 follows the reference's fixed update ordering (PRD → spec → AGENTS → CLAUDE).
  Strongest possible justification.
- `context-management` — **load-justified, borderline.** Like polish, refine runs no formal
  deepening rounds; the reference is used discretionarily at per-item transitions. SKILL.md frames
  it as optional. Load-justified but the weaker of refine's two loads; flag for a second look in
  Item 4.
- `sprint-tags` — **read-on-trigger.** Living-documents AC-editing rules and the open/closed
  sprint-file parser are reached by pointer; not a startup load.
- `right-sizing` — **read-on-trigger.** Permissive `smallProject` re-write via section pointer.

### `/sdd:retro`
- **Startup loads:** none of the 9 references explicitly. (Its `## Loading` list is project
  docs + state + profile only.)
- `open-concerns` — handled via the core protocol; retro's Step 1 / Step 8 exercise it, but it is
  not loaded as a retro-specific reference. No over-inclusion.
- No reference file is over-included.

### `/sdd:pause`
- **Startup loads (explicit):** `pause-resume`.
- `pause-resume` — **load-justified.** Pause's entire job is writing the resume file to that
  reference's schema, section order, and quality bar.

### `/sdd:unpause`
- **Startup loads (explicit):** `pause-resume`.
- `pause-resume` — **load-justified.** Unpause reads the resume file per the same schema and
  resumes at the `Pending decision` section; cleanup ownership is defined there.

### `/sdd:feedback`
- **Startup loads:** none. `## Loading` explicitly skips everything beyond core, the profile,
  and project-state ("Do not load … deepening rounds, living documents, or any other context.
  This command is designed to be instant.").
- No reference is over-included (none loaded). Correct — feedback is the instant-return command.

## Over-included (command, reference) pairs — Item 4 trim targets

**One firm over-inclusion found:**

1. **(`/sdd:plan`, `context-loading`)** — Plan startup-loads the full `context-loading.md` but
   only cross-references one line of it (the per-command loading-rule pointer in
   `## Prior-Sprint Process Notes`). Plan performs no surgical/per-item loading and does not use
   the build re-entry order. Item 4 should remove `context-loading` from plan's `## Loading`
   startup list and keep the inline pointer where it is actually used.

**Borderline / watch-list (load-justified today, but the weakest loads — revisit under footprint pressure, do not auto-trim):**

- **(`/sdd:polish`, `context-management`)** and **(`/sdd:refine`, `context-management`)** —
  both use the reference only discretionarily (no formal deepening rounds). Genuinely exercised
  at transitions, so not flagged as over-included, but they are the thinnest justifications in
  the map.

**Non-targets worth recording (so Item 4 does not mistake them for trims):** the
read-on-trigger references — `right-sizing` (discovery/scope/prd/spec/plan/polish/refine),
`sprint-tags` (build/polish/refine), `living-documents` (prd's health-monitoring pointer),
and `open-concerns` (all commands via core) — are *not* startup loads and have no startup
load to remove. Trimming them would mean removing an inline pointer at the point of use, which
would drop behavior, not footprint. Leave them.

## Per-file review outcomes (Item 4, `rfsa`)

Every one of the 9 reference files in `plugins/sdd/skills/sdd-guide/references/` has a recorded
review outcome below. Posture for this trim is conservative: these files are the canonical homes
for named behavioral rules, and other files (and command `SKILL.md`s) point at their **section
names** by name. Renaming or removing a section breaks a cross-pointer, which would drop behavior.
Where a file is already terse and every section is load-bearing, the correct outcome is
**left-as-is** with a reason — that is expected and is the majority outcome here. No consolidation
was warranted (see note after the table).

| # | Reference file | Outcome | Reason |
|---|---|---|---|
| 1 | `open-concerns.md` | left-as-is | Every section (File Location, Purpose, Entry Format schema, the 4-step Command Behavior protocol, File Lifecycle) is a named/load-bearing rule. Already terse; nothing to cut without risking the protocol or schema. |
| 2 | `backlog.md` | left-as-is | All sections are named anchors referenced by command SKILL.md by name (`## Write trigger`, `## Entry format`, `## Parser note`, `## Exclusions`-via-living-documents). The "never edited by `/sdd:refine`" rule, append-only rule, bootstrap marker, and `^### ` parser regex are all behavioral. No safe cut. |
| 3 | `context-loading.md` | trimmed | Removed one redundant intro sentence that restated the header's "keeps context efficient" intent (folded into a single sentence). No named rule touched: the Per-Command Loading Rules table, the `/sdd:build` Re-Entry Load Order (5 steps), the close-sprint-manifest re-open augmentation, and the 200-line Threshold Rule + CLAUDE.md exemption are all preserved verbatim. |
| 4 | `context-management.md` | left-as-is | Three named tiers (continue / `/compact` / `/clear`), their trigger lists, the Recognition signals, Tier-mapping table, Failure-mode mitigation rules, and the `/clear`-branch pause-resume coupling are all behavioral and cross-referenced by name. Examples are illustrative but short; trimming them risks the definite-recommendation discipline they demonstrate. Already tight. |
| 5 | `deepening-rounds.md` | left-as-is | Phase 1 / Phase 2 rules, the 5/10 cap policy, right-sized round size, and the End-of-round recommendation (definite framing) are all named rules pointed at by name from right-sizing.md and command SKILL.md. No redundant scaffolding to remove. |
| 6 | `living-documents.md` | left-as-is | Updatable Documents list, Default Stance, the numbered Update Ordering for `/sdd:refine` (with AC-editing semantics), Exclusions from the Living-Documents Chain, Re-Scoping Trigger, and PRD Health Monitoring are each referenced by name (e.g. backlog.md and prd point at `## Exclusions` / `## PRD Health Monitoring`). Every line load-bearing. |
| 7 | `pause-resume.md` | left-as-is | Location convention, the 7 Required sections (exact order), the Per-section quality bar table, the 5 distill-don't-dump rules, Cleanup ownership rule, and the three-tier `/clear` consolidation coupling are all behavioral. Schema is canonical for two callers; trimming risks the schema. |
| 8 | `right-sizing.md` | trimmed | Removed one motivational scaffolding sentence ("TPS-report drill") and folded its load-bearing clause ("Right-sizing is a quality-of-experience lever, not an excuse to skip rigor") into the surviving intro sentence verbatim. No named rule touched: `## The smallProject field`, Authoring lifecycle, Signals, Skippable Phase 1 beats per command (incl. the per-command sub-headings plan/scope/prd/spec point at by name), `## The thinness signal and sub-5-question rounds`, and `## Interaction with the question cap` all preserved. |
| 9 | `sprint-tags.md` | left-as-is | Pure parser/regex reference. Every section is a named tag grammar (`[PRD: ...]`, `[unmapped]`, `[FB: ...]`, `[iteration-candidate]`, `[close-sprint-manifest]`, `[sprint-reopened]`, PRD AC IDs, `## Validation summary`) cited by name from plan/build/polish/refine. Regexes are load-bearing verbatim; nothing is safely removable. |

**Why no consolidation.** The net-trim-per-command open concern test: a merge is only net-positive
if it never raises load for a command that currently loads just one of the two merged files. The
matrix above shows the references partition cleanly by phase — interview commands load
`deepening-rounds`+`context-management`+`backlog`; build loads `context-loading`+`living-documents`;
pause/unpause load `pause-resume` alone. Any merge (e.g. folding `context-management` into
`deepening-rounds`) would raise load for a command that loads only one of the pair (none here load
only one of that pair, but e.g. merging `context-loading` into `living-documents` would force build's
already-justified pair into a single larger file with no per-command saving, and would force `plan`
— which after this trim loads neither — to gain nothing). Consolidation yields no per-command net
trim, so the conservative trim-only posture is correct.

## Rule-inventory audit trail

This section is the audit trail proving that no behavioral rule was dropped when a load was trimmed.
Item 4 records the before/after for the one startup-load removal and the two prose trims. Item 8
independently re-verifies.

### Trim 1 — `(/sdd:plan, context-loading)` startup-load removal (the `rfsb` deliverable)

**Action taken.** Removed `skills/sdd-guide/references/context-loading.md` from `/sdd:plan`'s
`## Loading` startup list (it was item 4 of that ordered list; the list is renumbered 1–9). The
inline pointer in `## Prior-Sprint Process Notes` — "See `references/context-loading.md` for the
per-command loading rule." — is **kept**, unchanged.

**Before — what plan relied on from `context-loading.md`:**

| Named rule in `context-loading.md` | Was plan using it? |
|---|---|
| Per-Command Loading Rules table (the `/sdd:plan` row) | **Via the inline pointer only.** Plan's `## Prior-Sprint Process Notes` step cross-references "the per-command loading rule" by pointer; it does not exercise the full table. The plan row's content (load project-state, most recent process-notes, prd incomplete+unvetted, skip completed/full-spec) is independently restated in plan's own `## Loading` list and Step 1 / Step 8. |
| `/sdd:build` Re-Entry Load Order (5 steps) | **Not used.** This is build's `/clear` re-entry sequence. Plan does no surgical per-item loading and no build re-entry. |
| Re-opening a sprint (close-sprint-manifest lookup) | **Not used.** Build/polish concern, not plan. |
| Threshold Rule (200-line) + CLAUDE.md exemption | **Not used by plan specifically.** A global loading convention applied wherever large files are read; not a plan-specific behavior and not lost (file still exists, still loaded by build, still pointed at). |

**After — confirmation nothing dropped:**

- The **only** thing plan needed from `context-loading.md` was the one-line cross-reference in
  `## Prior-Sprint Process Notes`. That **inline pointer survives verbatim** (plan/SKILL.md,
  `## Prior-Sprint Process Notes`). A reader following it still reaches the file and the
  per-command loading rule.
- No other `context-loading.md` rule was in use by any plan step, so removing the startup *load*
  (the full-file read) drops **footprint only**, not behavior.
- `context-loading.md` itself is unchanged in substance (one redundant intro sentence trimmed; all
  rules intact) and remains loaded by `/sdd:build`, for which it is load-justified.

**Result:** `rfsb` satisfied — one over-included reference removed from a command that did not need
it, verifiable in `plan/SKILL.md`'s `## Loading` list (now 9 items, no `context-loading`), with the
inline pointer retained.

### Trim 2 — `context-loading.md` prose trim

**Before:** intro had two sentences; the second ("Following these rules keeps context usage
efficient and prevents commands from loading irrelevant material.") restated the header intent.

**Named rules before:** Per-Command Loading Rules table; `/sdd:build` Re-Entry Load Order (5 steps);
Re-opening a sprint (close-sprint-manifest lookup); Threshold Rule + CLAUDE.md exemption.

**After:** the two intro sentences are merged into one; the "keeps context efficient / prevents
loading irrelevant material" semantic is preserved in the merged sentence. **All four named rules
present and unchanged**, all section headings unchanged (no cross-pointer broken).

### Trim 3 — `right-sizing.md` prose trim

**Before:** intro had a paragraph plus a separate motivational sentence ("the interview shouldn't
feel like a TPS-report drill … Right-sizing is a quality-of-experience lever, not an excuse to skip
rigor.").

**Named rules before:** `## The smallProject field` (+ Authoring lifecycle); `## Signals for the
heuristic judgment` (+ four-or-more rule of thumb); `## Skippable Phase 1 beats per command` (with
`### /sdd:scope`, `### /sdd:prd`, `### /sdd:spec`, `### /sdd:plan` sub-beats pointed at by name);
`## The thinness signal and sub-5-question rounds` (two-of-three rule); `## Interaction with the
question cap`.

**After:** the motivational sentence is folded into the intro; its load-bearing clause
("Right-sizing is a quality-of-experience lever, not an excuse to skip rigor") is preserved verbatim.
**All named rules and all section headings unchanged** (no cross-pointer broken — deepening-rounds.md
and the plan/scope/prd/spec SKILL.md pointers all still resolve).

### Summary

- **Rules relocated:** none required relocation. The only rule plan touched in `context-loading.md`
  was reached via an inline pointer that was kept in place.
- **Rules folded in:** none — no file was merged.
- **Rules shown unused (so safe to stop loading):** the `/sdd:build` Re-Entry Load Order, the
  close-sprint-manifest re-open lookup, and the full Per-Command Loading Rules table were not used
  by `/sdd:plan` at startup; plan only ever used the inline pointer.
- **Sections renamed/removed:** none. Every cross-file pointer (which targets section names) still
  resolves.
- `rfsa` ✅ (all 9 files have a recorded outcome), `rfsb` ✅ (plan no longer loads `context-loading`,
  pointer kept), `rfsc` ✅ (no trimmed file dropped a named rule; each is present and traceable by
  name above).

### Item 8 sweep — independent "after" re-verification (`prva` / `rfsc` traceability)

The behavior-preservation sweep independently re-checked every before-rule Item 4 recorded against
the working copy (git HEAD = v2 baseline; all v3 trims are uncommitted, so `git diff HEAD` is the
exact v2→v3 delta). Each is confirmed present and reachable. Nothing flagged for revert.

**Trim 1 — `(/sdd:plan, context-loading)` startup-load removal — CONFIRMED.**
- `plan/SKILL.md` `## Loading` is now a 9-item ordered list with **no** `context-loading.md` entry
  (verified: the diff removed old item 4 and renumbered 4→9; only the load-list entry was removed,
  no numbered workflow **Step** was touched — Steps 1–11 of plan are all present, identical markers
  to HEAD).
- The inline cross-reference survives verbatim: `plan/SKILL.md` `## Prior-Sprint Process Notes`
  ("See `references/context-loading.md` for the per-command loading rule."), so a reader still
  reaches the file and the per-command rule. Footprint dropped, behavior retained.
- `context-loading.md` is **not orphaned**: still startup-loaded by `build/SKILL.md` (`## Loading`
  item 2, plus the `## Behavior` intro pointer) where it is load-justified, and still pointed at
  from plan. All four named rules present in the working copy: `## Per-Command Loading Rules`
  (line 7), `## /sdd:build Re-Entry Load Order` (line 29), `### Re-opening a sprint
  (close-sprint-manifest lookup)` (line 41), `## Threshold Rule` + the 200-line text and CLAUDE.md
  exemption (line 51+).

**Trim 2 — `context-loading.md` prose trim — CONFIRMED.** Diff vs HEAD touches only the intro: two
sentences merged into one; the "keeps context usage efficient / prevents loading irrelevant
material" semantic is preserved in the merged sentence. All four named rules and every section
heading unchanged (no cross-pointer broken).

**Trim 3 — `right-sizing.md` prose trim — CONFIRMED.** Diff vs HEAD folds the separate motivational
sentence into the intro; the load-bearing clause "Right-sizing is a quality-of-experience lever, not
an excuse to skip rigor" survives **verbatim**. All named rules and section headings present in the
working copy: `## The smallProject field` (+ `### Authoring lifecycle`), `## Signals for the
heuristic judgment`, `## Skippable Phase 1 beats per command` (with `### /sdd:scope`, `### /sdd:prd`,
`### /sdd:spec`, `### /sdd:plan` sub-beats pointed at by name), `## The thinness signal and
sub-5-question rounds`, `## Interaction with the question cap`. No cross-pointer broken.

**Process-notes condensation (the 8 command SKILLs) — CONFIRMED no step lost.** The condensed
process-notes steps in discovery / scope / prd / spec / plan / build / polish / refine each keep
their workflow step, their file target, their command-specific capture items, and their
append-only / create-on-first-append semantics. The generic four-category enumeration was condensed
to a pointer to sdd-guide's `## Process Notes`, which **does** define those four categories
(decisions, pushback, difficult questions, pivots) plus real-time and append-only semantics
(`sdd-guide/SKILL.md` `## Process Notes`, line 90+) — so every pointer resolves.

**Ordered-step structure (the core `prva` check) — CONFIRMED for all 9 changed SKILLs.** Extracting
the ordered-step / phase-heading markers (numbered Steps, lettered sub-steps 3a–3f, wrap-up steps
W1–W11, phase headings) from each file and diffing HEAD vs working copy: markers are **identical**
in build, discovery, plan, polish, prd, refine, scope, spec. sdd-guide differs only by an **added**
`## Output Constraints` section (an add, not a removal). No ordered step was removed or
renumbered-away in any file; prose inside steps was condensed only.

**`rfsc` / `prva` verdict (Item 8):** every before-rule Item 4 recorded is present and reachable in
the working copy; no ordered workflow step removed from any command. Independent re-verification
agrees with Item 4. Nothing requires revert.
