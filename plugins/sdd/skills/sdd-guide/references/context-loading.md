# Context Loading

**Used by:** all commands (critical for `/sdd:build`)

This reference defines exactly what each command loads into context on startup. Following these rules keeps context usage efficient and prevents commands from loading irrelevant material.

## Per-Command Loading Rules

| Command | Loads in full | Loads selectively | Skips |
|---------|--------------|-------------------|-------|
| `/sdd:onboard` | sdd-guide core | — | everything else |
| `/sdd:scope` | sdd-guide core, deepening-rounds, user profile | — | — |
| `/sdd:prd` | sdd-guide core, deepening-rounds, `docs/scope.md` | — | — |
| `/sdd:spec` | sdd-guide core, deepening-rounds, `docs/scope.md`, `docs/prd.md` | — | — |
| `/sdd:sprint` | sdd-guide core, deepening-rounds, `docs/project-state.json` | `docs/prd.md` (incomplete + unvetted items only) | completed PRD stories, full spec |
| `/sdd:build` | sdd-guide core, context-loading, living-documents, CLAUDE.md, current sprint file, `docs/project-state.json` | spec subsection + PRD story for current item only | full spec, full PRD, other sprint files, process notes |
| `/sdd:iterate` | sdd-guide core, current sprint file, CLAUDE.md | `docs/spec.md` (relevant sections) | full PRD, other sprint files |
| `/sdd:reflect` | sdd-guide core, current sprint file, `docs/prd.md`, `docs/project-state.json` | — | full spec (unless needed for story splitting) |
| `/sdd:refine` | sdd-guide core, living-documents, `docs/prd.md` (unvetted section) | `docs/spec.md` (affected sections only) | completed PRD stories |
| `/sdd:retro` | sdd-guide core, all process notes files, `docs/prd.md`, `docs/sdd-feedback.md` | `docs/spec.md` (summary/overview only) | sprint files (use process notes instead) |
| `/sdd:feedback` | sdd-guide core, `docs/project-state.json` | — | everything else |

**"Loads in full"** means the entire file is read into context. **"Loads selectively"** means only the relevant section(s) are extracted and loaded. **"Skips"** means the file is deliberately not loaded even if it exists.

## `/sdd:build` Re-Entry Load Order

After a `/clear` or context reset during a build session, reload context in this exact order:

1. **CLAUDE.md** — which instructs reading AGENTS.md as part of its content.
2. **Current sprint file** — find the next unchecked item in the checklist.
3. **The specific spec subsection and PRD story** referenced by that unchecked item.
4. **`docs/open-concerns.md`** — check for anything relevant to the current item.
5. **`docs/project-state.json`** — confirm build mode, sprint number, and last command.

This order ensures the agent has project conventions first, then the immediate task, then supporting context. Do not load the full spec or full PRD during re-entry.

## Threshold Rule

If any single file exceeds **200 lines**, load only the relevant section(s) rather than the full file. Extract the section by heading, checklist item reference, or other structural marker.

**Exemption:** `CLAUDE.md` is always loaded in full regardless of length. It contains project-wide conventions that apply to all work.
