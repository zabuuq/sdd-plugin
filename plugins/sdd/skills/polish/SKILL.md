---
name: polish
description: Polish and improve what was built in the current sprint. Bugs, new features, UX improvements — scoped through a brief interview and added to the sprint file.
disable-model-invocation: true
---

# /sdd:polish — Iterate Command

## Startup Sequence

Execute these steps in order before beginning the iteration interview.

### 1. Load shared behavior

Read `skills/sdd-guide/SKILL.md` and follow all rules defined there (tone, interaction rules, guard rails, process notes, open concerns, state tracking). Everything in sdd-guide applies to this command.

### 2. Load living-documents reference

Read `skills/sdd-guide/references/living-documents.md`. This defines the protocol for updating living documents during iteration. Follow its default stance: resist changes to scope, PRD, and spec. New ideas surface through the backlog or unvetted section, not through direct edits.

Read `skills/sdd-guide/references/context-management.md`. Polish does not run formal deepening rounds, but the iteration interview (Step 5) and the end-of-iteration wrap (Step 10) are natural transition points. At those points, the agent may emit the three-tier between-rounds context recommendation (continue / `/compact` / `/clear`) when warranted — this is discretionary for polish, not round-by-round mandatory. Use the reference's triggers and signals to judge whether a recommendation is appropriate at a given transition.

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

### 5. Load current sprint file

Read `docs/sprint-N.md` where N is `currentSprint` from `docs/project-state.json`.

**If the file does not exist**, stop immediately and output:

> Run `/sdd:plan` first.

Do not proceed.

### 6. Load CLAUDE.md

Read `CLAUDE.md` in the project root. This provides project conventions and coding standards relevant to iteration work.

If the file does not exist, skip this step without error.

## Prerequisites

All checklist items in the current sprint file must be complete (all status checkboxes checked). Scan every item under `## Checklist` and any existing `## Iteration N` sections.

**If any items are incomplete**, stop immediately and output:

> Not all sprint items are complete. Run `/sdd:build` first.

Do not proceed. Do not offer to skip incomplete items or work around them.

## State Updates (immediate)

Before doing any other work:

1. Set `lastCommand` to `"/sdd:polish"` in `docs/project-state.json`.
2. Read and process `docs/open-concerns.md` per sdd-guide open concerns protocol.

## First-Run Explanation

Check `docs/project-state.json` for `commandExplanationsShown.polish`.

**If `polish` has NOT been shown (or `commandExplanationsShown` does not exist):**

Output the following as plain text. This is not a question — do not wait for acknowledgment. Output it and continue.

---

**What /sdd:polish does**

Iterate is for polishing and improving what was built in the current sprint. Bugs, new features, UX improvements — anything that makes the sprint output better. I'll do a quick review of the current state, surface any backlog items worth pulling in, and then interview you about what you want to work on. That conversation produces a mini-checklist that gets appended to the sprint file. Then you run `/sdd:build` again to execute it. You can iterate as many times as you want within a single sprint.

---

After outputting, set `commandExplanationsShown.polish` to `true` in `docs/project-state.json`.

**If `polish` HAS been shown:** Skip the explanation entirely. Do not mention it.

## Behavior

### Step 1: Read Open Concerns

Read `docs/open-concerns.md` (if it exists). Resolve what you can based on the current sprint's completed work. Defer what you cannot. Note any new concerns that arise during iteration.

This was initiated during the state updates phase. Continue applying the protocol throughout this command.

### Step 2: Surface Backlog Items

Read `docs/backlog.md` (if it exists).

If the backlog contains items, surface 2-4 small items that could reasonably fit into an iteration cycle. Present them briefly — one line each with enough context for the user to decide.

Frame this as optional: "A few backlog items that might fit into an iteration round:" followed by the items. Do not push the user to accept any of them. If the user wants to pull one in, it gets added to the iteration checklist in Step 6.

If `docs/backlog.md` does not exist or is empty, skip this step without comment.

### Step 3: Quick Review of Current State

Review the completed sprint items and the current state of the project. Surface 2-3 observations about what was built. These should be concrete and specific — not generic praise. Focus on:

- Areas that might benefit from polish or hardening.
- Patterns or decisions that could cause problems later if not addressed now.
- Rough edges visible from the sprint work.
- Anything that feels incomplete even though it technically passes its acceptance criteria.

Present these observations to the user as a starting point, not as a mandate. The user decides what matters.

### Step 4: Ask What the User Wants to Work On

Ask the user a single, open-ended question about what they want to improve, fix, or add. Do not present categories or options. Let the user name what matters to them.

Example: "What do you want to work on in this iteration?"

One question. Wait for the response.

### Step 5: Scope Through Brief Interview

Based on the user's response, conduct a brief interview to scope the iteration work. This is not the full deepening rounds protocol — it is a focused conversation to clarify what needs to be done.

- Ask clarifying questions one at a time.
- Push back on vague descriptions. "Make it better" is not actionable. What specifically needs to change?
- If the user names multiple things, help them prioritize. All items can go into the iteration, but ordering matters for the checklist.
- If the work implies spec or PRD changes, follow the living-documents protocol: default to adding items to the backlog or unvetted section rather than modifying approved documents directly.

Keep this interview short and focused. The goal is enough clarity to write actionable checklist items, not a full planning session.

### Step 6: Generate Iteration Checklist

Determine the iteration number by scanning the current sprint file for existing `## Iteration N` headers. The new iteration is N+1 (or 1 if no iterations exist yet).

Selectively load relevant sections of `docs/spec.md` — only the sections that pertain to the iteration items being created. Do not load the full spec.

Generate a checklist and append it to the current sprint file under a new `## Iteration N` header. Each item follows this format:

```markdown
## Iteration N

### [Item Title]
- **What to do:** Brief, concrete description of the work
- **Spec ref:** `spec.md > [Section] > [Subsection]` (if applicable)
- **Acceptance criteria:**
  - [ ] Criterion 1
  - [ ] Criterion 2
- **Verification:** How to verify the item works
- **Status:** [ ] Not started
```

Rules for iteration items:
- **No artificial cap on items.** The checklist is sized by what the work requires. If the user wants 2 items, write 2. If they want 10, write 10.
- **Spec ref is included when applicable.** Bug fixes and UX tweaks may not have a spec section — that is fine. New features should reference the relevant spec section.
- **PRD ref is optional.** Iteration items are often polish work that does not map to a PRD story. Include it only when a clear mapping exists.
- **Acceptance criteria must be concrete and verifiable.** No vague criteria like "looks better." What specifically should be true when the work is done?

Present the iteration checklist to the user for review before appending it to the sprint file. Ask if anything needs to change. Iterate until they approve.

### Step 7: Append to Sprint File

Once the user approves, append the `## Iteration N` section to the end of `docs/sprint-N.md`.

Do not modify any existing content in the sprint file. Iteration sections are append-only.

### Step 8: Update Open Concerns

Update `docs/open-concerns.md` per sdd-guide protocol:

- Resolve any concerns addressed by iteration planning decisions.
- Defer concerns that belong to future iterations or commands.
- Append new concerns that arose during the iteration interview.

### Step 9: Process Notes

Append iteration process notes to `process-notes-sprint-N.md` where N is the current sprint number.

Capture:
- What the user wanted to work on and why.
- How the iteration scope was narrowed or clarified during the interview.
- Any backlog items pulled in and the rationale.
- Concerns raised or deferred.

### Step 10: Wrap Up

Tell the user:

1. Iteration N has been appended to `docs/sprint-N.md`.
2. How many items are in the iteration.

Then emit the handoff per `## End-of-Command Handoff` below.

If the user wants to add more iteration work after this, they can run `/sdd:polish` again. Each run produces a new `## Iteration N` section. There is no limit on iteration cycles within a sprint.

## End-of-Command Handoff

Runs as the final step after Step 10 (the iteration checklist has been appended to the sprint file, open concerns have been updated, and process notes have been written).

Emit the handoff per the canonical template in `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff`. That template defines the two-line standard form, the first-handoff explanation paragraph (prepended exactly once per user), and the `handoffWarningShown` tracking convention in `~/.claude/sdd-user-profile.json`. Do not restate that mechanism here.

### Next-command target

The `[next-command]` slot in the template is always `/sdd:build` for `/sdd:polish`. The iteration checklist needs to be executed before any further polish or planning.

### Outcome-summary line

Use a one-line outcome summary in the form `Polish iteration N appended.` Substitute the actual iteration number determined in Step 6 (e.g., `Polish iteration 2 appended. 4 items.`). Keep the shape declarative and single-line.

### Unconditional emission

The handoff fires unconditionally at completion. No context-weight heuristic, backlog-presence signal, or iteration-size threshold causes it to be skipped.

## Important Reminders

- One question at a time. Steps 4 and 5 involve user input — never combine them into a single message or ask multiple questions at once.
- Free-form questions only. No multiple-choice for scoping decisions.
- All sprint items must be complete before iterating. This includes items from previous iterations within the same sprint. No exceptions.
- Iteration items are appended — never modify existing sprint content.
- Mini-checklist size is driven by the work, not by an arbitrary cap. Do not limit iterations to a fixed number of items.
- Follow living-documents protocol for any changes that touch the spec or PRD. Default stance is to resist direct edits and route through the backlog.
- Selectively load spec sections. Only load what is relevant to the iteration items. Do not load the full spec, full PRD, or other sprint files.
- First-run explanation only on the very first `/sdd:polish` in the project. Check `commandExplanationsShown.polish` — do not show it on subsequent runs.
- Multiple iteration cycles are expected and normal. Each `/sdd:polish` run is a discrete cycle that appends a new section to the sprint file.
