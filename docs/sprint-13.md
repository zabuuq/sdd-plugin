# Sprint 13

**Theme:** `/sdd:prd` Polish — three sub-stories that lift `/sdd:prd` output quality: pre-write AC quality check, phase-split flag at 10+ epics, and an Unvetted section seeded with a marker comment. Nine ACs (`aacd`–`aacl`) close — the entire `/sdd:prd` Polish epic.

**Tangible outcome:** After this sprint, `/sdd:prd` produces measurably higher-quality PRDs end-to-end:
- Every acceptance criterion is evaluated for specific / observable / verifiable before the file is written; failing ACs are rewritten in the same run.
- When the in-conversation epic count crosses 10, the user gets an explicit phase-split nudge with reasoning (not a blocker — user can elect to keep one phase).
- `docs/prd.md` is written with an empty `## Unvetted` section preceded by a marker HTML comment explaining the section's purpose to future agents and `/sdd:refine`.

The three sub-stories are independent of each other (they hit different points in the `/sdd:prd` runtime — Items 1 and 2 fire before document generation; Item 3 is part of document generation). Single-file sprint: every edit lands in `plugins/sdd/skills/prd/SKILL.md`.

**Out of scope:**
- Process Notes Growth remainder (`aagf`–`aagn`, 9 ACs). Better as the next sprint — wires scope/prd/spec/plan/retro to per-phase notes files and benefits from running after this sprint's prd edits settle.
- `/sdd:polish` re-open (`aaev`–`aaex`, 3 ACs). Small, can ride alongside a later sprint.
- `/sdd:retro` at project close (`aaey`–`aafe`, 6 ACs). Terminal-command work; better near v2 GA.
- Automatic Backlog Generation (`aafs`–`aagd`, 12 ACs). Cross-cutting across 5 planning commands; bigger lift.
- v1→v2 Migration (`aago`–`aahi`, 21 ACs). Single-file but lower priority until v2 nears GA.
- v2 Release Verification remaining (~13 ACs). Seeding / persistence / manual-checkoff conventions.

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** No hard dependencies between items, but all three edit the same SKILL.md file. Sequential subagents avoid edit collisions and keep the file's pre-write / post-write structure coherent across items.

1. Item 1 (AC quality check) — fires before document generation; cleanest to land first.
2. Item 2 (phase-split flag) — also fires before document generation; sits naturally next to the quality check.
3. Item 3 (Unvetted section + marker comment) — part of document generation; lands last.

## Checklist

### Item 1: `/sdd:prd` pre-write AC quality check `[PRD: aacd, aace, aacf]`

- **PRD ref:** `prd.md > Interview Command Behaviors` (the `/sdd:prd`-only AC quality check story) `[PRD: aacd, aace, aacf]`
- **Spec ref:** `spec.md > /sdd:prd` (v2 changes — "Pre-write quality check: every AC evaluated against three checks — specific, observable, verifiable. Failing ACs rewritten before the file is generated. Check runs exactly once per `/sdd:prd` run.")
- **What to build:** Update `plugins/sdd/skills/prd/SKILL.md` so that immediately before `docs/prd.md` is written, the agent evaluates every acceptance criterion the run is about to write against three checks: **specific** (describes a concrete behavior), **observable** (someone can look at the system and determine pass/fail), **verifiable** (testable without subjective judgment). When an AC fails one or more checks, the agent rewrites it in place before generating the file. The check fires exactly once per `/sdd:prd` run, immediately before document generation — not during AC drafting, not during deepening rounds, not on subsequent reads of the file. After the rewrite pass, the agent writes the PRD with the cleaned-up ACs. Cross-reference any existing AC-handling language in the SKILL.md; extend, do not duplicate.

  Note: this check operates on ACs the run is about to write. It is not a re-validation of previously-written ACs in existing `docs/prd.md` — that would be `/sdd:refine`'s job. If `/sdd:prd` is rewriting an existing PRD (re-run scenario), the check applies to the in-memory ACs at write time.

- **Acceptance criteria:**
  - [x] `aacd` Before writing `docs/prd.md`, `/sdd:prd` evaluates every acceptance criterion against three checks: specific (describes a concrete behavior), observable (someone can look at the system and determine pass/fail), verifiable (testable without subjective judgment).
  - [x] `aace` When an acceptance criterion fails one or more of the three checks, `/sdd:prd` rewrites it before writing `docs/prd.md`.
  - [x] `aacf` The check runs exactly once per `/sdd:prd` run, immediately before document generation.
- **Verification:**
  - Re-read `prd/SKILL.md` and confirm a pre-write quality-check step exists, fires once per run, and immediately precedes document generation.
  - Confirm the three checks (specific / observable / verifiable) are each named and defined (or briefly characterized) — these are user-facing quality criteria, not just a label.
  - Confirm the rewrite-on-failure behavior is explicit: when an AC fails, the agent rewrites it in place before writing the file (not after, not in a separate run).
  - Confirm the check is scoped to "ACs about to be written," not a re-validation of previously-written ACs.
- **Status:** [x] Complete

---

### Item 2: `/sdd:prd` phase-split flag at 10+ epics `[PRD: aacg, aach, aaci]`

- **PRD ref:** `prd.md > Interview Command Behaviors` (the `/sdd:prd`-only phase-split flag story) `[PRD: aacg, aach, aaci]`
- **Spec ref:** `spec.md > /sdd:prd` (v2 changes — "Phase-split flag: when in-conversation epic count reaches 10+, recommend phase-splitting before generating the file. Recommendation does not block; the user may elect to keep one phase.") + `references/living-documents.md > ## PRD Health Monitoring` (the 10+ epics rule) — the spec inherits the threshold from this reference.
- **What to build:** Update `plugins/sdd/skills/prd/SKILL.md` so that before generating `docs/prd.md`, the agent counts the epics it is about to write. When the count reaches 10 or more, the agent emits a phase-split recommendation in a message to the user. The recommendation message explains phase-splitting as breaking the project into smaller pieces, each going through the full SDD workflow independently. The recommendation is informational, not blocking: if the user elects to keep one phase, the agent proceeds with PRD generation as a single PRD. Cross-reference `references/living-documents.md > ## PRD Health Monitoring` for the 10-epic threshold rule rather than restating the source.

  The recommendation message should make the trade-off concrete: phase-splitting means smaller, more digestible cycles; keeping one phase means a longer single cycle with greater coordination cost. Avoid over-prescribing the wording — a brief, accurate, non-coercive message is the goal. The user retains agency.

- **Acceptance criteria:**
  - [x] `aacg` When the in-conversation epic count reaches 10 or more, `/sdd:prd` emits a phase-split recommendation before generating `docs/prd.md`.
  - [x] `aach` The recommendation message explains phase-splitting as breaking the project into smaller pieces, each going through the full SDD workflow independently.
  - [x] `aaci` `/sdd:prd` proceeds with PRD generation on user election to keep one phase; the recommendation does not block generation.
- **Verification:**
  - Re-read `prd/SKILL.md` and confirm a pre-generation epic-count check exists, fires at 10+ epics, and emits a recommendation message.
  - Confirm the recommendation message explains phase-splitting as "breaking the project into smaller pieces, each going through the full SDD workflow independently" (literal or close paraphrase).
  - Confirm the recommendation is informational: on user election to keep one phase, `/sdd:prd` proceeds with PRD generation. No hard stop. No re-prompt loop.
  - Confirm cross-reference to `references/living-documents.md > ## PRD Health Monitoring` is present, with the threshold rule sourced rather than restated.
- **Status:** [x] Complete

---

### Item 3: `/sdd:prd` Unvetted section + marker comment at write time `[PRD: aacj, aack, aacl]`

- **PRD ref:** `prd.md > Interview Command Behaviors` (the `/sdd:prd`-only Unvetted section story) `[PRD: aacj, aack, aacl]`
- **Spec ref:** `spec.md > /sdd:prd` (v2 changes — "Generates the file with an `## Unvetted` section seeded with a marker HTML comment explaining its purpose to future agents and `/sdd:refine`. Section is empty at write time.")
- **What to build:** Update `plugins/sdd/skills/prd/SKILL.md` so that when `/sdd:prd` writes `docs/prd.md`, the output contains an `## Unvetted` section. The section is empty at write time — no list items, no inline content beyond the marker. The marker is an HTML comment placed inside the section body, explaining the section's purpose to future agents and `/sdd:refine` (what the section is for, what kinds of items go there, who processes them, why items don't go directly into the main PRD body).

  The marker comment should be self-contained and stable across PRD rewrites — `/sdd:prd` re-runs and `/sdd:refine` should both leave the marker intact when the section content changes. The actual literal wording of the marker is up to the agent at write time; what the AC pins is that the marker is present and explains the section's purpose. A reasonable form: a single HTML comment block of 2-5 lines naming the section's purpose, the kinds of items it holds, and the command that processes them.

- **Acceptance criteria:**
  - [x] `aacj` `/sdd:prd` writes `docs/prd.md` with an `## Unvetted` section present.
  - [x] `aack` The Unvetted section contains no list items at the moment `/sdd:prd` writes the file.
  - [x] `aacl` The Unvetted section contains a marker comment in the file body explaining the section's purpose to future agents and `/sdd:refine`.
- **Verification:**
  - Re-read `prd/SKILL.md` and confirm the PRD-writing logic explicitly emits an `## Unvetted` section header.
  - Confirm the section is empty at write time — no list items, no inline content other than the marker.
  - Confirm the marker is an HTML comment placed inside the section body, and explains the section's purpose (what it's for, kinds of items, who processes them — wording flexible, intent fixed).
  - Confirm a sample generation (mentally walk through, or check against the existing `docs/prd.md` in this project) would produce the expected shape: `## Unvetted\n\n<!-- ... -->\n` then the next `##`-level section.
- **Status:** [x] Complete

---

## Notes

- All 9 ACs of the `/sdd:prd` Polish epic close in this sprint: `aacd`–`aacl`. No collisions with closed IDs from Sprints 1-12. PRD Unvetted section is empty.
- **PRD checkoff watch.** Per Sprint 11's wrap-up watch item, sprint close must propagate AC checkoffs to `docs/prd.md` immediately. Sprint 12 followed this discipline; Sprint 13 should too.
- **No FB mapping.** None of `aacd`–`aacl` correspond to a row in `docs/v2-verification.md`. The `/sdd:prd` Polish epic is derived from the PRD walkthrough, not from the v1 feedback pile. No `[FB: ...]` tags on items; `/sdd:build` wrap-up writes no v2-verification row updates for this sprint.
- **Single-file sprint.** Every edit lands in `plugins/sdd/skills/prd/SKILL.md`. Sequential autonomous subagents are the right shape — parallel would risk edit collisions on the same file. Three items, three subagents in sequence, each adding a focused section to `prd/SKILL.md`.
- **Existing `docs/prd.md` is already valid.** This project's `docs/prd.md` already has an `## Unvetted` section (currently empty with a marker comment at line 363). Item 3's edits affect future `/sdd:prd` runs — they don't require any change to this project's PRD. Same for the AC quality check (Item 1) and phase-split flag (Item 2): they are command-behavior changes, not project-document edits.
- **No new design calls.** Sprint 13 is pure implementation. Every behavior is already resolved in `docs/spec.md > /sdd:prd` and `references/living-documents.md > ## PRD Health Monitoring`.
- **Cached marketplace plugin is still v1.** Same caveat as every prior sprint. Sprint 13's edits ship to disk in one SKILL.md file but aren't live as agent behavior until the maintainer reinstalls.
- **No deepening rounds requested at planning time.** Pre-vetted ACs, fully-specified spec material, single-file scope. Topics a round would have covered (deferred to `/sdd:build` implementation context): item granularity (kept at 3 items mirroring the three sub-stories; alternative was one bundled item), phase-split-recommendation wording (close paraphrase fine), Unvetted marker exact wording (resolved at write time by the agent), and guardrails against over-aggressive AC rewrites in Item 1 (best discovered at build time).
- **Natural next-sprint candidates after Sprint 13:**
  - Process Notes Growth remainder (`aagf`–`aagn`, 9 ACs) — wire `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan` to per-phase notes files; `/sdd:retro` selective reading.
  - Automatic Backlog Generation (`aafs`–`aagd`, 12 ACs) — cross-cutting across 5 planning commands.
  - `/sdd:polish` re-open mechanism (`aaev`–`aaex`, 3 ACs) — small.
  - `/sdd:retro` at project close (`aaey`–`aafe`, 6 ACs) — terminal command, better near v2 GA.
  - v1→v2 Migration guide (`aago`–`aahi`, 21 ACs).
  - v2 Release Verification remaining (~13 ACs).
