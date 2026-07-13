# Project Instructions for Claude

This repository is the **SDD plugin source** — a spec-driven development workflow plugin for Claude Code. The plugin itself lives at `plugins/sdd/`. Planning artifacts for the current development cycle live in `docs/`.

**Currently active cycle:** v6 — a clean-break rewrite of SDD's front end into a brainstorm-first, single-document flow. Six chain commands (`discovery → refine → validate → prototype → build → retro`, with `validate` optional) plus anytime utilities (`checkpoint`, `resolve-pr`) replace the old `scope`/`prd`/`spec`/`plan`/`polish` chain. One planning document, `docs/plan.md` (versioned in-file; AI assumptions/gaps/concerns tagged inline as markers), is the single source of truth. `build` runs an autonomous build-loop over GitHub issues — one issue → one branch → one PR, with human PR review as the only gate. **Planning in progress — the spec is written (`docs/spec.md`) but v6 is NOT yet built; the live plugin still has the v5 command set and structure.** See `docs/scope.md`, `docs/prd.md`, `docs/spec.md`. Prior cycles are archived at `docs/archive/v1|v2|v3|v4|v5/`.

Read `AGENTS.md` for full project context, conventions, and the rationale behind key architectural decisions.

## Quick orientation

- This is a markdown-only plugin. There is no runtime code, no build step, and no compiled artifacts. All behavior is defined through SKILL.md instructions with YAML frontmatter.
- The product being built IS a workflow for building things. The plugin runs against itself ("dogfood") for verification.
- The plugin distributes via GitHub as a Claude Code marketplace.

## Where things live

- `plugins/sdd/skills/sdd-guide/` — shared behavior loaded by every command. Tone, interaction rules, guard rails, command chain.
- `plugins/sdd/skills/sdd-guide/references/` — focused reference files for cross-cutting mechanisms (right-sizing, pause-resume, sprint-tags, context-management, etc.). The v2 spec posture is to put "how" detail here, not in `docs/spec.md`.
- `plugins/sdd/skills/sdd-guide/templates/` — markdown templates the command skills emit (scope, prd, spec, sprint, retro, discovery).
- `plugins/sdd/skills/<command>/SKILL.md` — one skill per command (`onboard`, `discovery`, `scope`, `prd`, `spec`, `plan`, `build`, `polish`, `refine`, `retro`, `pause`, `unpause`, `feedback`, `archive`). `archive` is the v4 addition (extended in v5 to handle `docs/refs/`) — an anytime, no-precondition close-and-reset command (not in the linear chain).
- `plugins/sdd/CLAUDE.md` — plugin-level loader pointer (instructs Claude to load sdd-guide when an `/sdd:*` command runs).
- `docs/archive/v1/`, `docs/archive/v2/`, `docs/archive/v3/`, `docs/archive/v4/` — prior-cycle planning, sprint, and process artifacts, each with an `INDEX.md` and a frozen `project-state.json`, kept untouched as historical record. v3's working docs (`efficiency-techniques.md`, `load-map.md`, `dogfood-comparison.md`) live in `docs/archive/v3/`. This archive convention is exactly what the `/sdd:archive` command (added v4, extended v5 for `docs/refs/`) automates.
- `docs/backlog.md`, `docs/sdd-feedback.md`, `docs/project-state.json` — the three cross-cycle living docs that carry forward across cycles (snapshotted, never swept, by `/sdd:archive`).

## Spec posture (read this before editing the spec)

`docs/spec.md` is a **design doc**, not a comprehensive runtime description. The current (v6) spec is a **command-rewrite design doc**: it defines the six-command v6 flow, the single-document (`plan.md`) model with its inline-marker and in-file-versioning contracts, the `project-state.json` schema change (v2), and the shared-behavior cascade. It designs the *new* reference files (`markers.md`, `build-loop.md`) in full because they don't exist yet, while still pointing at surviving `references/*.md` for unchanged cross-cutting "how." **Do not duplicate surviving reference-file content into the spec.** Note: v6 is planned, not built — the spec describes the target; the live plugin still has the v5 structure.

## Conventions

- All SKILL.md files use YAML frontmatter with `name`, `description`, and either `user-invocable: false` (sdd-guide only) or `disable-model-invocation: true` (every command). Frontmatter examples in `docs/archive/v1/spec.md` carry over.
- Sprint files use single-line checkbox items with inline tags: `[PRD: a7kp, b3mq]`, `[FB: FB-005]`, or `[unmapped]`. See `references/sprint-tags.md` for the exact parsers.
- Process notes split per phase: `process-notes-discovery.md`, `process-notes-scope.md`, `process-notes-prd.md`, `process-notes-spec.md`, `process-notes-sprint-N.md`. Append-only within a phase.
- `docs/backlog.md` is append-only deferral log. Never edited by `/sdd:refine`.
- `docs/v2-verification.md` is sdd-plugin-specific. Implementing commands (`/sdd:build` wrap-up) write to it when `[FB: ...]` tags appear on closed sprint items.

## Common patterns when working in this repo

- **Adding or changing a command's behavior:** edit the command's `SKILL.md` plus any reference file it loads. Update `docs/spec.md` only if the change affects the cross-cutting "what" — not the "how."
- **Adding a new reference file:** name it, add it to `references/`, and update the relevant command SKILL.md files to load it. Cross-reference from `docs/spec.md`.
- **Touching the plugin manifest:** `plugins/sdd/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` versions move together.
- **Editing PRD acceptance criteria:** every AC carries a 4-char lowercase alphabetic ID in inline backticks (`abcd`). IDs are stable; don't renumber. New ACs get new IDs; deleted ACs are gone, not recycled.

## Testing

No automated tests. Verification is structural and behavioral:
- `claude plugin validate .` for structural correctness.
- Dogfood: run the plugin's own commands on this project as it evolves.
- `docs/v2-verification.md` is the audit trail — implementing commands populate rows, the maintainer marks rows verified after end-to-end exercise.
