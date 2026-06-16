# SDD v2 — Scope

## Cycle context

v2 is a streamlining pass of the v1 SDD plugin, driven by real-use feedback collected across three v1-era projects (Event Planning App, fiver-gigs, sdd-plugin self-use). Refinement of what already works based on friction surfaced during actual project use — not a feature expansion.

v1 planning artifacts archived under `docs/archive/v1/`. The feedback pile in `docs/sdd-feedback.md` is the de facto seed for v2.

## Audience

Jason (solo build) plus a local tech group Jason is a member of and plans to actively hand the plugin to. The tech group are consumers and feedback sources, not contributors. They will use v2 for both real projects and kick-the-tires evaluation — v2 must clear a working-software bar AND its feedback loop must be real and easy to use.

## Goals

1. **Fix the bugs** surfaced in v1 use (e.g., `/sdd:feedback` clobbering `lastCommand`; `/sdd:reflect` PRD state-tracking gap on non-split items).
2. **Improve start-to-finish flow** — every place feedback called out interruption or pacing friction gets smoothed.

The audience-shift goal (clean hand-off to the tech group) is implicit in the two pillars, not a separate third goal.

## What "done" looks like

1. Every triaged-in-scope feedback item has shipped and been exercised end-to-end at least once.
2. A new project run from `/sdd:onboard` through `/sdd:retro` produces no new feedback note saying "this interrupted my flow" or "this slowed my pace."
3. The post-sprint flow specifically feels like one cohesive close-out, not three stacked ceremonies.

## v2 command chain

```
/sdd:onboard → /sdd:discovery → /sdd:scope → /sdd:prd → /sdd:spec → /sdd:plan → /sdd:build → /sdd:polish → /sdd:refine → /sdd:retro
```

Utilities (anytime): `/sdd:pause`, `/sdd:unpause`, `/sdd:feedback`.

**Changes vs v1 (honest framing — see note below on the earlier scope/prd shuffle):**

*Added commands:*
- `/sdd:discovery` — new. Sits between `/onboard` and `/scope`. Open exploration with file uploads. v1 had no equivalent step; exploration was implicitly mixed into v1 `/scope`.
- `/sdd:pause` and `/sdd:unpause` — new resume utilities.

*Renamed commands:*
- `/sprint` → `/plan` — name now matches the planning-only job after `/build` absorbed sprint wrap-up.
- `/iterate` → `/polish` — disambiguates from `/refine`.

*Scope-narrowed commands (same name, smaller job):*
- `/scope` — v1 did exploration + project definition. v2 does only the defining/boundary work. Exploration moved to `/discovery`.
- `/build` — gains a wrap-up phase (PRD checkoff, story splitting, "anything notable?" beat, tech-debt capture); absorbs work that used to live in `/reflect`.
- `/retro` — fires only at project close in v2 (no longer recommended per-sprint); gains a cross-project pattern-capture step.

*Removed commands:*
- `/reflect` — work split between `/build` wrap-up and `/retro`.

*Unchanged in name and intent:*
- `/onboard`, `/prd`, `/spec`, `/refine`, `/feedback`.

**Note on the earlier scope/prd shuffle:** during v2 scoping, an intermediate design briefly removed `/prd` and renamed `/scope→/discovery` and `/prd→/scope`. With `/prd` reinstated as a dedicated requirements step (it had been planned for removal, then brought back), those two renames net out to nothing useful. The honest net change is "added `/discovery` at the start; `/scope` got narrower; `/prd` and `/spec` unchanged." The migration guide and user-facing docs use this framing, not the intermediate shuffle.

**One-line jobs:**
- `/discovery` — what is this? (explore)
- `/scope` — what's the boundary? (in/out)
- `/prd` — what must it do? (requirements: non-tech + tech)
- `/spec` — how does it work? (implementation: architecture, behaviors, runtime)
- `/plan` — what are we building this sprint?
- `/build` — build it and close it out
- `/polish` — post-sprint cleanup
- `/refine` — handle unvetted items (mini-PRD+spec pass)
- `/retro` — project-close retrospective + cross-project pattern capture

## Artifact lineage

| Artifact | Written by | Lifecycle |
|---|---|---|
| `docs/discovery.md` | `/discovery` | Created once, frozen at `/scope` start |
| `docs/scope.md` | `/scope` | Owned end-to-end by `/scope`. (Earlier design floated `/discovery` writing a soft draft; dropped during PRD interview — with `/prd` reinstated as a dedicated requirements step, `/scope`'s load is light enough that a pre-draft handoff isn't needed.) |
| `docs/prd.md` | `/prd` | Owned end-to-end by `/prd`. Contains non-technical AND technical requirements. |
| `docs/spec.md` | `/spec` | Owned end-to-end by `/spec`. Architecture, behaviors, runtime. |
| `docs/sprint-N.md` | `/plan` + `/build` | One per sprint. |
| `docs/backlog.md` | All planning commands | Append-only deferral log. Pure-reference model from in-flight docs. |
| `docs/v2-verification.md` | All v2-implementing commands (at build time) | Audit checklist for v2 release. One row per in-scope feedback item. |
| `process-notes-<phase>.md` | Each interview command | Per-phase notes (`discovery`, `scope`, `prd`, `spec`); sprint notes are `process-notes-sprint-N.md`. |

## In-scope deliverables

### Bug fixes
- `/sdd:feedback` never updates `lastCommand` — its startup sequence contains no step that writes to that field. Resolves the back-to-back feedback case where the second `/sdd:feedback` would otherwise read `/sdd:feedback` as "After command."
- PRD state-tracking fix: when a sprint item maps fully to its PRD acceptance criteria (non-split case), `/build`'s wrap-up checks off the corresponding PRD criteria. Cross-cutting items (where one sprint item covers only part of a larger PRD story) stay unchecked until all contributing items satisfy them.

### New commands
- `/sdd:prd` — dedicated product-requirements interview. Owns `docs/prd.md` (non-tech and tech).
- `/sdd:pause` — distills the in-flight command's conversation into `docs/<command>-resume.md` using the dogfooded schema (Status / Setup-already-done / Confirmed decisions / Round status / Pending decision / Files for next-session / Instructions for next-session). Must distill, not dump — the resume file must seed a zero-context model. Tells user "Paused. Run `/sdd:unpause`."
- `/sdd:unpause` — reads `lastCommand` from `docs/project-state.json`, loads the corresponding `<command>-resume.md`, loads the source command's `SKILL.md`, picks up. No flag, no path, no name to remember. Applies to `discovery`, `scope`, `prd`, `spec`, `plan`, `refine`, `polish`. Excludes `build` (sprint file is the resume mechanism) and `retro`/`onboard`/`feedback` (single-session).
- Pause file cleanup: resumed command deletes its own resume file when the underlying work completes.

### Behavioral rules across interview commands
- **Clarifying questions never advance the interview.** When the user asks a mid-interview clarifying question, the agent answers only the clarification, holds position on the current question, never substitutes its own recommendation as the user's answer, and only advances on an explicit user answer to the question that was asked. Applies to `/discovery`, `/scope`, `/prd`, `/spec`, `/plan`, `/refine`, `/polish`.
- **5 suggested / 10 hard cap questions per round.** Past 10, the agent must explain why and get explicit user permission to continue.
- **Definite end-of-round recommendation with reasoning.** "I think we should do another round, because…" or "I do not think we need another round, because…" — never a blank "continue?" prompt.
- **Deepening-round previews.** When recommending another round, the agent previews the topics that round would cover.
- **Right-size detection.** Interview commands detect small-project signals (during `/discovery` and re-evaluate at subsequent steps), propagate a "small project" hint downstream. When set: fewer Phase 1 beats fire, lower round-count expectations, agent picks fewer than 5 questions per round when answers are obviously thin, recommendation leans hard toward "I do not think we need another round." Generalizes the existing simple-sprint feedback to every interview command. Detection signals and thresholds are spec-time details.
- **Encourage user input during scope.** Interview commands actively prompt for additional input rather than letting users feel they should skip or hold back.
- **File uploads during `/discovery`.** Discovery has a designated place for users to upload reference files; the agent reads them as context for the discovery interview. Whether uploads should also be supported at other workflow points is a spec-time investigation.

### Context management between phases
- **Between rounds inside a command:** three-tier model. Agent recommends one of:
  - **Continue** — short round, light context, artifact already captures the round's value
  - **/compact** — meaningful conversation that the artifact didn't capture in full, but artifact + summary will carry forward fine
  - **/clear** — long/heavy round, artifact is rich and self-contained, conversation has superseded noise
  
  Recommendation is always definite with reasoning. User can override upward (always allowed to go heavier). Plugin can only recommend `/compact` and `/clear`; user runs them.
- **Between commands:** default `/clear`. Plugin emits an explicit handoff message instructing the user to run `/clear`, then invoke the next command. Required for new-user safety.
- **Recognition heuristic signals** (spec-time: round length, artifact-vs-conversation coverage, noise ratio).

### `/sdd:onboard` polish
- Communication-style question fully open-ended; drop the bulleted example menu.
- Surface where preferences live (`~/.claude/sdd-user-profile.json`) and that re-running `/sdd:onboard` updates them.
- End with a heads-up that `/sdd:discovery` is an interview, not a one-shot.
- New beat for `/sdd:feedback` (what / when / where notes go).
- New optional question for `feedbackLocalPath` (skip-friendly).
- Informational paragraph on third-party plugin auto-update mechanics (information, not advocacy — share-back is v3).
- Update flow surfaces retro-written cross-project pattern preferences alongside originals.

### `/sdd:plan` and `/sdd:build` (sprint loop)
- `/sdd:plan` is purely planning: reads previous sprint's process notes (populated by `/build`), surfaces carry-forward + concerns by reading, runs PRD state check (plan new sprint / recommend `/refine` / recommend `/retro`), plans the new sprint, validates that every sprint-item line carries either a `[PRD: ...]` reference or an explicit `[unmapped]` tag (refuses to finalize the sprint until validation passes), end-of-run next-command recommendation.
- **Sprint-mode memory:** `/sdd:plan` asks "use last sprint's mode?" and offers an "always use this mode" toggle that writes to `defaultSprintMode` in `~/.claude/sdd-user-profile.json`. (Relocated from `/sdd:onboard` polish during PRD interview — the behavior lives on `/sdd:plan`, not `/sdd:onboard`.)
- `/sdd:build` grows a wrap-up phase: at end of checklist, asks "Satisfied with this sprint, or items to iterate on?" Surfaces legitimate iteration candidates only if they came up during execution; never fabricates. Satisfied → close sprint (PRD checkoff, story-split unfinished items, tech-debt capture, write `process-notes-sprint-N.md`), suggest next action. Not satisfied → suggest `/sdd:polish`. Sprint stays open. Re-open escape hatch covers "I thought I was done."

### `/sdd:retro` (project close only)
- Stops being recommended per-sprint. Fires only at project close (when all PRD criteria checked).
- Grows a final cross-project-patterns step that writes preferences to `~/.claude/sdd-user-profile.json` (or sibling file). Future `/sdd:onboard` reads those.

### `/sdd:feedback` cross-project transfer
- New `feedbackLocalPath` field in `~/.claude/sdd-user-profile.json`, collected during `/sdd:onboard` (skip-friendly).
- `/sdd:feedback` writes the note locally to current project's `docs/sdd-feedback.md` AND forwards to `<feedbackLocalPath>/docs/sdd-feedback.md` if path set.
- Inside-clone detection: if cwd equals `feedbackLocalPath` (or descendant), local write IS the forward — no double-write.
- Failure handling: if path is set but invalid, save locally, warn once per session ("Run `/sdd:onboard` to update"), do not fail. Note is never lost. No retry, no half-success.
- Bidirectional share-back (project → upstream repo) deferred to v3.

### Automatic backlog generation
- All four planning commands (`/discovery`, `/scope`, `/prd`, `/spec`) and `/plan` write to `docs/backlog.md` on user-confirmed deferral. User-confirmed-before-write (no surprise entries).
- Drops live in process notes only (no second tracker for rejected ideas).
- Entry schema: Title / What / Why deferred / Trigger to revisit / Dependencies / Source.
- In-flight planning docs use pure reference — say "deferred items: see backlog.md" with no inline restatement.

### Process notes growth
- Planning notes split per phase: `process-notes-discovery.md`, `process-notes-scope.md`, `process-notes-prd.md`, `process-notes-spec.md`.
- Sprint notes continue as `process-notes-sprint-N.md`.
- `/retro` reads selectively.
- No within-phase compaction — `/sdd:pause` covers in-flight summarization needs.

### v1 → v2 migration
- Ships as `MIGRATION-v1-to-v2.md` in the plugin.
- **Framing for users:** "v2 adds `/discovery` at the start of the planning chain. `/scope` is now narrower (boundary only — exploration moved to `/discovery`). `/prd` and `/spec` unchanged. `/sprint` renamed to `/plan`, `/iterate` renamed to `/polish`. `/reflect` removed (work absorbed into `/build` and `/retro`). New utilities: `/pause`, `/unpause`."
- Covers: rename pairs (`sprint→plan`, `iterate→polish`); state-aware migration table keyed on v1 `lastCommand` (handles dead `/sdd:reflect` two-case, mapping to nearest v2 equivalent); resume-file rename rules; `process-notes.md` legacy-format pointer with one-time split recommendation; `feedbackLocalPath` opt-in; `/sdd:pause`/`/sdd:unpause` introduction; the `/scope` narrowing (existing v1 scope work involving exploration now lives in `/discovery`).
- Atomic rename; no aliases.
- Full sweep on active material (skills, commands, sdd-guide, README, all live prose in `docs/`); `docs/archive/v1/` untouched with one-line "pre-rename snapshot" note on the v1 archive index.
- sdd-plugin's own self-migration runs the standard guide. Manual archival of v1 planning artifacts under `docs/archive/v1/` already done — meta-case is simpler than typical.

### v2 release verification
- **Primary dogfood:** Jason runs v2 end-to-end on a small website project covering `/discovery` through `/retro`.
- **Coverage for items that won't fire on a small build:** other in-progress projects in varying completion states (cross-machine pause, 10-question cap, per-round /clear-and-continue, cross-project pattern capture, longer deepening rounds).
- **Deliberate exercise:** `/sdd:pause` and `/sdd:unpause` even when not strictly needed.
- **New artifact: `docs/v2-verification.md`** in the sdd-plugin project. One row per in-scope feedback item, populated by the implementing command when work ships, checked off manually by Jason when verified end-to-end. Schema and columns are spec-time. Out-of-pattern with the rest of the plugin's flow is acceptable — the artifact lives in the project; Jason drives verification.

## Out of scope / deferred to v3 (see `docs/backlog.md`)

- Evaluation of Get Shit Done (https://github.com/gsd-build/get-shit-done) and Superpowers (https://github.com/obra/superpowers) as influences.
- Bidirectional share-back: feedback flowing from any project back to the plugin upstream repo. Includes `feedbackShareUrl` profile config, `gh issue create` integration, project-name redaction toggle, `/sdd:feedback-submit` batching.
- Recommending auto-update of the SDD marketplace (currently informational only; promotes to recommendation when v3 closes the share-back loop).

## Open concerns carried to spec phase

See `docs/open-concerns.md`. Three v1 carry-overs (clean-state for `/build` revert; spec/runtime duplication; soft acceptance criteria for MIT distribution) plus spec-time details flagged during v2 scoping (per-round summary schema, right-size detection signals, three-tier recognition heuristic, end-of-command handoff wording, v2-verification.md columns, file uploads at non-discovery workflow points, pause distillation quality bar).
