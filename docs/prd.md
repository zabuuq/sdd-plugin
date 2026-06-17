# SDD `/sdd:archive` Command — Product Requirements

## Problem Statement

The SDD plugin has no built-in "close and archive" step. `/sdd:retro` writes the retrospective and ends the chain, but leaves every cycle artifact live in `docs/` and the repo root. To start a new cycle in the same directory, the maintainer has to archive everything by hand: move artifacts into `docs/archive/v{N}/`, snapshot the cross-cycle living docs, reset `project-state.json`, then branch, commit, push, and open a PR. This is repetitive, error-prone, and easy to do inconsistently. `/sdd:archive` automates that hand-cranked close-and-reset as a single, distinct command — separate from `/sdd:retro`, which stays reflection-only and terminal.

## User Stories

### Run Control & Safety

- As the maintainer, I want to review a plan and confirm before any destructive change, and I want safe behavior when there is nothing to archive, so I never lose work to an accidental or premature run.
  - [x] `rcsa` Before moving any file or running any git operation, the command displays a plan that lists the files to be moved, the target archive directory `v{N}`, the branch name it will create, and the PR it will open, then waits for explicit user confirmation.
  - [x] `rcsb` When the user declines at the confirmation gate, the command makes no file changes and no git changes — no moves, no branch, no commit, no PR.
  - [x] `rcsc` When invoked with no cycle artifacts present (only the live carry-forward trio in `docs/` and no root-level `process-notes-*.md`), the command reports that there is nothing to archive and makes no changes — no `v{N}/` directory is created, no branch, no PR.

### Artifact Sweep

- As the maintainer, I want all cycle artifacts moved into a versioned archive directory so the working directory is cleared of the finished cycle, so a new project can start in place.
  - [x] `aswa` Running `/sdd:archive` moves every item in `docs/` — except the live carry-forward trio (`backlog.md`, `sdd-feedback.md`, `project-state.json`) — into `docs/archive/v{N}/`.
  - [x] `aswb` All root-level `process-notes-*.md` files are moved into `docs/archive/v{N}/`.
  - [x] `aswc` The target version `N` is read from the cycle-number field in `project-state.json`.
  - [x] `aswd` When `docs/archive/v{N}/` already exists at the version read from `project-state.json`, the command advances to the next unused `v{N}` and uses that directory for the archive.
  - [x] `aswe` After the sweep completes, the only files remaining in `docs/` (outside `docs/archive/`) are the three live carry-forward files.

### Living-Doc Preservation

- As the maintainer, I want the cross-cycle living docs kept live while also snapshotting them into the archive, so ongoing context survives the reset but the cycle record is complete.
  - [x] `ldpa` After the command completes, `backlog.md`, `sdd-feedback.md`, and `project-state.json` are all still present in `docs/`.
  - [x] `ldpb` A snapshot copy of each of the three living docs is written into `docs/archive/v{N}/`.
  - [x] `ldpc` Each snapshot copy in `docs/archive/v{N}/` is byte-identical to its live `docs/` counterpart as it stood at archive time (before the reset described under Directory Reset is applied to the live `project-state.json`).

### Archive Index

- As the maintainer, I want each archive to carry an auto-generated `INDEX.md` describing the cycle, matching the existing `docs/archive/vN/` convention, so a future reader lands on context immediately.
  - [x] `aixa` `docs/archive/v{N}/` contains an `INDEX.md` file after the command completes.
  - [x] `aixb` `INDEX.md` is generated automatically; the command does not prompt the user for a hand-authored cycle description.
  - [x] `aixc` `INDEX.md` names the cycle number `N`, lists the artifacts that were swept into the archive, and records the `lastCommand` value read from `project-state.json` at archive time.

### Directory Reset

- As the maintainer, I want `project-state.json` reset after archiving so a fresh `/sdd:scope` starts a clean cycle in the same directory.
  - [x] `drsa` After the command completes, running `/sdd:scope` begins a new cycle with no carryover of the prior cycle's sprint state or per-command explanation flags (the new cycle re-judges `smallProject` from scratch and re-shows first-run command explanations).
  - [x] `drsb` After the command completes, the cycle-number field in `project-state.json` is set so that the next `/sdd:scope`/`/sdd:archive` cycle targets a fresh version, not the version just archived.
  - [x] `drsc` The live `project-state.json` remains a valid, parseable JSON file after the reset.

### Git Integration

- As the maintainer working in a git repo, I want archive to branch, commit, push, and open a PR for the archive so the close is captured in version control.
  - [x] `gita` When the working directory is inside a git repository, the command creates a branch, commits the archive changes, pushes the branch, and opens a PR for the archive commit.
  - [x] `gitb` When the working directory is not a git repository, the command performs the file operations only and creates no branch, commit, or PR.
  - [x] `gitc` When the file archive has completed but a git step fails (for example `gh` is unavailable, no remote is configured, the push is rejected, or the branch already exists), the command reports the specific failure and prompts the user to confirm how to proceed; the completed file archive is left in place rather than rolled back or silently abandoned.

## Unvetted

<!--
  UNVETTED — holding area for items that surface mid-cycle and have not yet been refined.
  WHAT LANDS HERE: new requirements, stories, or acceptance criteria discovered during sprints
  (/sdd:build, /sdd:polish) or raised ad hoc, that have not been vetted into approved stories above.
  WHO PROCESSES IT: /sdd:refine, before future sprints — it triages these into approved stories,
  the backlog, or a drop. Leave this section and this marker intact across PRD rewrites even when
  its item list changes. Do not remove.
-->

## What We're Building

`/sdd:archive` — a new SDD plugin command that cleanly closes out a finished project cycle and resets the working directory for the next one. In a single run it sweeps the full cycle record (everything in `docs/` except the three cross-cycle living docs, plus the root `process-notes-*.md` files) into a versioned `docs/archive/v{N}/` directory, writes an auto-generated `INDEX.md` and snapshot copies of the living docs alongside it, and resets `project-state.json` so `/sdd:scope` can immediately start a fresh cycle in place. When a git repo is present it also branches, commits, pushes, and opens a PR for the archive. The command is runnable anytime with no precondition guard, shows a plan and gets one confirmation before touching anything, and behaves safely when there is nothing to archive. It serves the SDD plugin maintainer and any SDD user running multiple cycles in one directory — solo, self-directed use.

## What We'd Add With More Time

Deferred items: see `docs/backlog.md`.

## Non-Goals

- Does not modify or replace `/sdd:retro`. Retro stays reflection-only and terminal; archive is the separate mechanical close-and-reset step.
- Does not delete cycle artifacts. Everything is moved into the archive; nothing is destroyed.
- Does not enforce a chain-position precondition. There is no guard requiring sprints to be complete; archive operates on whatever artifacts are present when invoked.
- Does not target an external audience. This is solo, self-directed plugin-maintenance tooling, not a multi-user product.
