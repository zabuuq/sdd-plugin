# Sprint 14

**Theme:** Process Notes Growth remainder — three sub-stories that close the entire Process Notes Growth epic: per-phase notes file writes wired into every planning + sprint command, `/sdd:retro` selective reading, and the append-only invariant made explicit in `sdd-guide`. Nine ACs (`aagf`–`aagn`) close.

**Tangible outcome:** After this sprint:
- Every planning phase has its own dedicated process-notes file the phase's command owns end-to-end. `/sdd:scope` writes only to `process-notes-scope.md`, `/sdd:prd` writes only to `process-notes-prd.md`, `/sdd:spec` writes only to `process-notes-spec.md`, and `/sdd:plan` + `/sdd:build` share `process-notes-sprint-N.md`. No file is shared across two planning commands.
- `/sdd:retro` reads each phase's process-notes file as a separate read during the retro interview (not concatenated at startup), and proceeds silently when a phase's file is absent.
- The append-only invariant is explicit in `sdd-guide/SKILL.md > ## Process Notes`: each phase's command writes real-time entries in order and does not modify or remove its own entries after writing. Mid-phase summarization for multi-session continuity is `/sdd:pause`'s job (resume file), not edits to the process-notes file.

The three sub-stories are independent — they touch different files with zero overlap. The Process Notes Growth epic closes with this sprint (`/sdd:discovery`'s slice, `aage`, already shipped earlier).

**Out of scope:**
- `/sdd:polish` re-open (`aaev`–`aaex`, 3 ACs). Small, can ride alongside a later sprint.
- `/sdd:retro` at project close (`aaey`–`aafe`, 6 ACs). Terminal-command work; better near v2 GA.
- Automatic Backlog Generation (`aafs`–`aagd`, 12 ACs). Cross-cutting across 5 planning commands; bigger lift.
- v1→v2 Migration (`aago`–`aahi`, 21 ACs). Single-file but lower priority until v2 nears GA.
- v2 Release Verification remaining (~13 ACs). Seeding / persistence / manual-checkoff conventions.
- Legacy `process-notes.md` migration. Covered by the Migration epic (`aagy`, `aagz`), not this sprint.

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** The three items touch disjoint file sets (Item 1: 4-5 phase SKILL.md files; Item 2: `retro/SKILL.md`; Item 3: `sdd-guide/SKILL.md`), so parallel would be structurally safe — but sequential is consistent with the Sprint 5-13 pattern and keeps debugging straightforward. Sequential dispatch.

1. Item 1 (per-phase notes writes) — lands the core wire-up across the phase commands; largest of the three by file count.
2. Item 2 (`/sdd:retro` selective reading) — depends on Item 1's per-phase file names being settled; lands next.
3. Item 3 (append-only discipline) — independent attestation in `sdd-guide`; lands last so it sees the new per-phase file names already in place.

## Checklist

### Item 1: Per-phase process-notes file writes `[PRD: aagf, aagg, aagh, aagi, aagj]`

- **PRD ref:** `prd.md > Process Notes Growth` (the per-phase files user story) `[PRD: aagf, aagg, aagh, aagi, aagj]`
- **Spec ref:** `spec.md > Architecture Overview > Data Flow` (the per-phase process-notes file convention in the data-flow diagram lines 87, 113-114). The runtime convention is documented in the spec's data flow; this item makes the convention explicit in each command's SKILL.md.
- **What to build:** Update the following SKILL.md files so each phase command writes to its own dedicated process-notes file:
  - `plugins/sdd/skills/scope/SKILL.md` → writes to `process-notes-scope.md` in the project root.
  - `plugins/sdd/skills/prd/SKILL.md` → writes to `process-notes-prd.md` in the project root.
  - `plugins/sdd/skills/spec/SKILL.md` → writes to `process-notes-spec.md` in the project root.
  - `plugins/sdd/skills/plan/SKILL.md` (the v2 rename of `/sdd:sprint`; if `plan/SKILL.md` is not yet authored separately, edit whichever SKILL.md currently performs sprint-planning duties) → writes to `process-notes-sprint-N.md` in the project root where N is the sprint number being planned.
  - `plugins/sdd/skills/build/SKILL.md` → writes to `process-notes-sprint-N.md` in the project root where N is the current sprint number. (`/sdd:plan` and `/sdd:build` share this file by design — `aagi`.)

  Each SKILL.md change is a direct edit to the "process notes" or "wrap-up" section: change the file name from `process-notes.md` (or any other prior target) to the per-phase / per-sprint name above. After editing, cross-check that no file is shared by more than one planning command — the planning commands are `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`. (`/sdd:build` shares `process-notes-sprint-N.md` with `/sdd:plan` by spec — `aagj` reads in the planning-command sense, so build sharing with plan is correct.)

  Note: `/sdd:discovery` already ships its slice (`aage`) — `discovery/SKILL.md` already writes to `process-notes-discovery.md`. Confirm that in passing; do not re-edit unless needed.

  Note: stale prose in the same SKILL.md files (e.g., legacy mentions of `process-notes.md`, ordering rules, or templates that reference the old single-file pattern) should be updated to match the per-phase convention if doing so is mechanical. Substantive content edits are out of scope — fix the write target and any surrounding sentence that names the file by name. Do not refactor the surrounding sections.

- **Acceptance criteria:**
  - [x] `aagf` `/sdd:scope` writes to `process-notes-scope.md` in the project root.
  - [x] `aagg` `/sdd:prd` writes to `process-notes-prd.md` in the project root.
  - [x] `aagh` `/sdd:spec` writes to `process-notes-spec.md` in the project root.
  - [x] `aagi` `/sdd:plan` and `/sdd:build` write to `process-notes-sprint-N.md` in the project root.
  - [x] `aagj` No process-notes file is written to by more than one planning command.
- **Verification:**
  - Re-read each modified SKILL.md and confirm the write target is the per-phase file name listed above (no stray references to `process-notes.md` remain in the write step).
  - Cross-check: grep across `plugins/sdd/skills/*/SKILL.md` for `process-notes-` to confirm each planning command's SKILL.md writes to exactly one per-phase file, and no two planning commands write to the same file. `/sdd:plan` and `/sdd:build` both pointing at `process-notes-sprint-N.md` is intended (shared file; `aagi`); the `aagj` invariant covers the **planning-command** set.
  - Confirm `/sdd:discovery`'s SKILL.md write target is `process-notes-discovery.md` (already shipped via `aage`); flag if discovered otherwise.
- **Status:** [x] Complete

---

### Item 2: `/sdd:retro` selective process-notes reading `[PRD: aagk, aagl]`

- **PRD ref:** `prd.md > Process Notes Growth` (the `/sdd:retro` selective reading user story) `[PRD: aagk, aagl]`
- **Spec ref:** `spec.md > /sdd:retro` (lines 481-482 — "Reads `process-notes-*.md` files selectively (one phase at a time) rather than concatenating all at startup, to avoid loading the full project history." + "Missing per-phase process-notes files for skipped phases produce no warning.")
- **What to build:** Update `plugins/sdd/skills/retro/SKILL.md` so that:
  - The retro loading section does NOT include `process-notes-*.md` files in the startup-loading set. The retro interview reads each phase's process-notes file as a separate, intentional read during the corresponding phase's review block, not at startup and not concatenated.
  - When a phase's `process-notes-{phase}.md` (or `process-notes-sprint-N.md` for sprint phases) is absent — typically because the user skipped the phase entirely — `/sdd:retro` proceeds without emitting a warning for that absence. Treat the missing file as "this phase produced no notes" and move on.

  The change is mechanical against the existing retro/SKILL.md loading + interview-flow sections. Do not refactor surrounding sections; do not add new retro behaviors beyond what these two ACs require.

- **Acceptance criteria:**
  - [x] `aagk` `/sdd:retro` reads each phase's process-notes file as a separate read during the retro interview, rather than concatenating all process-notes files at startup.
  - [x] `aagl` When a process-notes file for a phase the project skipped is absent, `/sdd:retro` proceeds without emitting a warning for that absence.
- **Verification:**
  - Re-read `retro/SKILL.md` and confirm the startup loading section does not list `process-notes-*.md` as a pre-loaded artifact set.
  - Confirm the interview flow names per-phase process-notes reads as separate reads at the point each phase is reviewed.
  - Confirm a sentence or rule explicitly states that absent per-phase process-notes files are handled silently (no warning, no error path for "this phase was skipped").
- **Status:** [x] Complete

---

### Item 3: Append-only process-notes discipline `[PRD: aagm, aagn]`

- **PRD ref:** `prd.md > Process Notes Growth` (the append-only within-phase user story) `[PRD: aagm, aagn]`
- **Spec ref:** `spec.md > Architecture Overview > Data Flow` (per-phase + append-only data-flow convention, lines 113-114) + `plugins/sdd/skills/sdd-guide/references/pause-resume.md` (mid-phase summarization is `/sdd:pause`'s job via the resume file).
- **What to build:** Update `plugins/sdd/skills/sdd-guide/SKILL.md > ## Process Notes` to add an explicit append-only-within-phase rule and a pointer for mid-phase summarization:
  - **Append-only rule:** Each phase's command writes real-time entries to its process-notes file in the order written, and does not modify or remove its own entries after writing. End-of-phase state is the accumulated real-time entries — nothing is pruned, edited, or summarized in place by the writing command.
  - **Mid-phase summarization pointer:** When the user needs a mid-phase summary for multi-session continuity, the responsibility belongs to `/sdd:pause` — it writes a structured resume file (`docs/<command>-resume.md`) per `references/pause-resume.md`. Phase commands do not produce mid-phase summaries by editing their own process-notes files.

  Keep the addition tight — one or two short paragraphs in the existing `## Process Notes` section. Cross-reference `references/pause-resume.md` rather than restate its content. Do not add a new reference file; this lands in the existing sdd-guide core section.

  Note: this AC is about making the invariant explicit in `sdd-guide` so the convention can't drift in future SKILL.md edits. Individual command SKILL.md files do not need their own append-only language — the sdd-guide rule applies to all of them.

- **Acceptance criteria:**
  - [x] `aagm` At end of phase, each phase's process-notes file contains every real-time entry written by the phase's command, in the order written, with no entries modified or removed by the same command after writing.
  - [x] `aagn` Mid-phase summarization for multi-session continuity is handled by `/sdd:pause` writing to its resume file, not by edits to the phase's process-notes file.
- **Verification:**
  - Re-read `plugins/sdd/skills/sdd-guide/SKILL.md > ## Process Notes` and confirm an explicit append-only-within-phase rule is present, stated as: real-time entries written in order, no entries modified or removed by the same command after writing.
  - Confirm a mid-phase-summarization pointer to `/sdd:pause` and `references/pause-resume.md` is present, with the rule that phase commands do not produce mid-phase summaries by editing their own process-notes files.
  - Confirm no duplication of `references/pause-resume.md` content — cross-reference only.
- **Status:** [x] Complete

---

## Notes

- All 9 remaining Process Notes Growth ACs close in this sprint: `aagf`–`aagn`. With `aage` already shipped via `/sdd:discovery`, the entire Process Notes Growth epic closes at sprint end.
- **PRD checkoff watch.** Per Sprint 11/12/13 discipline, sprint close must propagate AC checkoffs to `docs/prd.md` immediately. Sprint 14 should too.
- **No FB mapping.** None of `aagf`–`aagn` correspond to a row in `docs/v2-verification.md`. Process Notes Growth is PRD-walkthrough-derived, not v1-feedback-derived. No `[FB: ...]` tags on items; `/sdd:build` wrap-up writes no v2-verification row updates for this sprint.
- **Multi-file sprint (Item 1).** Item 1 touches 4-5 SKILL.md files (`scope`, `prd`, `spec`, `plan`, `build`). The edits per file are mechanical (rename the write target), so a single subagent should be able to handle the whole item in one pass. If the subagent prefers to split the work across files internally, that's fine — the AC verification is per-command, not per-edit.
- **No new design calls.** Sprint 14 is pure implementation. Every behavior is already resolved in `docs/spec.md > Architecture Overview > Data Flow` and `docs/spec.md > /sdd:retro`. The append-only rule is a one-paragraph addition to an existing section.
- **Cached marketplace plugin is still v1.** Same caveat as every prior sprint. Sprint 14's edits ship to disk in 6 SKILL.md files but aren't live as agent behavior until the maintainer reinstalls.
- **No deepening rounds requested at planning time.** Pre-vetted ACs, fully-specified spec material, uniform per-phase pattern. Topics a round would have covered (deferred to `/sdd:build` implementation context):
  - How aggressive to be with stray `process-notes.md` references in surrounding prose (Item 1) — keep mechanical, leave substantive prose for a later cleanup pass.
  - Whether the append-only rule lands in sdd-guide alone or also in a new reference file (Item 3) — sdd-guide alone is cleaner; a new reference file would be overkill for a single rule.
  - Whether `/sdd:build` needs explicit instruction edits or already writes to `process-notes-sprint-N.md` by convention — verify at build time; convention has been in place since Sprint 11.
  - Whether `plan/SKILL.md` exists yet or whether the v2 rename has not yet materialized as a separate file — verify at build time; if not yet authored, edit whichever SKILL.md currently performs sprint-planning duties.
- **Natural next-sprint candidates after Sprint 14:**
  - `/sdd:polish` re-open mechanism (`aaev`–`aaex`, 3 ACs) — small.
  - Automatic Backlog Generation (`aafs`–`aagd`, 12 ACs) — cross-cutting across 5 planning commands.
  - `/sdd:retro` at project close (`aaey`–`aafe`, 6 ACs) — terminal command, better near v2 GA.
  - v1→v2 Migration guide (`aago`–`aahi`, 21 ACs).
  - v2 Release Verification remaining (~13 ACs).
