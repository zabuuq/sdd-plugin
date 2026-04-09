---
name: refine
description: Run unvetted PRD items through a compressed planning interview. Produces proper user stories with acceptance criteria and updates the spec if needed.
disable-model-invocation: true
---

# /sdd:refine — Refine Command

## Startup Sequence

Execute these steps in order before beginning the refinement interview.

### 1. Load shared behavior

Read `skills/sdd-guide/SKILL.md` and follow all rules defined there (tone, interaction rules, guard rails, process notes, open concerns, state tracking). Everything in sdd-guide applies to this command.

### 2. Load living-documents reference

Read `skills/sdd-guide/references/living-documents.md`. This defines the protocol for updating living documents during refinement, including the update ordering that this command must follow. The `/sdd:refine` command is the primary mechanism for making deliberate changes to project documents — unlike other commands, it does not default to resisting changes. It is the gatekeeper that makes changes legitimate.

### 3. Read user profile

Read `~/.claude/sdd-user-profile.json`.

**If the file does not exist**, stop immediately and output:

> Run `/sdd:onboard` first.

Do not proceed. Do not attempt to create the profile or work around its absence.

If the file exists, parse it and use the `communicationStyle` field to set tone for this session (per sdd-guide rules).

### 4. Read project state

Read `docs/project-state.json`.

**If the file does not exist**, stop immediately and output:

> Run `/sdd:scope` first.

Do not proceed.

If the file exists, read `currentSprint` to determine the current sprint number.

### 5. Load PRD (unvetted section)

Read `docs/prd.md`. Focus on the Unvetted/Proposed section — these are the items that need refinement. Scan the rest of the document for context (existing epics, approved stories, overall structure) but do not load it deeply.

**If `docs/prd.md` does not exist**, stop immediately and output:

> Run `/sdd:prd` first.

Do not proceed.

### 6. Read open concerns

Read `docs/open-concerns.md` (if it exists). Note any concerns relevant to the unvetted items being refined. These will be processed per sdd-guide protocol.

## Prerequisites

The PRD must have items in the Unvetted/Proposed section. Scan `docs/prd.md` for unvetted items — items that have not been through a refinement interview.

**If no unvetted items exist**, stop immediately and output:

> No unvetted items to refine.

Do not proceed. Do not offer to create new items or work around the absence of unvetted content.

## State Updates (immediate)

Before doing any other work:

1. Set `lastCommand` to `"/sdd:refine"` in `docs/project-state.json`.
2. Read and process `docs/open-concerns.md` per sdd-guide open concerns protocol.

## First-Run Explanation

Check `docs/project-state.json` for `commandExplanationsShown.refine`.

**If `refine` has NOT been shown (or `commandExplanationsShown` does not exist):**

Output the following as plain text. This is not a question — do not wait for acknowledgment. Output it and continue.

---

**What /sdd:refine does**

Refine takes unvetted items from the PRD and runs them through a compressed planning interview — enough to produce proper user stories with acceptance criteria. If those stories have architectural implications, the spec and other project documents get updated too, one at a time, with your approval at each step.

---

After outputting, set `commandExplanationsShown.refine` to `true` in `docs/project-state.json`.

**If `refine` HAS been shown:** Skip the explanation entirely. Do not mention it.

## Behavior

### Step 1: Read Open Concerns

Read `docs/open-concerns.md` (if it exists). Resolve what you can based on the unvetted items being refined. Defer what you cannot. Note any new concerns that arise during refinement.

This was initiated during the state updates phase. Continue applying the protocol throughout this command.

### Step 2: Survey Unvetted Items

List all unvetted items from `docs/prd.md`. Present them to the user with a brief summary of each. If related items exist, group them — they may be refined together in a single interview round.

Ask the user which item (or group of related items) to refine first. One question. Wait for the response.

### Step 3: Compressed Interview

For the selected item (or group), conduct a compressed planning interview. The goal is to produce proper user stories with concrete acceptance criteria.

- Ask clarifying questions one at a time.
- Push back on vague descriptions. "It should work well" is not an acceptance criterion. What specifically must be true?
- Understand the user's intent, the expected behavior, edge cases, and how this fits with the existing system.
- If the item is large, help the user break it into multiple stories. Each story should be independently deliverable.

Keep the interview focused. You need enough detail for proper stories and to assess spec impact — not a full design session.

### Step 4: Assess Spec Impact

After the interview, assess whether the refined items have architectural implications:

- **New data models** — Does this require new schemas, tables, or data structures not currently in the spec?
- **Changed components** — Does this modify the behavior or interface of existing components defined in the spec?
- **New service boundaries** — Does this introduce new services, APIs, or integration points?

If there is spec impact, discuss the implications with the user during the conversation. Explain what would need to change in the spec and why. Get alignment before proceeding to document updates.

If there is no spec impact, note that explicitly and move to document updates.

### Step 5: Selectively Load Spec Sections

If spec impact was identified in Step 4, load only the affected sections of `docs/spec.md`. Do not load the full spec. Read the specific sections that will need updates based on the architectural implications discussed with the user.

If no spec impact was identified, skip this step.

### Step 6: Incremental Document Updates

Update documents one at a time, in strict dependency order. Each update is shown to the user in full and confirmed before proceeding to the next document. Do not batch updates. Do not apply changes silently. Do not skip ahead to the next document until the current one is confirmed.

**The order is fixed and must not be changed:**

#### 6a. Update `docs/prd.md`

Move the refined items from the Unvetted/Proposed section to the appropriate vetted location (existing epic or new epic). Write proper user stories with:

- Clear title
- Description with enough context to be self-contained
- Acceptance criteria (concrete, verifiable, no vague language)

Show the user exactly what will change in the PRD — what is being moved, what the new stories look like, and where they will be placed. Wait for explicit confirmation before writing the changes.

**Do not proceed to the next document until the user confirms the PRD update.**

#### 6b. Update `docs/spec.md` (if needed)

If spec impact was identified in Step 4, update the affected sections of the spec to reflect the new or changed architectural components. Show the user exactly what will change — new sections, modified sections, or updated diagrams/models.

Wait for explicit confirmation before writing the changes.

If no spec impact was identified, skip this step and tell the user: "No spec changes needed for this item."

**Do not proceed to the next document until the user confirms the spec update (or it is skipped).**

#### 6c. Update `AGENTS.md` (if needed)

If the refined items change how work is structured, delegated, or organized — update `AGENTS.md` with new or modified conventions. This is uncommon but necessary when refinement introduces new agent roles, changes task boundaries, or modifies delegation patterns.

Show the user exactly what will change. Wait for explicit confirmation before writing.

If no changes are warranted, skip this step and tell the user: "No AGENTS.md changes needed."

**Do not proceed to the next document until the user confirms the AGENTS.md update (or it is skipped).**

#### 6d. Update `CLAUDE.md` (if needed)

If the refined items affect coding standards, project conventions, or Claude-specific instructions — update `CLAUDE.md`. This is uncommon but necessary when refinement introduces new patterns, changes existing conventions, or adds project-wide rules.

Show the user exactly what will change. Wait for explicit confirmation before writing.

If no changes are warranted, skip this step and tell the user: "No CLAUDE.md changes needed."

### Step 7: Repeat for Remaining Items

If there are more unvetted items to refine, return to Step 2 and ask the user which item to refine next.

The user can stop at any time. If they say they are done, proceed to Step 8 even if unvetted items remain.

### Step 8: PRD Health Check

After all refinement rounds are complete (or the user stops early), scan `docs/prd.md` for health indicators:

- **10+ epics:** Recommend phase-splitting. The project is likely too large for a single development cycle. Suggest breaking it into distinct phases with clear boundaries. Example: "The PRD now has N epics. Consider phase-splitting — breaking the project into distinct phases, each with its own sprint cycle."
- **5+ unvetted items remaining:** Flag scope creep. The rate of new ideas is outpacing deliberate planning. Example: "There are still N unvetted items in the PRD. The backlog is growing faster than it is being refined — worth keeping an eye on."

If neither threshold is triggered, skip this step without comment.

### Step 9: Update Open Concerns

Update `docs/open-concerns.md` per sdd-guide protocol:

- Resolve concerns that were addressed by refinement decisions.
- Defer concerns that belong to future commands or sprints.
- Append new concerns that arose during the refinement interview.

### Step 10: Process Notes

Append refinement process notes to `process-notes-sprint-N.md` where N is the current sprint number from `docs/project-state.json`.

Capture:
- Which unvetted items were refined and the key decisions from each interview.
- Spec impact assessment results — what changed or why nothing changed.
- Document updates applied (PRD, spec, AGENTS.md, CLAUDE.md) and what was modified in each.
- PRD health check results if any thresholds were triggered.
- Concerns raised, resolved, or deferred.

### Step 11: Wrap Up

Tell the user:

1. Which items were refined and moved from unvetted to vetted.
2. Which documents were updated (and which were not).
3. How many unvetted items remain (if any).
4. The recommended next command — typically `/sdd:sprint` if vetted items are ready, or `/sdd:refine` again if unvetted items remain.

## Important Reminders

- **One question at a time.** Steps 2 and 3 involve user input — never combine them into a single message or ask multiple questions at once.
- **Free-form questions only.** No multiple-choice for scoping or refinement decisions.
- **Incremental document updates in strict order.** PRD first, then spec, then AGENTS.md, then CLAUDE.md. Each update is shown and confirmed individually. Never batch or skip the confirmation step.
- **Do not proceed to the next document without confirmation.** This is the most important rule of the update sequence. If the user rejects a change, revise it and re-present. Do not move on until they approve.
- **Selectively load spec sections.** Only load the sections of `docs/spec.md` that are affected by the refined items. Do not load the full spec, full PRD (beyond the initial scan), or other sprint files.
- **PRD health thresholds are fixed.** 10+ epics triggers a phase-splitting recommendation. 5+ unvetted items triggers a scope creep flag. Do not adjust these thresholds.
- **First-run explanation only on the very first `/sdd:refine` in the project.** Check `commandExplanationsShown.refine` — do not show it on subsequent runs.
- **The user can stop at any time.** Refinement is per-item. If the user wants to stop after refining one item, let them. Do not pressure them to refine all unvetted items in a single session.
- **Refine is the gatekeeper for document changes.** Unlike `/sdd:build` or `/sdd:iterate`, this command is authorized to make deliberate changes to the PRD, spec, AGENTS.md, and CLAUDE.md. But every change still requires user confirmation.
