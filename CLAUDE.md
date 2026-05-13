# Project Instructions for Claude

This repository is the **SDD plugin source** — a spec-driven development workflow plugin for Claude Code. The plugin itself lives at `plugins/sdd/`. Planning artifacts for the current development cycle live in `docs/`.

**Currently active cycle:** v2. See `docs/scope.md`, `docs/prd.md`, `docs/spec.md`.

Read `AGENTS.md` for full project context, conventions, and the rationale behind key architectural decisions.

## Quick orientation

- This is a markdown-only plugin. There is no runtime code, no build step, and no compiled artifacts. All behavior is defined through SKILL.md instructions with YAML frontmatter.
- The product being built IS a workflow for building things. The plugin runs against itself ("dogfood") for verification.
- The plugin distributes via GitHub as a Claude Code marketplace.

## Where things live

- `plugins/sdd/skills/sdd-guide/` — shared behavior loaded by every command. Tone, interaction rules, guard rails, command chain.
- `plugins/sdd/skills/sdd-guide/references/` — focused reference files for cross-cutting mechanisms (right-sizing, pause-resume, sprint-tags, context-management, etc.). The v2 spec posture is to put "how" detail here, not in `docs/spec.md`.
- `plugins/sdd/skills/sdd-guide/templates/` — markdown templates the command skills emit (scope, prd, spec, sprint, retro, discovery).
- `plugins/sdd/skills/<command>/SKILL.md` — one skill per command (`onboard`, `discovery`, `scope`, `prd`, `spec`, `plan`, `build`, `polish`, `refine`, `retro`, `pause`, `unpause`, `feedback`).
- `plugins/sdd/CLAUDE.md` — plugin-level loader pointer (instructs Claude to load sdd-guide when an `/sdd:*` command runs).
- `docs/archive/v1/` — v1 planning artifacts, kept untouched as historical record.

## v2 spec posture (read this before editing the spec)

`docs/spec.md` is a **delta-and-resolution document**, not a comprehensive runtime description. It explains what v2 changes vs. v1 plus resolutions of every open concern, and points at `plugins/sdd/skills/sdd-guide/references/*.md` for the "how" — parser rules, schemas, loading orders, exact behavioral text. **Do not duplicate reference-file content into the spec.** Update the reference file; spec just names it.

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
