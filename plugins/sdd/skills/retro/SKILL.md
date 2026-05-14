---
name: retro
description: Project-level retrospective across all sprints. Synthesizes what worked, what didn't, and how the workflow performed. Explains how to act on plugin feedback.
disable-model-invocation: true
---

# /sdd:retro

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `docs/project-state.json` — read `currentSprint`, `commandExplanationsShown`, and `buildMode`
3. `docs/prd.md` — full load
4. `docs/open-concerns.md`
5. `docs/sdd-feedback.md` — if it exists, load in full. If it does not exist, skip without error.
6. `docs/spec.md` — **selective load only**. Read only the summary/overview section (typically the first heading and any high-level description). Do not load the full spec.
7. `~/.claude/sdd-user-profile.json` — read communication style preference

**Skips:**
- Individual sprint files (`docs/sprint-N.md`). The process notes already capture what happened in each sprint — loading the sprint files would be redundant.
- `process-notes-*.md` files at startup. Process-notes files are read **per-phase, during the interview** (see Step 2), not concatenated at startup. This avoids loading the full project history into context up front.

## Prerequisites

- `docs/project-state.json` must exist. If missing: "Run `/sdd:scope` first."
- `docs/prd.md` must exist. If missing: "Run `/sdd:prd` first."
- At least one `process-notes-*.md` file must exist in the project root (any per-phase file — `process-notes-discovery.md`, `process-notes-scope.md`, `process-notes-prd.md`, `process-notes-spec.md`, or any `process-notes-sprint-N.md`). If none exist: "No process notes found. Run at least one planning phase or sprint cycle before running `/sdd:retro`." This is a sanity check that the project produced *something* — the per-phase files themselves are still read selectively in Step 2.

If any prerequisite fails, stop immediately. Do not attempt partial execution.

**PRD completion check:** Scan `docs/prd.md` for unchecked items (`[ ]`). If unchecked items remain, warn the user:

"Not all PRD items are complete — N items remain unchecked. `/sdd:retro` is designed as a project-closing retrospective and works best when all planned work is done. You can still run it now if you want to retrospect on the project as it stands."

Do not block execution. Proceed after the warning.

## State Updates (immediate)

Before doing any other work:

1. Set `lastCommand` to `"/sdd:retro"` in `docs/project-state.json`.
2. Read and process `docs/open-concerns.md` per sdd-guide open concerns protocol.

## First-Run Explanation

Check `docs/project-state.json` for `commandExplanationsShown.retro`.

**If `retro` has NOT been shown (or `commandExplanationsShown` does not exist):**

Output the following as plain text. This is not a question — do not wait for acknowledgment. Output it and continue.

---

**What /sdd:retro does**

Project retrospective looks across all sprints and the full development lifecycle to assess what worked, what didn't, and how the SDD workflow itself performed. Unlike per-sprint wrap-up (handled inside `/sdd:build`'s close), retro synthesizes patterns across the entire project and captures cross-project patterns to your user profile. If you logged plugin feedback during the project, retro also explains how to act on it. The result is a project-level retrospective document at `docs/retro.md`.

---

After outputting, set `commandExplanationsShown.retro` to `true` in `docs/project-state.json`.

**If `retro` HAS been shown:** Skip the explanation entirely. Do not mention it.

## Behavior

### Step 1: Read Open Concerns

Read `docs/open-concerns.md` in full. Note all remaining concerns. These will be resolved or carried forward in Step 8.

### Step 2: Per-Phase Process Notes Reading (during interview)

Process-notes files are read **one phase at a time, during the interview blocks below** — not concatenated at startup. When the interview reaches a phase's topic, read that phase's process-notes file at that moment. Do not pre-read the full set.

**Per-phase mapping:**
- Discovery → `process-notes-discovery.md`
- Scope → `process-notes-scope.md`
- PRD → `process-notes-prd.md`
- Spec → `process-notes-spec.md`
- Each sprint → `process-notes-sprint-N.md` (read per sprint as that sprint comes up during the sprint-cadence and per-sprint review)

**Missing-file handling.** If a phase's process-notes file does not exist (typically because the user skipped that phase entirely — e.g., a small project that skipped `/sdd:discovery`), proceed silently. Do not emit a warning. Do not ask the user about the absence. Treat it as "this phase produced no notes" and move on to the next phase or topic.

Use the notes from each phase as the primary source of truth for that phase. Do not ask the user to summarize what happened sprint by sprint — the per-phase notes already capture it. Reference specific entries from the notes when the interview surfaces patterns worth probing.

### Step 3: Process Retrospective (Interview)

Ask the user one question at a time about the following topics. These cover how the overall project process went — not just one sprint, but the arc from start to finish.

**Topics to cover:**

1. **Planning quality** — Did the planning phases (scope, PRD, spec) set the project up well? Was anything missing or over-specified? Before asking, read the relevant planning-phase process-notes files as separate reads: `process-notes-discovery.md`, `process-notes-scope.md`, `process-notes-prd.md`, `process-notes-spec.md`. Apply the per-phase missing-file rule from Step 2 — absent files mean that phase produced no notes; proceed silently.
2. **Sprint cadence** — Did the sprint rhythm work? Were sprints too big, too small, or about right? Before asking, read each `process-notes-sprint-N.md` file as a separate read (one per sprint, in order). Apply the per-phase missing-file rule from Step 2 for any sprint whose process-notes file is absent.
3. **Workflow friction** — Where did the SDD workflow create friction? Where did it add value?
4. **Communication patterns** — How well did the back-and-forth between you and the agent work across the project? What would you change?
5. **Overall wins** — What went best across the whole project?
6. **Overall pain points** — What was the most frustrating part of the project?

Adapt the order and phrasing to the conversation flow. Skip any topic the user already addressed in their responses. If the user gives a one-word response, follow up once to draw out specifics — then move on.

### Step 4: Product Assessment (Interview)

Ask the user one question at a time about what was built versus what was planned.

**Topics to cover:**

1. **Outcome vs. intent** — Does the final product match what was originally envisioned in the scope and PRD?
2. **Scope changes** — What was added, cut, or changed during the project? Were those changes the right calls?
3. **Quality assessment** — How do you feel about the overall quality of what was built?
4. **Technical debt** — Is there significant technical debt? Is it acceptable given the project goals?

### Step 5: Plugin Feedback Handling

**If `docs/sdd-feedback.md` exists:**

Read the accumulated feedback entries. Summarize the themes and patterns in the feedback for the user. Then explain how to act on it:

"You logged feedback about the SDD plugin during this project. To turn that feedback into improvements:

1. Switch to the plugin's project directory (where the SDD plugin source lives).
2. Use the SDD process on the plugin itself — start with `/sdd:scope` or `/sdd:prd` to plan the improvements.
3. The feedback you logged here becomes input for that planning process.

The feedback file at `docs/sdd-feedback.md` has the raw entries. You can copy it to the plugin project as a starting reference."

**If `docs/sdd-feedback.md` does not exist:**

Note in the retro document that no plugin feedback was logged. Skip the explanation.

### Step 6: Generate Retrospective Document

Read `skills/sdd-guide/templates/retro-template.md` for the output format.

Generate `docs/retro.md`. This is the project-level retrospective document. Capture:

- Project metadata (name, total sprints, date range).
- Process patterns across sprints — what the process notes revealed.
- Process retrospective findings from the user interview (Step 3).
- Product assessment findings from the user interview (Step 4).
- SDD workflow performance assessment — how well did the plugin-driven process work for this project?
- Plugin feedback summary (if applicable, from Step 5).
- Open concerns final status (from Step 8).
- Key takeaways — the most important lessons from the project.

### Step 7: Cross-Project Pattern Capture

Fires only after the retrospective interview (Steps 3, 4, 5) and the retro document generation (Step 6) are complete. This step captures patterns the user wants to carry forward to future projects, not project-specific findings.

Ask the user, free-form, one question:

"Are there any patterns from this project you want to carry forward to future projects?"

**If the user says no (or equivalent):** Skip this step entirely. No file write. Proceed to Step 8.

**If the user names one or more patterns:** Process them one at a time. For each pattern:

1. Surface the user's chosen phrasing back to them.
2. Ask, free-form: "Capture this pattern as `[user's phrasing]`?" — explicit per-pattern confirmation. Wait for the user's answer before doing anything else.
3. **On confirmation:** Append one line to `~/.claude/sdd-cross-project-patterns.md` using the format below. If the file does not exist, create it on first append — no header required; entries are append-only bullets across projects.
4. **On rejection:** Do not write that pattern. Move to the next pattern, or exit the step if there are no more.

**Entry format** (per `docs/spec.md > /sdd:retro`, the cross-project pattern capture bullet — do not invent additional fields or reorder):

```
- YYYY-MM-DD (project-name): User's chosen phrasing of the pattern.
```

- `YYYY-MM-DD` — today's date in ISO format.
- `project-name` — read `docs/project-state.json` for a `name` field if present; otherwise fall back to the project directory's basename.
- Trailing period after the user's phrasing is part of the format.

**File handling:**

- Path: `~/.claude/sdd-cross-project-patterns.md` (user-profile location, **not** project-local, **not** under `docs/`).
- Append-only across all projects. Never rewrite, reorder, or edit existing entries.

### Step 8: Final Open Concerns Resolution

This is the final update to `docs/open-concerns.md` for the project. Every remaining concern must be explicitly handled:

- **Resolve** concerns that were addressed by completed work. Mark indirect resolutions with an explanation.
- **Carry to retro** — for concerns that were never fully resolved, note them in the retro document under the open concerns section with context about why they remain open and whether they matter going forward.
- **Close** — mark all entries in `docs/open-concerns.md` as resolved or carried to retro. The open concerns file should have no unresolved entries after this step.

### Step 9: Update Project State

Update `docs/project-state.json`:

- Ensure `lastCommand` is `"/sdd:retro"`.
- Ensure `commandExplanationsShown.retro` is `true`.

### Step 10: Wrap Up

Confirm to the user:
- The project retrospective has been written to `docs/retro.md`.
- Open concerns have been fully resolved or carried to the retro document.
- Plugin feedback handling status (if applicable).
- The project is now closed from an SDD workflow perspective. Future work can start a new SDD cycle.

## Important Reminders

- **One question at a time.** Steps 3 and 4 involve multi-topic interviews — never combine questions into a single message.
- **Free-form questions only.** No multiple-choice options for retrospective questions.
- **Warn but don't block on incomplete PRD.** The prerequisite check warns about unchecked items but allows the user to proceed.
- **Process notes are the source of truth.** Do not ask the user to recall what happened sprint by sprint — read the process notes and reference them.
- **All open concerns must be handled.** Step 8 is not optional. Every entry in `docs/open-concerns.md` must be resolved or explicitly carried to the retro document.
- **Cross-project pattern capture fires after the interview.** Step 7 runs only after Steps 3-6 are complete — never mid-interview. The step is skippable when the user has nothing to carry forward, and every captured pattern requires explicit per-pattern user confirmation before any write to `~/.claude/sdd-cross-project-patterns.md`.
- **Plugin feedback explanation is conditional.** Only explain how to act on feedback if `docs/sdd-feedback.md` exists.
- **First-run explanation only on the very first `/sdd:retro` in the project.** Check `commandExplanationsShown.retro` — do not show it on subsequent runs.
- **Skip sprint files.** Use process notes instead. Sprint files are redundant when process notes are available.
