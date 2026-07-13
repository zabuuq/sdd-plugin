# AGENTS.md

Project context, architecture decisions, and conventions for any AI agent or developer working in this repository.

## What this project is

**The SDD plugin** — a Claude Code plugin that drives a structured, interview-based workflow for planning and building software projects. The plugin produces real artifacts at every stage (scope, PRD, spec, sprint files, retros, process notes), and supports a feedback loop so users can capture friction and propose improvements during real-use projects.

**Who it's for:**
- The author (Jason), solo build.
- A local tech group Jason actively hands the plugin to. They run SDD on their own projects. Neither v4 nor v5 introduces an audience shift.

**What problem it solves:** ad-hoc prompting works for small tasks but breaks down on multi-week projects. SDD provides scaffolding — a known sequence, shared artifacts, and predictable interaction patterns — so multi-session, multi-sprint work stays coherent.

## Current cycle (v6)

v6 is a **clean-break rewrite of SDD's front end** into a brainstorm-first, single-document flow — the largest change since v1. It replaces the four-document planning chain (`scope → prd → spec`) and the old plan/polish machinery with **six chain commands** (`discovery → refine → validate → prototype → build → retro`, with `validate` optional) plus anytime utilities (`checkpoint`, `resolve-pr`). Audience unchanged: Jason solo, no external users, no backward compatibility — which is what makes a clean break acceptable.

Load-bearing design:
- **One document, `docs/plan.md`** — intent + requirements + architecture in a single source of truth, updated in place, never renamed. Draft/final/validated maturity is an in-file numeric **version** (`0.x` draft → `1.x` final → `2.x` validated). AI assumptions, gaps, and feasibility concerns are tagged **inline as markers** (`[ASSUMPTION a#]`, `[GAP g#]`, `[CONCERN c#]`); the new `references/markers.md` is their parser contract (replaces the retired `sprint-tags.md`). Nothing is ever force-decided — unresolved markers carry forward.
- **Discovery is data-first** — ingest mixed material (text / images / transcribed audio), inventory-gate it, interview from what's already provided, then auto-draft `plan.md`.
- **Validate is optional** — an ask-first self-critique plus external files dropped into `docs/validation/` (a source-agnostic drop-zone), reconciled one difference at a time.
- **Prototype** is disposable HTML/CSS/JS at repo-root `prototype/`, walled off from the real build, with an AI-authored navigation-paths companion file.
- **Build is an autonomous build-loop** — refined items → GitHub issues → one issue / one branch / one PR, checked by a separate agent, never auto-merged; human PR review is the only gate. The loop skeleton is **vendored** into `references/build-loop.md` (shared with prototype, minus git). `/sdd:resolve-pr` does PR/branch hygiene asynchronously.
- **GitHub is a hard dependency**, verified at `/sdd:onboard` (hard block, not a warning).
- **project-state.json → schema v2** — adds a `settings` object (persisted user choices, e.g. prototype fidelity), drops `currentSprint`/`buildMode`.
- **Context auto-suggest removed** — `references/context-management.md` retires; `/sdd:checkpoint` replaces it as an on-demand tool. `docs/learnings/` becomes a permanent, never-swept carry-forward store.

**Status: planning in progress.** The spec is written (`docs/spec.md`); v6 is **not yet built**. The live plugin still runs the v5 command set — the "Coding conventions", "File naming", and "Files to be aware of" sections below describe the *current* repo, not the v6 target. See `docs/scope.md`, `docs/prd.md`, `docs/spec.md`.

## Prior cycle (v5)

v5 **extends `/sdd:archive`** to handle the `docs/refs/` reference directory, which the command currently ignores entirely — `docs/refs/` is neither on the sweep allow-list nor part of the carry-forward trio, so it silently falls into "left in place" and is never read or moved. When a **real** archive run (non-empty sweep set) finds files under `docs/refs/`, the command:

- **Enumerates** them in a flattened, recursive **numbered list** — relative paths shown (so same-named files in different subdirs stay distinct), dotfiles/hidden excluded, symlinks counted — and asks the maintainer which to archive.
- **Parses** a numeric/keyword reply (`1,3` / `2-4` / `all` / `none` / empty; invalid tokens ignored-and-proceed) as a plan-*building* step inside a **two-step plan gate** that still takes exactly one final confirmation.
- **Moves** (not copies) the selected refs into `docs/archive/v{N}/refs/<relpath>`, preserving relative paths; leaves unselected refs live and untouched (no flag, copy, delete, rename, or report).
- **Enumerates** the swept refs per-file in the generated `INDEX.md` and **counts** them in the "Artifacts archived" total.

Strictly additive: when `docs/refs/` is empty/absent, or the sweep set is empty (the nothing-to-archive exit wins even if refs exist), every other archive surface is byte-for-byte unchanged. No `project-state.json` schema change and no new reference file — the feature is self-contained in `plugins/sdd/skills/archive/SKILL.md`. See `docs/scope.md`, `docs/prd.md`, `docs/spec.md`.

## Prior cycle (v4)

v4 added one **new command, `/sdd:archive`** — a mechanical close-and-reset that automates the hand-cranked archive the maintainer otherwise does between cycles. It is separate from `/sdd:retro` (which stays reflection-only and terminal). In a single run, with one confirmation gate, it:

- Sweeps the cycle's **SDD-produced** artifacts (an explicit allow-list: `docs/{scope,prd,spec,discovery,retro,open-concerns}.md`, `docs/sprint-*.md`, `docs/*-resume.md`, root `process-notes-*.md`) into `docs/archive/v{N}/`. Foreign/hand-placed files in `docs/` are left in place and surfaced in the plan.
- Writes an auto-generated `INDEX.md` and **byte-identical snapshots** of the three cross-cycle living docs (`backlog.md`, `sdd-feedback.md`, `project-state.json`), whose live copies stay in `docs/`.
- Resets live `project-state.json` (bumps `cycleNumber`, resets `smallProject`/`currentSprint`/explanations, etc.) so a fresh `/sdd:scope` starts clean in place.
- In a git repo, branches (`archive-v{N}`), commits with **tight staging**, pushes, and opens a PR via `gh`.

The plugin stays zero-runtime pure markdown — `/sdd:archive` drives `git`/`gh` as external CLIs it does not bundle. The one cross-cutting change is the `project-state.json` schema: a new `cycleNumber` field (owned solely by `/sdd:archive`) and a normalized `commandExplanationsShown` object. See `docs/archive/v4/{scope,prd,spec}.md`.

## Prior cycle (v3)

v3 was a **token-efficiency pass** — subtractive, behavior-unchanged, trimming the plugin's input footprint and adding a named `## Output Constraints` section (terseness + anti-sycophancy) to sdd-guide that every command inherits. Its working artifacts (`efficiency-techniques.md`, `load-map.md`, `dogfood-comparison.md`) and planning docs are archived at `docs/archive/v3/`.

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

**Allow-list sweep, not deny-list (v4 `/sdd:archive`).**
The archive sweeps only artifacts SDD itself produces (an explicit allow-list), never "everything in `docs/` except the trio." This guarantees a foreign or hand-placed file is never archived by accident. The cost — ad-hoc cycle docs aren't auto-swept — is mitigated by the plan gate surfacing a *left-in-place* list before any move, so nothing is silently stranded.

**`/sdd:archive` is the sole owner of `cycleNumber` (v4).**
The archive target version comes from a new `cycleNumber` field in `project-state.json`. Only `/sdd:archive` reads/writes it: after archiving cycle N it sets `cycleNumber = N+1`. Single writer ⇒ no double-increment, no stale-number race; `/sdd:scope` stays unaware of versioning. An absent field defaults to `1` and rides the existing "advance past an existing `v{N}`" collision check as its bootstrap.

**Snapshot-first; archive is exempt from the startup `lastCommand` overwrite (v4).**
Every command normally overwrites `lastCommand` on startup. `/sdd:archive` must not — it snapshots `project-state.json` byte-identical *before* the reset so the frozen snapshot preserves the cycle's true final command (matching the v3 snapshot, which correctly reads `"retro"`). It sets `lastCommand: "archive"` only in the live reset, after the snapshot.

**Two-step plan gate preserves the single-confirmation contract (v5).**
The refs-selection feature needs an interactive per-file choice, but `/sdd:archive`'s contract is "one confirmation covers the whole run." Resolution: the refs list + selection is a plan-*building* sub-step (Step A) that runs **before** the plan is displayed; the resolved selection folds into the plan as a "Will archive (refs)" group, and the existing single confirmation (Step B) approves the complete plan. The selection reply is input, not a go/no-go — the contract holds literally. Detection runs in pre-flight (read-only, all mutation stays behind the one confirmation); the nothing-to-archive exit short-circuits before Step A; and `docs/refs/` is excluded from the plan's "Left in place" set in all cases so refs are never double-surfaced. Execute ordering is load-bearing: selected refs move *before* `INDEX.md` generation so the index can enumerate and count them.

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
The plugin is used to plan and build each cycle of itself. The ultimate v5 dogfood is the real end-of-cycle run of `/sdd:archive` on this repo with a populated `docs/refs/` — archiving v5 into `docs/archive/v5/` (including the selected refs) and resetting for v6. See `docs/project-state.json` for current cycle state and the planning artifacts in `docs/`.

**Verification:**
v5 verification is structural and behavioral: `claude plugin validate .` for the edited skill, AC-by-AC manual checks against a populated `docs/refs/` fixture (nested subdirs, a same-named file in two subdirs, a `.gitkeep`, a symlink) with the destructive/git ACs exercised in a throwaway copy or disposable branch, the inert-path cases (absent / empty / dotfile-only refs, and empty-sweep-set), and the one-shot real dogfood archive run as the final proof — run only after the disposable-branch walkthrough passes. See `docs/spec.md > Testing Strategy`.

## Don't

- Don't add runtime code or build steps. The plugin is markdown-only.
- Don't duplicate `references/*.md` content into `docs/spec.md`. Spec is delta-and-resolution; reference files own "how."
- Don't introduce command-name aliases. v1 → v2 rename was atomic; aliasing was explicitly rejected.
- Don't edit `docs/archive/v1/`. It's a historical snapshot.
- Don't auto-commit during `/sdd:build`. Clean state = last user commit.
- Don't write to `lastCommand` from `/sdd:feedback`. The Bug Fix 1 acceptance criterion is explicit.

## Files to be aware of

- `docs/scope.md`, `docs/prd.md`, `docs/spec.md` — v5 planning artifacts. Living through the v5 cycle.
- `docs/refs/` — the reference directory the v5 `/sdd:archive` extension handles. May or may not exist at runtime; when populated during a real archive run, the maintainer selects per file what gets swept into `docs/archive/v{N}/refs/`.
- `docs/open-concerns.md` — cross-phase concern tracking. Every command reads/updates.
- `docs/project-state.json` — per-project state. `lastCommand`, `currentSprint`, `buildMode`, `commandExplanationsShown`, `smallProject`, and (v4) `cycleNumber`. Cross-cycle living doc — carried forward, snapshotted by `/sdd:archive`.
- `docs/backlog.md`, `docs/sdd-feedback.md` — cross-cycle living docs (deferral log, feedback pile), carried across cycles, snapshotted by `/sdd:archive`.
- `docs/archive/v1|v2|v3|v4/` — archived prior-cycle artifacts, each with an `INDEX.md` and frozen `project-state.json`. The structure `/sdd:archive` reproduces.
- `~/.claude/sdd-user-profile.json` — user profile, cross-project (don't commit user-specific data; this lives outside the repo).
- `~/.claude/sdd-cross-project-patterns.md` — v2 cross-project pattern capture from `/sdd:retro`, cross-project.
