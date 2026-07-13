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

### Output: plain web, walled off

- The prototype is **plain HTML/CSS/JS** — no frameworks, no build step, no package manager. Files open directly in a browser.
- Output goes to **`prototype/` at the repo root** (outside `docs/`). It is disposable by design and **walled off from any real build**: nothing in the code, comments, README, or plan marks it as a foundation, a starting point, or a scaffold for the final product. The real build starts fresh; the prototype's value is what it teaches, not its code. (`prototype/` sits outside `docs/`, so `/sdd:archive` never touches it — it stays live as the durable north-star reference.)

### Navigation paths

- Author the navigation path(s) — the ordered walk-throughs a reviewer follows to experience the product — **from `docs/plan.md` and nothing else**. Not from the prototype code as built, not from generic UX patterns: the paths express what the plan says the product does, so walking them tests the plan.
- Produce one or more paths as the plan warrants (e.g. a core flow plus secondary flows).
- Write the paths to a **companion file `prototype/navigation-paths.md`** — never into `plan.md`. Each path lists its steps: start page, action, expected next page/state.
- **Surface the paths to the maintainer** when the build completes: name the companion file and give the one-line gist of each path so the review can start immediately.

## Step 3: Loop, Review, and Issue Map

### Loop behavior

Build the pages with the vendored loop skeleton from `references/build-loop.md`, using its **prototype (disposable) specialization** — plan → make → separate checker → bounded retry (≤2) → compound — **without the git machinery**: no worktrees, no branches, no PRs. Pages build in place under `prototype/`. The checker verifies each page against the plan section and navigation-path step it implements.

**On a twice-failed page:** write a `[GAP: …]` marker into `docs/plan.md` per `references/markers.md`, placed in the section the page implements and naming what couldn't be built and why — then **continue building the remaining pages**. The prototype loop never halts on a failed page; a too-complex page is information about the plan, and the gap resurfaces in the review below and in the next `/sdd:refine` scan. (A `[GAP]` write pre-validate bumps the plan's minor version, e.g. `1.0 → 1.1`.)

### Prototype review

When the loop finishes, walk **each page along the navigation paths** from `prototype/navigation-paths.md` and check it against `docs/plan.md`: does the page do what the plan says at that step? Report per-path results to the maintainer — what matches, what diverges, and where `[GAP]` markers were written. The review is the command's deliverable as much as the pages are.

### Optional: page→issue map

On request (offer it once at review end, don't push), produce a **page→issue mapping** that seeds the real build: one row per page or flow — page, the `plan.md` requirement(s) it exercises (by AC ID), and the issue title that work would become. `/sdd:build` can use this as its starting queue. Write it to `prototype/issue-map.md` if the maintainer says yes.

## Process Notes

Per sdd-guide's `## Process Notes` section, append to `process-notes-prototype.md` at the project root throughout the run — decisions, pushback, difficult questions, pivots, and each page's loop outcome. Create the file on first append.

## End-of-Command Handoff

Runs after the prototype review completes. Emit the standard handoff form from `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff` pointing at `/sdd:build`. Outcome-summary line: `Prototype built at prototype/.` The handoff fires unconditionally at completion.
