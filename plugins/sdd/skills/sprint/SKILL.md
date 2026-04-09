---
name: sprint
description: Pull a logical batch of work from the PRD into a buildable sprint. Produces a sprint checklist with spec references and verification steps.
disable-model-invocation: true
---

# /sdd:sprint

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

Read `skills/sdd-guide/references/deepening-rounds.md` for the interview pattern used in this command.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/references/deepening-rounds.md`
3. `docs/project-state.json` — read `currentSprint`, `commandExplanationsShown`, and `buildMode`
4. `docs/prd.md` — scan for incomplete and unvetted items only. Do not load completed stories.
5. `docs/spec.md`
6. `docs/open-concerns.md`

## Prerequisites

- `docs/spec.md` must exist. If missing: "Run `/sdd:spec` first."
- `docs/prd.md` must exist. If missing: "Run `/sdd:prd` first."
- `docs/project-state.json` must exist. If missing: "Run `/sdd:scope` first."

If any prerequisite fails, stop immediately. Do not attempt partial execution.

## State Updates (immediate)

Before doing any other work:

1. Set `lastCommand` to `"/sdd:sprint"` in `docs/project-state.json`.
2. Read and process `docs/open-concerns.md` per sdd-guide open concerns protocol.

## First-Run Explanation

Check `docs/project-state.json` for `commandExplanationsShown.sprint`.

**If `sprint` has NOT been shown (or `commandExplanationsShown` does not exist):**

Output the following as plain text. This is not a question — do not wait for acknowledgment. Output it and continue.

---

**What /sdd:sprint does**

Sprint planning pulls a logical batch of work from your PRD into a focused, buildable checklist. Each item in the sprint references both the PRD story it comes from and the spec section that defines how to build it. This is how `/sdd:build` knows exactly what to implement without loading your entire project context.

Sprints are grouped by coherence — related items, dependency chains, and tangible milestones — not by time estimates. Each sprint should end with something you can see, use, or test.

You make the final call on what goes in. I propose; you decide.

---

After outputting, set `commandExplanationsShown.sprint` to `true` in `docs/project-state.json`.

**If `sprint` HAS been shown:** Skip the explanation entirely. Do not mention it.

## Behavior

### Step 1: Scan PRD for Incomplete and Unvetted Items

Read `docs/prd.md` and identify:

- **Incomplete items:** Stories or tasks that have not been marked as complete across all previous sprints.
- **Unvetted items:** Stories or tasks in the unvetted/backlog section that have not been through `/sdd:refine`.

Report a summary to the user: how many incomplete items and how many unvetted items exist.

### Step 2: Handle Unvetted Items

**If unvetted items exist:**

Recommend running `/sdd:refine` before sprint planning. Frame this as a recommendation, not a requirement. The user can proceed without refining.

Example: "You have N unvetted items in the PRD. Running `/sdd:refine` first would vet them through a compressed planning interview so they're sprint-ready. You can also proceed now and work with the vetted items only."

**If 5+ unvetted items exist:**

In addition to the recommendation above, flag scope creep per the living-documents reference. The rate of new ideas is outpacing deliberate planning. Surface this explicitly but let the user decide how to handle it.

### Step 3: Propose Sprint Grouping

Analyze the incomplete, vetted PRD items and propose a grouping for the next sprint. Base the grouping on:

- **Dependencies and relatedness:** Items that depend on each other or share infrastructure belong together.
- **Logical sequence:** Items that must be built in a specific order should be grouped to respect that order.
- **Tangible milestones:** Each sprint should end with something the user can see, use, or test. Prioritize groupings that produce a visible, demonstrable outcome.

**Do NOT time-box.** Group by coherence, not by time estimates. A sprint can be 2 items or 12 items — what matters is that the group forms a logical, completable unit with a tangible result.

Present the proposed grouping to the user with a brief rationale for why these items belong together and what the user will have at the end of the sprint.

### Step 4: User Decides

The user makes the final call on what goes in the sprint. They can:

- Accept the proposed grouping as-is.
- Add items to the sprint.
- Remove items from the sprint.
- Request a completely different grouping.

Ask the user one question: whether the proposed grouping works or what they want to change. Do not present multiple-choice options.

Iterate until the user confirms the sprint contents.

### Step 5: Build Mode Choice

Once the sprint contents are confirmed, ask the user to choose a build mode:

"How do you want `/sdd:build` to work through this sprint?

**Step-by-step:** `/build` stops between every checklist item and runs `/clear` to start fresh context. You review each item's output before moving to the next. Best for complex items or when you want tight control.

**Autonomous:** `/build` works through multiple items continuously, pausing at checkpoints every 3-4 items for you to review progress. Best for straightforward items where you trust the spec to guide implementation."

**Important:** Build mode is locked for the entire sprint. The user cannot switch modes mid-build. Make this clear when presenting the choice.

### Step 6: Deepening Rounds (Optional)

After the sprint contents and build mode are confirmed, offer deepening rounds per `references/deepening-rounds.md`.

The deepening target here is the sprint plan itself — not the PRD or spec. Deepening questions should probe:

- Whether the grouping truly forms a coherent unit.
- Whether any items have hidden dependencies not captured in the proposal.
- Whether the acceptance criteria are specific enough for `/sdd:build` to work from.
- Whether the verification steps are realistic and sufficient.
- Edge cases in the sprint items that could cause build problems.

Transition prompt: "I've got enough to generate the sprint checklist. Want another round to sharpen the plan, or ready to proceed?"

### Step 7: Generate Sprint Checklist

Determine the next sprint number from `docs/project-state.json` (`currentSprint + 1`, or `1` if `currentSprint` does not exist).

Read `skills/sdd-guide/templates/sprint-template.md` for the output format.

Generate `docs/sprint-N.md` where N is the sprint number.

For each checklist item:

- **PRD ref:** Must point to the exact epic and story in `docs/prd.md` (e.g., `prd.md > User Authentication > Login Flow`). This is how `/sdd:build` loads only the relevant PRD context.
- **Spec ref:** Must point to the exact section and subsection in `docs/spec.md` (e.g., `spec.md > API Design > Authentication Endpoints`). This is how `/sdd:build` loads only the relevant spec context.
- **What to build:** A brief, concrete description of the implementation work.
- **Acceptance criteria:** Copied or adapted from the PRD story. These are the checkboxes `/sdd:build` will verify against.
- **Verification:** How to verify the item works — automated tests, manual checks, or both, consistent with the testing strategy defined in the spec.
- **Status:** `[ ] Not started`

PRD ref and spec ref are critical. They enable surgical context loading during build — without them, `/sdd:build` would need to load the entire PRD and spec for every item, wasting context window.

### Step 8: Update Project State

Update `docs/project-state.json`:

- Set `currentSprint` to the new sprint number.
- Set `buildMode` to the user's chosen mode (`"step-by-step"` or `"autonomous"`).
- Ensure `lastCommand` is `"/sdd:sprint"`.

### Step 9: Update Open Concerns

Update `docs/open-concerns.md` per sdd-guide protocol:

- Resolve any concerns addressed by sprint planning decisions.
- Defer concerns that belong to future sprints or commands.
- Append new concerns that arose during sprint planning.

### Step 10: Process Notes

Write sprint planning process notes to `process-notes-sprint-N.md` where N is the current sprint number.

Capture:
- Which items were proposed and why.
- What the user changed in the proposal and their reasoning.
- Build mode choice and rationale.
- Number of deepening rounds and what they surfaced.
- Any concerns raised or deferred.

### Step 11: Wrap Up

Confirm to the user:
- Sprint N has been generated at `docs/sprint-N.md`.
- Build mode is locked as [their choice].
- Tell them to run `/sdd:build` to start executing the sprint.

## Important Reminders

- One question at a time. Steps 4, 5, and 6 each involve user input — never combine them into a single message.
- Free-form questions only. No multiple-choice for planning decisions. The build mode choice in Step 5 is an exception — it is a binary operational choice, not a planning question.
- Sprint grouping is by coherence and tangible milestones, never by time estimates.
- PRD ref and spec ref in each checklist item are non-negotiable. Every item must have both. If a PRD story has no corresponding spec section, flag this as a gap before generating the sprint.
- Build mode is locked for the sprint. State this clearly when presenting the choice.
- First-run explanation only on the very first `/sdd:sprint` in the project. Check `commandExplanationsShown.sprint` — do not show it on subsequent sprints.
- Only load incomplete and unvetted PRD items. Do not load completed stories — they waste context.
