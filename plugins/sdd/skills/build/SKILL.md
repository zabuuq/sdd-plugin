---
name: build
description: Execute the current sprint checklist. Builds each item according to spec references and acceptance criteria.
disable-model-invocation: true
allowed-tools: Bash(git *) Read Write Edit Glob Grep Agent
---

# /sdd:build

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

Read `skills/sdd-guide/references/context-loading.md` for the loading rules that govern this command. `/sdd:build` uses surgical context loading — it loads per-item spec and PRD sections, never full documents.

Read `skills/sdd-guide/references/living-documents.md` for the update protocol. Build work may reveal new patterns that need to be captured in CLAUDE.md or AGENTS.md.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/references/context-loading.md`
3. `skills/sdd-guide/references/living-documents.md`
4. `CLAUDE.md` (project root) — always loaded in full, regardless of length. CLAUDE.md instructs reading `AGENTS.md` as part of its content; follow that instruction.
5. `docs/project-state.json` — read `currentSprint`, `buildMode`, `commandExplanationsShown`, and `lastCommand`
6. Current sprint file: `docs/sprint-N.md` where N is `currentSprint` from project-state.json

**Do not load** the full `docs/spec.md` or full `docs/prd.md` at startup. Spec and PRD sections are loaded surgically per item during the build loop.

## Prerequisites

All prerequisites are checked before any work begins. If any check fails, stop immediately.

- `docs/project-state.json` must exist and parse as valid JSON. If missing or invalid: "Run `/sdd:scope` first."
- `currentSprint` must be set in `docs/project-state.json`. If missing: "Run `/sdd:plan` first."
- `docs/sprint-N.md` (where N = `currentSprint`) must exist. If missing: "Run `/sdd:plan` first."
- The sprint file must have at least one unchecked item (a `### ` item heading whose **Status** line contains `[ ]`). If all items are checked: "Sprint complete. Run `/sdd:polish` to add polish work, or proceed to `/sdd:plan` for the next sprint (or `/sdd:retro` if the project is complete)."

## State Updates (immediate)

Before doing any other work:

1. Set `lastCommand` to `"/sdd:build"` in `docs/project-state.json`.
2. Read and process `docs/open-concerns.md` per sdd-guide open concerns protocol.

## First-Run Explanation

Check `docs/project-state.json` for `commandExplanationsShown.build`.

**If `build` has NOT been shown (or `commandExplanationsShown` does not exist):**

Output the following as plain text. This is not a question — do not wait for acknowledgment. Output it and continue.

---

**What /sdd:build does**

Build executes your sprint checklist one item at a time. For each item, it loads only the specific spec section and PRD story referenced in the checklist — not the entire documents. This surgical loading keeps context focused on exactly what needs to be built.

Each item is built according to its spec reference and verified against its acceptance criteria. Tests are written when the testing strategy calls for them. After each item, the sprint checklist is updated and you review what was built before moving on.

If something breaks, we stop, assess, and fix it together before continuing. The sprint checklist is a living document — it adapts when plans meet reality.

---

After outputting, set `commandExplanationsShown.build` to `true` in `docs/project-state.json`.

**If `build` HAS been shown:** Skip the explanation entirely. Do not mention it.

## Behavior

Read `buildMode` from `docs/project-state.json`. Branch on its value:

- If `buildMode` is `"step-by-step"`: follow the **Step-by-Step Mode** section below.
- If `buildMode` is `"autonomous"`: follow the **Autonomous Mode** section below.
- If `buildMode` is missing or unrecognized: default to step-by-step mode and inform the user.

Build mode is locked for the entire sprint. Do not offer to switch modes. If the user asks to switch, explain that build mode is set during `/sdd:plan` and locked for the sprint's duration.

---

### Step-by-Step Mode

#### Step 1: Read Open Concerns

Read `docs/open-concerns.md`. Check for any concerns that are relevant to the current sprint or the next item to be built. Surface anything the user should be aware of before building begins.

#### Step 2: Identify the Next Unchecked Item

Read the current sprint file (`docs/sprint-N.md`). Find the first `### ` item heading whose **Status** line contains `[ ]` (not started or incomplete).

If no unchecked items remain, route directly into the **Wrap-Up Phase** (defined below). The wrap-up phase handles the close-or-recommend-polish decision and emits the next-command recommendation — do not emit a separate "sprint complete" stop message here.

Report to the user which item is next. Include the item title and a brief summary of what will be built.

#### Step 3: Surgical Context Loading

From the unchecked item, extract:

- **Spec ref** — e.g., `spec.md > API Design > Authentication Endpoints`
- **PRD ref** — e.g., `prd.md > User Authentication > Login Flow`

Load **only** the referenced section from `docs/spec.md`. Navigate to the exact heading path and extract that subsection. Do not load the full spec.

Load **only** the referenced story from `docs/prd.md`. Navigate to the exact epic and story and extract that section. Do not load the full PRD.

If either reference cannot be found (heading does not exist, file structure has changed):
- Report the broken reference to the user.
- Ask the user to clarify which section should be referenced.
- Do not guess or substitute a different section.

#### Step 4: Build the Item

Build the implementation described in the item's **What to build** field, guided by:

- The loaded spec subsection — this defines *how* to build it (architecture, patterns, data structures, APIs).
- The loaded PRD story — this defines *what* it should do (user-facing behavior, acceptance criteria).
- The item's **Acceptance criteria** — these are the specific checkboxes that must pass.
- `CLAUDE.md` and `AGENTS.md` conventions — these define project-wide patterns and coding standards.

Work through the implementation methodically. Follow the spec's architecture and patterns. Respect existing code conventions from CLAUDE.md.

#### Step 5: Testing and Verification

Check the item's **Verification** field and the project's testing strategy (from the spec).

**If the testing strategy calls for automated tests:**
- Write the tests according to the testing patterns defined in the spec and CLAUDE.md.
- Run the tests using the appropriate test runner.
- If tests fail: fix the implementation, re-run, and iterate until they pass.
- Report test results to the user.

**If manual verification is specified:**
- Explain clearly what to look for and how to test.
- Describe the expected behavior step by step.
- Note any edge cases the user should verify.

**If both automated and manual verification apply:** do both.

#### Step 6: Show What Was Built

Present a summary to the user:
- What files were created or modified.
- How the implementation satisfies each acceptance criterion.
- Test results (if automated tests were run).
- Manual verification instructions (if applicable).
- Any decisions made during implementation that the user should know about.

This is the user's opportunity to review before the item is marked complete. Wait for the user to confirm the item is acceptable. If the user identifies issues, address them before proceeding.

#### Step 7: Check Off the Item

Once the user confirms, update the sprint file:

- Change the item's **Status** from `[ ] Not started` to `[x] Complete`.
- Check off all acceptance criteria checkboxes for this item.

#### Step 8: Update CLAUDE.md and AGENTS.md

If the build revealed new patterns, conventions, or architectural decisions that should apply to future work:

- Update `CLAUDE.md` with Claude-specific conventions (coding patterns, file organization rules, testing conventions).
- Update `AGENTS.md` with agent-agnostic project context (architecture decisions, data flow patterns, API conventions).

Follow the living-documents reference: show the user what changed and why. These updates are lightweight — only add what is genuinely new and project-wide. Do not add item-specific details that only matter for this one checklist item.

If no new patterns emerged, skip this step.

#### Step 9: Update Open Concerns

Update `docs/open-concerns.md` per sdd-guide protocol:

- Resolve concerns that were addressed by this item's implementation.
- Defer concerns that belong to future items or sprints.
- Append new concerns that arose during the build.

If no concerns changed, skip this step.

#### Step 10: Process Notes

Append build notes to `process-notes-sprint-N.md` where N is the current sprint number, per sdd-guide's `## Process Notes` (four categories, real-time, append-only). Capture build specifics: which item was built and what was implemented; decisions made during the build and rationale; any deviations from the spec or sprint plan and why; issues encountered and how resolved; test results summary.

#### Step 11: Prompt to Continue

Tell the user:

"Item N is complete. Run `/clear`, then `/sdd:build` to continue with the next item."

This clears the context window of implementation details from the completed item, keeping the next build cycle focused. Do not automatically proceed to the next item in step-by-step mode.

---

### Autonomous Mode

Autonomous mode works through multiple checklist items sequentially, pausing at checkpoints for user review. It uses subagents to execute individual items.

#### Overview

1. Read the full sprint checklist and identify all unchecked items.
2. For each unchecked item, dispatch a subagent to build it.
3. Pause at checkpoints (every 3-4 items) for user review.
4. Produce a summary at the end rather than per-item process notes.

#### Step 1: Read Open Concerns

Read `docs/open-concerns.md`. Surface anything the user should be aware of before the autonomous build begins.

#### Step 2: Survey Unchecked Items

Read the current sprint file and list all unchecked items (items whose **Status** line contains `[ ]`). Report the total count to the user and confirm they are ready to begin the autonomous build.

#### Step 3: Build Loop

For each unchecked item, in sequence:

##### 3a. Surgical Context Loading

Extract the **Spec ref** and **PRD ref** from the item. Load only the referenced spec subsection and PRD story — not full documents. This is the same surgical loading as step-by-step mode.

##### 3b. Dispatch to Subagent

Use the `Agent` tool to spawn a subagent for the item. Provide the subagent with:

- The full checklist item (all fields: title, PRD ref, spec ref, what to build, acceptance criteria, verification, status).
- The loaded spec subsection content.
- The loaded PRD story content.
- The content of `CLAUDE.md` and `AGENTS.md` for project conventions.
- Clear instructions: build what is described, follow the acceptance criteria, commit when done (following the project's git conventions from CLAUDE.md), and report back what was built and any issues encountered.

The subagent builds the item, writes any tests called for by the verification field, and commits the work.

##### 3c. Collect Results

When the subagent finishes, note:
- What was built (files created/modified).
- Whether all acceptance criteria were met.
- Test results (if applicable).
- Any issues or deviations reported.

If the subagent reports a failure, follow the **Error Handling** protocol below — stop the autonomous build and work with the user before continuing.

##### 3d. Check Off the Item

Update the sprint file: change the item's **Status** from `[ ]` to `[x]` and check off its acceptance criteria.

##### 3e. Update CLAUDE.md and AGENTS.md

If the subagent's work revealed new patterns or conventions, update the living documents per the living-documents reference. Show the user what changed.

##### 3f. Checkpoint Check

After completing the item, check whether a user review checkpoint is due. Checkpoints occur every 3-4 items.

**If this is a checkpoint:**

1. Summarize what has been built since the last checkpoint (or since the start). This is a high-level summary, not a per-item replay. Focus on: what the user can now see, use, or test that they couldn't before.
2. Tell the user what to look for — what to run, what to click, what to verify.
3. Wait for the user's response. If something looks wrong, stop and fix it before continuing.
4. Ask the user to confirm they are ready to continue.

**If this is NOT a checkpoint:** continue to the next item without pausing.

#### Step 4: Completion Summary

When all items are built (or the build is stopped due to errors):

- Report total items completed vs. total items in the sprint.
- Summarize what was built across the entire autonomous run.
- Note any issues encountered and how they were resolved.
- Update `docs/open-concerns.md` with any new concerns from the build.

Step 4 does **not** write process notes for the sprint. The canonical close-sprint process notes are written by Step W10 of the **Wrap-Up Phase** (below), which is the single source of truth for the sprint's close-sprint write. Keep the bullets above as user-facing reporting only.

If the autonomous run completed all sprint items (rather than stopping due to errors), proceed into the **Wrap-Up Phase** (defined below) instead of emitting a terminal stop message. The wrap-up phase emits the next-command recommendation; Step 4 no longer does. If the run was halted by errors with items still unchecked, do not enter wrap-up — leave the sprint open and stop after the summary.

#### No Per-Item Process Notes

In autonomous mode, do not write per-item process notes during the build. The subagents handle the work; the orchestrator keeps moving. The sprint's process notes are written once, at close-sprint, by Step W10 of the Wrap-Up Phase.

#### Mode Is Locked

Build mode cannot be switched during a sprint. If the user asks to switch to step-by-step, explain that the mode was set during `/sdd:plan` and is locked until the next sprint.

---

## Wrap-Up Phase

The wrap-up phase runs immediately after the final sprint checklist item's **Status** flips to `[x]`. It applies in both step-by-step and autonomous modes and replaces the prior "Sprint complete. Run `/sdd:polish`..." stop message in each.

- **Step-by-step mode:** wrap-up fires after Step 7 of the item that closed the last `[ ]`. The remaining per-item steps (8 through 11) still run for that item; wrap-up follows them. Wrap-up also fires when `/sdd:build` is invoked and Step 2 finds no unchecked items remaining — that is the re-entry path into wrap-up.
- **Autonomous mode:** wrap-up fires after Step 3 has completed the last unchecked item and before Step 4's old completion stop message. Step 4's summary still runs; wrap-up follows it and replaces the terminal "Sprint build complete..." line.

Wrap-up does **not** commit changes. This is consistent with the `/sdd:build` no-auto-commit posture defined in `docs/spec.md > Command Skills > /sdd:build` and AGENTS.md. Any files wrap-up writes are left for the user to commit.

### Step W1: Surface Iteration Candidates

Before asking the satisfaction question, scan `process-notes-sprint-N.md` (where N is `currentSprint` from `docs/project-state.json`) for `[iteration-candidate]` entries written **before wrap-up began** — entries written during checklist execution, not entries the wrap-up itself might write. Use the `[iteration-candidate]` parser documented in `skills/sdd-guide/references/sprint-tags.md`; refer to it by name rather than redefining the regex.

For each matched entry, capture a brief one-line summary suitable for surfacing alongside the satisfaction question. If no `[iteration-candidate]` entries exist in the file, the candidate list is empty and only the satisfaction question is emitted in the next step.

### Step W2: Emit the Satisfaction Question

Emit the literal text below as a single message. Do **not** paraphrase, reword, or change punctuation — this exact string is required:

```
Satisfied with this sprint, or items to iterate on?
```

If Step W1 found one or more iteration candidates, surface them in the same message: a short header line, then one bullet per candidate with its one-line summary. The literal satisfaction question still appears verbatim in the same message. If Step W1 found no candidates, emit just the satisfaction question alone — do not invent a placeholder list or mention that none were found.

Wait for the user's answer before proceeding. One question at a time per shared behavior.

### Step W3: Route on the Satisfaction Answer

Interpret the user's response:

- **"items to iterate"** (or equivalent — the user wants to iterate before closing the sprint): recommend running `/sdd:polish`. The sprint stays open. **No close-sprint actions are performed.** Wrap-up exits here.
- **"satisfied"** (or equivalent — the user is done with this sprint): if Step W1 surfaced one or more candidates, proceed to Step W4 (per-candidate disposition). If no candidates were surfaced, proceed directly to the close-sprint actions handoff at the end of this section.

If the user's answer is ambiguous, ask a single clarifying question before routing — do not guess.

### Step W4: Per-Candidate Disposition

This step only runs if Step W1 surfaced one or more iteration candidates **and** the user answered "satisfied" in Step W3.

For each surfaced candidate, ask the user to choose one disposition from this set:

- `discard` — the candidate will not be acted on; record the discard decision for the close-sprint note.
- `capture as tech-debt` — the candidate will be recorded as a tech-debt entry in `process-notes-sprint-N.md` during close-sprint actions.
- `actually iterate` — the candidate is real iteration work; the sprint should not close yet.

Ask one question per candidate. Do **not** batch (one question at a time per shared behavior). Record each disposition decision in working memory for the close-sprint actions that follow this section — the actual writes to `process-notes-sprint-N.md` happen during close-sprint.

### Step W5: Route on Disposition Outcomes

After all dispositions are collected, evaluate the set:

- **Any candidate assigned `actually iterate`:** switch to the "items to iterate" branch — recommend running `/sdd:polish`, leave the sprint open, and perform **no** close-sprint actions. The recorded `discard` / `capture as tech-debt` decisions for the other candidates are not acted on in this run; if the user later closes the sprint, wrap-up runs again and the surviving candidates are re-evaluated. Wrap-up exits here.
- **No candidate assigned `actually iterate`** (every disposition is `discard` or `capture as tech-debt`, or no candidates existed): proceed to close-sprint actions. The recorded dispositions are handed off to the close-sprint actions section, which performs the actual `process-notes-sprint-N.md` writes for tech-debt entries and discard notes.

After all dispositions are collected and no `actually iterate` was chosen, proceed to close-sprint actions (defined below in this same `## Wrap-Up Phase` section).

### Step W6: PRD AC Checkoff

Close-sprint actions begin here. This step writes the PRD AC checkoff for every closed sprint item in the current sprint file (`docs/sprint-N.md` where N = `currentSprint` from `docs/project-state.json`).

Use the `[PRD: ...]` parser documented in `skills/sdd-guide/references/sprint-tags.md`; refer to it by name rather than redefining the regex. Use the AC line format (also documented there) when locating and updating AC checkboxes in `docs/prd.md`.

For each closed (`[x]`) item in the current sprint file:

1. Parse the item's `[PRD: id1, id2, ...]` tag. If the item carries `[unmapped]` instead, skip it — no PRD ACs to check off.
2. For each referenced PRD AC ID, decide whether to check it off using the rules below.

**Non-split case.** The sprint item maps fully to a story and no story-splitting occurred for that story across any sprint. Check off every PRD AC the item references in `docs/prd.md`. (Satisfies `aaad`.)

**Cross-cutting case.** An AC is referenced by sprint items across multiple sprint files. Scan every `docs/sprint-*.md` file for the AC ID. Check the AC off in `docs/prd.md` only when every sprint-item reference to that AC across every sprint file is closed (`[x]`). If any unchecked reference remains in any sprint file, leave the AC unchecked. (Satisfies `aaae` for the partial case; satisfies `aaaf` when this sprint's close is the final-completer.)

**Update format.** Change `- [ ] \`abcd\` …` to `- [x] \`abcd\` …` in `docs/prd.md` for each newly-checked AC. Preserve the rest of the line verbatim.

**Bookkeeping.** Record every AC ID newly checked off in this step in working memory under "PRD ACs checked" for the close-sprint manifest assembled in Step W10.

After the pass completes, no AC referenced by an entirely-closed mapping should remain unchecked. (Satisfies `aaag`.) This entire step is the close-sprint mechanism for PRD AC checkoff. (Satisfies `aaeq`.)

### Step W7: Story Splits for Unfinished Items

Identify every sprint item in the current sprint file whose **Status** line still contains `[ ]`. In autonomous mode this set is normally empty (wrap-up fires only after the last item closes), but the wrap-up may be re-entered from a different state — handle the unfinished-items case robustly even when it is expected to be empty.

For each unfinished item, in order:

1. Emit a single prompt asking the user whether to split the story off for a future sprint. **One question per item — do not batch.**
2. Wait for the user's answer before moving to the next item.

**On user confirmation (split this item):**

- Move the unfinished item's unchecked PRD ACs to a new story in `docs/prd.md`. Use a clear new-story anchor that conveys the carry-forward intent.
- Generate fresh 4-char lowercase alphabetic IDs (`[a-z]{4}` pattern, per `references/sprint-tags.md`) for any new ACs introduced by the split. **Collision-check against existing PRD IDs before settling** — do not reuse retired IDs.
- ACs that move unchanged from the original story to the new story keep their existing IDs (IDs are stable across reorders per `references/sprint-tags.md`).
- Sprint-file marking choice for this SKILL: leave the unfinished item's `[ ]` checkbox as-is in the sprint file. The unfinished status is the clearest signal that the item did not close, and the close-sprint manifest's "Story splits" line is the canonical record of where the work moved. The split is recorded in process notes (Step W10) and the manifest, not by mutating the sprint-file line. Future readers locate the split via the manifest.
- Record the split as `<original-story-anchor> → <new-story-anchor>: <ac-ids>` in working memory for the close-sprint manifest assembled in Step W10.

**On user rejection (do not split):**

- Leave the item exactly as-is in the sprint checklist (`[ ]` Status preserved). It carries forward to a future sprint via normal sprint-loop mechanisms; no PRD changes are made for this item.

(Satisfies `aaer`.)

### Step W8: "Anything Notable?" Prompt

Emit **exactly one** prompt covering quality concerns, tech debt, and surprises. A single open-ended question — not three separate questions, not a multi-part question split across messages. Suggested phrasing:

```
Anything notable from this sprint — quality concerns, tech debt, or surprises?
```

Wait for the user's response. Capture the answer verbatim for the process-notes write in Step W10. (Satisfies `aaes`.)

### Step W9: v2-verification Row Updates

This step is sdd-plugin-specific. It runs **before** the process-notes write (Step W10) because the manifest's `v2-verification rows updated` field carries the FB-IDs touched here.

**Activation gate.** Check whether `docs/v2-verification.md` exists in the running project's `docs/` directory. If the file is **absent: skip this step entirely. No warning, no fallback write, no auto-creation.** The file's presence in the current project is the sole activation signal. When this step is skipped, the manifest's `v2-verification rows updated` line in Step W10 reads `(none)`. (Satisfies `aahv`.)

**When the file exists**, for each closed (`[x]`) sprint item in the current sprint file (`docs/sprint-N.md` where N = `currentSprint`) that carries a `[FB: FB-NNN]` tag (use the `[FB: ...]` parser documented in `skills/sdd-guide/references/sprint-tags.md`; refer to it by name rather than redefining the regex):

1. For each `FB-NNN` ID in the tag's payload, locate the row in `docs/v2-verification.md` whose `ID` column matches `FB-NNN`. The full column schema is documented in `docs/spec.md > Plugin-Project-Specific Artifacts > docs/v2-verification.md`; refer to it by name rather than restating the schema.
2. Append `sprint N, item M` to that row's `Shipped (sprint/item)` column, where:
   - N is `currentSprint` from `docs/project-state.json`.
   - M is the sprint-item ordinal: the 1-based count of the closed item among the sprint file's `### Item M:` headings (or whatever item-numbering convention the sprint file uses — match the file's own numbering).
   - If the column already contains a value (e.g. a prior sprint's cross-sprint contribution), append with a `; ` separator. Example: `sprint 4, item 2; sprint 5, item 1`. (Satisfies `aahk`.)
3. The `Notes` column **may** receive a brief auto-note (e.g., the closed sprint item's title or a one-line summary of what shipped). Auto-notes are optional; the user remains free to edit Notes manually before or after this write.
4. The `Verified` column is **never written by `/sdd:build`** — not as `[ ]`, not as `[x]`, not at all. The Verified column remains `[ ]` until the user manually checks it. State this explicitly so a future reader does not misread silence as ambiguity.
5. Record each touched `FB-NNN` ID in working memory under "v2-verification rows updated" for the close-sprint manifest assembled in Step W10.

**Missing-row handling.** If a closed item's `[FB: ...]` tag references an `FB-NNN` that has no matching row in `docs/v2-verification.md`, emit a single informational warning naming the missing `FB-NNN`. Do **not** auto-create the row — row creation is `/sdd:spec` seeding territory, not `/sdd:build`'s. The warning is informational; the wrap-up continues.

After this step completes, the comma-separated list of touched FB-IDs is available for Step W10's manifest field. If no items carried `[FB: ...]` tags, or the activation gate skipped the step, the list is empty and the manifest field reads `(none)`.

### Step W10: Process Notes Write

Append a wrap-up section to `process-notes-sprint-N.md` (where N = `currentSprint`). The append is the canonical close-sprint write for this sprint; it is not duplicated by the autonomous-mode Step 4 summary.

Include the following content, in this order:

- **Decisions made during the sprint** and their rationale.
- **Pushback** from either side (user or agent) during the sprint.
- **Surprises** encountered during execution.
- **Notable items** captured from the Step W8 "anything notable?" response.
- **Tech-debt entries** for every candidate assigned `capture as tech-debt` in Step W4. Format each entry so future commands (e.g., `/sdd:plan`'s carry-forward read) can locate them. Use either an inline `### Tech-debt` header introducing a sub-list of entries, or one `[tech-debt]` line marker per entry — keep it human-readable and machine-locatable.
- **Discard decisions** for every candidate assigned `discard` in Step W4. One brief line each, e.g. `- Discarded: <one-line candidate summary> — reason (if user gave one).`
- **The structured `[close-sprint-manifest]` block** per `docs/spec.md > Cross-Cutting Mechanisms > Close-Sprint Manifest`. The full block format is documented in `references/sprint-tags.md`; follow it exactly. Use this literal layout:

  ```
  [close-sprint-manifest] timestamp: <ISO-8601 timestamp>
  - PRD ACs checked: <comma-separated AC IDs from W6>
  - Story splits: <original-story-anchor> → <new-story-anchor>: <ac-ids>; ... (semicolon-separated for multiples; "(none)" if no splits)
  - Iteration candidate dispositions: <candidate-summary>: <discard|tech-debt|iterate>; ... ("(none)" if no candidates were surfaced)
  - Tech-debt entries: <count> (informational)
  - v2-verification rows updated: <comma-separated FB-IDs> (informational; comma-separated FB-IDs from the preceding v2-verification step, or "(none)" when no rows were touched)
  [/close-sprint-manifest]
  ```

  Body-line rules:
  - When a field has no entries (no splits, no candidates, no PRD ACs checked, etc.), write `(none)` as the value — **do not omit the line**. This keeps the manifest format stable for `/sdd:polish` re-open parsing.
  - The opening anchor carries the timestamp on the same line; the closing anchor `[/close-sprint-manifest]` sits on its own line.
  - Exactly one manifest block is written per sprint close.

(Satisfies `aaet` and the unnumbered AC requiring the `[close-sprint-manifest]` block.)

### Step W11: Next-Command Recommendation

This is the final wrap-up output. After the process-notes write completes (Step W10, including the manifest block), emit a next-command recommendation based on PRD state.

**Re-read `docs/prd.md`'s current state.** The state used for routing is the **post-checkoff state** — that is, the state of `docs/prd.md` AFTER Step W6's PRD AC checkoff and any splits performed in Step W7. Do not route on the pre-checkoff state.

**Determine the recommendation** by inspecting the post-checkoff PRD:

- **All PRD ACs checked AND the Unvetted section is empty:** recommend `/sdd:retro` (project-close path). This is the **only** branch that recommends `/sdd:retro` from `/sdd:build`'s wrap-up. (Satisfies `aafa` — `/sdd:build`'s wrap-up recommends `/sdd:retro` only when its PRD checkoff causes every AC to be checked AND Unvetted is empty.)
- **The Unvetted section contains one or more items:** recommend `/sdd:refine`. The Unvetted backlog should be processed before the next sprint plans.
- **Neither of the above** (some PRD ACs remain unchecked AND the Unvetted section is empty): recommend `/sdd:plan` (continue the sprint loop).

**Emit the recommendation using the End-of-Command Handoff template** from `docs/spec.md > Cross-Cutting Mechanisms > End-of-Command Handoff` — refer to it by name rather than restating the full template. The standard form is a one-line outcome summary followed by the verbatim handoff sentence:

```
Sprint N closed.

Run `/clear`, then `/sdd:[next-command]` to continue.
```

The outcome summary is a single line — suggested phrasing `Sprint N closed.` (substitute the current sprint number); author's call on exact wording, but keep it to one line. The handoff sentence **Run `/clear`, then `/sdd:[next-command]` to continue.** is verbatim per the End-of-Command Handoff template — do not paraphrase it. Substitute `[next-command]` with the routed target (`/sdd:plan`, `/sdd:refine`, or `/sdd:retro`).

(Satisfies `aaeu`.)

---

## Re-Entry After /clear (Step-by-Step Mode)

When `/sdd:build` is invoked after a `/clear` (which is the normal flow in step-by-step mode), reload context in this exact order:

1. **CLAUDE.md** — project conventions first. This includes the instruction to read AGENTS.md.
2. **Current sprint file** (`docs/sprint-N.md`) — find the next unchecked item.
3. **The specific spec subsection and PRD story** referenced by that unchecked item — surgical loading, not full documents.
4. **`docs/open-concerns.md`** — check for anything relevant to the current item.
5. **`docs/project-state.json`** — confirm build mode, sprint number, and last command.

This order ensures: project conventions first, then the immediate task, then supporting context. Do not load the full spec or full PRD during re-entry.

After reloading, resume at **Step 2** of the step-by-step flow (identify the next unchecked item). The startup sequence (state updates, first-run explanation) does not repeat on re-entry — the explanation was already shown and `lastCommand` is already set.

## Error Handling

If something breaks during a build step — a test fails repeatedly, the implementation contradicts the spec, a dependency is missing, or the build produces unexpected results:

1. **Stop immediately.** Do not attempt to push through or work around the problem.
2. **Report what happened.** Describe the error, what was being built, and where things went wrong.
3. **Propose reverting to the last clean state.** If git is available and commits have been made, suggest reverting to the last successful commit. If no commits were made for this item, suggest discarding the in-progress changes.
4. **Work with the user to revise the checklist.** The sprint checklist is a living document. If the plan was wrong, fix the plan:
   - The current item's description, acceptance criteria, or spec reference may need updating.
   - The item may need to be broken into smaller pieces.
   - The item may need to be reordered or deferred.
   - A prerequisite item may be missing from the sprint.
5. **Resume only after the user confirms** the revised plan. Do not auto-resume after an error.

The sprint checklist adapts when plans meet reality. Revising it is not failure — it is the normal workflow when implementation reveals something the plan did not anticipate.

## Important Reminders

- **Surgical context loading is non-negotiable.** Never load the full spec or full PRD during build. Load only the section referenced by the current item. This is the entire point of the spec-ref and PRD-ref fields in the sprint checklist.
- **One item at a time in step-by-step mode.** Build one item, verify it, check it off, then tell the user to `/clear` and `/sdd:build` again. Never batch multiple items in step-by-step mode.
- **Wait for user confirmation before checking off an item.** Step 6 presents what was built; the user must confirm before Step 7 marks it complete.
- **Build mode is locked.** If the user asks to switch modes mid-sprint, explain that the mode was set during `/sdd:plan` and cannot be changed until the next sprint.
- **One question at a time.** If you need user input during a build step, ask one question and wait for the response.
- **First-run explanation only on the very first `/sdd:build` in the project.** Check `commandExplanationsShown.build` — do not show it on subsequent builds or sprints.
- **Re-entry is the normal path.** In step-by-step mode, most `/sdd:build` invocations are re-entries after `/clear`. The re-entry load order is optimized for this — follow it exactly.
- **Respect living-documents defaults.** If build work surfaces new ideas or feature requests, add them to the PRD's unvetted section or `docs/open-concerns.md`. Do not modify the spec or approved PRD stories without going through `/sdd:refine`.
