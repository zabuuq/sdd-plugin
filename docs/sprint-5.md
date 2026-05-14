# Sprint 5

**Theme:** `/sdd:build` wrap-up phase implementation. The single most-blocking SDD v2 deliverable still on the ground — wrap-up unlocks Bug Fix 2 (`aaad`–`aaag`), the `docs/v2-verification.md` Shipped-column auto-write (`aahk`, `aahv`), `/sdd:retro` project-close gating (`aafa`), and the cohesive one-ceremony close-out promised by the v2 scope's third "done" pillar.

**Tangible outcome:** After this sprint, `/sdd:build` no longer ends silently when the last sprint item is checked. Instead it runs a full wrap-up: emits the literal `Satisfied with this sprint, or items to iterate on?` question; surfaces any `[iteration-candidate]` entries written during checklist execution; handles disposition (`discard` / `capture as tech-debt` / `actually iterate`); on satisfied → checks off PRD ACs per Bug Fix 2 (cross-cutting + non-split cases), splits stories for unfinished items with user confirmation, runs the "anything notable?" prompt, writes `process-notes-sprint-N.md` with a structured `[close-sprint-manifest]` block, updates `docs/v2-verification.md` Shipped column for closed items carrying `[FB: ...]` (gated on file presence), and emits the next-command recommendation (`/sdd:plan`, `/sdd:refine`, or `/sdd:retro`) based on PRD state. By the end of the sprint, dogfooding this very sprint should trigger a real wrap-up run.

**Out of scope:**
- `/sdd:polish` re-open mechanism (`aaev`, `aaew`, `aaex`) — implemented by `/sdd:polish`, not `/sdd:build`. Reads the `[close-sprint-manifest]` block this sprint produces, but the parsing/reversal logic ships with `/sdd:polish`. Deferred to a polish-focused sprint.
- `/sdd:plan` implementation (`aado`–`aaeg`) — separate command, separate sprint.
- Other interview command behaviors (clarifying questions don't advance, deepening-round caps, etc.) — these apply across all interview commands, but `/sdd:build` is not an interview command, so they don't gate this sprint.
- `aaho`, `aahp`, `aahu`, `aahw`, `aahx` — negation/preservation ACs about `docs/v2-verification.md` (never written-as-verified, never gates release, sdd-plugin-only, never deleted, future-version pattern). All satisfied by what the SKILL.md does **not** do; verified by absence. No active implementation work for them.
- `aahl`, `aahm` — structural / spec-deferral ACs already satisfied by sprint 4's seeded table and the spec itself. No work in this sprint.

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** All three items edit the same file (`plugins/sdd/skills/build/SKILL.md`). Items must execute in numbered order — Item 2 builds on the wrap-up entry point introduced in Item 1; Item 3 routes the outcome of Item 2's close-sprint actions. Autonomous mode dispatches them sequentially, not in parallel.

## Checklist

### Item 1: Wrap-up entry + iteration-candidate handling

- **PRD ref:** `prd.md > Sprint Loop` (satisfaction question + iteration-candidate disposition stories) `[PRD: aaeh, aaei, aaej, aaek, aael, aaem, aaen, aaeo, aaep]`
- **Spec ref:** `spec.md > Command Skills > /sdd:build` (wrap-up phase, steps 1–4), `spec.md > Cross-Cutting Mechanisms > Iteration-Candidate Markers`, `spec.md > Cross-Cutting Mechanisms > Sprint-Item Tags` (for the `[iteration-candidate]` parser)
- **What to build:** Append a `## Wrap-Up Phase` section to `plugins/sdd/skills/build/SKILL.md` (positioned after the autonomous/step-by-step mode sections, before "Re-Entry After /clear"). Implements the wrap-up entry point fired when the final sprint checklist item is marked complete. Specific content:
  1. **Trigger:** wrap-up fires immediately after the final sprint item's Status flips to `[x]` (both step-by-step and autonomous modes; replaces the current "Sprint complete. Run /sdd:polish..." stop message).
  2. **Satisfaction question:** emit the literal text `Satisfied with this sprint, or items to iterate on?` as a single message.
  3. **Iteration-candidate surfacing:** before asking, scan `process-notes-sprint-N.md` for `[iteration-candidate]` entries (parser per `references/sprint-tags.md`) **written before wrap-up began** — entries written during checklist execution, not entries the wrap-up itself might write. If candidates exist, surface them in the same message as the satisfaction question with a brief one-line each.
  4. **Branch routing:**
     - **"items to iterate":** recommend running `/sdd:polish`. Sprint stays open. No close-sprint actions. Wrap-up exits.
     - **"satisfied":** proceed to disposition for any surfaced candidates (step 5).
  5. **Per-candidate disposition** (only if candidates were surfaced): ask the user to assign one of `discard` / `capture as tech-debt` / `actually iterate` for each candidate. One question per candidate; do not batch.
  6. **Disposition routing:**
     - **Any `actually iterate`:** switch to the "items to iterate" branch — recommend `/sdd:polish`, leave sprint open, no close-sprint actions. Wrap-up exits.
     - **`capture as tech-debt`:** record the candidate as a tech-debt entry in `process-notes-sprint-N.md` during close-sprint (Item 2 picks this up).
     - **`discard`:** no further action beyond noting the discard decision in `process-notes-sprint-N.md` (Item 2 picks this up).
     - **No remaining iterate-now candidates:** proceed to close-sprint actions (Item 2 picks this up).
  7. **Wrap-up phase does NOT auto-commit.** Consistent with `/sdd:build`'s no-auto-commit posture (per `docs/spec.md > Command Skills > /sdd:build` and AGENTS.md).
- **Acceptance criteria:**
  - [x] `aaeh` After the final sprint checklist item is marked complete, `/sdd:build` emits the literal question "Satisfied with this sprint, or items to iterate on?" as the wrap-up entry point.
  - [x] `aaei` When `process-notes-sprint-N.md` contains `[iteration-candidate]`-tagged entries written before wrap-up began, `/sdd:build` surfaces those candidates alongside the satisfaction question.
  - [x] `aaej` `/sdd:build` surfaces an iteration candidate at wrap-up only when a `[iteration-candidate]` entry exists in `process-notes-sprint-N.md` written before wrap-up began.
  - [x] `aaek` When the user answers "satisfied," `/sdd:build` proceeds with close-sprint behavior (handed to Item 2).
  - [x] `aael` When the user answers "items to iterate," `/sdd:build` recommends running `/sdd:polish` and leaves the sprint file open (no close-sprint actions are performed).
  - [x] `aaem` When the user answers "satisfied" and tagged iteration candidates exist, `/sdd:build` asks the user to assign a disposition for each surfaced candidate from the set {discard, capture as tech-debt, actually iterate} before proceeding with close-sprint behavior.
  - [x] `aaen` When the user assigns "actually iterate" to any candidate during disposition, `/sdd:build` switches to the "items to iterate" branch and does not perform close-sprint actions.
  - [x] `aaeo` When the user assigns "capture as tech-debt" to a candidate, the candidate is recorded as a tech-debt entry in `process-notes-sprint-N.md` during close-sprint (the actual write happens in Item 2; Item 1 records the disposition decision).
  - [x] `aaep` When the user assigns "discard" to a candidate, no further action is taken beyond noting the discard decision in `process-notes-sprint-N.md` (Item 2 writes the note).
- **Verification:**
  - Read the updated SKILL.md end-to-end and confirm the new `## Wrap-Up Phase` section satisfies each AC.
  - Walk the wrap-up branches mentally against each AC: satisfied + no candidates → close-sprint; satisfied + candidates with all-tech-debt → close-sprint; satisfied + candidates with any-iterate → polish recommendation; items-to-iterate → polish recommendation.
  - Confirm the literal text "Satisfied with this sprint, or items to iterate on?" appears in the SKILL.md (not paraphrased).
  - Confirm one-question-at-a-time discipline for disposition (per shared behavior).
  - Confirm wrap-up reads `[iteration-candidate]` markers via the parser in `references/sprint-tags.md`, not via a new ad-hoc regex.
- **Status:** [x] Complete

---

### Item 2: Close-sprint actions

- **PRD ref:** `prd.md > Bug Fixes` (Bug Fix 2 — PRD AC auto-checkoff) `[PRD: aaad, aaae, aaaf, aaag]` + `prd.md > Sprint Loop` (close-sprint story) `[PRD: aaeq, aaer, aaes, aaet]`
- **Spec ref:** `spec.md > Command Skills > /sdd:build` (wrap-up phase, step 5 — close-sprint actions), `spec.md > Cross-Cutting Mechanisms > Close-Sprint Manifest`, `spec.md > Cross-Cutting Mechanisms > PRD Acceptance Criteria IDs`, `spec.md > Cross-Cutting Mechanisms > Sprint-Item Tags`
- **What to build:** Add the close-sprint actions to the `## Wrap-Up Phase` section in `plugins/sdd/skills/build/SKILL.md` (continuing from where Item 1 left off — close-sprint runs when Item 1 reaches the "satisfied, no iterate-now candidates" terminus). Five actions, in this order:
  1. **PRD AC checkoff** (Bug Fix 2 mechanics):
     - For each closed sprint item in the current sprint, parse its `[PRD: id1, id2, ...]` tag (parser in `references/sprint-tags.md`).
     - For each referenced PRD AC ID:
       - **Non-split case:** if the sprint item maps fully to a story (no story-splitting occurred for that story across any sprint), check off every PRD AC the item references.
       - **Cross-cutting case:** if the AC is referenced by sprint items across multiple sprint files, only check it off when every sprint item across every sprint file that references it has closed. Scan all `docs/sprint-*.md` files for the AC ID; if any unchecked reference exists, leave the AC unchecked.
     - Update `docs/prd.md` in place, changing `- [ ] \`abcd\` ...` to `- [x] \`abcd\` ...` for each newly-checked AC.
     - Record every AC checked off in this step for inclusion in the `[close-sprint-manifest]` block.
  2. **Story splits for unfinished items:**
     - Identify any sprint items still showing `[ ]` Status at this point (unfinished).
     - For each unfinished item, emit a single prompt asking the user whether to split the story (one question per item).
     - On confirmation: move the unfinished item's unchecked PRD ACs to a new story in `docs/prd.md`. Generate fresh 4-char lowercase alphabetic IDs for any new ACs introduced by the split (collision-checked against existing PRD IDs). Old IDs that move to the new story keep their IDs (stable across reorders).
     - On rejection: leave the item as-is in the sprint checklist; it will carry forward to a future sprint.
     - Record every split (original story anchor → new story anchor, AC IDs) for inclusion in the `[close-sprint-manifest]` block.
  3. **"Anything notable?" prompt:** emit exactly one prompt covering quality concerns, tech debt, and surprises. Single open-ended question. User responses logged to process notes in step 5.
  4. **Process notes write:** append to `process-notes-sprint-N.md` a wrap-up section containing:
     - Decisions made during the sprint and rationale.
     - Pushback from either side (user or agent).
     - Surprises during execution.
     - Notable items captured in the "anything notable?" prompt.
     - Tech-debt entries (from Item 1's `capture as tech-debt` dispositions, formatted distinctly so future commands can locate them).
     - Discard decisions for any candidates (from Item 1's `discard` dispositions, brief one-line each).
     - The structured `[close-sprint-manifest]` block per `spec.md > Cross-Cutting Mechanisms > Close-Sprint Manifest`. Block contents:
       - Timestamp (ISO-8601).
       - PRD ACs checked: comma-separated AC ID list from step 1.
       - Story splits: original-story-anchor → new-story-anchor: ac-ids list, semicolon-separated for multiples.
       - Iteration candidate dispositions: candidate-summary: disposition, semicolon-separated.
       - Tech-debt entries: count (informational).
       - v2-verification rows updated: comma-separated FB-ID list (informational; populated by Item 3 when it runs — Item 2's manifest writer may leave this field empty or hand off to Item 3 to append).
  5. **Hand off to Item 3:** after writing process notes including the manifest, control passes to Item 3's routing logic. Item 2 does NOT emit the next-command recommendation — that's Item 3's responsibility.
- **Acceptance criteria:**
  - [x] `aaad` When `/sdd:build`'s wrap-up phase confirms a sprint item is fully complete and no story-split is required, it checks off every PRD acceptance criterion that sprint item references in `docs/prd.md`.
  - [x] `aaae` When a sprint item maps to only some of the acceptance criteria of a larger cross-cutting PRD story, only the satisfied criteria are checked; criteria requiring contributions from any sprint item across any sprint file that is still incomplete remain unchecked.
  - [x] `aaaf` When the final sprint item contributing to a cross-cutting PRD acceptance criterion completes, that criterion gets checked during that sprint's `/sdd:build` wrap-up.
  - [x] `aaag` After `/sdd:build`'s wrap-up runs on a sprint, no PRD acceptance criterion remains unchecked while every sprint-item AC across all sprint files that references it is checked.
  - [x] `aaeq` On close-sprint, `/sdd:build` performs PRD AC checkoff per Bug Fix 2 behavior.
  - [x] `aaer` On close-sprint, `/sdd:build` identifies any unfinished items in the sprint checklist, offers story-splitting with explicit user confirmation per item, and updates `docs/prd.md` to reflect each confirmed split.
  - [x] `aaes` On close-sprint, `/sdd:build` emits exactly one "anything notable?" prompt covering quality concerns, tech debt, and surprises.
  - [x] `aaet` On close-sprint, `/sdd:build` writes `process-notes-sprint-N.md` containing decisions made during the sprint, pushback from either side, surprises, notable items captured in the wrap-up, and dispositions assigned to any iteration candidates.
  - [x] Close-sprint emits a `[close-sprint-manifest]` block conforming to the format in `spec.md > Cross-Cutting Mechanisms > Close-Sprint Manifest`.
- **Verification:**
  - Read the close-sprint actions section in SKILL.md and confirm each AC is satisfied.
  - Trace the Bug Fix 2 cross-cutting case mentally: an AC referenced by sprint items in two different sprint files; close sprint N first → AC stays unchecked; close sprint M (the other one) → AC checks off in sprint M's wrap-up.
  - Confirm AC ID collision-checking is specified for new IDs (split-generated ACs).
  - Confirm story splits update both `docs/prd.md` AND the sprint file (move unchecked item to a new story; leave a marker or note in the original sprint file noting the split).
  - Confirm the close-sprint-manifest format matches `spec.md > Cross-Cutting Mechanisms > Close-Sprint Manifest` field for field.
  - Confirm "anything notable?" is exactly one prompt, not three separate ones.
- **Status:** [x] Complete

---

### Item 3: Next-command routing + v2-verification writes + /retro gating

- **PRD ref:** `prd.md > Sprint Loop` (next-command recommendation story) `[PRD: aaeu]` + `prd.md > /sdd:retro at Project Close` (/sdd:build /retro gating) `[PRD: aafa]` + `prd.md > v2 Release Verification` (v2-verification writes + activation gate) `[PRD: aahk, aahv]`
- **Spec ref:** `spec.md > Command Skills > /sdd:build` (wrap-up phase, step 5 v2-verification writes + step 6 next-command recommendation), `spec.md > Plugin-Project-Specific Artifacts > docs/v2-verification.md` (Shipped column write semantics + activation gate)
- **What to build:** Add the closing two actions to the `## Wrap-Up Phase` section in `plugins/sdd/skills/build/SKILL.md`, picking up from where Item 2 hands off (after process notes + manifest are written):
  1. **v2-verification row updates** (sdd-plugin-specific behavior):
     - **Activation gate:** check whether `docs/v2-verification.md` exists in the running project's `docs/` directory. If absent, **skip this step entirely** (no warning, no fallback write). The file's presence in the current project is the sole activation signal.
     - When the file exists:
       - For each closed sprint item carrying a `[FB: FB-NNN]` tag (parser in `references/sprint-tags.md`):
         - Locate the row in `docs/v2-verification.md` whose `ID` column matches `FB-NNN`.
         - Append `sprint N, item M` to the row's `Shipped (sprint/item)` column (where N is `currentSprint`, M is the sprint-item ordinal). If the column already contains a value (cross-sprint contribution), append with `; ` separator: `sprint 4, item 2; sprint 5, item 1`.
         - The `Notes` column MAY receive a brief auto-note (e.g., the closed sprint item's title or a one-line summary). Auto-note is optional; user remains free to edit Notes manually.
         - The `Verified` column is **never written** by `/sdd:build`. Verified stays `[ ]` until the user manually checks it. Negation AC `aaho` is preserved.
       - Update the close-sprint manifest entry's `v2-verification rows updated` field with the comma-separated list of FB-IDs touched.
     - If a `[FB: ...]` tag references an FB-NNN ID that has no row in `docs/v2-verification.md`, emit a single warning naming the missing FB-NNN. Do not auto-create the row; row creation is `/sdd:spec` seeding only. The warning is informational; the wrap-up continues.
  2. **Next-command recommendation** (the final wrap-up output):
     - Re-read `docs/prd.md`'s current state (after this sprint's PRD checkoff and any splits).
     - Determine the project's wrap-up state:
       - **All PRD ACs checked AND Unvetted section empty:** recommend `/sdd:retro` (project-close path). This is the only branch that recommends `/sdd:retro`; AC `aafa` is the gate.
       - **Unvetted section contains items:** recommend `/sdd:refine`. The Unvetted backlog should be processed before the next sprint plans.
       - **Neither:** recommend `/sdd:plan` (continue the sprint loop).
     - Emit the recommendation with the standard between-commands handoff (run `/clear`, then invoke the named next command). Wording follows `spec.md > Cross-Cutting Mechanisms > End-of-Command Handoff`.
- **Acceptance criteria:**
  - [x] `aaeu` On close-sprint, after writing the process notes file, `/sdd:build` emits a next-command recommendation based on the PRD state check (continue sprint loop → `/sdd:plan`, run `/sdd:refine`, or run `/sdd:retro`).
  - [x] `aafa` `/sdd:build`'s wrap-up recommends `/sdd:retro` only when its PRD checkoff causes every acceptance criterion in `docs/prd.md` to be checked AND the Unvetted section is empty.
  - [x] `aahk` When a sprint item implementing an in-scope feedback item completes (carries a `[FB: ...]` tag and closes during wrap-up), `/sdd:build` writes/updates a row in `docs/v2-verification.md` for that feedback item.
  - [x] `aahv` The v2-verification implementing-command write behavior fires only when `docs/v2-verification.md` already exists in the running project's `docs/` directory; the write is skipped without warning when the file is absent.
  - [x] The `Verified` column in `docs/v2-verification.md` is never written by `/sdd:build` (negation AC `aaho` preserved; verified by absence in the SKILL.md spec).
  - [x] The next-command recommendation uses the End-of-Command Handoff template wording (run `/clear`, then invoke the recommended next command).
- **Verification:**
  - Read the closing wrap-up actions section in SKILL.md and confirm each AC is satisfied.
  - Confirm the activation gate logic: missing `docs/v2-verification.md` → skip silently; present → write/update rows for `[FB: ...]` items.
  - Confirm the `Verified` column is explicitly mentioned as never-written (so future readers don't misread silence as ambiguity).
  - Confirm the next-command branches: all-checked-empty-Unvetted → retro; Unvetted-non-empty → refine; else → plan.
  - Confirm the recommendation uses the standard handoff wording from `spec.md > Cross-Cutting Mechanisms > End-of-Command Handoff`.
- **Status:** [x] Complete

---

## Notes

- All three items edit the same file: `plugins/sdd/skills/build/SKILL.md`. Sequential execution is mandatory. Autonomous mode dispatches them one at a time.
- Sprint 5 closes 21 PRD ACs: 9 (Item 1: `aaeh`–`aaep`) + 8 (Item 2: `aaad`–`aaag`, `aaeq`–`aaet`) + 4 (Item 3: `aaeu`, `aafa`, `aahk`, `aahv`).
- `[FB: ...]` tagging: this sprint's items do **not** carry `[FB: ...]` tags. Sprint 5 implements Bug Fix 2 (`aaad`–`aaag`), which corresponds to v2-verification row FB-002 in `docs/v2-verification.md`. Tagging Sprint 5 Item 2 with `[FB: FB-002]` would be appropriate, **but** the wrap-up that processes `[FB: ...]` tags is itself being built in this sprint — sprint 5 is the bootstrap. So no `[FB: ...]` tags. Sprint 5's contribution to FB-002 will be manually noted when `/sdd:build`'s own wrap-up first dogfoods on a later sprint.
- After sprint 5: the natural next-sprint candidates are (a) `/sdd:plan` command implementation (`aado`–`aaeg`, 13 ACs) — the planning side of the sprint loop, with sprint-mode memory and validation; (b) `/sdd:polish` re-open mechanism (`aaev`–`aaex`, 3 ACs) — small, depends on Item 2's close-sprint-manifest format; (c) `/sdd:discovery` command implementation (9 ACs). Each is independent. Choose based on what's needed first.
- The wrap-up implementation is markdown-only (SKILL.md edits). No code, no runtime. Verification is by SKILL.md re-read against ACs; end-to-end dogfooding fires when /build runs against a future sprint.
