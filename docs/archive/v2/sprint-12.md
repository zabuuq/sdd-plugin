# Sprint 12

**Theme:** Right-Sizing Wire-Through — small-project detection + cross-command read/re-evaluation + skippable Phase 1 beats + deepening-round defaults. Eight ACs (`aabr`–`aaby`) close — the entire Right-Sizing epic.

**Tangible outcome:** After this sprint, a user starting a genuinely small project (one feature, no external users, no third-party integrations, concise framing) gets a measurably tighter interview cadence. `/sdd:scope` writes the authoritative `smallProject` judgment at end of Phase 1. The other six interview commands read it on startup and adjust three behaviors: skip designated Phase 1 beats, default end-of-round recommendation to "I do not think we need another round" (absent contradicting evidence), and compose sub-5-question next rounds when the thinness signal fires. Per-command re-evaluation flips the value when later commands surface contradicting signals.

**Out of scope:**
- `/sdd:prd`-only polish (`aacd`–`aacl`, 9 ACs). Separate small sprint.
- `/sdd:polish` re-open mechanism (`aaev`–`aaex`, 3 ACs). Small, can ride alongside another sprint.
- `/sdd:retro` at project close (`aaey`–`aafe`, 6 ACs). Terminal-command work; better near v2 GA.
- Automatic Backlog Generation (`aafs`–`aagd`, 12 ACs). Cross-cutting across 5 planning commands; benefits from right-sizing landing first.
- Process Notes Growth remainder (`aagf`–`aagn`, 9 ACs). Per-phase wiring + `/sdd:retro` selective reading.
- v1→v2 Migration guide (`aago`–`aahi`, 21 ACs). Single-file work; lower priority until v2 nears GA.
- v2 Release Verification remaining (~13 ACs). Seeding, manual checkoff convention, persistence.

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** Item 1 establishes the authoritative write. Items 2-4 react to that field across the other six interview commands and the deepening-round reference.

1. Item 1 (`/sdd:scope` detection + authoritative write) — bedrock; everything else reads what scope writes
2. Item 2 (cross-command read + per-command re-evaluation) — wires the startup read and re-evaluation logic into seven SKILL.md files
3. Item 3 (skippable Phase 1 beats) — per-command beat-skip logic in scope/prd/spec/plan
4. Item 4 (small-project deepening defaults) — end-of-round recommendation default + sub-5-question round composition

Item 2 is foundational for Items 3 and 4 — both depend on `smallProject` being read on startup. Within autonomous mode the subagents run sequentially so this ordering holds naturally.

## Checklist

### Item 1: `/sdd:scope` `smallProject` detection and authoritative write `[PRD: aabr, aabs, aabt]` `[FB: FB-011]`

- **PRD ref:** `prd.md > Interview Command Behaviors` (small-project detection story for `/sdd:scope`) `[PRD: aabr, aabs, aabt]`
- **Spec ref:** `spec.md > /sdd:scope` (v2 changes — "Authoritative `smallProject` detection at end of Phase 1") + `references/right-sizing.md > ## Signals for the heuristic judgment` + `references/right-sizing.md > ## The smallProject field > Authoring lifecycle`
- **What to build:** Update `plugins/sdd/skills/scope/SKILL.md` so that during Phase 1, the agent evaluates the small-project signals listed in `references/right-sizing.md > ## Signals for the heuristic judgment`. At the end of Phase 1, after the mandatory scoping questions have landed, write the authoritative `smallProject` value to `docs/project-state.json`. Value semantics: `true` when signals are present (rule of thumb: four or more of the six signals, with none strongly contradicted); `false` when absent or when any single strong contradiction is present (e.g., user explicitly names external paying customers). The write fires exactly once per `/sdd:scope` run, after Phase 1 closes — do NOT write earlier; do NOT skip the write even if Phase 1 was abbreviated. Cross-reference `references/right-sizing.md` for the signal list and rule of thumb rather than restating them in the SKILL.md.

  Note that pre-existing language in `scope/SKILL.md` may already mention right-sizing detection placement (relocated from `/sdd:discovery` to `/sdd:scope` per the open-concerns resolution and `spec.md > PRD Edits Required`). Extend or replace as needed; the actual detection-and-write behavior is the new work.
- **Acceptance criteria:**
  - [x] `aabr` `/sdd:scope` detects small-project signals during Phase 1.
  - [x] `aabs` At the end of Phase 1, `/sdd:scope` writes the authoritative `smallProject` value to `docs/project-state.json` (`true` when signals are present; `false` or omitted when absent).
  - [x] `aabt` The small-project signals `/sdd:scope` evaluates are the ones listed in `references/right-sizing.md`.
- **Verification:**
  - Re-read `scope/SKILL.md` and confirm Phase 1 includes signal observation against the right-sizing reference list.
  - Confirm the end-of-Phase-1 write step exists, writes `smallProject` to `docs/project-state.json`, fires exactly once per command run, and lives AFTER the mandatory scoping questions.
  - Confirm the signal list is cross-referenced to `references/right-sizing.md > ## Signals for the heuristic judgment`, not restated inline.
  - Confirm both branches are handled — `true` when signals present, `false` (or omitted) when absent.
- **Status:** [x] Complete

---

### Item 2: Cross-command `smallProject` startup read + per-command re-evaluation `[PRD: aabu, aaby]` `[FB: FB-011]`

- **PRD ref:** `prd.md > Interview Command Behaviors` (small-project read story + per-command re-evaluation story) `[PRD: aabu, aaby]`
- **Spec ref:** `spec.md > /sdd:scope, /sdd:prd, /sdd:spec, /sdd:plan` (v2 changes referencing re-evaluation) + `references/right-sizing.md > ## The smallProject field > Authoring lifecycle` (the re-evaluation paragraph and the discovery exclusion)
- **What to build:** Wire `smallProject` startup-reading into all seven interview commands: `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:refine`, `/sdd:polish`. Each command, after its standard `lastCommand` state-update, reads the current `smallProject` value from `docs/project-state.json`. The value informs subsequent behavior per Items 3 and 4 (skip beats, adjust deepening defaults).

  Additionally, wire per-command re-evaluation where it's mandated by `references/right-sizing.md > ## The smallProject field > Authoring lifecycle`:
  - `/sdd:prd`, `/sdd:spec`, and `/sdd:plan` re-evaluate on startup against the signals using the material they have access to. A flip in either direction is logged in the appropriate process-notes file (`process-notes-prd.md`, `process-notes-spec.md`, `process-notes-sprint-N.md`) with a one-line rationale. A re-evaluation that confirms the prior value is a no-op — no log entry.
  - `/sdd:refine` and `/sdd:polish` MAY re-evaluate and write if observed signals contradict the current value (AC `aaby` permits this; the right-sizing reference does not mandate it). Keep the language permissive: "may re-write if observed signals contradict."
  - `/sdd:discovery` does NOT detect, re-evaluate, or write `smallProject`. The right-sizing reference's authoring lifecycle explicitly excludes discovery (scale judgment at that stage is premature and biases framing). `/sdd:discovery` still reads the value on startup per AC `aabu`, but never writes.
  - `/sdd:scope` writes the authoritative initial value (Item 1). On a re-run, `/sdd:scope` re-evaluates against current Phase 1 answers and may overwrite — the write at end of Phase 1 IS its re-evaluation step. No separate startup re-evaluation needed.

  Bias toward humility: when in doubt, leave the value alone and proceed with standard cadence.
- **Acceptance criteria:**
  - [x] `aabu` Every interview command reads the `smallProject` value from `docs/project-state.json` on startup.
  - [x] `aaby` Each interview command may write a new value to `smallProject` in `docs/project-state.json` during its own run based on signals it observes.
- **Verification:**
  - Re-read all seven SKILL.md files (`discovery`, `scope`, `prd`, `spec`, `plan`, `refine`, `polish`) and confirm each has a startup read of `smallProject` from `docs/project-state.json` after the state-update step.
  - Confirm `/sdd:prd`, `/sdd:spec`, `/sdd:plan` each have language describing on-startup re-evaluation against the signals, with a process-notes log entry on flip and no-op on confirm.
  - Confirm `/sdd:refine` and `/sdd:polish` have permissive re-evaluation language (`may re-write if observed signals contradict`) — not mandated.
  - Confirm `/sdd:discovery` reads but does NOT write — the authoring lifecycle exclusion is honored.
  - Confirm cross-references to `references/right-sizing.md > ## The smallProject field > Authoring lifecycle` are present rather than restated.
- **Status:** [x] Complete

---

### Item 3: Small-project skippable Phase 1 beats `[PRD: aabv]` `[FB: FB-011]`

- **PRD ref:** `prd.md > Interview Command Behaviors` (small-project skippable beats story) `[PRD: aabv]`
- **Spec ref:** `references/right-sizing.md > ## Skippable Phase 1 beats per command` (the four sub-sections enumerating skippable beats for `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`)
- **What to build:** Wire skip-when-`smallProject`-is-`true` logic into the four interview commands enumerated in `references/right-sizing.md > ## Skippable Phase 1 beats per command`: `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`. For each, update the SKILL.md's Phase 1 section so that the designated skippable beats (per the reference) are skipped when `smallProject=true` was read at startup (Item 2). Cross-reference the reference's skippable-beats sub-sections rather than restating the lists inline.

  Important semantics: the lists in the reference are SKIPPABLE, not REQUIRED-SKIPPED. A command should still ask a beat if the user's earlier answers leave it genuinely open. The skip is the default behavior when `smallProject=true`; the agent retains judgment to ask anyway. Cross-reference `references/right-sizing.md > ## Skippable Phase 1 beats per command` for the specific beat lists per command.

  `/sdd:discovery`, `/sdd:refine`, `/sdd:polish` are NOT enumerated in the reference's skippable-beats section. They have no skippable beats listed and are out of scope for Item 3 — no edits required.
- **Acceptance criteria:**
  - [x] `aabv` When `smallProject` is `true`, the agent skips Phase 1 beats designated as small-project-skippable.
- **Verification:**
  - Re-read `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan` SKILL.md files. Confirm each has Phase 1 logic that cross-references `references/right-sizing.md > ## Skippable Phase 1 beats per command` and skips designated beats when `smallProject=true`.
  - Confirm the skippable-beats lists are NOT restated inline in any SKILL.md.
  - Confirm the skip is conditional, not mandatory — language should preserve the agent's judgment to ask a beat if earlier answers leave it open.
  - Confirm `/sdd:discovery`, `/sdd:refine`, `/sdd:polish` were NOT edited for this item — they have no skippable beats per the reference.
- **Status:** [x] Complete

---

### Item 4: Small-project deepening-round defaults `[PRD: aabw, aabx]` `[FB: FB-011]`

- **PRD ref:** `prd.md > Interview Command Behaviors` (small-project deepening-round behavior stories) `[PRD: aabw, aabx]`
- **Spec ref:** `references/right-sizing.md > ## The thinness signal and sub-5-question rounds` + `references/right-sizing.md > ## Interaction with the question cap` + `references/deepening-rounds.md` (the existing cross-reference at line 38 anchors right-sizing into the deepening flow)
- **What to build:** Wire two behavior adjustments into the deepening-round mechanism that fire when `smallProject=true`:

  **(a) End-of-round recommendation default.** When `smallProject=true`, the agent's structured end-of-round recommendation (per `references/deepening-rounds.md`) defaults to "I do not think we need another round, because [reason; topics a next round would have covered]" UNLESS concrete evidence in the just-completed round contradicts the default. The recommendation form stays the same — definite, with reasoning and topic preview, never a blank prompt. Only the default direction changes from "depends on round content" to "close, unless evidence contradicts."

  **(b) Sub-5-question next round.** When `smallProject=true` AND the thinness signal fires (≥2 of the three observations from `references/right-sizing.md > ## The thinness signal and sub-5-question rounds`: short answers, deflections, agent's natural framing toward closure), the next round is composed with fewer than 5 questions (typically 2-3). The transition message names this plainly — recommended literal or close paraphrase: "This is feeling well-covered. Want a quick 2-3 question pass to close gaps, or are you ready to proceed?" The user can still decline the round entirely, same as standard cadence.

  The 5/10 question caps from `references/deepening-rounds.md` remain unchanged. Right-sizing is a floor adjustment, not a ceiling adjustment.

  **Implementation locus call.** The two behaviors are cross-cutting across every interview command's deepening flow. Centralizing the logic in `references/deepening-rounds.md` (with cross-references to `references/right-sizing.md`) keeps each SKILL.md slim and the rule single-sourced. Subagent: extend `references/deepening-rounds.md` to incorporate (a) and (b), and cross-reference from each interview command's deepening section if any per-command instruction is needed. The reference already cross-references right-sizing at line 38 — extend that anchor.
- **Acceptance criteria:**
  - [x] `aabw` When `smallProject` is `true`, the agent's end-of-round recommendation defaults to "I do not think we need another round" unless concrete evidence in the just-completed round contradicts that default.
  - [x] `aabx` When `smallProject` is `true` and the answers in the most recently completed round are thin (specific thinness signal deferred to `/sdd:spec`), the agent composes the next round with fewer than five questions.
- **Verification:**
  - Re-read `references/deepening-rounds.md` and confirm the right-sizing-default language is present for both (a) end-of-round recommendation and (b) sub-5-question next round.
  - Confirm caps (5 per round, 10 across deepening rounds) are explicitly preserved — right-sizing moves the default down, not the ceiling.
  - Confirm the thinness signal is cross-referenced to `references/right-sizing.md > ## The thinness signal and sub-5-question rounds` rather than restated.
  - Confirm the transition message language exists (literal or close paraphrase of "well-covered" + "2-3 question pass").
  - Confirm the end-of-round recommendation form is preserved (definite, with reasoning, with topic preview) — only the default direction shifts.
  - Confirm any per-command edits in interview SKILL.md files cross-reference the reference rather than restate the rules.
- **Status:** [x] Complete

---

## Notes

- All 8 ACs of the Right-Sizing epic close in this sprint: `aabr`–`aaby`. No collisions with closed IDs from Sprints 1-11. PRD Unvetted section is empty.
- **PRD checkoff watch.** Per Sprint 11's wrap-up watch item, sprint close must propagate AC checkoffs to `docs/prd.md` immediately. Sprints 9-10 regressed; Sprint 11 caught and fixed. Sprint 12 should follow the same discipline at close.
- **FB mapping.** All four items carry `[FB: FB-011]` (Right-size detection across interview commands with small-project hint propagated downstream). `/sdd:build` wrap-up writes a single `Shipped (sprint 12, items 1-4)` entry to `docs/v2-verification.md` on close.
- **Reference is canonical.** `references/right-sizing.md` is complete (signals, lifecycle, skippable beats per command, thinness signal, cap interaction). No reference-file additions expected beyond a small extension in `references/deepening-rounds.md` for Item 4. Subagents should cross-reference the right-sizing reference rather than restate its content in each SKILL.md.
- **`/sdd:discovery` exclusion.** Per the right-sizing reference's authoring lifecycle, `/sdd:discovery` does NOT detect, re-evaluate, or write `smallProject`. Item 2 has discovery READING the value (per AC `aabu`'s "every interview command") but never writing. This is a deliberate v2 relocation (the original PRD assigned detection to discovery; v2 moved it to scope). See `spec.md > PRD Edits Required`.
- **No new design calls.** Sprint 12 is pure implementation — every behavioral specification is already resolved in `references/right-sizing.md` and `references/deepening-rounds.md`. No `lastCommand` no-clobber–type design surprises expected.
- **Cached marketplace plugin is still v1.** Same caveat as every prior sprint. Sprint 12's edits ship to disk in seven SKILL.md files plus `references/deepening-rounds.md`, but they aren't live as agent behavior until the maintainer reinstalls.
- **No deepening rounds requested at planning time.** Pre-vetted ACs, fully-specified reference material, single-epic scope, well-understood cross-cutting pattern. Topics a round would have covered (deferred to `/sdd:build` implementation context): grouping granularity (kept at 4 items; alternative was per-command split), the specific Phase 1 beats to mark skippable in each command (resolved by the reference), thinness-signal phrasing (resolved by the reference), and guards against `aaby` re-evaluation thrashing (best discovered at build time).
- **Natural next-sprint candidates after Sprint 12:**
  - `/sdd:prd`-only polish (`aacd`–`aacl`, 9 ACs) — small, single-file work.
  - Process Notes Growth remainder (`aagf`–`aagn`, 9 ACs) — wire scope/prd/spec/plan/retro to per-phase notes files.
  - Automatic Backlog Generation (`aafs`–`aagd`, 12 ACs) — cross-cutting across 5 planning commands.
  - `/sdd:retro` at Project Close (`aaey`–`aafe`, 6 ACs) — terminal-command gating + cross-project pattern capture.
  - v1→v2 Migration guide (`aago`–`aahi`, 21 ACs) — single `MIGRATION-v1-to-v2.md` file.
  - `/sdd:polish` re-open mechanism (`aaev`–`aaex`, 3 ACs) — small.
  - v2 Release Verification remaining (~13 ACs across seeding, manual checkoff convention, approach documentation, persistence).
