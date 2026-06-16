# Context Loading

**Used by:** all commands (critical for `/sdd:build`)

This reference defines exactly what each command loads into context on startup, keeping context usage efficient and preventing commands from loading irrelevant material.

## Per-Command Loading Rules

| Command | Loads in full | Loads selectively | Skips |
|---------|--------------|-------------------|-------|
| `/sdd:onboard` | sdd-guide core, `~/.claude/sdd-user-profile.json`, `~/.claude/sdd-cross-project-patterns.md` | — | everything else |
| `/sdd:discovery` | sdd-guide core, deepening-rounds, user profile, `docs/refs/` (if non-empty) | — | everything else (writes `docs/discovery.md`) |
| `/sdd:scope` | sdd-guide core, deepening-rounds, user profile | — | — |
| `/sdd:prd` | sdd-guide core, deepening-rounds, `docs/scope.md` | — | — |
| `/sdd:spec` | sdd-guide core, deepening-rounds, `docs/scope.md`, `docs/prd.md`, `docs/refs/` (at startup, if non-empty) | — | — |
| `/sdd:plan` | sdd-guide core, deepening-rounds, `docs/project-state.json`, most recent `process-notes-sprint-N.md` (carry-forward items: iteration-candidate markers, tech-debt entries surfaced at the previous wrap-up) | `docs/prd.md` (incomplete + unvetted items only) | completed PRD stories, full spec |
| `/sdd:build` | sdd-guide core, context-loading, living-documents, CLAUDE.md, current sprint file, `docs/project-state.json` | spec subsection + PRD story for current item only, `[close-sprint-manifest]` block from `process-notes-sprint-N.md` when re-opening a sprint | full spec, full PRD, other sprint files, process notes |
| `/sdd:polish` | sdd-guide core, current sprint file, CLAUDE.md | `docs/spec.md` (relevant sections), most recent sprint file's `[close-sprint-manifest]` block if present (re-open mechanism) | full PRD, other sprint files |
| `/sdd:refine` | sdd-guide core, living-documents, `docs/prd.md` (unvetted section) | `docs/spec.md` (affected sections only) | completed PRD stories |
| `/sdd:retro` | sdd-guide core, `docs/prd.md`, `docs/sdd-feedback.md`, `~/.claude/sdd-cross-project-patterns.md` | process notes files (selective per-phase loading: read each phase's notes only as that phase is reviewed), `docs/spec.md` (summary/overview only) | sprint files (use process notes instead) |
| `/sdd:pause` | sdd-guide core, `docs/project-state.json`, source command's current working context | — | everything not needed to write `docs/<command>-resume.md` |
| `/sdd:unpause` | sdd-guide core, `docs/<command>-resume.md`, source command's SKILL.md | — | everything else (the resume file names what else to load) |
| `/sdd:feedback` | sdd-guide core, `docs/project-state.json` | — | everything else |

**"Loads in full"** means the entire file is read into context. **"Loads selectively"** means only the relevant section(s) are extracted and loaded. **"Skips"** means the file is deliberately not loaded even if it exists.

**Empty/absent files:** if a file or directory listed above does not exist or is empty (e.g., `docs/refs/` with no contents, no prior `process-notes-sprint-N.md`, no `~/.claude/sdd-cross-project-patterns.md`), the reader proceeds without prompting.

## `/sdd:build` Re-Entry Load Order

After a `/clear` or context reset during a build session, reload context in this exact order:

1. **CLAUDE.md** — which instructs reading AGENTS.md as part of its content.
2. **Current sprint file** — find the next unchecked item in the checklist.
3. **The specific spec subsection and PRD story** referenced by that unchecked item.
4. **`docs/open-concerns.md`** — check for anything relevant to the current item.
5. **`docs/project-state.json`** — confirm build mode, sprint number, and last command.

This order ensures the agent has project conventions first, then the immediate task, then supporting context. Do not load the full spec or full PRD during re-entry.

### Re-opening a sprint (close-sprint-manifest lookup)

When `/sdd:build` is invoked against a sprint that was previously closed and is now being re-opened (typically via `/sdd:polish`), augment the re-entry sequence with a `[close-sprint-manifest]` lookup:

- After step 2 (current sprint file), scan `process-notes-sprint-N.md` for a `[close-sprint-manifest]` block. See `references/sprint-tags.md` for the exact parser.
- If a `[close-sprint-manifest]` block is present, **the close-sprint state is the authoritative reference** for what was checked off, what splits happened, and what dispositions were recorded at the previous close. Treat the live sprint checklist as the working surface, but resolve any discrepancy against the manifest.
- If no manifest is present, the sprint was never formally closed; proceed with the standard re-entry order above.

This lookup runs in addition to — not in place of — the steps above.

## Threshold Rule

If any single file exceeds **200 lines**, load only the relevant section(s) rather than the full file. Extract the section by heading, checklist item reference, or other structural marker.

**Exemption:** `CLAUDE.md` is always loaded in full regardless of length. It contains project-wide conventions that apply to all work.
