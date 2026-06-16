# Project Retrospective

## Project Metadata
- **Project:** SDD Plugin v2 — streamlining cycle
- **Total sprints:** 18
- **Date range:** 2026-05-12 (Sprint 1 planning) to 2026-05-14 (Sprint 18 build + merge)
- **Date of retro:** 2026-05-14

## Process Patterns Across Sprints

### What the Process Notes Revealed

The v2 cycle had a remarkably consistent shape across all 18 sprints:

- **Autonomous build mode locked early and held.** Sprint 1 chose autonomous; every subsequent sprint stayed autonomous without revisiting the question. No mid-sprint mode switches.
- **Subagent dispatch matured quickly into a stable pattern.** Sprint 1 dispatched per-item subagents; by sprint 3 the orchestrator had a clear playbook — small mechanical items inline (parallel where file paths didn't collide), judgment-heavy items as dedicated subagents with structured prompts (allow-lists, AC enumerations, verification expectations). Sprint 4's three-parallel dispatch and Sprint 18's strict-sequential dispatch represent the two ends of the same playbook.
- **Surgical context loading worked exactly as designed.** Per-item spec subsections + PRD stories were the load pattern from sprint 3 onward; no sprint loaded full spec or full PRD during build. Process notes from multiple sprints explicitly call this out as the thing that made autonomous mode practical.
- **Self-dogfooding lag was acknowledged at every sprint.** Sprints 1-3 ran on v1 `/sdd:sprint` because the v2 `/sdd:plan` rename hadn't shipped yet. Even after Sprint 3 landed the rename, the cached marketplace plugin continued running v1 — meaning every v2 sprint built v2 source against a v1 runtime. Sprint notes consistently flagged this as a caveat. The retro is the first command where the running plugin matters less than the source on disk (because retro doesn't write runtime behavior).

### Planning Phase Assessment

Scope, PRD, and spec held up structurally across 18 sprints. Revisions happened but were learning-driven rather than error-driven — the user couldn't recall specific revisions during retro, which itself is a positive signal (revisions blended into normal workflow rather than constituting events worth remembering).

No re-scoping events fired. No phase-splitting recommendations triggered (PRD stayed at 13 epics, under the 10+ phase-split threshold — actually it crossed the 10-epic line and stayed there comfortably). Sprint 1's PRD-foundation pass backfilled 206 stable 4-char alphabetic AC IDs and scrubbed alphanumeric slips from spec; this was spec-mandated cleanup, not planning miss.

The lean delta-and-resolution v2 spec posture (vs. v1's 935-line monolith) reduced spec maintenance friction noticeably — references owned "how," spec owned "what + why." Sprint process notes never reported needing both files updated for the same change.

### Sprint Cadence

User assessment: "the rhythm was great."

Observable: 18 sprints over 3 calendar days. Sprint sizes ranged from 3-9 items and 0-21 ACs. Some sprints were pure infrastructure ([unmapped], like Sprint 2's reference-file creation); others were heavy AC closure (Sprint 18 closed 21 ACs in one sprint). The rhythm didn't normalize sprint size — it normalized sprint coherence (one theme per sprint, even when the theme produced highly varied AC counts).

### Workflow Friction

User assessment: "I follow the workflow in a way that slightly matches what we were changing it to be, and it skipped some of the friction that the workflow currently has." When asked about residual friction: "Not really."

This is the most important retro finding. The dogfooder was working in a v2-adjacent style throughout the v1-runtime period, which insulated the cycle from observing real v1 friction firsthand. v2's design decisions came from the three real-use feedback projects (Event Planning App, fiver-gigs, sdd-plugin self-use) — but the dogfooder's adaptive workflow style means the cycle did not stress-test the friction those decisions targeted.

Implication: real v2 friction signal will come from the local tech group landing on v2 post-installation, not from the dogfood.

### Communication Patterns

User assessment: "I think it was pretty good."

Process notes show no friction-worthy communication events: clarifying questions resolved, deepening rounds stayed inside their caps, end-of-round recommendations carried explicit reasoning + topic previews. The "no clarifying questions, work without stopping" directive shaped autonomous build runs but didn't degrade interview quality during planning sprints.

## Product Assessment

### Outcome vs. Intent

User assessment: "The scope grew, but it grew with purpose adding to what I had originally envisioned."

v2 was scoped as a streamlining pass driven by v1 real-use feedback. Final state: all 21 in-scope feedback items absorbed (FB-001 through FB-021 in `docs/v2-verification.md`), plus the cohort of v2-specific structural changes (rename pairs, `/sdd:discovery`, `/sdd:pause`/`/sdd:unpause`, lean spec posture, per-phase process notes, automatic backlog generation, `/sdd:retro` project-close-only gating). The growth was purposeful and stayed inside the streamlining frame — no feature expansion beyond what feedback justified.

### Scope Changes

User assessment: nothing cut that's regretted.

All cuts that did happen were documented in `docs/backlog.md` (Get Shit Done / Superpowers external evaluation; bidirectional feedback share-back; auto-update recommendation in `/sdd:onboard`) — these were deferred deliberately to v3, with triggers documented for revisit. The backlog held its purpose as the "deferred-with-context" log throughout the cycle.

### Quality Assessment

User assessment: "I feel pretty good about it."

Project signals match: every PRD acceptance criterion (~130 ACs across 13 epics) is checked; Unvetted section is empty; sprint files all closed; no Story splits left dangling; no `[iteration-candidate]` markers stuck in a sprint's process notes. Sprint 18's v1-name sweep confirmed the active codebase is consistent on v2 naming with documented migration-context exceptions.

### Technical Debt

User assessment: "nothing significant."

No deferred bugs. No `[iteration-candidate]` entries carried forward into tech-debt logs across the cycle. The one open concern (soft AC criteria for MIT distribution) is a refinement-targeted note, not debt.

## SDD Workflow Performance

### What Worked

- **Autonomous build mode + subagent dispatch.** User's top win: "It freed me up while keeping me in the loop at checkpoints." This was the workflow shape that made an 18-sprint cycle in 3 calendar days possible.
- **Surgical context loading.** Per-item spec subsections + PRD stories kept context lean. Without this, autonomous mode would not have scaled past a handful of items per sprint before context bloat degraded subagent quality.
- **Per-phase process-notes files.** Made retro selective reading possible (v2 AC `aagk`) and gave each phase a self-contained history. The Sprint 18 process notes alone tell the entire migration-guide story; no need to read sprint 18's file to reconstruct what happened.
- **PRD AC-ID stability.** 206 stable 4-char IDs from Sprint 1 onward meant `[PRD: ...]` tags in sprint files survived across reorders and refinements without churn.
- **Open concerns as cross-phase continuity.** The mechanism caught five spec-mandated PRD edits in Sprint 1's planning and resolved them cleanly — none fell through the cracks at `/clear` boundaries.

### What Didn't Work

- **Dogfooding by the project author under-sampled real friction.** The dogfooder's adaptive workflow style insulated the cycle from the friction v2 was designed to resolve. The cycle proved the *implementation* worked; the *value* claim relies on tech-group exposure for validation.
- **The cached marketplace plugin running v1 throughout the v2 cycle.** Every sprint's behavioral changes shipped to disk but did not affect the running plugin. This is a structural artifact of building a Claude Code plugin while using it — not a workflow bug — but it's worth being explicit about: the first real exercise of v2's behavior happens *after* the marketplace cutover and reinstall.

### Workflow Recommendations

- **For v3 (or any future cycle):** consider an explicit "naive user pass" during dogfooding — periodically have the author exercise the v1-shipped workflow without their adaptive workarounds, to surface friction the adaptive style hides.
- **Tag releases earlier.** Sprint 4 produced release-prep artifacts but never tagged a release. A `v2.0.0-alpha` tag at Sprint 4 or 9 would have given the marketplace a stable anchor before the all-in v2 push and made dogfooding the in-progress v2 more practical.
- **Tech-group feedback collection should be aggressive in early v3.** The local tech group landing on v2 is the first true friction stress test. Feedback channels should be primed before the marketplace cutover.

## Plugin Feedback Summary

`docs/sdd-feedback.md` contains 19 entries logged during v1 use across Event Planning App, fiver-gigs, and sdd-plugin self-use.

### Feedback Themes
- **v1 bugs (2)** — `/sdd:feedback` clobbering `lastCommand`; `/sdd:reflect` PRD state-tracking gap. Both fixed in v2 (FB-001, FB-002).
- **New commands** — `/sdd:discovery` (separate from `/sdd:scope`), `/sdd:pause`/`/sdd:unpause` (multi-session resume). All shipped in v2.
- **Cross-cutting interview behaviors** — clarifying questions never advance, 5/10 question caps with topic preview, end-of-round recommendation with reasoning, right-size detection, active prompting for additional input. All shipped in v2.
- **Context management** — three-tier between-rounds model. Shipped in v2.
- **Automatic backlog generation** — gated on user confirmation. Shipped in v2.
- **Cross-project feedback transfer** — `feedbackLocalPath` profile field. Shipped in v2.
- **`/sdd:reflect` dissolution** — per-sprint ceremony absorbed into `/sdd:build` wrap-up + project-close `/sdd:retro`. Shipped in v2.
- **Communication-style onboarding** — open-ended free-form prompt; `defaultSprintMode` memory. Shipped in v2.

### How to Act on Feedback

The standard guidance (switch to plugin project, run `/sdd:scope` on the plugin) doesn't apply here — this IS the plugin project, and v2 has already absorbed every triaged-in-scope item. The remaining work on feedback:

1. **End-to-end verification of in-scope items.** The `Verified` column in `docs/v2-verification.md` is the manual checkbox per item; mark each row after exercising it during the post-installation dogfood + supplementary projects.
2. **Land the four floating feedback items.** Four entries logged on 2026-05-12 during `/sdd:spec` never entered the v2 PRD and aren't in `docs/backlog.md`:
   - `/sdd:sdd` environment-setup command.
   - `--help` modifier on every command.
   - `/sdd:backlog` manual-add tag.
   - `/sdd:viability` optional stress-test command.

   If these are v3 candidates, add them to `docs/backlog.md` deliberately (with Why deferred / Trigger to revisit fields). Otherwise note them as explicitly dropped. Leaving them floating is the only loose thread the v2 cycle didn't tie off.

## Open Concerns — Final Status

### Resolved During Retro
None. The retro itself did not surface new resolutions; it surfaced one floating-feedback item (above) that should land in the backlog as a follow-up action.

### Carried to Retro (Unresolved)
- **Soft acceptance criteria for MIT distribution** — Source: `/sdd:retro` (v1 cycle, friction-at-boundaries pattern). Target: `/sdd:refine`. Status: still open. Why it remains: `/sdd:refine` never ran on this project — refinements were handled inline during sprint work, not through a dedicated refine pass. Whether it matters going forward: only if v3 introduces similar judgment-based ACs. If v3 stays close to v2's testable-behavior bar, this concern can be marked dropped during v3 scope. If v3 introduces softer criteria (e.g., quality-of-pattern-capture targets), this concern should anchor anti-examples or example entries during the writing of those criteria.

### Previously Resolved
14 concerns resolved during the project (full list in `docs/open-concerns.md`'s `## Resolved` section):
- One-time PRD AC ID backfill, PRD edit for smallProject relocation, new profile fields for /sdd:onboard, cross-project pattern file name, AC-ID notation form (all resolved Sprint 1).
- Post-sprint command ceremony (resolved during v2 scope).
- Global user profile location, per-project state file format, PRD size threshold, seed file format, /scope seed file detection, plugin-level CLAUDE.md content, communication style enforcement, spec/runtime duplication, per-round summary file schema, right-size detection signals, three-tier between-rounds heuristic, end-of-command handoff wording, docs/v2-verification.md granularity, file uploads at non-discovery points, pause distillation quality bar, PRD-AC reference tag syntax, iteration-candidate marker syntax, definition of "clean state" for /sdd:build revert, /sdd:polish re-open mechanism, feedbackLocalPath path normalization (all resolved during /sdd:spec phase).

## Key Takeaways

### Top Wins
1. **Autonomous build mode + subagent dispatch** as the project's core workflow — freed the user while keeping them in the loop at sprint-checkpoint granularity. The pattern is the project's headline workflow finding.
2. **Surgical context loading** scaled past 18 sprints without context-bloat degrading subagent quality. Per-item spec/PRD refs were non-negotiable infrastructure for the autonomous-mode-at-scale pattern.
3. **v2 design discipline** — scope grew purposefully, additively, and stayed inside the streamlining frame. No drift, no cuts regretted, no rescue refactors.

### Top Lessons
1. **Author dogfooding can under-sample friction.** The author's adaptive workflow style insulated v2's design from observing the friction v2 was supposed to fix. The next stress test happens when fresh users land on v2 post-installation.
2. **The cached marketplace plugin lag is an artifact of building a Claude Code plugin with itself.** Every behavioral change shipped to disk but did not affect the running plugin. Recognized, not resolved — structural to plugin self-development.
3. **The "PRD foundation sprint" pattern (Sprint 1) is reusable.** A deliberate first implementation sprint that backfills cross-cutting infrastructure (here: 4-char AC IDs, spec-mandated PRD edits, alphanumeric scrub) cleared the runway for every subsequent sprint to land on a stable PRD/spec base.

### If Starting Over
- Same playbook overall — autonomous mode, subagent dispatch, surgical context loading, per-phase process notes, opens-concerns continuity.
- Two adjustments worth trying: (1) tag releases incrementally (alpha tag at Sprint 4, beta at Sprint 9, etc.) to give the marketplace a stable anchor before the all-in dogfooding push; (2) build in a deliberate "naive user pass" exercise during dogfooding — periodically run the workflow without the author's adaptive shortcuts to surface friction the adaptive style hides.
- One specific edit: triage feedback items the moment they're logged, not at the next planning phase. The four 2026-05-12 items that floated would have been routed cleanly (to v2 scope or to backlog) if triage had fired immediately rather than waiting for the next planning beat.
