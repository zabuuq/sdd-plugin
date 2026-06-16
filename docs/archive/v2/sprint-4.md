# Sprint 4

**Theme:** v2 release-prep artifacts. Every release-required document that isn't yet written: the migration guide, the archive index, the verification audit file (seeded), and the v2 verification-approach documentation.

**Tangible outcome:** After this sprint, v2 has a maintained `MIGRATION-v1-to-v2.md` at the plugin root with state-aware migration table; `docs/archive/v1/INDEX.md` clarifies the archive's nature; `docs/v2-verification.md` exists in the sdd-plugin project with one row per in-scope feedback item from the triaged list and a manually-editable Verified column; and the v2 verification approach is explicitly documented (primary dogfood, supplementary projects, pause/unpause exercise, Pillar 1 mapping). v2 release-readiness is unblocked from the artifact side; per-command behavior implementation is the natural next multi-sprint phase.

**Out of scope:**
- Bug Fix 2 (`aaad`–`aaag`, PRD AC auto-checkoff during `/sdd:build` wrap-up) — depends on `/sdd:build` wrap-up implementation, a substantial epic in its own right.
- Per-command behavior implementation (Interview Command Behaviors, Context Management, `/sdd:onboard` polish, Sprint Loop, `/sdd:retro` at project close, Cross-Project Feedback Transfer forwarding, Automatic Backlog Generation, Process Notes Growth, `/sdd:discovery` full impl, `/sdd:pause`/`/sdd:unpause` full impl, `/sdd:build` wrap-up).
- Behavioral ACs `aahk`, `aahl`, `aahm`, `aaho`, `aahp`, `aahv`, `aahw`, `aahx` from v2 Release Verification — these all describe runtime command behavior tied to `/sdd:build` wrap-up or future-plugin-version conventions; their structural prerequisites are met by Items 3 and 4 but the behaviors themselves ship when the relevant command implementations land.

## Build Preferences
- **Build mode:** autonomous

## Execution order

All four items are independent (different files, no shared edits) and may run in parallel. There is no ordering constraint between them.

## Checklist

### Item 1: Create `MIGRATION-v1-to-v2.md`

- **PRD ref:** `prd.md > v1 → v2 Migration` (Stories 1, 2, 3, 4 partial, 5, 6 partial) `[PRD: aago, aagp, aagq, aagr, aags, aagt, aagu, aagv, aagw, aagx, aagy, aagz, aaha, aahc, aahd, aahe, aahi]`
- **Spec ref:** `spec.md > Migration > MIGRATION-v1-to-v2.md`, `spec.md > Migration > Resume-File Renames`, `spec.md > Migration > Other Migration Content`
- **What to build:** Write `plugins/sdd/MIGRATION-v1-to-v2.md` containing every PRD-required section. Structure (in order):
  1. **Opening framing** — the PRD-mandated literal text covering: v2 adds `/sdd:discovery`; `/sdd:scope` is narrower (boundary only — exploration moved to `/sdd:discovery`); `/sdd:prd` and `/sdd:spec` unchanged; `/sdd:sprint` renamed to `/sdd:plan`; `/sdd:iterate` renamed to `/sdd:polish`; `/sdd:reflect` removed; new utilities `/sdd:pause` and `/sdd:unpause`. The intermediate-shuffle framing (in which `/scope` and `/prd` were renamed in tandem) is NOT presented.
  2. **State-aware migration table** keyed on v1 `lastCommand` with two outcome columns (mid-flight / completed), per the table in `spec.md > Migration > MIGRATION-v1-to-v2.md`. Must include a row for every v1 command name that could appear as `lastCommand`, including `/sdd:reflect` with separate "mid-reflect" and "post-reflect" cases.
  3. **Renamed commands and resume files** — explicit statement of the rename pairs `/sdd:sprint → /sdd:plan` and `/sdd:iterate → /sdd:polish`; resume-file rename table; instruction to users to rename v1 resume files to v2 names when such files exist; statement that `/sdd:scope` is narrower in v2 (exploration moved to `/sdd:discovery`).
  4. **Legacy `process-notes.md`** — statement that legacy `process-notes.md` is a read-only historical reference; optional one-time split recommendation for converting it into per-phase files.
  5. **`docs/archive/v1/`** — statement that the archive is left untouched by v2.
  6. **New v2 capabilities sections** — one section each introducing `/sdd:pause` + `/sdd:unpause`; `feedbackLocalPath`; `/sdd:discovery` as a genuinely new command (not a rename).
  7. **No aliases statement** — explicit statement that v1 command names (`/sdd:sprint`, `/sdd:iterate`, `/sdd:reflect`) are not aliased in v2.
- **Acceptance criteria:** (one per AC ID — checked as the corresponding migration-guide content is verified to be present)
  - [x] `aago` File `plugins/sdd/MIGRATION-v1-to-v2.md` exists at the plugin root.
  - [x] `aagp` Opening framing states the v2 changes per the literal text above.
  - [x] `aagq` The migration guide does NOT present the intermediate `/scope`/`/prd` renaming framing.
  - [x] `aagr` A state-aware migration table keyed on v1 `lastCommand` values is present.
  - [x] `aags` The table contains a row for every v1 command name that could appear as `lastCommand`, including `/sdd:reflect`.
  - [x] `aagt` The table contains separate rows for "mid-reflect" and "post-reflect" cases.
  - [x] `aagu` Each row names the v2 command to invoke next and any one-time action required.
  - [x] `aagv` The guide documents the rename pairs `/sdd:sprint → /sdd:plan` and `/sdd:iterate → /sdd:polish`.
  - [x] `aagw` The guide instructs users to rename v1 resume files to v2 names when such files exist.
  - [x] `aagx` The guide includes a statement that `/sdd:scope` is narrower in v2.
  - [x] `aagy` The guide states that legacy `process-notes.md` is treated as a read-only historical reference.
  - [x] `aagz` The guide includes an optional one-time split recommendation for converting legacy `process-notes.md` into per-phase files.
  - [x] `aaha` The guide states that `docs/archive/v1/` is left untouched by v2.
  - [x] `aahc` The guide contains a section introducing `/sdd:pause` and `/sdd:unpause`.
  - [x] `aahd` The guide contains a section introducing the `feedbackLocalPath` profile field and its purpose.
  - [x] `aahe` The guide contains a section stating that `/sdd:discovery` is a genuinely new command (not a rename of any v1 command).
  - [x] `aahi` The guide explicitly states that v1 command names are not aliased in v2.
- **Verification:**
  - Read the file end-to-end and confirm each AC checkbox can be checked off against present content.
  - Grep the file for each v1 command name (`/sdd:sprint`, `/sdd:iterate`, `/sdd:reflect`) — these may appear (this is explicit migration-context content) but every appearance must serve a documented migration purpose (rename pair, state-aware table row, no-alias statement). No prospective directives ("run `/sdd:sprint` to ...") should appear.
- **Status:** [x] Complete

---

### Item 2: Add `docs/archive/v1/INDEX.md`

- **PRD ref:** `prd.md > v1 → v2 Migration` (Story 4 — legacy process notes / archive guidance) `[PRD: aahb]`
- **Spec ref:** `spec.md > Migration > Other Migration Content` (the `docs/archive/v1/` index file requirement)
- **What to build:** Create a new file `docs/archive/v1/INDEX.md` containing a one-line note clarifying that the archive contains a pre-rename snapshot of v1 planning artifacts. Example: "This directory contains a pre-rename snapshot of v1 planning artifacts. Files here are historical and not modified by v2." The exact wording is left to the implementer's discretion as long as the one-line "pre-rename snapshot" intent is conveyed.
- **Note on AC `aahh` consistency:** AC `aahh` ("`docs/archive/v1/` is left untouched by the v2 migration sweep") refers to the v2 active-prose sweep not modifying existing archive files. Adding a NEW `INDEX.md` file inside the archive is explicitly required by AC `aahb` and does not contradict `aahh`. Item 2's write should not touch any existing file under `docs/archive/v1/`.
- **Acceptance criteria:**
  - [x] `aahb` `docs/archive/v1/INDEX.md` exists.
  - [x] `aahb` (cont.) The file contains a one-line note clarifying that the archive contains a pre-rename snapshot of v1 planning artifacts.
  - [x] No existing file under `docs/archive/v1/` is modified by this item's work (preserves `aahh`).
- **Verification:**
  - `ls docs/archive/v1/INDEX.md` succeeds.
  - Read the file and confirm the one-line note is present and conveys the "pre-rename snapshot" framing.
  - `git status docs/archive/v1/` shows only the new INDEX.md addition; no other files in that directory are modified.
- **Status:** [x] Complete

---

### Item 3: Seed `docs/v2-verification.md`

- **PRD ref:** `prd.md > v2 Release Verification` (Story 1 — seeded audit trail; Story 2 — manual verified-column) `[PRD: aahj, aahn]`
- **Spec ref:** `spec.md > Plugin-Project-Specific Artifacts > docs/v2-verification.md`
- **What to build:** Create `docs/v2-verification.md` (sdd-plugin project only) with a 6-column markdown table per the spec schema: `ID | Source | Summary | Shipped (sprint/item) | Verified | Notes`. Populate the table with one row per in-scope feedback item drawn from `docs/scope.md`'s in-scope deliverables list (sections "Bug fixes", "New commands", "Behavioral rules across interview commands", "Context management between phases", "`/sdd:onboard` polish", "`/sdd:plan` and `/sdd:build` (sprint loop)", "`/sdd:retro` (project close only)", "`/sdd:feedback` cross-project transfer", "Automatic backlog generation", "Process notes growth", "v1 → v2 migration"). Each row gets a sequential `FB-NNN` ID (three-digit-padded, starting at FB-001). The Source column names the project of origin (Event Planning App, fiver-gigs, sdd-plugin self-use) — derived from the scope's discovery interview. The Summary column carries a one-line description. The Shipped column is left empty (auto-written by `/sdd:build` wrap-up when items ship). The Verified column gets `[ ]` (manual checkoff later). The Notes column starts empty.
- **Acceptance criteria:**
  - [x] `aahj` `docs/v2-verification.md` exists in the sdd-plugin project.
  - [x] `aahj` (cont.) The file is seeded with one row per in-scope feedback item from the v2 scope's triaged list (every in-scope deliverable named in `docs/scope.md`'s sections under "In-scope deliverables" maps to at least one row).
  - [x] `aahn` Each row in the file includes a manually-editable checkable status field (`[ ]` in the Verified column).
  - [x] The table follows the 6-column schema: `ID | Source | Summary | Shipped (sprint/item) | Verified | Notes`.
  - [x] FB-IDs are three-digit-padded sequential integers starting at `FB-001`. Stable from this point forward.
  - [x] Shipped column is left empty for every row (will be auto-populated by `/sdd:build` wrap-up when the corresponding work ships).
- **Verification:**
  - Read the file; confirm schema and row count.
  - Cross-check the seeded rows against `docs/scope.md`'s in-scope deliverables — every named in-scope deliverable should map to at least one row.
  - Confirm every row's Verified column is `[ ]` (not `[x]`).
- **Status:** [x] Complete

---

### Item 4: Document the v2 verification approach

- **PRD ref:** `prd.md > v2 Release Verification` (Story 3 — verification-approach documentation) `[PRD: aahq, aahr, aahs, aaht]`
- **Spec ref:** `spec.md > Plugin-Project-Specific Artifacts > docs/v2-verification.md`, `docs/scope.md > What "done" looks like` (the Pillar 1 mapping)
- **What to build:** Add documentation of the v2 verification approach to the sdd-plugin project. Location: a header section at the top of `docs/v2-verification.md` (above the seeded table), so the audit file is self-documenting and travels with the verification artifact. The header must name:
  - **Primary dogfood:** Jason runs v2 end-to-end on a small website project covering `/sdd:discovery` through `/sdd:retro` — the principal verification path that exercises every in-the-chain command.
  - **Supplementary projects:** projects used to cover items the primary dogfood won't reach. Specifically: cross-machine pause; the 10-question deepening-round cap; per-round `/clear`-and-continue; cross-project pattern capture written by `/sdd:retro`; longer deepening rounds.
  - **Explicit pause/unpause exercise:** `/sdd:pause` and `/sdd:unpause` are deliberately exercised even when not strictly required for the work, so the resume-file schema and round-trip behavior get verified end-to-end.
  - **Pillar 1 mapping:** the statement that "verified end-to-end at least once" satisfies Pillar 1 of the scope's "done" definition (cross-reference `docs/scope.md > What "done" looks like`).
- **Acceptance criteria:**
  - [x] `aahq` The sdd-plugin project contains documentation of the v2 verification approach naming the primary dogfood (Jason runs v2 end-to-end on a small website project covering `/sdd:discovery` through `/sdd:retro`).
  - [x] `aahr` The documentation names supplementary projects used to cover items that won't fire on the primary dogfood (cross-machine pause; 10-question cap; per-round `/clear`-and-continue; cross-project pattern capture; longer deepening rounds).
  - [x] `aahs` The documentation names `/sdd:pause` and `/sdd:unpause` as items to deliberately exercise even when not strictly needed.
  - [x] `aaht` The documentation states that "verified end-to-end at least once" satisfies Pillar 1 of the scope's "done" definition.
- **Verification:**
  - Read the header section in `docs/v2-verification.md`; confirm each named element (primary dogfood, supplementary projects with the specific items listed, pause/unpause exercise, Pillar 1 statement) is present.
  - Cross-reference the Pillar 1 statement against `docs/scope.md`'s "What 'done' looks like" section to confirm consistency.
- **Status:** [x] Complete

---

## Notes

- Items 1–4 are independent (different files). Autonomous mode can dispatch them in parallel. No sequencing constraint.
- Sprint 4 ships **17 + 1 + 2 + 4 = 24 ACs** worth of release-prep artifacts. This is a large AC count for a sprint, but the bulk (17 ACs) are concentrated in Item 1 because the migration guide is one cohesive document — splitting it horizontally would create awkward fragments and multiple file-creation events for the same artifact.
- After sprint 4: the next natural sprint candidate is `/sdd:build` wrap-up implementation, which unlocks Bug Fix 2 (`aaad`–`aaag`) and the v2-verification.md write behavior (`aahk`, `aahl`, `aahm`, `aahv`). That's a substantial standalone sprint and may itself need to split.
- `[FB: ...]` tags are NOT applied to sprint 4 items even though `docs/v2-verification.md` will exist by the end of the sprint. Reason: the activation gate (file presence) becomes true mid-sprint at Item 3's completion, but Items 1, 2, 4 are about creating the audit artifact and the migration guide, not about implementing feedback items. The FB-row "Shipped" updates start firing in sprint 5+ when behavior implementation begins.
- `docs/v2-verification.md` seeding (Item 3) was originally supposed to fire at `/sdd:spec` finalization per the spec's seeding trigger. That didn't happen during the v2 spec phase. Sprint 4 picks it up as a one-off seed; the spec's seeding trigger is a forward-looking convention for future plugin versions.
