---
name: plan
description: Pull a logical batch of work from the PRD into a buildable sprint. Produces a sprint checklist with spec references and verification steps.
disable-model-invocation: true
---

# /sdd:plan

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

Read `skills/sdd-guide/references/deepening-rounds.md` for the interview pattern used in this command.

Read `skills/sdd-guide/references/context-management.md` for the three-tier between-rounds context recommendation (continue / `/compact` / `/clear`) emitted alongside the end-of-round content recommendation.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/references/deepening-rounds.md`
3. `skills/sdd-guide/references/context-management.md`
4. `skills/sdd-guide/references/context-loading.md`
5. `docs/project-state.json` — read `currentSprint`, `commandExplanationsShown`, and `buildMode`
6. Most recent `process-notes-sprint-N.md` in project root, if any — see `## Prior-Sprint Process Notes` below for selection rule and extraction.
7. `docs/prd.md` — scan for incomplete and unvetted items only. Do not load completed stories.
8. `docs/spec.md`
9. `docs/open-concerns.md`

## Prerequisites

- `docs/spec.md` must exist. If missing: "Run `/sdd:spec` first."
- `docs/prd.md` must exist. If missing: "Run `/sdd:prd` first."
- `docs/project-state.json` must exist. If missing: "Run `/sdd:scope` first."

If any prerequisite fails, stop immediately. Do not attempt partial execution.

## State Updates (immediate)

Before doing any other work:

1. Set `lastCommand` to `"/sdd:plan"` in `docs/project-state.json`.
2. Read and process `docs/open-concerns.md` per sdd-guide open concerns protocol.

## First-Run Explanation

Check `docs/project-state.json` for `commandExplanationsShown.plan`.

**If `plan` has NOT been shown (or `commandExplanationsShown` does not exist):**

Output the following as plain text. This is not a question — do not wait for acknowledgment. Output it and continue.

---

**What /sdd:plan does**

Sprint planning pulls a logical batch of work from your PRD into a focused, buildable checklist. Each item in the sprint references both the PRD story it comes from and the spec section that defines how to build it. This is how `/sdd:build` knows exactly what to implement without loading your entire project context.

Sprints are grouped by coherence — related items, dependency chains, and tangible milestones — not by time estimates. Each sprint should end with something you can see, use, or test.

You make the final call on what goes in. I propose; you decide.

---

After outputting, set `commandExplanationsShown.plan` to `true` in `docs/project-state.json`.

**If `plan` HAS been shown:** Skip the explanation entirely. Do not mention it.

## Prior-Sprint Process Notes

Runs after state updates and the first-run explanation, before the PRD state check. See `references/context-loading.md` for the per-command loading rule.

Glob `process-notes-sprint-*.md` in the project root. Select the file with the highest `N`. If no such file exists, proceed silently to the PRD state check — no warning, no error.

If a file is found, read it and extract two lists:

- **Carry-forward items:** anything the previous `/sdd:build` wrap-up noted as not closed.
- **Quality concerns and tech-debt:** entries in the Wrap-Up Notes section, including `[tech-debt]` markers or `### Tech-debt` sub-sections — accept whichever format the prior wrap-up author used.

Surface both lists to the user before proposing the new sprint grouping. Format as a brief bullet list — one line per item, with the source sprint-item reference where available. If either list is empty, omit it; do not emit empty headings.

This read is informational. It does not modify the prior process-notes file.

## PRD State Check

Runs after Prior-Sprint Process Notes, before `## Behavior`. Single read-and-evaluate of `docs/prd.md`. This check does not modify the PRD.

Determine three signals:

- Is every `- [ ] \`abcd\`` acceptance criterion line now `- [x]`?
- Does the Unvetted section contain any items?
- Do any unchecked acceptance criteria remain?

Apply routing rules in priority order — first matching rule wins:

1. **Unvetted contains items:** Recommend running `/sdd:refine` first. Pause sprint planning and ask whether to proceed with sprint planning anyway or exit to run `/refine`. On user election to proceed, continue to `## Behavior`. On user election to run `/refine`, exit gracefully using the standard end-of-command handoff naming `/sdd:refine`.
2. **All ACs checked AND Unvetted empty:** Recommend running `/sdd:retro` instead. Exit gracefully using the standard end-of-command handoff naming `/sdd:retro`. Do not proceed to sprint planning.
3. **Neither (unchecked ACs remain, Unvetted empty):** Proceed to `## Behavior`.

## Sprint-Mode Resolution

Runs after the PRD state check decides "proceed with planning" and before sprint-grouping in `## Behavior`. Resolves which build mode (`"step-by-step"` or `"autonomous"`) the resulting sprint will lock in.

### Resolution order

Apply in priority order — first matching rule wins:

1. **User profile default:** Read `~/.claude/sdd-user-profile.json`. If a non-empty `defaultSprintMode` field is present, use that value directly. Do not ask the user.
2. **Prior-sprint mode:** Else, read `docs/project-state.json`. If a `buildMode` value is recorded from the previous sprint, ask the user one question: "Last sprint used [mode]. Reuse that mode?" — affirmative uses that mode, negative falls through to rule 3.
3. **Ask fresh:** Else, ask the user to select a mode in a free-form open-ended question. Per `skills/sdd-guide/SKILL.md` (one-question-at-a-time), do not present multiple-choice options — describe the two modes and let the user answer in their own words.

Valid mode values: `"step-by-step"` and `"autonomous"`.

### "Always use this mode" toggle

After the mode is resolved by any path above (including the profile-default path), offer the user a single toggle as a separate question:

- **When `defaultSprintMode` was unset:** "Always use [mode]? (Sets `defaultSprintMode` in your profile.)" — affirmative writes the selected mode to `defaultSprintMode` in `~/.claude/sdd-user-profile.json` and updates `updatedAt` to the current ISO-8601 timestamp. Negative or skip writes nothing.
- **When `defaultSprintMode` was already set (resolution rule 1 hit):** Frame as confirmation: "Keep `defaultSprintMode` set to [mode]?" — affirmative is a no-op write. Negative offers to clear the field, which writes `null` (or omits the field — semantically equivalent) and updates `updatedAt`.

### Per-project memory write

Regardless of the toggle answer, write the selected mode to `docs/project-state.json` under `buildMode`. This write happens unconditionally so the next `/sdd:plan` run has a recorded prior mode to offer in rule 2 above.

### Single-message discipline

The mode-confirm question (or fresh-pick question) and the toggle are two separate questions. Per `skills/sdd-guide/SKILL.md` (one-question-at-a-time), never combine them into a single message.

## Sprint-Item Tag Validation

Runs after `## Behavior` Step 6 (deepening rounds) and immediately before Step 7 (generate sprint checklist) — the validator is the precondition for the file write. Although declared as a top-level section here, execution-wise it sits at the tail of the planning flow, gating the write of `docs/sprint-N.md`.

### Rule

Every checkbox bullet in the proposed sprint contents must carry either a `[PRD: ...]` tag or an explicit `[unmapped]` tag. `[FB: ...]` is supplementary and never satisfies the validation requirement on its own.

### Parser

Parse every checkbox bullet line in the proposed sprint contents. See `references/sprint-tags.md > Validation summary` for parser regex and matching rules. Do not re-implement the patterns here.

### Failure handling

When one or more lines fail validation, refuse to write `docs/sprint-N.md`. Surface every offending line back to the user with its line number and tag analysis (e.g., "Line 14 carries `[FB: FB-007]` but no `[PRD: ...]` or `[unmapped]` tag"). Ask the user to address each one — add a `[PRD: ...]` tag pointing at the relevant AC, or add an explicit `[unmapped]` if the item genuinely doesn't map. Re-run validation after the user updates. Loop until validation passes.

### `[FB: ...]` prompting (sdd-plugin-specific)

Gated entirely on the presence of `docs/v2-verification.md` in the project. The file's presence is the sole activation signal — skip this step silently when the file does not exist.

When `docs/v2-verification.md` exists, after PRD-tag validation passes, scan each item for whether it implements an in-scope feedback item. For each item without a `[FB: ...]` tag, ask the user whether to add one, cross-referencing `docs/v2-verification.md` rows.

### Precondition

The validator is a precondition, not a post-hoc check. `/sdd:plan` does not write `docs/sprint-N.md` to disk until validation passes for every sprint-item line and any `[FB: ...]` prompting completes.

## End-of-Command Handoff

Runs as the final step after `## Behavior` Step 11 (Wrap Up), after the sprint file write and the project-state.json update.

Emit the handoff per the canonical template in `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff`. That template defines the two-line form, the first-handoff explanation paragraph (prepended exactly once per user), and the `handoffWarningShown` tracking convention in `~/.claude/sdd-user-profile.json`. Do not restate that mechanism here.

### Next-command target

The `[next-command]` slot in the template is always `/sdd:build` for `/sdd:plan`. Routing exits to `/sdd:refine` or `/sdd:retro` were already handled upstream by `## PRD State Check`; by the time execution reaches this section, the project state is "proceed with sprint loop," so the next command is unambiguously `/sdd:build`.

### Outcome-summary line

Use a one-line outcome summary in the form `Sprint N planned. N items, [mode] mode.` (e.g., `Sprint 3 planned. 7 items, autonomous mode.`). This format is guidance, not a rigid template — substitute the actual sprint number, item count, and resolved build mode.

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

Build mode was already resolved upstream in `## Sprint-Mode Resolution`. Confirm it to the user in one line: "Build mode for this sprint: [mode]." Then proceed.

**Important:** Build mode is locked for the entire sprint. The user cannot switch modes mid-build. State this clearly in the confirmation line.

### Step 6: Deepening Rounds (Optional)

After the sprint contents and build mode are confirmed, follow the deepening rounds protocol defined in `skills/sdd-guide/references/deepening-rounds.md` — including the Phase 1 → Phase 2 transition, the per-round question count (5 default, up to 10 with explicit reason+permission past the cap), and the structured end-of-round content recommendation (continue-with-topic-preview or close-with-reasoning). Do not emit a bare transition prompt; the reference defines the recommendation wording.

After the end-of-round content recommendation fires, emit the three-tier between-rounds context recommendation per `skills/sdd-guide/references/context-management.md`. Order is fixed: content recommendation first, then context recommendation, in the same end-of-round message group. The two are separate and independent.

The deepening target here is the sprint plan itself — not the PRD or spec. Deepening questions should probe:

- Whether the grouping truly forms a coherent unit.
- Whether any items have hidden dependencies not captured in the proposal.
- Whether the acceptance criteria are specific enough for `/sdd:build` to work from.
- Whether the verification steps are realistic and sufficient.
- Edge cases in the sprint items that could cause build problems.

### Step 7: Generate Sprint Checklist

Run `## Sprint-Item Tag Validation` first; do not proceed with the file write until validation passes (and any `[FB: ...]` prompting completes).

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
- Ensure `lastCommand` is `"/sdd:plan"`.

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

Emit the handoff per `## End-of-Command Handoff`.

## Important Reminders

- One question at a time. `## Sprint-Mode Resolution` (mode-confirm + toggle), Step 4 (sprint grouping), and Step 6 (deepening) each involve user input — never combine them into a single message.
- Free-form questions only. No multiple-choice for planning decisions.
- Sprint grouping is by coherence and tangible milestones, never by time estimates.
- PRD ref and spec ref in each checklist item are non-negotiable. Every item must have both. If a PRD story has no corresponding spec section, flag this as a gap before generating the sprint.
- Build mode is locked for the sprint. State this clearly when presenting the choice.
- First-run explanation only on the very first `/sdd:plan` in the project. Check `commandExplanationsShown.plan` — do not show it on subsequent sprints.
- Only load incomplete and unvetted PRD items. Do not load completed stories — they waste context.
