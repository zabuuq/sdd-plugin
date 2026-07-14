---
name: build
description: Turns refined work from docs/plan.md into GitHub issues and runs the autonomous build-loop - one issue, one branch, one PR, each checked by a separate agent. Never merges; human PR review is the only gate.
disable-model-invocation: true
allowed-tools: Bash(git *) Bash(gh *) Read Write Edit Glob Grep Agent
---

# /sdd:build

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, guard rails, and lesson capture throughout.

This is the v6 `/sdd:build` — a clean-break rewrite. No behavior from the v5 sprint-checklist build (sprint files, step-by-step mode, wrap-up phase, PRD checkoff machinery) carries over.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/references/build-loop.md` (the vendored loop skeleton — build specialization)
3. `CLAUDE.md` (project root) — always in full; it instructs reading `AGENTS.md`, follow that
4. `docs/plan.md` — the Requirements section at minimum (issues lift ACs from here)
5. `docs/project-state.json` — `lastCommand`, `commandExplanationsShown`
6. `prototype/issue-map.md` (if it exists — an optional seed for the issue queue)

## State Updates (immediate)

Update `lastCommand` in `docs/project-state.json` to `"/sdd:build"` before any other work, per the state-tracking rule in sdd-guide.

## Prerequisites

- `docs/plan.md` must exist with version major `≥ 1` (finalized). If missing: "Run `/sdd:discovery` first." If still at major `0`: "Run `/sdd:refine` first."
- The GitHub gate must hold: a Git repository and an authenticated `gh`. On failure: "GitHub gate failed. Run `/sdd:onboard` to fix it." Stop; this is a block.

## Step 1: Issues from Refined Work

Build agents work **GitHub issues**, and issues come only from **refined work** — content that lives in `docs/plan.md` (settled requirements with ACs). The pipeline is: idea → refined into `plan.md` → issue → build.

### backlog.md is never acted on

`docs/backlog.md` stays the local, unrefined holding area. Build agents **never act on backlog entries directly** — no issue is created from a backlog item, no backlog line is picked up as work. A backlog idea enters the build only after it has been refined into `plan.md` through the normal front of the chain. If the user points build at a backlog entry, say exactly that and stop.

### Creating an issue from a refined item

A refined item (a story or coherent slice of `plan.md`'s Requirements) becomes one GitHub issue carrying all five parts:

1. **Title** — imperative, from the story.
2. **Description** — what to build and why, from the plan's content.
3. **Acceptance criteria** — **lifted, not authored**, from `plan.md` by AC ID: copy the AC lines verbatim with their 4-char IDs. The document owns the ACs; issue creation never invents, rewords, or extends them. If the work needs an AC that doesn't exist, that is a `plan.md` refinement, not an issue-creation edit.
4. **A link back** — a reference to `docs/plan.md` and the section/AC IDs the issue implements.
5. **Labels** — the `sdd` label (part of the stamping convention) plus any type labels that fit.

Use `gh issue create`. If `prototype/issue-map.md` exists, offer it as the starting queue — each row is a candidate issue — but every issue still carries the five parts lifted from `plan.md`.

Before creating issues, show the user the proposed issue list (titles + AC IDs) and confirm once; then create them.

## Step 2: Build-Loop

Run the vendored loop skeleton from `references/build-loop.md` with its **build (durable) specialization** — full git machinery. Work issues in issue-number order unless the user reorders.

### One issue → one branch → one PR

Each issue maps to exactly one branch and one pull request. No batching several issues into a branch, no splitting one issue across PRs. The PR closes the loop for its issue (`Closes #N` in the body).

### Stamping convention

Stamp everything the loop creates so `/sdd:resolve-pr` can recognize its own:

- **Branch:** prefix `sdd/<issue>-<slug>` (e.g. `sdd/14-login-form`).
- **PR:** the `sdd` label.

### Worktrees and fan-out

Each issue builds in **its own git worktree** (`git worktree add`), keeping items isolated from each other and from the user's working tree. Multiple agents **fan out across issues in parallel** — dispatch one agent per issue (via the Agent tool), each in its own worktree, each running the loop stages for its issue. Scale the fan-out to what the machine and the issue dependencies allow; dependent issues run in dependency order, independent ones in parallel. Remove each worktree after its PR opens.

### Loop stages per issue

Per `references/build-loop.md`: **plan → make → separate checker → bounded retry (≤2) → compound.**

- **Plan/make:** the agent reads the issue (ACs, plan link), plans, builds, and commits on the stamped branch.
- **Separate checker:** a distinct agent — never the maker — verifies the result against the issue's ACs before the PR reaches the maintainer. The checker's findings go back to the maker for the bounded retry.
- **Twice-failed:** stop that issue and **surface it** — report what failed and why, leave the branch as-is for inspection, and continue or halt per the failure's blast radius (an isolated failure doesn't stop other issues; a shared-contract failure does). Never guess past a failure.
- **Compound:** lesson-worthy findings go to `docs/learnings/`.

### Never auto-merge

The loop opens PRs and stops there. **No PR is ever merged by the loop or any agent** — not on green checks, not on checker approval, not on user inactivity. Human PR review is the only gate. Merged/closed PR cleanup belongs to `/sdd:resolve-pr`.

### No decisions mid-build

Required decisions are expected to already live in `plan.md` and the issue. When an agent hits an under-specified issue — a missing decision, an ambiguous AC, a contradiction with the plan — it **surfaces the question and stops that issue**; it never guesses, never picks "the reasonable default," never resolves ambiguity by fiat. The surfaced question goes to the user; the answer belongs in `plan.md` (via `/sdd:refine`) or the issue before that issue re-enters the queue.

## Process Notes

Per sdd-guide's `## Process Notes` section, append to `process-notes-build.md` at the project root — issue-queue decisions, loop outcomes per issue, surfaced under-specifications, and anything lesson-worthy (which also goes to `docs/learnings/` per sdd-guide's Lesson Capture).

## Completion

When the issue queue is drained (every issue has an open PR or was surfaced as blocked), report: issues built, PRs opened (with URLs), issues surfaced/blocked and why, and lessons captured. Remind the user that PR review is theirs — `/sdd:resolve-pr` handles feedback rounds and branch hygiene afterward. If the project looks complete (all plan ACs shipped and merged), recommend `/sdd:retro`; otherwise name what remains. No `/clear` handoff template — build ends on a status report.
