---
name: prd
description: Produces a product requirements document from the scope. Covers users, features, requirements, success criteria with testable acceptance criteria.
disable-model-invocation: true
---

# /sdd:prd

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

Read `skills/sdd-guide/references/deepening-rounds.md` for the two-phase interview pattern used by this command.

Read `skills/sdd-guide/references/context-management.md` for the three-tier between-rounds context recommendation (continue / `/compact` / `/clear`) emitted alongside the end-of-round content recommendation.

## Loading

- Load: `skills/sdd-guide/SKILL.md` (core behavior)
- Load: `skills/sdd-guide/references/deepening-rounds.md` (interview pattern)
- Load: `skills/sdd-guide/references/context-management.md` (between-rounds context recommendation)
- Load: `skills/sdd-guide/references/backlog.md` (defer-to-backlog write trigger and entry format)
- Load: `docs/scope.md` in full

## Prerequisites

- `docs/scope.md` must exist. If missing, tell the user: "Run `/sdd:scope` first."

## Startup Sequence

### 1. Update State

Set `lastCommand` to `/sdd:prd` in `docs/project-state.json`. If the file does not exist, create it.

### 1a. Read and Re-Evaluate `smallProject`

After the `lastCommand` update, read the top-level `smallProject` value from `docs/project-state.json`. If the key is absent or `null`, treat it as "no judgment yet" — standard cadence applies. See `skills/sdd-guide/references/right-sizing.md > ## The smallProject field` for what the value means and how it shapes downstream behavior.

Then re-evaluate per `skills/sdd-guide/references/right-sizing.md > ## The smallProject field > Authoring lifecycle`. Apply the signals from that reference's `## Signals for the heuristic judgment` section against the material this command has access to at startup (`docs/scope.md` already loaded, plus anything in `docs/open-concerns.md` or `docs/discovery.md` that is on hand). Do not restate the signal list or rule of thumb here — the reference is canonical.

Outcomes:

- **Re-evaluation confirms the prior value:** no-op. Do not write `docs/project-state.json`. Do not append to process notes.
- **Re-evaluation flips the value (`true` → `false` or `false` → `true`):** write the new value to `docs/project-state.json` under the top-level `smallProject` key, and append a one-line entry to `process-notes-prd.md` at the project root noting the flip direction and a brief rationale (which signal or signals tipped the judgment). If `process-notes-prd.md` does not yet exist, create it on this append.

Bias toward humility — when in doubt, leave the value alone and proceed with standard cadence.

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

**Small-project right-sizing (conditional).** Consult the `smallProject` value read and re-evaluated at startup (Step 1a above).

- **When `smallProject` is `true`:** the Phase 1 beats designated in `skills/sdd-guide/references/right-sizing.md > ## Skippable Phase 1 beats per command > ### \`/sdd:prd\`` default to skipped during the section-by-section walkthrough. Do not restate that list here — the reference is canonical.
- **When `smallProject` is `false`, `null`, or absent:** walk through every scope section as normal.

These beats are **skippable, not required-skipped**. If the user's scope-document content or earlier answers leave a designated beat genuinely open, ask about it anyway. The skip is the default behavior at `smallProject=true`, not a prohibition on inquiry. Per the reference, acceptance-criteria generation remains in full regardless of `smallProject`.

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

Epic count is monitored as the requirements set takes shape, but the phase-split recommendation fires later — at the pre-generation step in `## Document Generation > ### Phase-Split Recommendation`. Do not flag it here.

### Deferrals

When an out-of-scope item surfaces during the PRD interview — something the user explicitly wants to push off rather than fold into a story, an acceptance criterion, or the Unvetted section — route the decision through the defer-to-backlog vs drop prompt defined in `skills/sdd-guide/references/backlog.md > ## Write trigger`. An affirmative defer answer appends an entry to `docs/backlog.md` per the schema in that reference; a drop answer (or any non-affirmative response) writes no entry and records the drop in `process-notes-prd.md` only. Items destined for the Unvetted section are not deferrals — those stay in the PRD and are processed later by `/sdd:refine`.

### Deepening Rounds

After the section-by-section walkthrough is complete, follow the deepening rounds protocol defined in `skills/sdd-guide/references/deepening-rounds.md` — including the Phase 1 → Phase 2 transition, the per-round question count (5 default, up to 10 with explicit reason+permission past the cap), and the structured end-of-round content recommendation (continue-with-topic-preview or close-with-reasoning). Do not emit a bare transition prompt; the reference defines the recommendation wording.

After the end-of-round content recommendation fires, emit the three-tier between-rounds context recommendation per `skills/sdd-guide/references/context-management.md`. Order is fixed: content recommendation first, then context recommendation, in the same end-of-round message group. The two are separate and independent.

Deepening questions for the PRD should target edge cases, ambiguities, thin areas, hidden assumptions, and unexplored angles. Ask them one at a time per the reference.

## Document Generation

When the user is ready to proceed, generate `docs/prd.md` using the template at `skills/sdd-guide/templates/prd-template.md`.

**Template usage:**
- Replace `[Project Name]` with the project name from `docs/scope.md`.
- `## Problem Statement` — distilled from the scope's problem/motivation section.
- `## User Stories` — all epics and stories from the conversation. Epics are `###` headings inside this section. Stories are list items. Acceptance criteria are checkboxes under each story.
- `## Unvetted` — write the section header with no list items and no inline body content beyond a single HTML marker comment placed inside the section body. The marker comment is the only content the section carries at write time. Compose the marker so it names, in plain language to future agents and `/sdd:refine`: (a) what the section is for, (b) the kinds of items that land here (new items that surface during sprints and have not yet been refined), and (c) who processes them (`/sdd:refine`, before future sprints). The exact wording is flexible; the marker's presence and explanatory intent are not. Keep the marker self-contained and stable across PRD rewrites — both `/sdd:prd` re-runs and `/sdd:refine` should leave it intact when section content changes around it.
- `## What We're Building` — concise summary of the product being built, synthesized from the full conversation.
- `## What We'd Add With More Time` — conditional backlog pointer. Before writing the artifact, check `docs/backlog.md` against the heading-scan rule in `skills/sdd-guide/references/backlog.md > ## Parser note` — if the file exists and has at least one entry by that rule, emit the section heading and the literal line `Deferred items: see \`docs/backlog.md\`.`. Otherwise omit both the heading and the body. Do not inline-list nice-to-haves here; deferred items live in `docs/backlog.md` and are the single source of truth. Do not restate the parser rule inline; the reference is canonical.
- `## Non-Goals` — things this project explicitly does not do. Pulled from scope if present, supplemented by anything surfaced during the PRD conversation.

### Phase-Split Recommendation

This is the first ordered step in document generation. It runs **before** `### Pre-Write AC Quality Check` and **before** `docs/prd.md` is written to disk. Fires once per `/sdd:prd` run, at the transition from organization to "ready to generate."

Count the epics the run is about to write — the in-memory set headed for the file. When that count reaches the threshold defined in `skills/sdd-guide/references/living-documents.md > ## PRD Health Monitoring`, emit a recommendation to the user before proceeding. Source the threshold from that reference; do not restate it here.

The recommendation message:

- Names the epic count and that it has crossed the threshold.
- Explains phase-splitting as breaking the project into smaller pieces, each going through the full SDD workflow independently (`/sdd:scope` through `/sdd:retro`).
- Names the trade-off concretely: phase-splitting yields smaller, more digestible cycles; keeping one phase keeps everything in a single PRD with greater coordination cost across a longer sprint sequence.
- Asks whether the user wants to split into phases or keep one phase, and waits for an answer.

Keep the message brief, accurate, and non-coercive. The user retains agency.

The recommendation is **informational, not blocking**. On user election to keep one phase, proceed to `### Pre-Write AC Quality Check` and then write the file as a single PRD. No re-prompt loop, no hard stop. If the user elects to split, stop generation, point them at re-running `/sdd:scope` for the first phase, and do not write `docs/prd.md` on this run.

Below the threshold, this step is a no-op — proceed directly to `### Pre-Write AC Quality Check`.

### Pre-Write AC Quality Check

This is a distinct, ordered step in document generation. It runs **exactly once per `/sdd:prd` run**, **immediately before** `docs/prd.md` is written to disk — after all template-population work above is complete and before the file write itself. It does not run during AC drafting in Phase 1, during deepening rounds, or on any subsequent re-read of an already-written PRD. Re-validation of ACs already on disk in `docs/prd.md` is `/sdd:refine`'s job, not this step.

Scope: every acceptance criterion the run is about to write — the in-memory set headed for the file. On a re-run that rewrites an existing PRD, the check still applies to the in-memory ACs at write time, not to whatever was previously on disk.

Evaluate every such acceptance criterion against three checks:

- **Specific** — describes a concrete behavior, not a category or aspiration. "Page load completes in under 2 seconds" is specific; "is fast" is not.
- **Observable** — someone can look at the system and determine pass/fail by inspection or interaction. The criterion names an artifact, output, state, or behavior a reader can point at.
- **Verifiable** — testable without subjective judgment. Two reasonable people running the check arrive at the same answer.

When an acceptance criterion fails one or more of these checks, rewrite it in place — replace the failing text with a version that passes all three — before generating the file. Do not write the PRD with failing criteria intact and patch them later. After the rewrite pass, write `docs/prd.md` with the cleaned-up acceptance criteria.

## Wrap-Up

### Delete docs/prd-resume.md

After `docs/prd.md` has been written (per `## Document Generation` above), delete `docs/prd-resume.md` if it exists. A missing file is not an error — continue silently. Per `skills/sdd-guide/references/pause-resume.md > ## Cleanup`, the resumed command owns this deletion; `/sdd:pause` is the only writer.

### Append to Process Notes

Append to `process-notes-prd.md` in the project root, per sdd-guide's `## Process Notes` (four categories, real-time, append-only). Also capture PRD specifics: number of deepening rounds and what they surfaced; any phase-splitting discussion and outcome.

If `process-notes-prd.md` does not yet exist at the project root, create it on the first append.

### Update Open Concerns

Append any new concerns that arose during the PRD conversation to `docs/open-concerns.md`. Follow the entry format from `skills/sdd-guide/references/open-concerns.md`.

### Update State

Confirm `lastCommand` is set to `/sdd:prd` in `docs/project-state.json`.

### Next step

Emit the handoff per `## End-of-Command Handoff` below.

## End-of-Command Handoff

Runs as the final step after `## Wrap-Up` (process notes appended, open concerns updated, and `lastCommand` confirmed).

Emit the handoff per the canonical template in `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff`. That template defines the two-line standard form, the first-handoff explanation paragraph (prepended exactly once per user), and the `handoffWarningShown` tracking convention in `~/.claude/sdd-user-profile.json`. Do not restate that mechanism here.

### Next-command target

The `[next-command]` slot in the template is always `/sdd:spec` for `/sdd:prd`.

### Outcome-summary line

Use a one-line outcome summary in the form `PRD generated.` Substitute project-specific phrasing only if it preserves the one-line, declarative shape (e.g., `PRD generated. N epics, M stories.`).

### Unconditional emission

The handoff fires unconditionally at completion. No context-weight heuristic, deepening-rounds outcome, or epic-count signal causes it to be skipped.

## Important Reminders

- One question at a time. Never ask multiple questions in a single message.
- Free-form questions only. No multiple choice for interview or planning questions.
- Acceptance criteria must be testable. Specific, observable, verifiable. This is non-negotiable.
- Heading names are stable addresses. `/sdd:plan` references them. Do not use generic names like "Miscellaneous" or "Other."
- The Unvetted section must exist in the initial PRD with no list items, carrying only an HTML marker comment that explains the section's purpose to future agents and `/sdd:refine`. It is where new items land during sprints.
- The PRD is both a requirements document and a progress tracker. Checkboxes get checked during sprints as work is completed.
- Edge cases and "what if" questions should be resolved during the conversation, not deferred to a later phase unless they genuinely belong elsewhere.
