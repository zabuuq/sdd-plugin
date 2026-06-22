# Project Instructions for Claude

This repository is the **SDD plugin source** — a spec-driven development workflow plugin for Claude Code. The plugin itself lives at `plugins/sdd/`. Planning artifacts for the current development cycle live in `docs/`.

**Currently active cycle:** v5 — extends the existing `/sdd:archive` command to handle the `docs/refs/` reference directory, which it currently ignores entirely. When a real archive run finds files under `docs/refs/`, the command presents a numbered list and lets the maintainer choose, per file, which references to sweep into `docs/archive/v{N}/refs/` (relative paths preserved) and which to leave live for the next cycle. Strictly additive: when `docs/refs/` is empty/absent, or there is otherwise nothing to archive, behavior is unchanged. See `docs/scope.md`, `docs/prd.md`, `docs/spec.md`. Prior cycles are archived at `docs/archive/v1|v2|v3|v4/` (v4 added the original `/sdd:archive` command).

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

`docs/spec.md` is a **design doc**, not a comprehensive runtime description. The current (v5) spec is a **command-extension design doc**: it defines what the refs-selection feature adds to `/sdd:archive`, its behavioral contract, and where the new behavior slots into the command's existing pipeline, leaving the exact `SKILL.md` prose to `/sdd:plan` and `/sdd:build`. The v5 feature is self-contained to `plugins/sdd/skills/archive/SKILL.md` — no `project-state.json` schema change and no new reference file (refs handling is command-specific, not cross-cutting). The spec still points at `plugins/sdd/skills/sdd-guide/references/*.md` for cross-cutting "how" rather than restating it. **Do not duplicate reference-file content into the spec.** Update the reference file; spec just names it.

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
