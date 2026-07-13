---
name: resolve-pr
description: Anytime PR hygiene for build-loop PRs - deletes branches of closed/merged PRs it created, and works review feedback on its still-open PRs in bounded rounds (fix, reply, resolve, push).
disable-model-invocation: true
allowed-tools: Bash(git *) Bash(gh *) Read Write Edit Glob Grep
---

# /sdd:resolve-pr

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command.

`/sdd:resolve-pr` is a **vendored anytime SDD command** — owned by the plugin, distinct from the personal `resolve-pr-feedback` skill whose thread-resolution logic it reuses. It is vendored by necessity: it must track the PRs *the SDD build-loop* created, which is scope a personal skill doesn't carry. It runs independent of chain position — the PR-review gate is asynchronous and usually a fresh session, which is why this is its own command rather than a step inside `/sdd:build`.

## Loading

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `docs/project-state.json` (state tracking only)

No `plan.md` needed — the working surface is GitHub.

## State Updates (immediate)

Update `lastCommand` in `docs/project-state.json` to `"/sdd:resolve-pr"` if the file exists; do not create it.

## Prerequisites

The GitHub gate must hold: a Git repository and an authenticated `gh`. On failure: "GitHub gate failed. Run `/sdd:onboard` to fix it." Stop.

## Recognizing Its Own PRs

The command acts **only** on PRs the build-loop stamped per the convention in `references/build-loop.md`:

- the `sdd` label on the PR, **and**
- a head branch prefixed `sdd/<issue>-<slug>`.

**Unstamped PRs are ignored entirely** — never listed, never touched, no matter their state. The stamp is the sole ownership signal.

## Job 1: Branch Hygiene

Find stamped PRs that are now **closed or merged** (`gh pr list --label sdd --state merged` / `--state closed`). For each, delete its branches:

- **Remote:** `git push origin --delete <branch>` (skip silently if GitHub auto-deleted it).
- **Local:** `git branch -D <branch>` if present; also remove any leftover worktree registered on it (`git worktree remove`).

Report what was deleted. A branch that is already gone is a no-op, not an error.

## Job 2: Feedback Resolution (bounded rounds)

Find stamped PRs that are **open and carry unresolved review threads** (`gh pr list --label sdd --state open`, then read each PR's review threads).

For each open PR, work its unresolved threads in **bounded rounds** (at most 2 rounds per run):

1. **Fix** — address each thread independently on the PR's branch: make the change the reviewer asked for, or determine (with evidence) that no change is needed.
2. **Reply** — answer the thread: what changed and where, or why no change with reasoning.
3. **Resolve** — mark the thread resolved.
4. **Push** — push the branch once per round after all threads in that round are handled.

Thread-resolution discipline (reused from the personal `resolve-pr-feedback` skill): one thread at a time; never resolve a thread without either a fix or a reasoned reply; a thread whose ask is unclear or contradicts `plan.md` gets a clarifying reply and stays unresolved for the maintainer. If threads remain after the bounded rounds, stop and report them — the maintainer decides what's next. Never merge the PR.

## Completion

Report per PR: branches deleted, threads fixed/replied/resolved, threads left open and why. No handoff template — this is an anytime command; it reports and returns.
