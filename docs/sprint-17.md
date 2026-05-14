# Sprint 17

**Theme:** v2 Release Verification epic closeout — closes the 13 remaining ACs in the v2 Release Verification epic (`aahj`, `aahl`, `aahm`, `aahn`, `aaho`, `aahp`, `aahq`, `aahr`, `aahs`, `aaht`, `aahu`, `aahw`, `aahx`). Most of the prose content already lives in `docs/v2-verification.md` from earlier work — this sprint is an audit-and-checkoff pass plus a small amount of net-new structural work: confirming the SKILL.md surface obeys the negative constraints (no "verified" writes, no creation outside sdd-plugin, no release gating, no deletes), and adding the forward-looking "future versions follow the same pattern" statement.

**Tangible outcome:** After this sprint:
- `docs/v2-verification.md` is verified row-for-row against the v2 scope's triaged in-scope feedback list, the 6-column schema is confirmed (ID / Source / Summary / Shipped / Verified / Notes), and the verification-approach prose is confirmed to name the primary dogfood, the supplementary projects, the deliberate `/sdd:pause`/`/sdd:unpause` exercise, and the Pillar-1 mapping. Any drift is patched.
- The plugin SKILL.md surface and reference files are audited against four negative constraints: no SDD command writes a checked Verified box; no SDD command creates `docs/v2-verification.md` in any project other than sdd-plugin; no SDD command gates v2 release on the file's state; no SDD command deletes, archives, or removes the file. The aahv-checked write-conditional in `/sdd:build` wrap-up (Shipped + Notes only, when `docs/v2-verification.md` already exists) is the sole legitimate writer and remains untouched.
- `docs/v2-verification.md` carries a one-paragraph forward-looking statement that future plugin versions (v3+) follow the same out-of-pattern allowance — `docs/<version>-verification.md` is sdd-plugin-specific and persists.
- The v2 Release Verification epic closes in full (13 ACs). Only the v1→v2 Migration epic remains (21 ACs, `aago`–`aahi`) for a final pre-GA sprint.

**Out of scope:**
- v1→v2 Migration guide (`aago`–`aahi`, 21 ACs). Written against final-state v2; the natural Sprint-18 sequel.
- Soft acceptance criteria for MIT distribution (carried open concern, target `/sdd:refine`). Not addressable in a closeout sprint — judgment-anchored ACs need a refinement pass, not a verification pass.
- Re-seeding `docs/v2-verification.md` from scratch. The file is already seeded with 44 rows (FB-001 – FB-044) using the 6-column schema; Item 1 audits the existing seed against the triaged list rather than rewriting it.

**Activation caveat:** Same as every prior v2 sprint. Edits to disk under `plugins/sdd/` do not change live agent behavior until the cached marketplace plugin is reinstalled. The audit reports in Item 2 reflect the on-disk state of `plugins/sdd/`, which is the v2 source of truth even when the cached plugin is still v1.

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** Three items; later items build on earlier confirmations.

1. Item 1 (audit existing v2-verification.md content) — settles the row-level and prose-level claims first, before structural audits run against the same file.
2. Item 2 (negative-constraint audit across SKILL.md surface) — grep-based audit; runs against the on-disk source of truth.
3. Item 3 (future-version forward-looking statement) — single small write to `docs/v2-verification.md`. Doing it last avoids re-touching the file mid-audit.

## Checklist

### Item 1: Audit and close existing v2-verification.md content `[PRD: aahj, aahl, aahm, aahn, aahq, aahr, aahs, aaht]`

- **PRD ref:** `prd.md > v2 Release Verification` (stories 1, 3 — schema/seed + dogfood-approach documentation) `[PRD: aahj, aahl, aahm, aahn, aahq, aahr, aahs, aaht]`
- **Spec ref:** `spec.md > Beat 9 (docs/v2-verification.md granularity and columns)` — 6-column table; FB-NNN three-digit-padded IDs; write triggers limited to `/spec` seed + `/build` wrap-up Shipped/Notes; Verified manual-only.
- **What to build:** Audit-and-checkoff pass against the existing `docs/v2-verification.md`:

  1. **Row-level audit.** Walk every row in the Feedback items table and confirm:
     - Every in-scope feedback item from `docs/scope.md`'s "In-scope deliverables" / triaged list has exactly one row in the table (`aahj`, `aahl`).
     - No rows exist for items that aren't in the triaged in-scope list.
     - The ID column uses the `FB-NNN` three-digit-padded form (`FB-001`, `FB-002`, … `FB-044`).
     - The schema is the agreed 6-column form: `ID | Source | Summary | Shipped (sprint/item) | Verified | Notes` (`aahm`).
     - The Verified column on every row is a manually-editable checkbox (`[ ]` or `[x]`) — not auto-populated text (`aahn`).
     - Where rows already carry `Shipped` values, the value names a sprint and item (e.g., "sprint 11, items 1-2") — confirming the write convention; this also confirms the aahv write-conditional has been firing as intended.
  2. **Verification-approach prose audit.** Confirm the `## Verification approach` section names:
     - The primary dogfood as "Jason runs v2 end-to-end on a small website project covering `/sdd:discovery` through `/sdd:retro`" (`aahq`).
     - Supplementary projects covering, at minimum: cross-machine pause, the 10-question deepening-round cap, per-round `/clear`-and-continue, cross-project pattern capture, longer deepening rounds (`aahr`).
     - `/sdd:pause` and `/sdd:unpause` as items to deliberately exercise across these projects even when not strictly required (`aahs`).
     - The Pillar-1 mapping statement that "verified end-to-end at least once" satisfies Pillar 1 of `docs/scope.md`'s "What 'done' looks like" definition (`aaht`).
  3. **Patch any drift.** If the row-level audit finds extra or missing rows, schema deviations, or non-checkbox Verified entries, fix them. If the prose audit finds a missing or weak claim, rewrite the relevant paragraph. Touch only what's wrong; do not rewrite passing content.
  4. **PRD checkoff.** After both audits pass on disk, mark `aahj`, `aahl`, `aahm`, `aahn`, `aahq`, `aahr`, `aahs`, `aaht` checked in `docs/prd.md`.

- **Acceptance criteria:**
  - [x] `aahj` `docs/v2-verification.md` exists in the sdd-plugin project before any v2 implementation sprint begins, seeded with one row per in-scope feedback item from the v2 scope's triaged list.
  - [x] `aahl` Each row in `docs/v2-verification.md` maps to exactly one in-scope feedback item from the v2 scope's triaged list.
  - [x] `aahm` The exact schema and column set of `docs/v2-verification.md` is defined and present on disk (6-column table per `spec.md > Beat 9`).
  - [x] `aahn` Each row in `docs/v2-verification.md` includes a manually-editable checkable status field.
  - [x] `aahq` The sdd-plugin project contains documentation of the v2 verification approach naming the primary dogfood (Jason runs v2 end-to-end on a small website project covering `/sdd:discovery` through `/sdd:retro`).
  - [x] `aahr` The documentation names supplementary projects used to cover items that won't fire on the primary dogfood (cross-machine pause, 10-question cap, per-round `/clear`-and-continue, cross-project pattern capture, longer deepening rounds).
  - [x] `aahs` The documentation names `/sdd:pause` and `/sdd:unpause` as items to deliberately exercise even when not strictly needed.
  - [x] `aaht` The documentation states that "verified end-to-end at least once" satisfies Pillar 1 of the scope's "done" definition.
- **Verification:**
  - Diff the row ID set in `docs/v2-verification.md` against the in-scope feedback item set in `docs/scope.md`'s triaged list. Expected: exact match, no extras, no gaps.
  - Confirm the table header row literally reads `| ID | Source | Summary | Shipped (sprint/item) | Verified | Notes |`.
  - Spot-check 3-5 rows in the Verified column — each should be `[ ]` (or `[x]` if manually checked). No row should contain auto-populated text in the Verified column.
  - Read the `## Verification approach` section against the four claims listed under **What to build** Part 2. Each claim should resolve to a specific sentence or bullet in the file.
  - Confirm the 8 PRD ACs are now checked in `docs/prd.md`.
- **Status:** [x] Complete

---

### Item 2: Negative-constraint audit across SKILL.md surface `[PRD: aaho, aahp, aahu, aahw]`

- **PRD ref:** `prd.md > v2 Release Verification` (stories 2, 4 — Verified is manual-only; sdd-plugin-specific creation; persistence) `[PRD: aaho, aahp, aahu, aahw]`
- **Spec ref:** `spec.md > Beat 9` — sole legitimate write hook is `/sdd:build` wrap-up writing Shipped + Notes when an item with a `[FB: FB-NNN]` tag closes (the aahv-checked conditional). Verified is manual-only. File presence is the sdd-plugin-specific gate; commands do not create the file in any other project.
- **What to build:** Grep audit of the on-disk plugin source under `plugins/sdd/` (skills, reference files, templates, plugin-level CLAUDE.md). For each of the four negative constraints, run a search and record the result. Where a search returns a hit that violates the constraint, fix it; where it returns no hits, the constraint is structurally satisfied on disk.

  1. **`aaho` — no SDD command writes a checked Verified status.**
     - Search every SKILL.md and reference file for any write path that targets the `Verified` column of `docs/v2-verification.md`.
     - The aahv-checked write-conditional in `plugins/sdd/skills/build/SKILL.md` writes only Shipped + Notes; confirm Verified is explicitly out of scope for that write path (the SKILL.md text should name Shipped and Notes specifically and not Verified).
     - If any SKILL.md or reference file mentions writing Verified, fix the prose to forbid it.
  2. **`aahp` — no SDD command gates v2 release on the file's state.**
     - Search SKILL.md and reference files for any gating logic keyed on v2-verification.md row counts, all-rows-checked, percent-verified, or equivalent.
     - Expected: no such logic exists. The file is an audit-trail artifact, not a gate. Pillar 1 of the scope's "done" definition is manually evaluated.
  3. **`aahu` — no SDD command creates `docs/v2-verification.md` in any project other than sdd-plugin.**
     - Search SKILL.md and reference files for any file-creation path targeting `docs/v2-verification.md`. Expected hits: only the sdd-plugin-specific gate in `/sdd:build` wrap-up that checks file presence and writes only when the file already exists. No SKILL.md should contain a "create this file if missing" path.
     - Confirm the aahv-checked conditional in `/sdd:build` wrap-up is the gate (write only when file exists) and does not include a fallback create.
  4. **`aahw` — no SDD command deletes, archives, or removes the file.**
     - Search SKILL.md and reference files for any delete, archive, rename-out-of-place, or remove path targeting `docs/v2-verification.md` or matching the `v2-verification.md` pattern.
     - Expected: no such logic exists. The file is post-release persistence.

  Each grep result (the search command, the count, and any hits) lands in `process-notes-sprint-17.md` for the audit trail. If a search returns zero hits the constraint is satisfied; if it returns hits, the build phase fixes them and re-runs the search.

- **Acceptance criteria:**
  - [x] `aaho` No SDD command writes a "verified" status into a row in `docs/v2-verification.md`.
  - [x] `aahp` No SDD command gates v2 release on the state of `docs/v2-verification.md`.
  - [x] `aahu` `docs/v2-verification.md` is created only in the sdd-plugin project; no SDD command creates the file in any other project.
  - [x] `aahw` After v2 ships, no SDD command deletes, archives, or removes `docs/v2-verification.md`.
- **Verification:**
  - Four grep commands recorded in `process-notes-sprint-17.md` with their results: (a) Verified-column-write search; (b) release-gating search; (c) file-creation-outside-sdd-plugin search; (d) delete/archive/remove search.
  - Each search either returns zero hits or has its hits resolved in the same build phase and re-grepped to zero.
  - Confirm `plugins/sdd/skills/build/SKILL.md`'s v2-verification.md write path names Shipped and Notes specifically and is conditional on file presence (the aahv pattern stays intact and remains the sole writer).
  - Confirm the 4 PRD ACs are now checked in `docs/prd.md`.
- **Status:** [x] Complete

---

### Item 3: Future-version pattern statement in v2-verification.md `[PRD: aahx]`

- **PRD ref:** `prd.md > v2 Release Verification` (story 4 — persistence and future-version pattern) `[PRD: aahx]`
- **Spec ref:** `spec.md > Beat 9` — sdd-plugin-specific gate; persistence rule.
- **What to build:** Add a one-paragraph forward-looking statement to `docs/v2-verification.md` stating that future plugin versions (v3 and beyond) follow the same out-of-pattern allowance: `docs/<version>-verification.md` is sdd-plugin-specific and persists after release. Placement: under the existing opening prose, before the `## Verification approach` heading, or as a new short subsection. Phrasing should mirror the spec's resolution (Beat 9) and the existing sdd-plugin-specific framing already in the file.

  The statement is forward-looking convention, not implementation. No SDD command behavior changes. It captures, in the audit-trail file itself, the pattern that future v3+ verification files inherit so a future maintainer doesn't have to re-derive it from the v2 PRD.

- **Acceptance criteria:**
  - [x] `aahx` Future plugin versions (v3 and beyond) follow the same out-of-pattern allowance: `docs/<version>-verification.md` is sdd-plugin-specific and persists.
- **Verification:**
  - Open `docs/v2-verification.md` and confirm the future-version paragraph is present, names the `docs/<version>-verification.md` form, and states both the sdd-plugin-specific scoping and the persistence rule.
  - Confirm the 1 PRD AC is now checked in `docs/prd.md`.
- **Status:** [x] Complete

---

## Notes

- **Closes the v2 Release Verification epic in full.** 13 ACs (`aahj`, `aahl`, `aahm`, `aahn`, `aaho`, `aahp`, `aahq`, `aahr`, `aahs`, `aaht`, `aahu`, `aahw`, `aahx`). Pre-Sprint-17 unchecked count: 34 ACs across 2 epics. Post-Sprint-17 projection: 21 ACs remaining in the v1→v2 Migration epic only. 5 of 5 original v2 epics close with this sprint — the migration guide is GA-eve work, distinct from the v2 development cycle proper.
- **PRD checkoff watch.** Per Sprints 11-16 discipline: sprint close must propagate AC checkoffs to `docs/prd.md` immediately. Sprint 17 follows the same pattern.
- **No FB mapping.** None of `aahj`–`aahx` correspond to a row in `docs/v2-verification.md`. The v2 Release Verification epic is meta-infrastructure for the audit trail itself, not v1-feedback-derived. No `[FB: ...]` tags on items; `/sdd:build` wrap-up writes no v2-verification row updates for this sprint. Same posture as Sprint 16.
- **Audit-heavy, write-light sprint.** Item 1 is verify-and-checkoff against existing content; Item 2 is grep audit with conditional fixes; Item 3 is a single paragraph. The bulk of the v2 Release Verification work shipped earlier (the seed file, the verification-approach prose, the per-sprint Shipped writes). What's left is closing the bookkeeping and confirming the structural negatives.
- **Topics deferred to `/sdd:build` context** (in case they surface during implementation):
  - Item 1: if the row-level audit finds the seeded list out of sync with the triaged in-scope feedback list (e.g., scope.md adds or removes items since seeding), reconcile by following scope.md as the source of truth.
  - Item 1: the `Shipped` column convention — confirm at build whether existing rows use "sprint N, item M" consistently or whether prior writes drifted. Patch drift in place.
  - Item 2: the grep terms — likely `v2-verification`, `Verified`, `release gate`, `delete`, `archive`, `remove`. Adjust at build if the obvious terms miss legitimate code paths.
  - Item 2: if a violation is found, decide between fixing the SKILL.md text (preferred — narrow correction) and reaching for a broader rewrite (escalate if needed).
  - Item 3: paragraph placement. Default plan is to put it in the file header before `## Verification approach`. Alternative: a dedicated `## Forward-looking convention` subsection. Build picks whichever reads cleaner.
- **Cached marketplace plugin is still v1.** All Sprint 17 edits ship to disk; the negative-constraint audit reflects on-disk state, which is the v2 source of truth. Cached behavior is unchanged regardless. First real exercise of the audited constraints requires running v2 post-reinstall.
- **Next-sprint candidate after Sprint 17:**
  - v1→v2 Migration guide (`aago`–`aahi`, 21 ACs) — the last open epic. Plan to land this when v2 is fully GA-ready and the cached plugin is about to flip; the migration guide is written against final-state v2.
