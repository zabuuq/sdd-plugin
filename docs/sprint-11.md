# Sprint 11

**Theme:** Multi-Session Resume — `/sdd:pause` and `/sdd:unpause` end-to-end. Replaces the 30-line stubs at `plugins/sdd/skills/pause/SKILL.md` and `plugins/sdd/skills/unpause/SKILL.md` with real command behavior, leveraging the canonical schema and quality bar already defined in `plugins/sdd/skills/sdd-guide/references/pause-resume.md`. 17 ACs (`aaar`–`aabg`) close — the entire Multi-Session Resume epic.

**Tangible outcome:** After this sprint, a user mid-`/sdd:scope` (or any pause-able interview command) can run `/sdd:pause`, close the terminal, and return tomorrow on the same or a different machine. Running `/sdd:unpause` reads `lastCommand` from `docs/project-state.json`, loads the matching `docs/<command>-resume.md` plus the source command's SKILL.md, and resumes at the Pending decision captured in the resume file. When the resumed command's document-generation step completes, the resumed command itself deletes the resume file. Cross-session work becomes possible for every interview command in the v2 chain.

**Out of scope:**
- Right-sizing wire-through (`aabr`–`aaby`, 8 ACs). `/sdd:scope` small-project detection + cross-command reading; separate sprint. Note: `/sdd:pause` and `/sdd:unpause` are NOT pauseable themselves (they're utilities, not interview commands) and have no right-sizing surface, so multi-session resume does not depend on right-sizing landing first.
- `/sdd:prd`-only polish (`aacd`–`aacl`, 9 ACs). Separate small sprint.
- `/sdd:polish` re-open mechanism (`aaev`–`aaex`, 3 ACs). Different command, separate sprint.
- `/sdd:retro` at Project Close (`aaey`–`aafe`, 6 ACs). Separate sprint.
- Automatic Backlog Generation (`aafs`–`aagd`, 12 ACs). Cross-cutting across 5 planning commands; separate sprint.
- Process Notes Growth remainder (`aagf`–`aagn`, 9 ACs). Per-phase wiring + `/sdd:retro` selective reading; separate sprint.
- v1→v2 Migration guide (`aago`–`aahi`, 21 ACs). Single-file `MIGRATION-v1-to-v2.md` work; separate sprint.
- v2 Release Verification remaining (~12 ACs). Separate work.
- Three-tier `/clear` consolidation writes to the resume-file path from within interview commands (`aacm`–`aacs` already closed in Sprint 9 at the recommendation level; the actual *write* invocation from interview-command bodies is a wire-through that happens naturally as commands use the existing `references/context-management.md` directive — no new ACs and no new SKILL.md edits needed in Sprint 11).

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** Items 1-2 build `plugins/sdd/skills/pause/SKILL.md`. Items 3-4 build `plugins/sdd/skills/unpause/SKILL.md`. Item 5 is cross-cutting cleanup that wires `<command>-resume.md` deletion into seven pause-able command SKILL.md files (`discovery`, `scope`, `prd`, `spec`, `plan`, `refine`, `polish`).

1. Item 1 (pause startup gating) — guard rails before any write
2. Item 2 (pause resume-file write + final message) — the real work of `/sdd:pause`
3. Item 3 (unpause core flow) — the real work of `/sdd:unpause`
4. Item 4 (unpause missing-file handling) — error path for `/sdd:unpause`
5. Item 5 (resume-file cleanup, cross-cutting) — wires deletion into seven pause-able command SKILL.md files

Items 1+2 form a complete `/sdd:pause`. Items 3+4 form a complete `/sdd:unpause`. Item 5 closes the cleanup loop across the seven pause-able commands.

## Checklist

### Item 1: `/sdd:pause` startup gating `[PRD: aaar, aaas]` `[FB: FB-004]`

- **PRD ref:** `prd.md > Multi-Session Resume` (pause stories) `[PRD: aaar, aaas]`
- **Spec ref:** `spec.md > /sdd:pause (NEW)` (prerequisites — lastCommand gate)
- **What to build:** Replace the stub body of `plugins/sdd/skills/pause/SKILL.md` with the startup gating logic. After the standard `lastCommand` state-update (per sdd-guide state-tracking rule — but note: `/sdd:pause` should NOT clobber `lastCommand` since that would break the gate it then reads; the state-tracking rule applies but the update writes `lastCommand` as the value-it-was-when-pause-started so unpause can read it back), check the `lastCommand` value from `docs/project-state.json`.

  **Important:** The `lastCommand` state-update rule from sdd-guide says every command writes `lastCommand` to its own name on startup. For `/sdd:pause`, this would clobber the in-flight command name and break `/sdd:unpause`'s ability to know what to resume. The spec resolves this implicitly via the prerequisite check (pause's job is to capture state for the IN-FLIGHT command, not itself). Approach: `/sdd:pause` reads `lastCommand` BEFORE updating it, captures the in-flight command, and does NOT write `/sdd:pause` to `lastCommand` (or, if it does write, the resumed command's name is what `/sdd:unpause` reads). Cross-reference the Bug Fix 1 pattern (`/sdd:feedback` no-lastCommand-write) — similar exception. The subagent should resolve this concretely; the cleanest interpretation per AC `aaaz` ("`/sdd:unpause` reads `lastCommand` from `docs/project-state.json` and treats that value as the command to resume") is that `lastCommand` continues to hold the in-flight command name across `/sdd:pause` and `/sdd:unpause` invocations. Subagent: design `/sdd:pause` so it does not overwrite `lastCommand` with `/sdd:pause`.

  Gating logic:
  - If `lastCommand` is one of `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:refine`, `/sdd:polish` → proceed to Item 2's write logic.
  - If `lastCommand` is one of `/sdd:onboard`, `/sdd:build`, `/sdd:retro`, `/sdd:feedback`, or unset → emit "nothing in-flight to pause" message, write no resume file, exit cleanly.
- **Acceptance criteria:**
  - [x] `aaar` `/sdd:pause` proceeds with its work only when `lastCommand` in `docs/project-state.json` equals one of: `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:refine`, `/sdd:polish`.
  - [x] `aaas` When `lastCommand` equals `/sdd:onboard`, `/sdd:build`, `/sdd:retro`, `/sdd:feedback`, or is unset, `/sdd:pause` writes no resume file and exits with a message stating nothing is in-flight to pause.
- **Verification:**
  - Re-read the updated `pause/SKILL.md` and confirm the gating logic is the first non-state-read action.
  - Confirm the no-op branch writes nothing (no resume file, no other `docs/` writes).
  - Confirm `/sdd:pause` does NOT clobber `lastCommand` — the in-flight command name must remain readable by `/sdd:unpause`. Cross-reference with `aaaz` to confirm this interpretation holds.
  - Confirm the message for the no-op case names "nothing in-flight to pause" (or equivalent literal that conveys the same meaning).
- **Status:** [x] Complete

---

### Item 2: `/sdd:pause` resume-file write + final message `[PRD: aaat, aaau, aaav, aaaw, aaax]` `[FB: FB-004]`

- **PRD ref:** `prd.md > Multi-Session Resume` (pause stories) `[PRD: aaat, aaau, aaav, aaaw, aaax]`
- **Spec ref:** `spec.md > /sdd:pause (NEW)` (behavior — write, overwrite, final message) + `references/pause-resume.md` (full schema, location convention, per-section quality bar, distill-don't-dump rules)
- **What to build:** After Item 1's gate passes, derive the resume file path as `docs/<command>-resume.md`, where `<command>` is the in-flight command name (`lastCommand` value) with the `/sdd:` prefix stripped. Examples: `/sdd:scope` → `docs/scope-resume.md`; `/sdd:plan` → `docs/plan-resume.md`.

  Write the resume file per the schema in `references/pause-resume.md`:
  - **Required sections (in this exact order):** Status, Setup-already-done, Confirmed decisions, Round status, Pending decision, Files for next-session, Instructions for next-session. Missing sections are a defect. Empty sections are acceptable when genuinely empty — the heading still appears.
  - **Per-section quality bar:** apply the bar from the reference. Status is 1-2 sentences. Pending decision is the literal open question + options weighed. Length target overall: 1-3 pages of markdown.
  - **Distill, don't dump:** no verbatim conversation, no agent reasoning trail, no re-explanation of plugin behavior, cross-reference don't restate. Five rules in the reference apply.

  Overwrite semantics: if `docs/<command>-resume.md` already exists at the target path (e.g., a prior `/sdd:pause` or a `/clear`-tier consolidation wrote one earlier), overwrite it. The new file is the current state. Do not append. Do not refuse.

  Final output: emit the literal message `Paused. Run /sdd:unpause to resume.` This is the LAST thing the command emits — no handoff, no `/clear` instruction. `/sdd:pause` is not an interview command; it overrides the standard handoff template per sdd-guide line 160.

  Update the Loading section to add `references/pause-resume.md`. Also update the frontmatter `description` to drop the "Stub in this release" phrase and reflect that the command is implemented. Delete the `## Status` section.
- **Acceptance criteria:**
  - [x] `aaat` `/sdd:pause` writes the resume file to `docs/<command>-resume.md`, where `<command>` is the in-flight command name without the `/sdd:` prefix.
  - [x] `aaau` The resume file contains these sections, in this order: Status, Setup-already-done, Confirmed decisions, Round status, Pending decision, Files for next-session, Instructions for next-session.
  - [x] `aaav` The resume file contains enough captured detail to seed a fresh zero-context model into the same decision state as the paused conversation (specific quality bar deferred to `/sdd:spec`).
  - [x] `aaaw` When a resume file already exists at the target path, `/sdd:pause` overwrites it rather than appending or refusing.
  - [x] `aaax` `/sdd:pause` emits the message "Paused. Run `/sdd:unpause` to resume." as its final output.
- **Verification:**
  - Re-read the updated `pause/SKILL.md` and confirm the schema is referenced (not restated) and that the seven required sections are named in order.
  - Confirm the file-path derivation rule is described concretely (`docs/<command>-resume.md`, strip `/sdd:` prefix).
  - Confirm the overwrite-on-existing semantics is explicit (no append, no refuse).
  - Confirm the final-message text is the literal `Paused. Run /sdd:unpause to resume.`
  - Confirm the per-section quality bar references `references/pause-resume.md` rather than restating it.
  - Confirm the frontmatter description is updated and `## Status` section is deleted.
  - Confirm Loading section includes `references/pause-resume.md`.
  - Confirm `/sdd:pause` does not emit a standard end-of-command handoff (it overrides per sdd-guide line 160).
- **Status:** [x] Complete

---

### Item 3: `/sdd:unpause` core flow `[PRD: aaay, aaaz, aaba, aabb, aabc]` `[FB: FB-005]`

- **PRD ref:** `prd.md > Multi-Session Resume` (unpause stories) `[PRD: aaay, aaaz, aaba, aabb, aabc]`
- **Spec ref:** `spec.md > /sdd:unpause (NEW)` (behavior — read lastCommand, load resume file + source SKILL, resume at Pending decision) + `references/pause-resume.md` (schema for understanding what to read)
- **What to build:** Replace the stub body of `plugins/sdd/skills/unpause/SKILL.md` with the real resume flow.

  Behavior:
  - **No arguments.** `/sdd:unpause` accepts no positional arguments and no flags. It is a zero-argument command sourced entirely from `docs/project-state.json`.
  - **Read `lastCommand`** from `docs/project-state.json`. This value names the command to resume. Per Item 1's design, `/sdd:pause` does NOT overwrite `lastCommand` with `/sdd:pause`; it preserves the in-flight command name. So `/sdd:unpause` reads the same `lastCommand` value that the original command set when it started.
  - **Locate and read the resume file** at `docs/<command>-resume.md`, where `<command>` is the `lastCommand` value with the `/sdd:` prefix stripped. Read this file BEFORE any other interview work — it is the seed for the resumed context.
  - **Read the source command's SKILL.md** at `plugins/sdd/skills/<command>/SKILL.md` before resuming the interview. This loads the source command's loading directives, behavior, and any references it pulls in transitively. The source SKILL.md governs the resumed flow.
  - **Resume at the Pending decision section** of the resume file. That section captures the literal open question (or decision in progress) at pause time. `/sdd:unpause` re-poses that question to the user — same wording, same context, same options — and then proceeds with the source command's interview from that point.

  Note on the `lastCommand` state-update rule: per sdd-guide every command writes `lastCommand` on startup. But `/sdd:unpause`'s job is to delegate to the source command, not act as a separate command. The cleanest approach: `/sdd:unpause` either (a) preserves `lastCommand` at the source-command value so subsequent commands continue the chain correctly, or (b) sets `lastCommand` to the source command's name (which it should already be). Subagent: confirm `lastCommand` remains the source command's name through the `/sdd:unpause` invocation. This mirrors the `/sdd:pause` no-clobber rule.

  Update the Loading section to add `references/pause-resume.md`. Update the frontmatter `description` to drop "Stub in this release". Delete the `## Status` section.
- **Acceptance criteria:**
  - [x] `aaay` `/sdd:unpause` accepts no arguments.
  - [x] `aaaz` `/sdd:unpause` reads `lastCommand` from `docs/project-state.json` and treats that value as the command to resume.
  - [x] `aaba` `/sdd:unpause` locates and reads `docs/<command>-resume.md` (where `<command>` is the `lastCommand` value without the `/sdd:` prefix) before any other interview work.
  - [x] `aabb` `/sdd:unpause` reads the source command's `SKILL.md` before resuming the interview.
  - [x] `aabc` `/sdd:unpause` resumes the interview at the question or decision captured in the resume file's Pending decision section.
- **Verification:**
  - Re-read the updated `unpause/SKILL.md` and confirm the zero-argument rule is stated.
  - Confirm the `lastCommand`-read step happens before any interview work.
  - Confirm the resume file is loaded BEFORE the source command's SKILL.md, or in tandem — the spec says "before any other interview work" which means both are loaded before resuming. Order between them is flexible.
  - Confirm the source command's SKILL.md path is `plugins/sdd/skills/<command>/SKILL.md` and is read before resuming.
  - Confirm the resume point is the literal Pending decision section, not a generic "resume the interview" gesture.
  - Confirm `lastCommand` is preserved as the source command's name (not overwritten to `/sdd:unpause`) so the next command in the chain sees a consistent state.
  - Confirm the frontmatter description is updated and `## Status` section is deleted.
- **Status:** [x] Complete

---

### Item 4: `/sdd:unpause` missing-file handling `[PRD: aabd]` `[FB: FB-005]`

- **PRD ref:** `prd.md > Multi-Session Resume` (unpause stories) `[PRD: aabd]`
- **Spec ref:** `spec.md > /sdd:unpause (NEW)` (missing-file → exit with message)
- **What to build:** In `plugins/sdd/skills/unpause/SKILL.md`, after reading `lastCommand` from `docs/project-state.json` but before reading the source command's SKILL.md, check whether `docs/<command>-resume.md` exists.

  If the resume file does not exist:
  - Exit cleanly. Do not attempt to read the source SKILL.md. Do not attempt to resume.
  - Emit a message that names the expected file path (`docs/<command>-resume.md`) and gives the user clear next steps. Two options the message should surface:
    1. Run `/sdd:pause` if the user has a command in-flight and wants to pause it (this would only apply if the user mistakenly ran `/sdd:unpause` first).
    2. Re-run the original command (e.g., `/sdd:scope`) from scratch if nothing was paused and the user wants to start fresh.

  If the resume file exists, proceed to Item 3's read-and-resume flow.

  Item 4 should land as a check inserted into the Item 3 flow at the point between `lastCommand` read and source-SKILL.md read. Cleanly handle the case where `lastCommand` itself is unset — that is a separate edge case (no in-flight command to resume, no resume file possible). Subagent: decide how to message that case — either treat it as the same "no resume file" path with an adjusted message, or as a separate path. Use judgment per the spec wording.
- **Acceptance criteria:**
  - [x] `aabd` When no resume file exists at the expected path for the current `lastCommand` value, `/sdd:unpause` exits with a message naming the missing file and pointing the user to either `/sdd:pause` or re-running the original command.
- **Verification:**
  - Re-read the updated `unpause/SKILL.md` and confirm the missing-file check fires after the `lastCommand` read but before the source-SKILL.md read.
  - Confirm the exit message names the missing file path literally.
  - Confirm the message points the user at both options: `/sdd:pause` (if something is in-flight) and re-running the original command (if nothing is in-flight).
  - Confirm the no-write rule on this exit path (no `lastCommand` clobber, no other writes).
- **Status:** [x] Complete

---

### Item 5: Resume-file cleanup (cross-cutting) `[PRD: aabe, aabf, aabg]` `[FB: FB-006]`

- **PRD ref:** `prd.md > Multi-Session Resume` (cleanup story) `[PRD: aabe, aabf, aabg]`
- **Spec ref:** `spec.md > /sdd:unpause (NEW)` (resume-file cleanup paragraph) + `references/pause-resume.md > ## Cleanup`
- **What to build:** Wire resume-file deletion into each pause-able command. When a pause-able command's underlying interview completes its document-generation step (i.e., once the command has successfully produced or updated its target artifact — `docs/discovery.md`, `docs/scope.md`, `docs/prd.md`, `docs/spec.md`, `docs/sprint-N.md`, etc.), the command itself deletes `docs/<command>-resume.md` if it exists.

  Pause-able commands per AC `aaar`:
  - `/sdd:discovery` → deletes `docs/discovery-resume.md` after `docs/discovery.md` is written
  - `/sdd:scope` → deletes `docs/scope-resume.md` after `docs/scope.md` is written
  - `/sdd:prd` → deletes `docs/prd-resume.md` after `docs/prd.md` is written
  - `/sdd:spec` → deletes `docs/spec-resume.md` after `docs/spec.md` is written
  - `/sdd:plan` → deletes `docs/plan-resume.md` after `docs/sprint-N.md` is written (sprint generation is `/sdd:plan`'s document-generation step)
  - `/sdd:refine` → deletes `docs/refine-resume.md` after refinement writes complete (updates to `docs/prd.md` and/or `docs/spec.md`)
  - `/sdd:polish` → deletes `docs/polish-resume.md` after the polish item's artifact write completes

  Cleanup rules per `references/pause-resume.md > ## Cleanup`:
  - `/sdd:pause` does NOT delete. Only writes.
  - `/sdd:unpause` does NOT delete on entry. It reads, resumes, then the resumed command deletes on completion.
  - **Missing-file cleanup is not an error.** The delete is best-effort — `delete-if-exists`. A missing file means either the user already cleared it manually or the consolidation path never wrote one. Both are valid states; the command continues silently.
  - After deletion, no resume file exists at the expected path until `/sdd:pause` is invoked again.

  Implementation: each of the seven pause-able command SKILL.md files needs a small addition to its end-of-interview / wrap-up flow that says: "After writing `<artifact>.md`, delete `docs/<command>-resume.md` if it exists. Missing file is not an error — continue silently." Match the prose convention used for other end-of-interview cleanup steps. Look at how `/sdd:discovery` describes its end-of-interview writes (Sprint 10 Item 6) for the prior-art convention.

  This is the largest cross-cutting item in the sprint — seven SKILL.md files get a small addition each. They are not interconnected; the edits can be made in any order. The subagent should batch the edits for efficiency.
- **Acceptance criteria:**
  - [x] `aabe` When a resumed command's underlying interview completes its document generation step, the resumed command deletes its own resume file at `docs/<command>-resume.md`.
  - [x] `aabf` When a command completes its document generation and no resume file exists at the expected path, the command raises no error and continues.
  - [x] `aabg` After a resumed command's deletion step runs, no resume file for that command exists in `docs/` until `/sdd:pause` is invoked again.
- **Verification:**
  - Re-read each of the seven pause-able command SKILL.md files (`discovery`, `scope`, `prd`, `spec`, `plan`, `refine`, `polish`) and confirm each has an end-of-interview / wrap-up step that deletes its own `docs/<command>-resume.md`.
  - Confirm each deletion step is `delete-if-exists` — missing file is not an error.
  - Confirm the deletion fires after the artifact write, not before.
  - Confirm the cleanup phrasing references the rule from `references/pause-resume.md` rather than restating it.
  - Side-effect check: this item's resumed-command deletion behavior is the symmetric counterpart of `/sdd:pause`'s write behavior. AC `aabg` ("no resume file for that command exists … until `/sdd:pause` is invoked again") is satisfied transitively: cleanup deletes it, `/sdd:pause` is the only path that writes it.
- **Status:** [x] Complete

---

## Notes

- All 17 ACs of the Multi-Session Resume epic close in this sprint: `aaar`–`aaax` (Items 1-2), `aaay`–`aabd` (Items 3-4), `aabe`–`aabg` (Item 5). No collisions with closed IDs from Sprints 1-10. PRD Unvetted section is empty.
- **Sprint 9 + Sprint 10 PRD checkoff regression detected and fixed at the start of Sprint 11 planning.** Sprints 9 and 10 closed 24 + 10 = 34 ACs in their sprint files but did not propagate the checkoffs to `docs/prd.md`. All 34 ACs are now marked complete in the PRD. Sprints 1-8 propagated correctly; the regression was specific to Sprints 9 and 10. Watch for the same miss at Sprint 11 wrap-up.
- **`lastCommand` no-clobber design call for `/sdd:pause` and `/sdd:unpause`.** The sdd-guide state-tracking rule says every command writes `lastCommand` to its own name on startup. For `/sdd:pause` and `/sdd:unpause` this would clobber the in-flight command name and break the read-back chain. The cleanest interpretation per AC `aaaz` ("`/sdd:unpause` reads `lastCommand` from `docs/project-state.json` and treats that value as the command to resume") is that `lastCommand` continues to hold the in-flight command name across both pause and unpause invocations. Item 1's subagent resolves this concretely; Items 3 and 4 inherit the same design. Cross-reference: AC `aaaa` ("`/sdd:feedback`'s startup sequence contains no step that writes to `lastCommand`") establishes the no-clobber pattern for utility commands; `/sdd:pause` and `/sdd:unpause` follow the same exception.
- **FB mapping.** `/sdd:pause` is FB-004 (Items 1-2). `/sdd:unpause` is FB-005 (Items 3-4). Pause-file cleanup is FB-006 (Item 5). `/sdd:build` wrap-up writes `Shipped (sprint/item)` entries to `docs/v2-verification.md` when the sprint closes — three FB rows update.
- **Cached marketplace plugin is still v1.** Same caveat as every prior sprint — Sprint 11's edits ship to disk in `plugins/sdd/skills/pause/`, `plugins/sdd/skills/unpause/`, and the seven pause-able command SKILL.md files, but they aren't live as agent behavior until the maintainer reinstalls. First post-Sprint-11 invocation of `/sdd:pause` and `/sdd:unpause` will be the first verification of the implementation in production.
- **No deepening rounds requested.** Pre-vetted ACs, fully-specified spec resolutions (`references/pause-resume.md` is canonical and complete), single-epic scope, two-command granularity. The only genuine design call (`lastCommand` no-clobber) is captured in the sprint plan notes above and resolved by the Item 1 subagent.
- **Three-tier `/clear` consolidation interaction.** The three-tier between-rounds context-management model (Sprint 9, `aacm`–`aacs`) may recommend `/clear` after a deepening round. Before instructing the user to `/clear`, the agent writes a resume file using the SAME schema and location as `/sdd:pause` (per `references/pause-resume.md > ## Three-tier /clear consolidation`). After `/clear`, the user runs `/sdd:unpause`, which reads that file and resumes via Sprint 11's Item 3 + Item 4 logic. So Sprint 11's `/sdd:unpause` implementation serves BOTH manual `/sdd:pause` resumption AND the automatic `/clear`-consolidation resume path. No additional ACs or items required — the `/sdd:unpause` flow is identical for both triggers.
- **Natural next-sprint candidates after Sprint 11:**
  - **Right-sizing wire-through** (`aabr`–`aaby`, 8 ACs) — `/sdd:scope` detection + cross-command reading. Touches every interview command including the freshly-implemented `/sdd:discovery`.
  - **`/sdd:prd`-only polish** (`aacd`–`aacl`, 9 ACs) — AC quality check, phase-split threshold, Unvetted section. Single-file work in `prd/SKILL.md`.
  - **Process Notes Growth remainder** (`aagf`–`aagn`, 9 ACs) — wire scope/prd/spec/plan/retro to per-phase notes files.
  - **Automatic Backlog Generation** (`aafs`–`aagd`, 12 ACs) — cross-cutting across 5 planning commands.
  - **`/sdd:retro` at Project Close** (`aaey`–`aafe`, 6 ACs) — gating + cross-project pattern capture.
  - **v1→v2 Migration guide** (`aago`–`aahi`, 21 ACs) — single `MIGRATION-v1-to-v2.md` file. Becomes more valuable as v2 nears GA.
  - **`/sdd:polish` re-open mechanism** (`aaev`–`aaex`, 3 ACs) — small.
  - **v2 Release Verification remaining** (~12 ACs across seeding, manual checkoff convention, approach documentation, persistence).
