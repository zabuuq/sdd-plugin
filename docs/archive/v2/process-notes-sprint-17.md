# Process Notes — Sprint 17

## Planning (2026-05-14)

**PRD scan at startup.** Sprint 16 closed the Automatic Backlog Generation epic. Pre-Sprint-17 unchecked count: 34 ACs across 2 epics — v1→v2 Migration (21 ACs, `aago`–`aahi`) and v2 Release Verification remaining (13 ACs, `aahj`, `aahl`, `aahm`, `aahn`, `aaho`, `aahp`, `aahq`, `aahr`, `aahs`, `aaht`, `aahu`, `aahw`, `aahx`). Unvetted section empty. No `/sdd:refine` recommendation triggered.

**Sprint-grouping decision.** Two candidate epics. Picked v2 Release Verification rather than v1→v2 Migration based on three factors:

1. **Smaller and self-contained.** 13 ACs vs 21; mostly audit-and-checkoff against existing `docs/v2-verification.md` content vs a large net-new migration guide write.
2. **GA-sequencing.** The migration guide is GA-eve work — written against final-state v2 so the rename pairs, state-aware table, and resume-file rename rules don't drift between draft and ship. Doing it last avoids re-writes if anything else lands first. Sprint 16's notes explicitly flagged this sequencing.
3. **Closes an epic.** Sprint 17 closes the second-to-last open v2 epic in full, leaving exactly one epic (Migration) for Sprint 18. Clean ledger going into GA.

**Pre-existing content recognition.** Read `docs/v2-verification.md` at planning time. Most v2 Release Verification AC content is already on disk — the verification-approach prose names the primary dogfood, the supplementary projects (cross-machine pause, 10-question cap, /clear-and-continue, cross-project pattern capture, longer deepening rounds), the deliberate `/sdd:pause`/`/sdd:unpause` exercise, and the Pillar-1 mapping. The 6-column schema (`ID | Source | Summary | Shipped | Verified | Notes`) is in place. The table is seeded FB-001 through FB-044 with `[ ]` Verified boxes. So Item 1 is verify-and-checkoff, not write-from-scratch. Items 2 and 3 are net-new structural work but small.

**Build-mode decision (autonomous).** Continued autonomous from Sprint 16's mode. Sprint 17 is mostly audit work with tightly-scoped items; user has been autonomous for multiple sprints; the planning input explicitly directed "no clarifying questions" — picked autonomous without re-asking. Build mode locks for the sprint per spec.

**No deepening rounds.** Skipped per the small-sprint pattern. Item 1 is verify-against-existing; Item 2 is grep-based audit with fixed search targets; Item 3 is a single paragraph. Spec already pins every substantive call in Beat 9 (schema, write triggers, sdd-plugin-specific gating, manual-only Verified). No planning-time ambiguity remained.

**Item 1 (audit existing content).** 8 ACs. Row-level audit confirms in-scope feedback list mapping (1-to-1, FB-NNN three-digit padding, 6-column schema, manually-editable Verified). Prose audit confirms four named claims about dogfood approach. Patch drift if found; check off ACs on disk.

**Item 2 (negative-constraint audit).** 4 ACs. Grep targets:
- `aaho` no Verified-column writes — search SKILL.md and reference files for any path writing Verified
- `aahp` no release gating — search for any logic keyed on v2-verification.md state
- `aahu` no creation outside sdd-plugin — search for any file-creation path; confirm `/sdd:build` wrap-up is conditional on presence (the aahv pattern)
- `aahw` no deletes/archives/removes — search for delete/archive/remove paths

Grep commands and results land in this process-notes file for the audit trail. If a grep returns zero hits the constraint is structurally satisfied; if it returns hits, build fixes and re-greps.

**Item 3 (future-version statement).** 1 AC (`aahx`). One paragraph in `docs/v2-verification.md` stating future v3+ versions follow the same pattern — `docs/<version>-verification.md` is sdd-plugin-specific and persists. Placement: file header before `## Verification approach`, or a short subsection — build picks whichever reads cleaner.

**Cached marketplace caveat carried forward.** Sprint 17 edits ship to disk under `plugins/sdd/` and `docs/` but don't change live agent behavior until reinstall. Same caveat as every prior v2 sprint. Item 2's negative-constraint audit reflects the on-disk state of `plugins/sdd/`, which is the v2 source of truth even when the cached plugin is still v1.

**Open concern carried forward.** "Soft acceptance criteria for MIT distribution" (target `/sdd:refine`) remains open. Not addressable in a closeout sprint; revisit when judgment-anchored ACs next get refined.

**Next-sprint candidate.** v1→v2 Migration guide (`aago`–`aahi`, 21 ACs). Final epic for GA.

## Build (2026-05-14)

**Build mode:** autonomous (locked from planning). Three items, sequential, all closed in one pass with no error revisions.

### Item 1 — Audit existing v2-verification.md content

**Outcome:** Clean. No patches needed. 8 ACs closed (`aahj`, `aahl`, `aahm`, `aahn`, `aahq`, `aahr`, `aahs`, `aaht`).

**Row-level audit.** Walked the 44 rows in `docs/v2-verification.md` against the in-scope deliverables in `docs/scope.md`. Counted by category:

| Scope category | Count | FB IDs |
|----------------|-------|--------|
| Bug fixes | 2 | FB-001 – FB-002 |
| New commands | 4 | FB-003 – FB-006 |
| Behavioral rules across interview commands | 7 | FB-007 – FB-013 |
| Context management between phases | 3 | FB-014 – FB-016 |
| `/sdd:onboard` polish | 7 | FB-017 – FB-023 |
| `/sdd:plan` and `/sdd:build` (sprint loop) | 3 | FB-024 – FB-026 |
| `/sdd:retro` (project close only) | 2 | FB-027 – FB-028 |
| `/sdd:feedback` cross-project transfer | 4 | FB-029 – FB-032 |
| Automatic backlog generation | 4 | FB-033 – FB-036 |
| Process notes growth | 3 | FB-037 – FB-039 |
| v1 → v2 migration | 5 | FB-040 – FB-044 |

Total: 44 rows, 44 in-scope items — exact match. No extras, no gaps. (The v2 release verification scope subsection itself is meta-infrastructure for this audit trail, not an FB row — same posture as Sprint 16.)

**Schema audit.** Header row literally reads `| ID | Source | Summary | Shipped (sprint/item) | Verified | Notes |`. 6 columns, agreed schema per `spec.md > Beat 9`. ✓

**ID-padding audit.** All rows use `FB-NNN` three-digit-padded form (`FB-001` through `FB-044`). ✓

**Verified-column audit.** Every row's Verified column is `[ ]` (manually-editable checkbox). No row contains auto-populated text. ✓

**Shipped-column convention.** Rows with non-empty Shipped values use the "sprint N, item M" form consistently — examples: `sprint 8, item 1` (FB-001), `sprint 11, items 1-2` (FB-004), `sprint 9, item 5` (FB-014), `sprint 12, items 1-4` (FB-011), `sprint 10, item 5 (partial — discovery only)` (FB-037). The "(partial — …)" qualifier on FB-037 is a legitimate refinement of the convention, not drift. ✓

**Verification-approach prose audit.** Four claims confirmed in the existing `## Verification approach` section:

1. **Primary dogfood** (`aahq`): line 7 — "Jason runs v2 end-to-end on a small website project covering `/sdd:discovery` through `/sdd:retro`."
2. **Supplementary projects** (`aahr`): lines 11-15 — names cross-machine pause, 10-question deepening-round cap, per-round `/clear`-and-continue, cross-project pattern capture, longer deepening rounds.
3. **Deliberate pause/unpause exercise** (`aahs`): line 17 — "`/sdd:pause` and `/sdd:unpause` are deliberately exercised across these projects even when not strictly required for the work."
4. **Pillar 1 mapping** (`aaht`): line 19 — "Verified end-to-end at least once" satisfies Pillar 1, with the specific quote from `docs/scope.md > What "done" looks like`.

All four prose claims resolve to specific named sentences in the file. ✓

**[iteration-candidate]** Stale phrasing on PRD AC `aahm`: the PRD AC text reads "The exact schema and column set of `docs/v2-verification.md` is deferred to `/sdd:spec`." The schema deferral has been resolved (`/sdd:spec` Beat 9 locked the 6-column form; the seed file reflects it). The AC is satisfied — schema is defined and present on disk — but the AC text still references the now-resolved deferral. Per `references/living-documents.md` ("Do not modify the spec or approved PRD stories without going through `/sdd:refine`"), the AC text was not edited in this sprint. The box is checked because the substantive claim ("the schema is defined") is true; the text rewording belongs in a future `/sdd:refine` pass. (item: aahm)

### Item 2 — Negative-constraint grep audit

**Outcome:** All four constraints structurally satisfied on disk. No fixes needed. 4 ACs closed (`aaho`, `aahp`, `aahu`, `aahw`).

#### Search (a) — Verified-column writes (`aaho`)

**Command:** `Grep "Verified" plugins/sdd/`

**Result:** 1 hit.

- `plugins/sdd/skills/build/SKILL.md:417` — "The `Verified` column is **never written by `/sdd:build`** — not as `[ ]`, not as `[x]`, not at all. The Verified column remains `[ ]` until the user manually checks it. State this explicitly so a future reader does not misread silence as ambiguity."

The single hit is an **explicit prohibition** against writing the Verified column. The aahv-checked write-conditional in `/sdd:build` wrap-up (Step W9) writes only the `Shipped` and `Notes` columns; Verified is named explicitly and excluded. **Constraint satisfied.**

#### Search (b) — Release gating (`aahp`)

**Commands:**
- `Grep -i "release.{0,10}(gate|gating|block|ready|ship)" plugins/sdd/`
- `Grep -i "gate.{0,40}(release|v2|ship)|release.{0,40}gate|all.{0,10}(rows|verified).{0,40}(check|complete)" plugins/sdd/`

**Result:** 2 hits from the broader search; both are activation gates, not release gates.

- `plugins/sdd/skills/build/SKILL.md:407` — "**Activation gate.** Check whether `docs/v2-verification.md` exists in the running project's `docs/` directory." This gates **whether Step W9 runs**, keyed on file presence in the current project. It does not gate v2 release on the file's row counts, verified-percent, or any equivalent.
- `plugins/sdd/skills/plan/SKILL.md:162` — "Gated entirely on the presence of `docs/v2-verification.md` in the project." Same posture: gates whether the FB-tag-prompt step runs, not release.

No SKILL.md or reference file contains release-gating logic keyed on v2-verification.md state. **Constraint satisfied.**

#### Search (c) — File creation outside sdd-plugin (`aahu`)

**Commands:**
- `Grep "v2-verification" plugins/sdd/`
- `Grep "verification\.md|verification\.json" plugins/sdd/` (broader pattern)
- `Grep "v2-verification|version-verification|<version>" plugins/sdd/skills/spec/SKILL.md` (spec-specific check)

**Result:** 6 files reference `v2-verification.md`. No file-creation path exists.

- `plugins/sdd/skills/build/SKILL.md:407, 420` — explicit "**absent: skip this step entirely. No warning, no fallback write, no auto-creation.**" and "Do **not** auto-create the row — row creation is `/sdd:spec` seeding territory, not `/sdd:build`'s." File presence is the sole activation signal; no fallback creation.
- `plugins/sdd/skills/plan/SKILL.md:162` — "skip this step silently when the file does not exist." Same posture.
- `plugins/sdd/skills/polish/SKILL.md:140` — reference to "Shipped" rows context, not a write/create path.
- `plugins/sdd/skills/sdd-guide/references/backlog.md:48` — string in an illustrative source attribution; not executable behavior.
- `plugins/sdd/skills/sdd-guide/references/living-documents.md:45` — "manual audit trail, not updated by `/refine`." Read-only reference.
- `plugins/sdd/skills/sdd-guide/references/sprint-tags.md:111, 138, 183, 195, 261` — describes the `[FB: ...]` tag and `[close-sprint-manifest]` shape; behavior is conditional on file presence.
- `plugins/sdd/skills/spec/SKILL.md` — **zero references** to `v2-verification.md`. The `/sdd:spec` SKILL does not contain any v2-verification file-creation logic. The build SKILL line 420 ("row creation is `/sdd:spec` seeding territory") refers to conceptual ownership, not an automated path; the sdd-plugin seed file was created manually by the maintainer.

No SKILL.md or reference file contains a "create this file if missing" path. The file's existence in the sdd-plugin project is hand-seeded; every consumer is presence-gated. **Constraint satisfied.** Stronger than required: no SDD command creates the file in **any** project — manual seeding is the only path.

#### Search (d) — Delete / archive / remove (`aahw`)

**Command:** `Grep -i "(delete|archive|remove|rm\s).{0,40}verification" plugins/sdd/`

**Result:** Zero hits.

No SKILL.md or reference file contains a delete, archive, rename-out-of-place, or remove path targeting `docs/v2-verification.md`. The file is post-release persistence. **Constraint satisfied.**

#### Item 2 summary

All four negative constraints structurally satisfied on the on-disk plugin source. The aahv-checked write-conditional in `plugins/sdd/skills/build/SKILL.md` Step W9 remains the sole legitimate writer to `docs/v2-verification.md`, and that writer is correctly scoped to the `Shipped` and `Notes` columns only (Verified explicitly excluded at line 417), conditioned on file presence (line 407), and forbidden from creating missing rows (line 420). The file is structurally a persistent artifact in the sdd-plugin project that no SDD command can corrupt or destroy.

### Item 3 — Forward-looking v3+ pattern paragraph

**Outcome:** One paragraph added to `docs/v2-verification.md`. 1 AC closed (`aahx`).

Inserted a `**Forward-looking convention.**` paragraph immediately under the file's opening prose, before `## Verification approach`. The paragraph:

- Names the generalized form `docs/<version>-verification.md`.
- States the sdd-plugin-specific scoping.
- States the persistence rule (the file lives only in sdd-plugin, persists after release).
- Lists the four negative behaviors that hold per version (no creation in user projects, no release gating, no Verified-column writes, no deletes/archives).
- Names the per-version pattern: seed at planning time → `/sdd:build` wrap-up writes Shipped/Notes when `[FB: ...]` tags fire and the file is present → maintainer manually checks Verified.

Placement under the opening prose (rather than as a dedicated subsection) was chosen because the paragraph is framing convention, not implementation detail — it reads as natural continuation of the file's "Sdd-plugin-specific audit-trail artifact" framing. A future maintainer looking at `docs/<version>-verification.md` in v3+ sees the precedent stated in the v2 file itself rather than having to re-derive it from a buried PRD AC.

### Decisions made

- **No PRD AC text rewrites.** The `aahm` AC text references its own pre-resolution deferral ("deferred to `/sdd:spec`"). Per `references/living-documents.md`, modifying approved PRD stories requires `/sdd:refine`. The AC is substantively satisfied (the schema IS defined and present on disk per spec Beat 9), so the box is checked; the stale text wording is flagged as an `[iteration-candidate]` for a future `/sdd:refine` pass. Touching the wording here would have been scope creep.
- **No v2-verification.md row-content edits.** The audit found the seed file already correct. The sprint plan's "patch any drift" Part 3 was conditional on the audit finding drift; the audit found none, so Part 3 was a no-op.
- **No spec-SKILL changes.** The `/sdd:spec` SKILL has zero v2-verification references. The build SKILL line 420 refers to "spec seeding territory" conceptually, but no automated spec seeding logic exists. This is structurally fine — manual seeding is what happened, and the audit confirms no automated creation path exists in any direction. No fix needed.

### Anything notable

- **Closes the v2 Release Verification epic in full.** 13 ACs closed in this sprint. Pre-sprint unchecked count was 34 ACs across 2 open epics. Post-sprint: 21 ACs in v1→v2 Migration only (`aago` – `aahi`). Five of five original v2 epics close with this sprint; the migration guide is GA-eve work.
- **Audit-heavy, write-light sprint.** Items 1 and 2 produced zero file edits (audit confirmed existing state correct). Item 3 added one paragraph. Total disk delta: 13 checked boxes in `docs/prd.md`, 3 checked statuses + 13 checked ACs in `docs/sprint-17.md`, 1 added paragraph in `docs/v2-verification.md`. Exactly the right footprint for a verification-epic closeout — substantive work shipped earlier; what's left is bookkeeping and confirming structural negatives.
- **Cached marketplace caveat.** Sprint 17 edits ship to disk under `docs/` only (no `plugins/sdd/` edits — Item 2 was audit-only). The cached marketplace plugin is still v1; none of these edits change live agent behavior until plugin reinstall. The negative-constraint audit reflects on-disk source-of-truth state, which is correct regardless of cached behavior. First real exercise of the audited constraints requires running v2 post-reinstall.
- **PRD checkoff discipline maintained.** Per Sprints 11-16, the sprint close propagated AC checkoffs to `docs/prd.md` immediately, not deferred. All 13 ACs (`aahj`, `aahl`, `aahm`, `aahn`, `aaho`, `aahp`, `aahq`, `aahr`, `aahs`, `aaht`, `aahu`, `aahw`, `aahx`) flipped to `[x]` in both files within the sprint cycle.
- **No FB mapping.** None of `aahj`–`aahx` correspond to FB rows in `docs/v2-verification.md`. The v2 Release Verification epic is meta-infrastructure for the audit trail itself, not v1-feedback-derived. No `[FB: ...]` tags on Sprint 17 items; `/sdd:build` wrap-up writes no v2-verification row updates for this sprint. Same posture as Sprint 16.

### [close-sprint-manifest]

- Sprint: 17
- Mode: autonomous
- Items closed: 3 of 3 (Item 1, Item 2, Item 3)
- PRD ACs checked: aahj, aahl, aahm, aahn, aaho, aahp, aahq, aahr, aahs, aaht, aahu, aahw, aahx
- Story-split mappings: (none)
- Iteration-candidate dispositions: 1 captured (aahm AC text drift — targets future `/sdd:refine`)
- Tech-debt entries: (none)
- v2-verification rows updated: (none)

### [/close-sprint-manifest]

### Next action

v2 Release Verification epic now closed in full. Run `/sdd:iterate` to add polish work (none anticipated — the iteration-candidate is text-only PRD drift targeting `/sdd:refine`, not in-flight implementation polish), or `/sdd:reflect` to review the sprint. Per Sprint plan: next-sprint candidate is the v1→v2 Migration guide (`aago` – `aahi`, 21 ACs, final epic for GA).
