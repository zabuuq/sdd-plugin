---
name: amend
description: Appends new items to an existing docs/plan.md through a short data-first interview, tags them with inline markers, bumps the version a single minor point, and hands off to refine. Never edits existing plan content. Anytime utility, any plan version.
disable-model-invocation: true
---

# /sdd:amend

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, guard rails, feasibility/viability pushback, and lesson capture throughout.

`/sdd:amend` exists for one job: add scope discovered *after* a plan already exists, without disturbing what's there. `/sdd:discovery` overwrites the plan from scratch; `/sdd:refine` only resolves existing markers — neither appends net-new items to a settled plan. `/sdd:amend` fills that gap. It is an **anytime utility**, chain-independent, sitting alongside `checkpoint` and `resolve-pr`.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/references/deepening-rounds.md` (interview mechanics — reused for the additions interview)
3. `skills/sdd-guide/references/markers.md` (marker syntax and ID rules for the new items)
4. `skills/sdd-guide/references/backlog.md` (defer-vs-drop for mid-interview tangents)
5. `skills/sdd-guide/templates/plan-template.md` (the epic/story/AC shape to match)
6. `docs/plan.md` (in full — the working surface; new items must fit its structure and not collide with its IDs)
7. `docs/project-state.json` — `lastCommand`, `commandExplanationsShown`
8. `docs/refs/` (if non-empty — optional ingest for the additions)

## State Updates (immediate)

Update `lastCommand` in `docs/project-state.json` to `"/sdd:amend"` before any other work, per the state-tracking rule in sdd-guide.

## Prerequisites

`docs/plan.md` must exist. If it does not, stop immediately and output: "Run `/sdd:discovery` first." Do not proceed.

There is **no version prerequisite**: amend works on a plan at **any version**, draft (`0.x`) or finalized (`≥1.0`). This is its only precondition.

## Step 1: Scope the Additions (data-first interview)

Run a **short, data-first interview scoped to the additions only** — reusing discovery's mechanics exactly, regardless of which command last ran. Amend never inherits or adapts to the prior command's behavior; it always uses these same mechanics:

- **Optional ingest.** If `docs/refs/` holds new material (or the user pasted some), inventory it per discovery's inventory gate and interview from it data-first. If there's nothing to ingest, run the interview as a focused brainstorm over the new items.
- **Interview mechanics** per sdd-guide and `references/deepening-rounds.md`: one question at a time, free-form, never multiple-choice; data-first confirm (state what the material already says, accept confirm/decline/add); active prompting at the end of each beat; the structured deepening-round recommendation (definite continue-with-topic-preview or close-with-reasoning), never a bare "another round?".
- **Scope discipline.** Questions cover only the **new** items — what they are, why, their acceptance criteria, any architecture-shaping facts. Do not re-interview or re-open anything already in the plan.

Mid-interview tangents the user wants to push off route through the defer-vs-drop prompt in `references/backlog.md`.

## Step 2: Append to the Plan

When the user accepts the interview close, write the new items into `docs/plan.md`:

- **Append only.** Add new epics/stories/acceptance criteria in the appropriate sections (e.g. new epics under `Requirements`, new rows under `Key Decisions` where warranted). **Never edit, reorder, or remove existing plan content** — every byte already in the plan stays exactly as it is. Amend only ever touches `plan.md`; it never touches prototype pages, navigation paths, or GitHub issues.
- **Fresh AC IDs.** Each new acceptance criterion gets a fresh 4-char lowercase alphabetic ID in inline backticks that is **not already used anywhere in the plan** (scan existing IDs first; never recycle or collide). Write ACs as plain list items — `` - `abcd` — <criterion> `` — with no `[ ]` checkbox.
- **Inline markers.** Tag the new items per `references/markers.md`: `[ASSUMPTION a#]`, `[GAP g#]`, `[CONCERN c#]`, with IDs computed per that file's stateless/round-local rule (max live ID of that type + 1). The optional `PROPOSED` form is available. Tag liberally — an untagged guess is invisible to `/sdd:refine`.

## Step 3: Version Bump

Bump `docs/plan.md`'s version by a **single minor point** from wherever it currently is — **never a major bump**. Examples: `0.2 → 0.3`, `1.1 → 1.2`, `2.0 → 2.1`. Edit the `**Version:**` line in place; no separate file, no rename. Finalization stays refine's job — amend never crosses a major boundary.

Confirm the bump to the user in one line (e.g. "Amended: `docs/plan.md` is now `1.2`; added 2 epics, 6 ACs, 3 new markers.").

## Process Notes

Per sdd-guide's `## Process Notes` section, append to `process-notes-amend.md` at the project root throughout the command — real-time entries capturing decisions, pushback, difficult questions, and pivots. Create the file on first append.

## End-of-Command Handoff

Emit the standard handoff form from `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff` pointing at `/sdd:refine`, so the newly added markers get walked like any others. Outcome-summary line: `Plan amended to <version>.` The handoff fires unconditionally at completion.
