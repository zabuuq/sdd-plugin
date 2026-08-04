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
- **Global lesson** (about how the user works, applies beyond this project) → route to `~/.claude` (global CLAUDE.md, a skill, or the cross-project patterns file — suggest the fit). A global promotion edits the file in place but is **not** committed here — it lands through a single PR handled at retro end (see `### Global promotions land through a PR`).

**Re-promotion guard:** before suggesting promotion, check whether the lesson (or its substance) already exists in the user's global config. If it does, **actively recommend against re-promoting** — name where it already lives. The user can override, but the default is "already captured, skip."

A declined lesson stays in `docs/learnings/` — the store is permanent and never swept.

### Part 2: Timing

Report a rough **dump-to-prototype timing as a session count** — how many sessions elapsed from the discovery brain-dump to the first prototype review (count the sessions evident from process-notes files and git history). This is a count, not a stopwatch: "about 4 sessions," never hours and minutes. The north star is brain-dump → working prototype in about a day; the session count is how the maintainer tracks drift from it.

### Part 3: Issue Queue

Report the current issue-queue status via `gh`: open issues, open PRs awaiting review, merged/closed counts. Flag anything unmerged or unresolved so closing the project is an informed choice, not an accident.

## Global promotions land through a PR

Global promotions (Part 1) edit files under the global directory `~/.claude` in place. Those edits are **never committed directly to the global repo's default branch, and never committed without asking.** Batch them and settle the commit once, at the end of retro — after every lesson has been walked.

- **Preconditions.** The commit prompt and PR flow fire only when `~/.claude` is a git repo with a remote and `gh` is authenticated for it. Detect this at retro end. If any precondition fails — not a git repo, no remote, or `gh` unauthenticated — fall back to leaving the edited files in place (today's behavior) and tell the maintainer the promotions were written but no PR could be opened. Never hard-stop, never lose a promotion. With no repo to commit to, there is nothing to ask.
- **Ask once.** When the preconditions hold and at least one global promotion was made this session, ask the maintainer whether to commit the promotions to the global repo. Only an affirmative answer triggers the branch/push/PR flow.
- **On yes:** detect the global repo's remote and default branch — run `git -C ~/.claude symbolic-ref refs/remotes/origin/HEAD` (or `gh repo view --json defaultBranchRef` against `~/.claude`) — and branch off the **detected default**, never a hardcoded `origin`/`main`. Batch **all** of the session's global promotions into **one branch** named per the maintainer's `type/short-desc` convention (e.g. `docs/promote-retro-learnings`), apply the changes, push, and open a **single PR** — one PR per retro, not one per learning. The commit subject is imperative and ≤50 chars; the PR body lists the promoted learnings.
- **On no:** leave the promotion edits uncommitted in the `~/.claude` working tree for the maintainer to handle manually, and say so.

## Retro Artifact

Write `docs/retro.md` from `templates/retro-template.md` — the lessons table with promotion decisions, the session count, and the issue-queue status. Nothing longer.

## Closing

`/sdd:retro` is terminal. Emit `Project closed.` plus a one-line pointer to where promoted lessons went. No `/clear` instruction, no next-command line.
