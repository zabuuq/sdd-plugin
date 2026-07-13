---
name: prototype
description: Builds a disposable HTML/CSS/JS prototype of the plan at repo-root prototype/, with AI-authored navigation paths in a companion file, so the maintainer can walk the product before the real build.
disable-model-invocation: true
---

# /sdd:prototype

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, guard rails, and feasibility/viability pushback throughout.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/references/build-loop.md` (the vendored loop skeleton — prototype specialization)
3. `skills/sdd-guide/references/markers.md` (writes `[GAP]` markers on twice-failed pages)
4. `docs/plan.md` (in full — the sole source for what to prototype)
5. `docs/project-state.json` — read `settings.prototypeFidelity`

## State Updates (immediate)

Update `lastCommand` in `docs/project-state.json` to `"/sdd:prototype"` before any other work, per the state-tracking rule in sdd-guide.

## Prerequisites

`docs/plan.md` must exist. If it does not, stop immediately and output: "Run `/sdd:discovery` first." Do not proceed.

## Step 1: Fidelity (persisted)

Read `settings.prototypeFidelity` from `docs/project-state.json`.

- **`null` (first prototype run in this project):** ask the maintainer one question — **hi-fi** (clickable, navigable pages) or **lo-fi** (static screens/screenshots)? Persist the answer to `docs/project-state.json` under `settings.prototypeFidelity` (`"hifi"` or `"lofi"`) before building.
- **`"hifi"` or `"lofi"`:** reuse the persisted choice silently. Do **not** re-ask, re-confirm, or re-surface the choice — later runs proceed straight to the build.
- **Explicit switch only:** the persisted value changes only when the maintainer explicitly asks to switch (e.g. "make it hi-fi this time"). On an explicit request, update `settings.prototypeFidelity` and proceed. The command never volunteers the option on its own.

## Step 2: Build & Navigation

## Step 3: Loop, Review, and Issue Map

## Process Notes

Per sdd-guide's `## Process Notes` section, append to `process-notes-prototype.md` at the project root throughout the run — decisions, pushback, difficult questions, pivots, and each page's loop outcome. Create the file on first append.

## End-of-Command Handoff

Runs after the prototype review completes. Emit the standard handoff form from `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff` pointing at `/sdd:build`. Outcome-summary line: `Prototype built at prototype/.` The handoff fires unconditionally at completion.
