---
name: reflect
description: Sprint-level retrospective covering both process and product. Reviews what worked, splits partial stories, recommends next steps.
disable-model-invocation: true
---

# /sdd:reflect

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `docs/project-state.json` — read `currentSprint`, `commandExplanationsShown`, and `buildMode`
3. `docs/sprint-N.md` where N is `currentSprint` from project state
4. `docs/prd.md`
5. `docs/open-concerns.md`

## Prerequisites

- `docs/project-state.json` must exist with a valid `currentSprint` value. If missing or no sprint has been created: "Run `/sdd:sprint` first."
- `docs/sprint-N.md` (current sprint file) must exist. If missing: "Run `/sdd:sprint` first."
- `docs/prd.md` must exist. If missing: "Run `/sdd:prd` first."

If any prerequisite fails, stop immediately. Do not attempt partial execution.

**Sprint completion check:** Scan the current sprint file for unchecked items (`[ ]`). If unchecked items remain, warn the user:

"Sprint N has incomplete items. Reflect works best after a sprint is fully complete — but you can still run it now if you want to review what's been done so far."

Do not block execution. Proceed after the warning.

## State Updates (immediate)

Before doing any other work:

1. Set `lastCommand` to `"/sdd:reflect"` in `docs/project-state.json`.
2. Read and process `docs/open-concerns.md` per sdd-guide open concerns protocol.

## First-Run Explanation

Check `docs/project-state.json` for `commandExplanationsShown.reflect`.

**If `reflect` has NOT been shown (or `commandExplanationsShown` does not exist):**

Output the following as plain text. This is not a question — do not wait for acknowledgment. Output it and continue.

---

**What /sdd:reflect does**

Sprint reflection reviews both process (how the work went) and product (what was built vs. what was planned). If any stories were only partially completed, reflect splits them — completed acceptance criteria get checked off in the PRD, and remaining criteria become a new story ready for the next sprint. The result is a clear picture of where the project stands and what to do next.

---

After outputting, set `commandExplanationsShown.reflect` to `true` in `docs/project-state.json`.

**If `reflect` HAS been shown:** Skip the explanation entirely. Do not mention it.

## Behavior

### Step 1: Read Open Concerns

Read `docs/open-concerns.md` in full. Note any concerns that are relevant to the current sprint or that may have been resolved by work done during the sprint. These will be addressed in Step 7.

### Step 2: Phase 1 — Sprint Retro (Process)

This phase focuses on how the sprint went as a process. Ask the user one question at a time about the following topics. Adapt the order and phrasing to the conversation flow — skip any topic the user already addressed in their responses.

**Topics to cover:**

1. **Smoothness** — Did the sprint go smoothly overall? Were there rough patches?
2. **Sizing** — Were the items sized right? Did anything turn out much bigger or smaller than expected?
3. **Blockers** — What blocked progress? Technical obstacles, unclear requirements, external dependencies, context window limits?
4. **Wins** — What went well? What should be repeated in future sprints?

Do not rush through these. Each topic should get a genuine answer. If the user gives a one-word response, follow up once to draw out specifics — then move on.

### Step 3: Phase 2 — Product Check

This phase focuses on what was built versus what was planned. Review the sprint file alongside the PRD and ask the user one question at a time about the following topics:

**Topics to cover:**

1. **Built vs. planned** — Does what was built match what was planned in the sprint? Any deviations from the spec or PRD?
2. **Quality concerns** — Are there quality issues, technical debt, or shortcuts that need to be addressed?
3. **Partial completions** — Are any items partially complete? Which acceptance criteria were met and which were not?

For each sprint item, compare the acceptance criteria in the sprint file against what was actually built. Flag any discrepancies to the user for confirmation before proceeding to story splitting.

### Step 4: Partial Story Splitting

For any stories identified as partially complete in Step 3, split them into two self-contained stories:

**Completed portion:**
- Create a story containing only the acceptance criteria that were met.
- This story is marked as complete in `docs/prd.md` (check off the criteria).
- The story must be self-contained — it should make sense on its own without referencing the remaining work.

**Remaining portion:**
- Create a new story containing only the acceptance criteria that were NOT met.
- This story remains open in `docs/prd.md` as a new, unstarted item.
- The story must be self-contained — it should have its own complete description, context, and acceptance criteria. Do not write "remaining items from Story X" — write a proper story that stands alone.

**Splitting rules:**
- Each split story gets its own title that accurately describes its scope.
- Acceptance criteria are never duplicated across the split — each criterion belongs to exactly one of the two resulting stories.
- If a story has dependencies that were partially addressed, note this in the new story's description.
- Present the proposed split to the user for approval before writing anything to the PRD.

If no stories need splitting, skip this step entirely.

### Step 5: PRD State Check and Next-Step Recommendation

Scan `docs/prd.md` for the overall project state:

**If unvetted items exist in the PRD** (items in the backlog/unvetted section that have not been through `/sdd:refine`):

Recommend running `/sdd:refine` before the next sprint to vet those items. Example: "You have N unvetted items in the PRD. Running `/sdd:refine` would vet them through a compressed planning interview before your next sprint."

**If all PRD items are complete** (every story across all epics is checked off):

The project is done. Recommend running `/sdd:retro` for a full project retrospective instead of another sprint. Example: "All PRD items are complete. Run `/sdd:retro` for a full project retrospective."

**If incomplete vetted items remain:**

Recommend running `/sdd:sprint` to plan the next sprint. Example: "There are N remaining items ready for the next sprint. Run `/sdd:sprint` when you're ready to plan."

Present the recommendation to the user. They make the final call on what to do next.

### Step 6: Generate Process Notes

Read `skills/sdd-guide/templates/reflect-template.md` for the output format.

Generate `process-notes-sprint-N.md` where N is the current sprint number. If the file already exists (because `/sdd:sprint` or `/sdd:build` wrote to it earlier in the sprint), append the reflect sections rather than overwriting.

Capture:
- Sprint metadata (sprint number, build mode, date).
- Phase 1 findings: smoothness, sizing, blockers, wins.
- Phase 2 findings: built vs. planned, quality concerns, partial completions.
- Story splitting log: which stories were split, what the completed and remaining portions are.
- Open concerns update: what was resolved, deferred, or added.
- Next-step recommendation and the user's decision.

### Step 7: Update Open Concerns

Update `docs/open-concerns.md` per sdd-guide protocol:

- Resolve concerns that were addressed by work done during the sprint. Mark indirect resolutions with an explanation.
- Defer concerns that belong to future sprints or commands.
- Append new concerns that arose during the reflect conversation — quality issues, technical debt, blockers that may recur, etc.

### Step 8: Update Project State

Update `docs/project-state.json`:

- Ensure `lastCommand` is `"/sdd:reflect"`.
- Ensure `commandExplanationsShown.reflect` is `true`.

### Step 9: Wrap Up

Confirm to the user:
- Process notes have been written to `process-notes-sprint-N.md`.
- Any story splits that were applied to `docs/prd.md`.
- Open concerns have been updated.
- The recommended next command based on PRD state.

## Important Reminders

- **One question at a time.** Steps 2 and 3 involve multi-topic interviews — never combine questions into a single message.
- **Free-form questions only.** No multiple-choice options for retrospective questions.
- **Warn but don't block on incomplete sprints.** The prerequisite check warns about unchecked items but allows the user to proceed.
- **Story splitting must be confirmed.** Always present the proposed split to the user before writing to the PRD. Do not auto-split without approval.
- **Each split story is self-contained.** Never reference "remaining work from X" — each story must stand alone with full context and acceptance criteria.
- **Next-step recommendations are conditional.** Check PRD state to determine whether to recommend `/sdd:refine`, `/sdd:sprint`, or `/sdd:retro`.
- **First-run explanation only on the very first `/sdd:reflect` in the project.** Check `commandExplanationsShown.reflect` — do not show it on subsequent runs.
- **Process notes append, not overwrite.** Earlier sprint commands may have already written to `process-notes-sprint-N.md`.
