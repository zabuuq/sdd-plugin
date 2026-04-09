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
- `currentSprint` must be set in `docs/project-state.json`. If missing: "Run `/sdd:sprint` first."
- `docs/sprint-N.md` (where N = `currentSprint`) must exist. If missing: "Run `/sdd:sprint` first."
- The sprint file must have at least one unchecked item (a `### ` item heading whose **Status** line contains `[ ]`). If all items are checked: "Sprint complete. Run `/sdd:iterate` to add polish work or `/sdd:reflect` to review the sprint."

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

Build mode is locked for the entire sprint. Do not offer to switch modes. If the user asks to switch, explain that build mode is set during `/sdd:sprint` and locked for the sprint's duration.

---

### Step-by-Step Mode

#### Step 1: Read Open Concerns

Read `docs/open-concerns.md`. Check for any concerns that are relevant to the current sprint or the next item to be built. Surface anything the user should be aware of before building begins.

#### Step 2: Identify the Next Unchecked Item

Read the current sprint file (`docs/sprint-N.md`). Find the first `### ` item heading whose **Status** line contains `[ ]` (not started or incomplete).

If no unchecked items remain: "Sprint complete. Run `/sdd:iterate` to add polish work or `/sdd:reflect` to review the sprint." Stop.

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

Append build notes to `process-notes-sprint-N.md` where N is the current sprint number.

Capture:
- Which item was built and what was implemented.
- Decisions made during the build and their rationale.
- Any deviations from the spec or sprint plan and why.
- Issues encountered and how they were resolved.
- Test results summary.

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
- Write a build summary to `process-notes-sprint-N.md`.

Tell the user: "Sprint build complete. Run `/sdd:iterate` to add polish work or `/sdd:reflect` to review the sprint."

#### No Per-Item Process Notes

In autonomous mode, do not write per-item process notes during the build. The subagents handle the work; the orchestrator keeps moving. Write a single summary to `process-notes-sprint-N.md` at the end (Step 4).

#### Mode Is Locked

Build mode cannot be switched during a sprint. If the user asks to switch to step-by-step, explain that the mode was set during `/sdd:sprint` and is locked until the next sprint.

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
- **Build mode is locked.** If the user asks to switch modes mid-sprint, explain that the mode was set during `/sdd:sprint` and cannot be changed until the next sprint.
- **One question at a time.** If you need user input during a build step, ask one question and wait for the response.
- **First-run explanation only on the very first `/sdd:build` in the project.** Check `commandExplanationsShown.build` — do not show it on subsequent builds or sprints.
- **Re-entry is the normal path.** In step-by-step mode, most `/sdd:build` invocations are re-entries after `/clear`. The re-entry load order is optimized for this — follow it exactly.
- **Respect living-documents defaults.** If build work surfaces new ideas or feature requests, add them to the PRD's unvetted section or `docs/open-concerns.md`. Do not modify the spec or approved PRD stories without going through `/sdd:refine`.
