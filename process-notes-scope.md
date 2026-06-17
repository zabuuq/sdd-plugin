# Process Notes — Scope

## Decisions
- New cycle seeded from external SDD feedback. Searched five OneDrive project roots; only `goldenhivegatherings.com` carried a feedback entry (2026-05-22, after `/sdd:spec`): need a clean way to mark a project done (scope → all sprints) and re-run `/sdd:scope` in the same directory, archiving the old project completely.
- User chose to fold that feedback in as the cycle's core idea: a "close and archive a finished project" capability that resets the directory for a new cycle. Motivated directly by the manual archive just performed (branch → move artifacts to `docs/archive/v3/` → snapshot living docs → reset `project-state.json` lastCommand).

## Pushback
_(none yet)_

## Deepening Round 1 (short, right-sized)
- Q1 carry-forward: confirmed — snapshot `backlog.md`, `sdd-feedback.md`, `project-state.json` into `docs/archive/vN/`, keep live copies in `docs/`, reset `project-state.json` for fresh cycle.
- Q2 version source: **add a cycle-number field to `project-state.json`**; `/sdd:archive` reads it to choose `docs/archive/v{N}/`. Increment-ownership (archive-time vs new-scope-time) left as a spec open question.
- Q3 sweep scope: confirmed — archive sweeps **all `docs/` contents + root-level `process-notes-*.md`**. Root process notes are part of the cycle record and must be pulled in.

## Wrap-up
- One deepening round (short, right-sized: 3 questions). Surfaced and resolved carry-forward handling, version source, and sweep scope.
- Four open concerns logged to `docs/open-concerns.md`, all targeted at `/sdd:spec`: cycle-number increment owner, git step shape, post-archive handoff, field-level reset map.
- Project not split (single command). `docs/scope.md` generated and locked by user. `smallProject=true` written as authoritative end-of-Phase-1 value.

## Difficult questions
- Relationship to existing `/sdd:retro` (already the terminal "project close" command) is the open design fork: extend retro vs. a new command. Raised in Beat 1.

## Pivots
_(none yet)_

## Decisions (cont.)
- Design fork resolved: a **new separate `/sdd:archive` command**, not an extension of `/sdd:retro`. Retro stays reflection-only / terminal; archive is the mechanical close-and-reset, runnable as a distinct step.
- Trigger/precondition: **anytime, no guard.** No "project must be complete / retro must have run" precondition. The command archives whatever artifacts are present when invoked.
- Scope of archive: **all docs artifacts from discovery to finish** — the full SDD chain output set.
- Git behavior: **if a repo exists**, create an archive branch, commit, push the branch, and create a PR. (Automatic when repo present; mirrors the manual archive done this session.) No repo → file operations only.

## Right-sizing
- All six small-project signals present (one command, solo maintainer, markdown-only, no external users, no integrations, concise framing). Running right-sized scope interview; skipping users-beyond-self, six-month-horizon, standalone out-of-scope beats.
