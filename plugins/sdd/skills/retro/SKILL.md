---
name: retro
description: Closes the project with a streamlined three-part retro - promote captured lessons with routing, report dump-to-prototype timing as a session count, and report the issue-queue status.
disable-model-invocation: true
---

# /sdd:retro

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

This is the v6 `/sdd:retro` — streamlined to three parts. The old long-form structured retro (process-pattern synthesis, product assessment, workflow-performance prose, the multi-section retro document) is gone and must not be reintroduced.

## Loading

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/templates/retro-template.md`
3. `docs/learnings/` (every file)
4. `~/.claude/sdd-cross-project-patterns.md` and global config the user may promote into (read-only, for the re-promotion check)
5. `docs/project-state.json`

## State Updates (immediate)

Update `lastCommand` in `docs/project-state.json` to `"/sdd:retro"` before any other work.

## Behavior — Three Parts

### Part 1: Lessons

List the lessons captured to `docs/learnings/` during the project — one line each (filename + gist). If the directory is empty or absent, say so and move to Part 2.

Then walk the lessons **one at a time**. For each, prompt whether to promote it, and suggest a routing:

- **Plugin-level lesson** (about how SDD itself should behave) → route to `/sdd:feedback`, which records it in `docs/sdd-feedback.md` for the plugin's next cycle.
- **Global lesson** (about how the user works, applies beyond this project) → route to `~/.claude` (global CLAUDE.md, a skill, or the cross-project patterns file — suggest the fit).

**Re-promotion guard:** before suggesting promotion, check whether the lesson (or its substance) already exists in the user's global config. If it does, **actively recommend against re-promoting** — name where it already lives. The user can override, but the default is "already captured, skip."

A declined lesson stays in `docs/learnings/` — the store is permanent and never swept.

### Part 2: Timing

Report a rough **dump-to-prototype timing as a session count** — how many sessions elapsed from the discovery brain-dump to the first prototype review (count the sessions evident from process-notes files and git history). This is a count, not a stopwatch: "about 4 sessions," never hours and minutes. The north star is brain-dump → working prototype in about a day; the session count is how the maintainer tracks drift from it.

### Part 3: Issue Queue

Report the current issue-queue status via `gh`: open issues, open PRs awaiting review, merged/closed counts. Flag anything unmerged or unresolved so closing the project is an informed choice, not an accident.

## Retro Artifact

Write `docs/retro.md` from `templates/retro-template.md` — the lessons table with promotion decisions, the session count, and the issue-queue status. Nothing longer.

## Closing

`/sdd:retro` is terminal. Emit `Project closed.` plus a one-line pointer to where promoted lessons went. No `/clear` instruction, no next-command line.
