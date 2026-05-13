---
name: prd
description: Produces a product requirements document from the scope. Covers users, features, requirements, success criteria with testable acceptance criteria.
disable-model-invocation: true
---

# /sdd:prd

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

Read `skills/sdd-guide/references/deepening-rounds.md` for the two-phase interview pattern used by this command.

## Loading

- Load: `skills/sdd-guide/SKILL.md` (core behavior)
- Load: `skills/sdd-guide/references/deepening-rounds.md` (interview pattern)
- Load: `docs/scope.md` in full

## Prerequisites

- `docs/scope.md` must exist. If missing, tell the user: "Run `/sdd:scope` first."

## Startup Sequence

### 1. Update State

Set `lastCommand` to `/sdd:prd` in `docs/project-state.json`. If the file does not exist, create it.

### 2. First-Run Explanation

Check `commandExplanationsShown.prd` in `docs/project-state.json`. If it has not been shown:

> **/sdd:prd** converts your scope document into a structured product requirements document. It walks through your scope section by section, translating brainstorm language into precise behavior descriptions organized as user stories with testable acceptance criteria. The PRD serves a dual purpose: it's both your requirements document and your progress tracker — acceptance criteria checkboxes get checked off during sprints as work is completed.

Output this as plain text. Not gated — do not wait for acknowledgment. Set `commandExplanationsShown.prd` to `true` in `docs/project-state.json`.

### 3. Open Concerns

Read `docs/open-concerns.md`. Follow the open concerns protocol from sdd-guide:
- Resolve any concerns that can be addressed now, including concerns targeted at `/sdd:prd`.
- Defer what cannot be resolved with rationale.
- New concerns from this command's work get appended throughout the session.

## Primary Work

### Phase 1 — Section-by-Section Scope Conversion

Walk through `docs/scope.md` section by section. For each section:

1. Read the brainstorm language the user wrote during scoping.
2. Convert it into precise behavior descriptions — what the system does, not what the user hopes it does.
3. Ask clarifying questions to resolve ambiguity. One question at a time, free-form only.
4. Surface edge cases and "what if" questions as they arise. Do not save them for later — resolve them during the conversation while context is fresh.

The goal is to transform every meaningful piece of the scope into a requirement that can be verified. Brainstorm language like "it should be fast" becomes a specific, testable statement like "page load completes in under 2 seconds on a 3G connection."

### Phase 2 — Requirements Organization

Organize requirements as user stories grouped into epics:

- **Epics** are logical groupings of related functionality (e.g., "User Authentication", "Dashboard", "Data Export").
- **User stories** are list items under each epic, written in the standard format: "As a [user], I want [capability] so that [benefit]."
- **Acceptance criteria** are checkboxes under each story. Every criterion must be testable — specific, observable, and verifiable. No vague criteria like "works well" or "is intuitive."

**Heading stability matters.** Epic names and story descriptions become addresses that `/sdd:plan` references when pulling work into sprints. Once a heading is written, it should not change unless the requirement itself changes. Choose clear, stable names.

### Epic Count Monitoring

If 10 or more epics emerge, recommend phase-splitting to the user. Phase-splitting means breaking the project into smaller pieces, each going through the full SDD workflow independently (`/sdd:scope` through `/sdd:retro`). This keeps individual PRDs manageable and sprints focused.

Present this as a recommendation, not a gate. The user decides whether to split.

### Deepening Rounds

After the section-by-section walkthrough is complete, follow the deepening rounds pattern from `skills/sdd-guide/references/deepening-rounds.md`:

1. Offer the user a choice: "I've got enough to generate your PRD. Want another round to sharpen things, or ready to proceed?"
2. If they want another round, generate 4-5 targeted questions covering edge cases, ambiguities, thin areas, hidden assumptions, and unexplored angles.
3. Ask questions one at a time. After the round, offer the choice again.
4. Repeat as many times as the user wants.

## Document Generation

When the user is ready to proceed, generate `docs/prd.md` using the template at `skills/sdd-guide/templates/prd-template.md`.

**Template usage:**
- Replace `[Project Name]` with the project name from `docs/scope.md`.
- `## Problem Statement` — distilled from the scope's problem/motivation section.
- `## User Stories` — all epics and stories from the conversation. Epics are `###` headings inside this section. Stories are list items. Acceptance criteria are checkboxes under each story.
- `## Unvetted` — leave empty. This section is critical: during sprints, new items that surface but haven't been refined land here. `/sdd:refine` processes them before future sprints.
- `## What We're Building` — concise summary of the product being built, synthesized from the full conversation.
- `## What We'd Add With More Time` — nice-to-have features that were discussed but deliberately excluded from the current scope.
- `## Non-Goals` — things this project explicitly does not do. Pulled from scope if present, supplemented by anything surfaced during the PRD conversation.

**Acceptance criteria quality check:** Before writing the file, review every acceptance criterion. Each one must be:
- **Specific** — describes a concrete behavior, not a category.
- **Observable** — someone can look at the system and determine pass/fail.
- **Verifiable** — can be tested without subjective judgment.

Rewrite any criterion that fails this check.

## Wrap-Up

### Append to Process Notes

Append to `process-notes.md` in the project root. Capture:
- Decisions made and rationale.
- Pushback from either side and resolution.
- Questions that were difficult and why.
- Number of deepening rounds and what they surfaced.
- Any phase-splitting discussion and outcome.

### Update Open Concerns

Append any new concerns that arose during the PRD conversation to `docs/open-concerns.md`. Follow the entry format from `skills/sdd-guide/references/open-concerns.md`.

### Update State

Confirm `lastCommand` is set to `/sdd:prd` in `docs/project-state.json`.

Tell the user the PRD has been written and to run `/sdd:spec` when ready.

## Important Reminders

- One question at a time. Never ask multiple questions in a single message.
- Free-form questions only. No multiple choice for interview or planning questions.
- Acceptance criteria must be testable. Specific, observable, verifiable. This is non-negotiable.
- Heading names are stable addresses. `/sdd:plan` references them. Do not use generic names like "Miscellaneous" or "Other."
- The Unvetted section must exist and be empty in the initial PRD. It is where new items land during sprints.
- The PRD is both a requirements document and a progress tracker. Checkboxes get checked during sprints as work is completed.
- Edge cases and "what if" questions should be resolved during the conversation, not deferred to a later phase unless they genuinely belong elsewhere.
