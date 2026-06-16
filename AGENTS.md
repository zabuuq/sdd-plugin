# AGENTS.md

Project context, architecture decisions, and conventions for any AI agent or developer working in this repository.

## What this project is

**The SDD plugin** — a Claude Code plugin that drives a structured, interview-based workflow for planning and building software projects. The plugin produces real artifacts at every stage (scope, PRD, spec, sprint files, retros, process notes), and supports a feedback loop so users can capture friction and propose improvements during real-use projects.

**Who it's for:**
- The author (Jason), solo build.
- A local tech group Jason actively hands the plugin to. They run SDD on their own projects. v3 introduces no audience shift.

**What problem it solves:** ad-hoc prompting works for small tasks but breaks down on multi-week projects. SDD provides scaffolding — a known sequence, shared artifacts, and predictable interaction patterns — so multi-session, multi-sprint work stays coherent.

## Current cycle (v3)

v3 is a **token-efficiency pass**. Plugin behavior is unchanged; the goal is to make it cheaper to run — fewer input tokens spent on the plugin's own load stack, fewer output tokens spent on verbose responses. Techniques are mined from an external efficiency toolkit (credited in prose in `docs/efficiency-techniques.md`; never named in shipped artifacts) and reimplemented as native markdown. The plugin stays zero-runtime pure markdown — no hook, no scripts, no MCP middleware.

The work is subtractive and held to a hard line: **no workflow step lost, no behavioral rule dropped, artifacts still complete, still passing `claude plugin validate .`.** Two persistent artifacts drive and audit it: `docs/efficiency-techniques.md` (technique inventory + port verdicts) and `docs/load-map.md` (per-command load map + over-inclusion flags + rule-inventory audit trail). Output terseness and anti-sycophancy become a named `## Output Constraints` section in sdd-guide that every command inherits.

Success is judged by inspection against `docs/archive/v2/` (the v3 dogfood baseline), with rough ~23% input / ~32% output reductions as sanity-check guide-rails, not gates. See `docs/scope.md`, `docs/prd.md`, `docs/spec.md`.

## Prior cycle (v2)

v2 is a **streamlining pass** of v1, driven by real-use feedback from three projects (Event Planning App, fiver-gigs, sdd-plugin self-use). Not a feature expansion — refinement of what already works.

Key v2 additions:
- New `/sdd:discovery` command at the start of the planning chain (open exploration with file uploads).
- New `/sdd:pause` / `/sdd:unpause` utilities for multi-session resume.
- Cross-cutting interview behaviors (clarifying questions don't advance; 5-suggested-10-cap question rounds; definite end-of-round recommendations; right-size detection; active prompting for additional input).
- Three-tier between-rounds context management (continue / `/compact` / `/clear`).
- Cross-project feedback transfer via `feedbackLocalPath`.
- Per-phase process-notes files.
- Automatic backlog generation gated on user confirmation.
- `/sdd:retro` fires only at project close and captures cross-project patterns.
- Bug fixes: `/sdd:feedback` no longer touches `lastCommand`; PRD state-tracking checks off ACs when sprint items close.

v2 also renames `/sdd:sprint` → `/sdd:plan` and `/sdd:iterate` → `/sdd:polish`, removes `/sdd:reflect` (work absorbed into `/sdd:build` wrap-up and `/sdd:retro`), and ships `MIGRATION-v1-to-v2.md`.

See `docs/scope.md`, `docs/prd.md`, and `docs/spec.md` for full v2 details.

## Architecture decisions (the why)

**Pure markdown plugin, no runtime code.**
The reference implementation (`hackathon-in-a-plugin`) demonstrates that complex agent workflows can be expressed entirely as skill instructions and templates. No build step, no dependencies, no deployment infrastructure. Trade-off: limited to what can be expressed in agent instructions; no programmatic validation or state management beyond what the agent does through tool calls.

**Skills split into core + references.**
`sdd-guide` core (SKILL.md) defines tone, interaction rules, guard rails. Reference files (`deepening-rounds.md`, `context-loading.md`, etc.) define specific patterns. Every command loads core; only commands that need a specific pattern load that reference. Reduces context overhead per command. Trade-off: more files to maintain.

**Surgical context loading for `/sdd:build`.**
Multi-sprint projects accumulate large specs and PRDs. `/sdd:build` loads only the spec subsection and PRD story for the current item, not full documents. CLAUDE.md serves as the architectural summary for re-entry after `/clear`. Trade-off: agent has narrower context per item; might miss cross-cutting concerns in other spec sections.

**Lean spec posture.**
v1's `docs/spec.md` was 935 lines and partially duplicated runtime content from `references/*.md`. The duplication became a maintenance liability — the spec was both planning artifact AND runtime artifact, and updates had to happen twice. v2 made the spec a delta-and-resolution document; v3's spec is a trim-plan design doc in the same spirit. Either way the runtime files own "how"; the spec names what and why. Trade-off: spec readers must follow cross-references for full detail.

**Shared output constraints (v3).**
Terseness and anti-sycophancy live in one named `## Output Constraints` section in `sdd-guide/SKILL.md`, enumerated as concrete banned patterns rather than a vague "be terse" instruction. Every command inherits them by loading sdd-guide; no command overrides them. Trade-off: a single section governs all output voice — tuning it affects every command at once (which is the point).

**ID-based PRD acceptance criteria references.**
Sprint items carry `[PRD: a7kp]` tags pointing at AC IDs assigned by `/sdd:prd` at write time. IDs are stable across `/sdd:refine` reorders. Trade-off: sprint files unreadable without loading PRD context; mitigated by `/sdd:plan` and `/sdd:build` always loading relevant PRD sections.

**Open-concerns.md for cross-phase continuity.**
Embedded feedback from one phase evaporates with conversation context. `docs/open-concerns.md` carries unresolved questions and △ items across phases so they don't fall through the cracks at `/clear` boundaries. Every command reads it on startup; resolves what it can (including implicit resolutions); defers what it can't with rationale; appends new concerns.

**Process notes split per phase.**
v1 had a single `process-notes.md`. v2 splits per-phase (`process-notes-discovery.md`, `process-notes-scope.md`, etc.) so `/sdd:retro` can read selectively and history of each phase is locatable and self-contained. Append-only within a phase.

**`docs/v2-verification.md` is sdd-plugin-specific.**
Acceptable departure from the "plugin code works the same in every project" principle because the verification artifact lives in the project and the maintainer drives verification manually. Future plugin versions ship `<version>-verification.md` per the same pattern.

## Coding conventions

**SKILL.md frontmatter:**
```yaml
---
name: <command>
description: <one-line description>
disable-model-invocation: true   # for command skills
# OR
user-invocable: false             # for sdd-guide (loaded by other skills, not user-invoked)
---
```

**File naming:**
- Skills live at `plugins/sdd/skills/<command>/SKILL.md`.
- References live at `plugins/sdd/skills/sdd-guide/references/<name>.md`.
- Templates live at `plugins/sdd/skills/sdd-guide/templates/<name>-template.md`.
- User-project artifacts live at `docs/<name>.md`.
- Process notes at project root: `process-notes-<phase>.md` or `process-notes-sprint-N.md`.
- Resume files at `docs/<command>-resume.md` (written by `/sdd:pause`, deleted by the resumed command on completion).

**Sprint item format:**
Single checkbox bullet per item, with inline tag:
```
- [ ] What to build [PRD: a7kp, b3mq]
- [ ] What to build [PRD: c1xw] [FB: FB-005]
- [ ] What to build [unmapped]
```

**PRD acceptance criterion format:**
```
- [ ] `abcd` AC text describing the testable behavior.
```
IDs are 4-character lowercase alphabetic, assigned at write time, stable.

**Tag conventions (full reference in `plugins/sdd/skills/sdd-guide/references/sprint-tags.md`):**
- `[PRD: a7kp, b3mq]` — sprint-item PRD AC refs.
- `[FB: FB-NNN]` — sprint-item feedback refs (sdd-plugin only).
- `[unmapped]` — sprint item with no PRD ref.
- `[iteration-candidate]` — process-notes marker for in-flight iteration observations.
- `[close-sprint-manifest]` / `[/close-sprint-manifest]` — structured close-sprint record block.
- `[sprint-reopened]` — process-notes marker emitted by `/sdd:polish` re-open.

**Tone in skills:**
The user's `communicationStyle` from `~/.claude/sdd-user-profile.json` is authoritative. Default (when unset): collegial peer crossed with a sharp PM. Direct. No sycophancy.

## Development workflow

**Running the plugin locally:**
```
claude --plugin-dir ./plugins/sdd
```

**Plugin validation:**
```
/plugin validate .
```
Run after any change to manifest files, frontmatter, or directory structure.

**Self-dogfooding:**
The plugin is used to plan and build each cycle of itself. The v3 dogfood is this very planning chain, compared by inspection against `docs/archive/v2/`. See `docs/project-state.json` for current cycle state and the planning artifacts in `docs/`.

**Verification:**
v3 verification is structural and behavioral: `claude plugin validate .`, a rule-inventory check that proves no behavioral rule was dropped by a trim (recorded in `docs/load-map.md`), a workflow-step comparison against `docs/archive/v2/`, and the dogfood input/output comparison. See `docs/spec.md > Verification`.

## Don't

- Don't add runtime code or build steps. The plugin is markdown-only.
- Don't duplicate `references/*.md` content into `docs/spec.md`. Spec is delta-and-resolution; reference files own "how."
- Don't introduce command-name aliases. v1 → v2 rename was atomic; aliasing was explicitly rejected.
- Don't edit `docs/archive/v1/`. It's a historical snapshot.
- Don't auto-commit during `/sdd:build`. Clean state = last user commit.
- Don't write to `lastCommand` from `/sdd:feedback`. The Bug Fix 1 acceptance criterion is explicit.

## Files to be aware of

- `docs/scope.md`, `docs/prd.md`, `docs/spec.md` — v3 planning artifacts. Living through the v3 cycle.
- `docs/efficiency-techniques.md` — v3 technique inventory + port-to-markdown verdicts.
- `docs/load-map.md` — v3 per-command load map (load-justified / over-included flags) + rule-inventory audit trail.
- `docs/dogfood-comparison.md` — v3 Sprint 1 dogfood record: v3-vs-v2 input/output reduction by inspection, guide-rail verdict.
- `docs/open-concerns.md` — cross-phase concern tracking. Every command reads/updates.
- `docs/project-state.json` — per-project state. `lastCommand`, `currentSprint`, `buildMode`, `commandExplanationsShown`, `smallProject`.
- `docs/sdd-feedback.md` — feedback pile, carried across cycles.
- `docs/archive/v2/` — archived v2 artifacts; v3 dogfood comparison baseline.
- `~/.claude/sdd-user-profile.json` — user profile, cross-project (don't commit user-specific data; this lives outside the repo).
- `~/.claude/sdd-cross-project-patterns.md` — v2 cross-project pattern capture from `/sdd:retro`, cross-project.
