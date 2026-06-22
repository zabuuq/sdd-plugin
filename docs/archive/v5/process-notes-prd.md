# Process Notes — /sdd:prd (v5)

## Decisions

- **smallProject re-evaluated, confirmed `true` (no-op).** All signals still point small: one command extension, internal dev tooling, markdown-only, solo maintainer. No flip, no write to project-state.json.
- **Refs-only edge case resolved: nothing-to-archive exit wins.** When the normal sweep set is empty, `/sdd:archive` takes its existing early exit even if `docs/refs/` has files. A populated `docs/refs/` alone does not count as "something to archive." Refs selection is strictly additive to a real archive run. (ACs `bprd`, `bpre`; resolves the open concern targeted at `/sdd:prd`.)
- **Numbered list is fully flattened.** The list recurses into nested subdirectories and shows one entry per file at any depth, each displayed by its path relative to `docs/refs/`. Same-named files in different subfolders appear distinctly. (ACs `rfsb`, `rfsc`.)
- **Swept files preserve relative path.** A selected `docs/refs/<relpath>` lands at `docs/archive/v{N}/refs/<relpath>`, keeping subfolder structure. Chosen over flattening-on-write for collision safety (two `notes.md` in different subfolders won't clobber). (AC `rfsf`.)
- **Empty-subdir cleanup left to spec.** Whether an emptied `docs/refs/<subdir>/` is removed after its last file is swept is a spec-level "how" detail; not given a PRD-level answer.

## Pushback

- None. User answers were terse and consistently directional; no disagreement either way.

## Difficult Questions

- None rated hard. The sharpest was the refs-only exit (the open concern routed to PRD), resolved quickly.

## Pivots

- None.

## PRD Specifics

- **Deepening rounds: 0.** Closed at the Phase 1 → Phase 2 transition. Recommended close (smallProject default direction, no contradicting evidence): small additive feature, single clear behavior, all three edge cases that fed acceptance criteria resolved during section conversion. User accepted.
- **Phase-split: no-op.** 2 epics (Refs Selection, Behavior Preservation) — below the PRD-health threshold. Not flagged.
- **New open concern raised → `/sdd:spec`:** whether the generated `INDEX.md` should enumerate the swept refs. Left unspecified at PRD level.
- **Output:** `docs/prd.md` — 2 epics, 5 stories, 16 acceptance criteria, all passing the specific/observable/verifiable pre-write check.
