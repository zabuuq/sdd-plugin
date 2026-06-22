# SDD `/sdd:archive` Refs Selection — Product Requirements

## Problem Statement

`/sdd:archive` sweeps a finished cycle's SDD artifacts into `docs/archive/v{N}/`, but it has a blind spot: the `docs/refs/` reference directory. `docs/refs/` is neither on the sweep allow-list nor part of the carry-forward trio, so it silently falls into "left in place" — never read, never moved, never surfaced. A maintainer who has accumulated cycle-specific reference material in `docs/refs/` gets no say over its fate at archive time; it just stays live and bleeds into the next cycle. This project closes that gap: when `docs/refs/` has contents during a real archive run, the command surfaces a numbered list and lets the maintainer choose, per file, what gets archived and what stays live — without disturbing any existing archive behavior.

## User Stories

### Refs Selection

- As the maintainer running `/sdd:archive`, I want the command to detect a populated `docs/refs/` and present a numbered list of its files, so I can choose which references to archive instead of having them silently ignored.
  - [x] `rfsa` During a real archive run (the normal sweep set is non-empty) in which `docs/refs/` contains at least one file, the command outputs a numbered list before performing any refs move.
  - [x] `rfsb` The numbered list contains exactly one entry per file found anywhere under `docs/refs/`, including files inside nested subdirectories — the listing is fully flattened, recursing to any depth.
  - [x] `rfsc` Each numbered entry displays the file's path relative to `docs/refs/`, so two files with the same name in different subdirectories appear as distinct entries.
  - [x] `rfsd` The command waits for the user's selection reply and moves no file under `docs/refs/` until that reply is received.

- As the maintainer, I want my selected refs moved into the cycle archive, so the cycle's frozen record includes the references I chose to keep.
  - [x] `rfse` Every file the user selects is moved — not copied — out of `docs/refs/` into `docs/archive/v{N}/refs/`.
  - [x] `rfsf` A selected file at `docs/refs/<relpath>` is written to `docs/archive/v{N}/refs/<relpath>`, preserving its relative subdirectory path under `refs/`.
  - [x] `rfsg` After the move completes, each selected file no longer exists at its original `docs/refs/` location.

- As the maintainer, I want unselected refs left in place and untouched, so they carry forward to the next cycle.
  - [x] `rfsh` Every file the user does not select remains at its original path under `docs/refs/` after the command completes.
  - [x] `rfsi` Unselected files receive no other action — they are not copied to the archive, deleted, renamed, or reported beyond their absence from the swept set.

### Behavior Preservation

- As the maintainer, I want refs handling to be completely inert when there are no refs, so archive behaves exactly as it did before this feature for cycles without references.
  - [x] `bpra` When `docs/refs/` is absent, the command produces no numbered list, no selection prompt, and no refs-related process note.
  - [x] `bprb` When `docs/refs/` exists but contains zero files (empty, or only empty subdirectories), the command produces no numbered list, no selection prompt, and no refs-related process note.
  - [x] `bprc` When `docs/refs/` is empty or absent, the existing sweep allow-list, living-doc trio snapshot, `INDEX.md` generation, `project-state.json` reset, and git steps each produce the same result they produced before this feature.

- As the maintainer, I want the refs prompt suppressed when there is otherwise nothing to archive, so the existing "nothing to archive" exit is preserved.
  - [x] `bprd` When the normal sweep set is empty, the command takes its existing "nothing to archive" exit even if `docs/refs/` contains one or more files.
  - [x] `bpre` In the empty-sweep-set case, no numbered list is shown and no file under `docs/refs/` is moved.

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

An extension to the existing `/sdd:archive` command that gives the maintainer per-file control over the `docs/refs/` reference directory at archive time. When a real archive run finds files under `docs/refs/`, the command flattens them into a numbered list (recursing into subdirectories), asks which to include, moves the selected files into `docs/archive/v{N}/refs/` with their relative paths preserved, and leaves the unselected files live for the next cycle. The feature is strictly additive: when `docs/refs/` is empty or absent, or when there is otherwise nothing to archive, the command behaves exactly as it does today. It serves the SDD plugin maintainer — solo, self-directed, internal dev tooling dogfooded against this repository.

## What We'd Add With More Time

Deferred items: see `docs/backlog.md`.

## Non-Goals

- Does not change any existing archive behavior — sweep allow-list, living-doc trio snapshot, `INDEX.md` generation, `project-state.json` reset, and git steps are untouched except for the addition of refs handling.
- Does not act on unselected refs. They are left exactly where they are; no flagging, deletion, copying, renaming, or reporting.
- Does not introduce elaborate selection UI or configuration. Selection is a numbered list and a numeric reply — nothing more.
- Does not decide the reply-parsing syntax (e.g. `1,3,5`, ranges, `all`/`none`) or where the refs prompt sits relative to the existing plan-confirmation gate. Both are deferred to `/sdd:spec`.
