# SDD v4 — `/sdd:archive` Command — Scope

## Idea

Add a new `/sdd:archive` command to the SDD plugin that cleanly closes out a finished project: it sweeps the cycle's artifacts into a versioned archive directory, resets the working directory so a new `/sdd:scope` run can start a fresh project in place, and — when the project is a git repo — branches, commits, pushes, and opens a PR for the archive.

Today there is no built-in "close and archive" step. `/sdd:retro` writes the retrospective and ends the chain, but leaves all artifacts live in `docs/` and the repo root. To start a new cycle in the same directory, the maintainer has to archive everything by hand (move artifacts into `docs/archive/vN/`, snapshot the cross-cycle living docs, reset `project-state.json`, then do the git branch/commit/PR dance). `/sdd:archive` automates exactly that hand-cranked process.

`/sdd:archive` is a **new, separate command** — not an extension of `/sdd:retro`. Retro stays reflection-only and terminal; archive is the mechanical close-and-reset, runnable as a distinct step.

## Who It's For

The SDD plugin maintainer and any SDD user who runs multiple project cycles in the same directory. Solo, self-directed use — this is a self-improvement cycle for the plugin itself, with no external audience.

## Inspiration & References

- **Direct user feedback** (`goldenhivegatherings.com/docs/sdd-feedback.md`, 2026-05-22, after `/sdd:spec`): "We need a clean way to mark a project as done from scope to all sprints completed so we can run scope again in the same directory and start with a new project (the old one being archived completely)."
- **The manual archive performed this session** — branching `archive-v3`, moving v3 artifacts into `docs/archive/v3/`, snapshotting the living docs, resetting `project-state.json`, and opening PR #6. That sequence is the reference implementation `/sdd:archive` should reproduce.
- **Existing archive convention** — `docs/archive/v1/`, `v2/`, `v3/`, each with an `INDEX.md` describing contents and a `project-state.json` snapshot frozen at cycle close.

## Goals

- Add a `/sdd:archive` command, runnable **anytime** with no precondition guard — it archives whatever artifacts are present when invoked.
- Sweep the full cycle record into `docs/archive/v{N}/`: all `docs/` contents **plus** the root-level `process-notes-*.md` files.
- Determine the archive target version `N` from a **cycle-number field added to `project-state.json`** (rather than a directory scan or a prompt).
- Preserve the three cross-cycle living docs — `backlog.md`, `sdd-feedback.md`, `project-state.json`: write a **snapshot** copy into `docs/archive/v{N}/` while keeping the live copies in `docs/`.
- Reset `project-state.json` so a subsequent `/sdd:scope` starts a clean new cycle.
- When a git repo is present: create an archive branch, commit, push the branch, and open a PR. With no repo, perform the file operations only.

## What "Done" Looks Like

Running `/sdd:archive` in a project directory:

- Moves every cycle artifact (all of `docs/`, except the live carry-forward trio, plus root `process-notes-*.md`) into `docs/archive/v{N}/`, where `N` comes from the cycle number in `project-state.json`.
- Leaves `backlog.md`, `sdd-feedback.md`, and `project-state.json` live in `docs/`, with snapshot copies in the archive.
- Resets `project-state.json` so the directory is ready for a fresh `/sdd:scope`.
- In a git repo, ends with an archive branch pushed and a PR opened for the archive commit.
- Produces a directory where `/sdd:scope` can immediately start a new project without manual cleanup.

## What's Explicitly Cut

Deferred items: see `docs/backlog.md`.

## Loose Implementation Notes

These are early hunches, not commitments — `/sdd:spec` settles them.

- **Cycle-number field** — `project-state.json` needs a new field (e.g., `cycleNumber`). Open question for spec: who increments it — `/sdd:archive` after archiving, or `/sdd:scope` on new-cycle start.
- **Archive contents** — mirror the established convention: an `INDEX.md` describing the cycle and a `project-state.json` snapshot frozen at the archived cycle's final state.
- **Git step shape** — branch naming, whether the PR step confirms before opening, and behavior when `gh` is unavailable are spec-level details.
- **Handoff** — `/sdd:archive` is not currently in the interview-command set; its post-run next-step recommendation (likely `/sdd:scope`) needs defining in spec.
- **Living-docs reset semantics** — exactly which `project-state.json` fields reset vs. carry forward (e.g., `cycleNumber` increments; `commandExplanationsShown`, `smallProject`, `currentSprint`, `buildMode` reset) is a spec detail.
