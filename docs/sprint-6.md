# Sprint 6

**Theme:** `/sdd:plan` full v2 implementation. The planning side of the v2 sprint loop. Currently `plugins/sdd/skills/plan/SKILL.md` is a rename of v1's `/sprint` with none of v2's plan-side mechanics wired in (prior-sprint notes reading, PRD state check, sprint-mode memory, sprint-item tag validation, End-of-Command handoff). This sprint wires them in.

**Tangible outcome:** After this sprint, running `/sdd:plan` reads `process-notes-sprint-N.md` (highest N) at startup and surfaces carry-forward / tech-debt observations from the previous `/sdd:build` wrap-up; scans `docs/prd.md` and routes deterministically (Unvetted has items → recommend `/sdd:refine`; all PRD ACs checked AND Unvetted empty → recommend `/sdd:retro`; else → proceed with sprint planning); resolves sprint mode without re-asking when a profile default or per-project recorded mode is available, and offers an "always use this mode" toggle; validates every proposed sprint-item line carries either `[PRD: ...]` or `[unmapped]` (and prompts for `[FB: ...]` when `docs/v2-verification.md` exists) and refuses to write `docs/sprint-N.md` if any line fails; ends with the standard `/clear`-then-next-command handoff naming `/sdd:build`. Sprint 7's planning session dogfoods all of this end-to-end.

**Out of scope:**
- Cross-cutting interview command behaviors that `/sdd:plan` is supposed to apply once they exist (clarifying-questions-don't-advance, 5/10-question deepening caps, end-of-round recommendations, active prompting, 3-tier context management). These ship in a sdd-guide-focused sprint. `/sdd:plan` references the cross-cutting behaviors in its SKILL.md but does not embed their implementation.
- Right-sizing skippable-beat behavior for `/sdd:plan` (the "sprint-mode confirmation skip when last mode is persisted" beat). Depends on `/sdd:scope` writing the authoritative `smallProject` value, which is its own sprint.
- `/sdd:polish` re-open mechanism (`aaev`–`aaex`). Belongs to `polish/SKILL.md`, not `plan/SKILL.md`. Reads the `[close-sprint-manifest]` block Sprint 5 produces; cleaner as a separate small sprint.
- Bug Fix 1 (`aaaa`–`aaac`, `/sdd:feedback` no-lastCommand-clobber). Different file, unrelated mechanics. Separate sprint.
- `/sdd:discovery` command implementation (`aaah`–`aaaq`). Separate sprint.
- Manual PRD-hygiene backfill of historical ACs (Sprints 1–4 shipped before Bug Fix 2 auto-checkoff existed; their PRD ACs remain unchecked). Not sprint work; the user may manually check off when convenient.

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** All four items edit the same file (`plugins/sdd/skills/plan/SKILL.md`). Items must execute in numbered order — Item 1 establishes startup structure that Items 2–4 attach to. Autonomous mode dispatches them sequentially, not in parallel.

## Checklist

### Item 1: Startup — prior-sprint notes + PRD state check

- **PRD ref:** `prd.md > Sprint Loop` (read previous sprint's process notes story + PRD-state-check story) `[PRD: aado, aadp, aadq, aadr, aads, aadt, aadu, aadv]`
- **Spec ref:** `spec.md > Command Skills > /sdd:plan` (Reads the most recent process-notes-sprint-N.md... + PRD state check), `spec.md > Behavioral Layer > Reference: context-loading.md` (process-notes-sprint-N read by /plan)
- **What to build:** Add a `## Loading` section (or extend the existing one) and a `## PRD State Check` section to `plugins/sdd/skills/plan/SKILL.md` covering two startup behaviors that run before any sprint-grouping work:
  1. **Read most recent `process-notes-sprint-N.md`:**
     - Glob `process-notes-sprint-*.md` in the project root and select the file with the highest N.
     - If no such file exists, proceed silently — no warning, no error.
     - If found, read it and extract:
       - Carry-forward items (anything noted in the previous `/sdd:build` wrap-up as not closed).
       - Quality concerns and tech-debt observations (entries in the Wrap-Up Notes section, including `[tech-debt]` markers or `### Tech-debt` sub-sections, whichever format the prior wrap-up author used).
     - Surface both lists to the user before proposing the new sprint grouping. Format: brief bullet list, one line per item, with the source sprint-item reference where available.
  2. **PRD state check** (runs after the process-notes read):
     - Read `docs/prd.md`.
     - Determine three signals:
       - Are all `- [ ] \`abcd\` ...`-format AC lines now `- [x] \`abcd\` ...`? (i.e., every PRD AC checked.)
       - Does the `## Unvetted` section contain any list items? (Items being anything matching the AC pattern under the Unvetted header. The marker HTML comment is not an item.)
       - Are there unchecked criteria remaining?
     - Routing rules (priority order — first matching rule wins):
       - **Unvetted contains items:** emit a recommendation to run `/sdd:refine` first. Pause sprint planning and ask whether to proceed anyway or run `/refine`. On user election to proceed, continue with sprint planning. On user election to run `/refine`, exit gracefully with the standard handoff naming `/sdd:refine`.
       - **All ACs checked AND Unvetted empty:** emit a recommendation to run `/sdd:retro` instead. Exit gracefully — do not proceed to sprint planning. Use the standard handoff naming `/sdd:retro`.
       - **Neither (unchecked ACs remain, Unvetted empty):** proceed with sprint planning.
     - The PRD state check is a single read+evaluate; it does NOT modify the PRD.
- **Acceptance criteria:**
  - [x] `aado` On startup, `/sdd:plan` reads the file `process-notes-sprint-N.md` with the highest N in the project root, if any such file exists.
  - [x] `aadp` `/sdd:plan` surfaces carry-forward items recorded by the previous `/sdd:build` wrap-up.
  - [x] `aadq` `/sdd:plan` surfaces quality concerns and tech-debt items recorded by the previous `/sdd:build` wrap-up.
  - [x] `aadr` When no `process-notes-sprint-N.md` file exists at startup, `/sdd:plan` proceeds to its next step without emitting a warning.
  - [x] `aads` After reading prior process notes, `/sdd:plan` scans `docs/prd.md` and determines whether every acceptance criterion is checked, whether the Unvetted section contains items, and whether unchecked criteria remain.
  - [x] `aadt` When every acceptance criterion is checked and the Unvetted section is empty, `/sdd:plan` recommends running `/sdd:retro` and does not proceed to sprint planning.
  - [x] `aadu` When the Unvetted section contains items, `/sdd:plan` recommends running `/sdd:refine` and does not proceed to sprint planning until the user acknowledges or overrides.
  - [x] `aadv` When neither closing condition holds, `/sdd:plan` proceeds with sprint planning.
- **Verification:**
  - Read the updated SKILL.md and confirm both startup behaviors are present and ordered (process-notes read first, PRD state check second).
  - Confirm the no-file path silently proceeds (no warning text in the SKILL.md for that branch).
  - Walk the three routing branches mentally against the prd.md AC IDs the user is most likely to encounter (Unvetted populated → /refine; all checked → /retro; default → proceed).
  - Confirm Unvetted priority wins over all-checked (Unvetted items mean the project isn't actually done even if every numbered AC is checked).
  - Confirm the prior-sprint reader uses globbing on `process-notes-sprint-*.md` rather than tracking a separate index.
- **Status:** [x] Complete

---

### Item 2: Sprint-mode memory

- **PRD ref:** `prd.md > Sprint Loop` (remember preferred sprint mode story) `[PRD: aaec, aaed, aaee, aaef, aaeg]`
- **Spec ref:** `spec.md > Command Skills > /sdd:plan` (Sprint-mode memory subsection), `spec.md > Command Skills > /sdd:onboard` (User profile schema — defaultSprintMode field)
- **What to build:** Add a `## Sprint-Mode Resolution` section to `plugins/sdd/skills/plan/SKILL.md`, executed after the PRD state check decides "proceed with planning" and before sprint-grouping. Resolves which build mode (`step-by-step` or `autonomous`) the resulting sprint will lock in:
  1. **Resolution order:**
     - Read `~/.claude/sdd-user-profile.json` (created by `/sdd:onboard`). If a non-empty `defaultSprintMode` field is present, use that value directly — do not ask the user.
     - Else, read `docs/project-state.json`. If a recorded `buildMode` value is present from the previous sprint, ask the user a single question: "Last sprint used [mode]. Reuse that mode?" — yes → use that mode, no → fall through to ask.
     - Else, ask the user to select a mode (free-form open-ended question — same one-question-at-a-time rule as sdd-guide).
  2. **"Always use this mode" toggle:**
     - After the mode is resolved by any path above (including profile default), offer the user a single toggle: "Always use [mode]? (Sets `defaultSprintMode` in your profile.)"
     - On affirmative: write the selected mode to `defaultSprintMode` in `~/.claude/sdd-user-profile.json`. Update `updatedAt` to the current ISO-8601 timestamp.
     - On negative or skip: do not write to the profile.
     - When `defaultSprintMode` was already set in the profile (first resolution path hit), still offer this toggle but framed as a confirmation ("Keep `defaultSprintMode` set to [mode]?") — affirmative = no-op write; negative offers to clear the field. Clearing writes `null` (or omits the field, semantically equivalent).
  3. **Per-project memory write:**
     - After mode selection regardless of toggle answer, write the selected mode to `docs/project-state.json` under `buildMode` for next-sprint reference. This write happens unconditionally so the next `/sdd:plan` run has a recorded prior mode to offer.
  4. **Single message discipline:** the mode-confirm question and the toggle are two separate questions (one-question-at-a-time per sdd-guide). Do not combine.
- **Acceptance criteria:**
  - [x] `aaec` When `~/.claude/sdd-user-profile.json` contains a non-empty `defaultSprintMode` field, `/sdd:plan` uses that value as the sprint mode without asking the user.
  - [x] `aaed` When `defaultSprintMode` is unset but `docs/project-state.json` contains a recorded mode from the previous sprint, `/sdd:plan` asks the user whether to reuse that mode and proceeds based on the answer.
  - [x] `aaee` When neither `defaultSprintMode` nor a recorded previous-sprint mode is available, `/sdd:plan` asks the user to select a mode.
  - [x] `aaef` After mode selection by any path above, `/sdd:plan` offers an "always use this mode" toggle; on user acceptance, the selected mode is written to `defaultSprintMode` in `~/.claude/sdd-user-profile.json`.
  - [x] `aaeg` `/sdd:plan` writes the selected mode to `docs/project-state.json` for next-sprint reference.
- **Verification:**
  - Read the updated SKILL.md and confirm the three-tier resolution order is explicit in the file (profile → project-state → ask).
  - Confirm the "always use this" toggle appears regardless of which resolution path was taken — including when the profile already has `defaultSprintMode` set.
  - Confirm the per-project write to `project-state.json` is unconditional (every plan run records the mode).
  - Confirm the mode-confirm and toggle questions are issued as separate messages (not bundled).
  - Confirm the profile write updates `updatedAt` to the current ISO-8601 timestamp.
- **Status:** [x] Complete

---

### Item 3: Sprint-item tag validation

- **PRD ref:** `prd.md > Sprint Loop` (validate sprint-item tags story) `[PRD: aadw, aadx, aady, aadz]`
- **Spec ref:** `spec.md > Cross-Cutting Mechanisms > Sprint-Item Tags`, `spec.md > Behavioral Layer > Reference: sprint-tags.md`, `spec.md > Command Skills > /sdd:plan` (Sprint-item tag validation subsection)
- **What to build:** Add a `## Sprint-Item Tag Validation` section to `plugins/sdd/skills/plan/SKILL.md`, executed immediately before the file-write step that produces `docs/sprint-N.md`:
  1. **Parse every checkbox bullet line in the proposed sprint contents.** Use the parser regexes documented in `references/sprint-tags.md` (cross-reference, do not re-implement):
     - `[PRD: id1, id2, ...]` regex: `\[PRD:\s*[a-z]{4}(?:\s*,\s*[a-z]{4})*\s*\]`
     - `[unmapped]` regex: literal `[unmapped]`
     - `[FB: FB-NNN, FB-MMM, ...]` regex: `\[FB:\s*FB-\d{3}(?:\s*,\s*FB-\d{3})*\s*\]`
  2. **Validation rule:** every checkbox bullet must carry either a `[PRD: ...]` tag OR an explicit `[unmapped]` tag. `[FB: ...]` is supplementary and never satisfies the validation requirement on its own.
  3. **Failure handling:** when one or more lines fail validation, `/sdd:plan` refuses to write `docs/sprint-N.md`. Surface every offending line back to the user with its line number and tag analysis (e.g., "Line 14 carries `[FB: FB-007]` but no `[PRD: ...]` or `[unmapped]` tag"). Ask the user to address each one (add a `[PRD: ...]` tag pointing at the relevant AC, or add an explicit `[unmapped]` if the item genuinely doesn't map). Re-run validation after the user updates. Loop until validation passes.
  4. **`[FB: ...]` prompting (sdd-plugin-specific):** when `docs/v2-verification.md` exists in the project, after PRD-tag validation passes, scan each item for whether it implements an in-scope feedback item. For each item without a `[FB: ...]` tag, ask the user whether to add one (cross-referencing `docs/v2-verification.md` rows). Skip this step entirely when `docs/v2-verification.md` does not exist — the file's presence is the sole activation signal.
  5. **File write:** only after validation passes (and any `[FB: ...]` prompting completes) does `/sdd:plan` write `docs/sprint-N.md`. The validator is a precondition, not a post-hoc check.
- **Acceptance criteria:**
  - [x] `aadw` Every sprint-item line in `docs/sprint-N.md` written by `/sdd:plan` carries either a PRD acceptance-criterion reference tag (e.g., `[PRD: Epic / Story / AC]` or equivalent — exact syntax per `references/sprint-tags.md`) or an explicit `[unmapped]` tag.
  - [x] `aadx` At the end of sprint planning, before finalizing `docs/sprint-N.md`, `/sdd:plan` validates that every sprint-item line in the proposed sprint carries one of the two tag types.
  - [x] `aady` When any sprint-item line lacks both a PRD reference tag and an `[unmapped]` tag, `/sdd:plan` refuses to finalize the sprint file and surfaces every offending line for the user to address.
  - [x] `aadz` `/sdd:plan` does not write `docs/sprint-N.md` to disk until validation passes for every sprint-item line.
- **Verification:**
  - Read the updated SKILL.md and confirm the validator section is positioned immediately before the file-write step.
  - Confirm the SKILL.md cross-references `references/sprint-tags.md` for the parser regexes rather than restating them inline (single source of truth).
  - Trace a failing-validation scenario mentally: 2 of 5 items lack tags → /plan refuses to write, surfaces both lines, loops on user input until both pass.
  - Confirm `[FB: ...]` does not satisfy validation alone — a line with only `[FB: FB-007]` and no `[PRD: ...]` or `[unmapped]` must fail validation.
  - Confirm the `docs/v2-verification.md` gate is correctly framed (file present → prompt for FB tags; file absent → skip silently, no warning).
- **Status:** [x] Complete

---

### Item 4: End-of-command handoff

- **PRD ref:** `prd.md > Sprint Loop` (next-command recommendation story) `[PRD: aaea, aaeb]`
- **Spec ref:** `spec.md > Cross-Cutting Mechanisms > End-of-Command Handoff`, `spec.md > Behavioral Layer > sdd-guide Core` (handoff template)
- **What to build:** Add a `## End-of-Command Handoff` section to `plugins/sdd/skills/plan/SKILL.md`, executed as the final step after the sprint file is written:
  1. **Standard two-line handoff:**
     - Line 1: one-line outcome summary, e.g., `Sprint N planned. N items, autonomous mode.`
     - Line 2 (verbatim): `` Run `/clear`, then `/sdd:build` to continue. ``
  2. **First-handoff explanation** (prepended exactly once per user):
     - Read `~/.claude/sdd-user-profile.json` and check `handoffWarningShown`.
     - If false (or the field is missing), prepend the standard first-handoff paragraph (per `spec.md > Cross-Cutting Mechanisms > End-of-Command Handoff`) before the two-line form, then write `handoffWarningShown: true` to the profile.
     - If true, emit only the two-line form.
  3. **Next-command target:** always `/sdd:build`. `/sdd:plan` never recommends `/sdd:refine` or `/sdd:retro` at its handoff step — those routing decisions were already made by Item 1's PRD state check (and would have exited `/sdd:plan` before reaching this step). By the time control reaches Item 4, the project state is "proceed with sprint loop," which means `/sdd:build` next.
  4. **Wording:** cross-reference the inline template in `plugins/sdd/skills/sdd-guide/SKILL.md` rather than duplicating it. If the sdd-guide template hasn't yet been authored (sdd-guide core still uses v1 wording), add a placeholder cross-reference noting that the wording follows the template once it lands.
- **Acceptance criteria:**
  - [x] `aaea` At the end of `/sdd:plan`'s run, the command emits a recommendation naming the next command (typically `/sdd:build`).
  - [x] `aaeb` The recommendation includes the cross-cutting between-commands handoff (run `/clear`, then invoke the named next command).
- **Verification:**
  - Read the updated SKILL.md and confirm the handoff section is positioned as the final step (after file write, after any post-write logging).
  - Confirm the two-line form is locked verbatim per the spec.
  - Confirm `handoffWarningShown` is read and conditionally flipped to true on first emit.
  - Confirm the next-command target is hard-coded to `/sdd:build` (no branching in this step — branching happened in Item 1).
- **Status:** [x] Complete

---

## Notes

- All four items edit the same file: `plugins/sdd/skills/plan/SKILL.md`. Sequential execution is mandatory.
- Sprint 6 closes 19 PRD ACs: 8 (Item 1: `aado`–`aadv`) + 5 (Item 2: `aaec`–`aaeg`) + 4 (Item 3: `aadw`–`aadz`) + 2 (Item 4: `aaea`–`aaeb`).
- `[FB: ...]` tagging: this sprint's items do **not** carry `[FB: ...]` tags. The Sprint Loop epic ACs being implemented (`aado`–`aaeg`) are sdd-plugin-internal architecture, not surfaced feedback items in `docs/v2-verification.md`. No FB rows correspond to `/sdd:plan` mechanics directly.
- The wrap-up phase from Sprint 5 will run on this sprint as the **first real dogfood** of `/sdd:build`'s wrap-up. Since Sprint 6 items carry real `[PRD: ...]` tags, the wrap-up should auto-check 19 PRD ACs in `docs/prd.md`. Since no `[FB: ...]` tags are present, no `docs/v2-verification.md` rows update. The cross-cutting Bug Fix 2 logic doesn't fire (no AC referenced across multiple sprint files in this sprint).
- After Sprint 6: the natural next-sprint candidates remain (a) Bug Fix 1 (`aaaa`–`aaac`, /sdd:feedback no-lastCommand-clobber); (b) `/sdd:polish` re-open mechanism (`aaev`–`aaex`); (c) `/sdd:discovery` command implementation (`aaah`–`aaaq`); (d) Interview Command Behaviors cross-cutting (sdd-guide + deepening-rounds reference changes); (e) `/sdd:onboard` Polish; (f) Cross-Project Feedback Transfer; (g) Multi-Session Resume (`/sdd:pause` + `/sdd:unpause`). Choose based on what's needed first.
- The implementation is markdown-only (SKILL.md edits). No code, no runtime. Verification is by SKILL.md re-read against ACs; end-to-end dogfooding fires when `/sdd:plan` runs against Sprint 7.
