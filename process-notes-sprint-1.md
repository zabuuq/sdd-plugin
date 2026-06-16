# Process Notes — Sprint 1 (/sdd:plan, v3)

## Session 2026-06-12

**Right-sizing.** smallProject re-evaluated at startup, confirmed `true` — no-op, no flip. The skippable `/sdd:plan` beats (multi-sprint sequencing, dependency-graph) were *not* skipped wholesale: v3 has a real dependency chain, so item ordering was reasoned through even though the project stays small.

**PRD state check.** All 24 ACs unchecked, Unvetted empty → routing rule 3 (proceed to planning). No `/sdd:refine` or `/sdd:retro` detour.

**Build mode.** Resolved from profile `defaultSprintMode: autonomous` (rule 1, no ask). Confirmation toggle: user kept the default. `buildMode: autonomous` written to project state.

**Grouping — proposed vs chosen.**
- Agent first proposed a 3-item Sprint 1 (foundation + output rules: inventory, load map, Output Constraints), deferring the reference/template/process-notes trims to Sprint 2 and verification to Sprint 3. Rationale: clean analysis-first foundation with a low-risk behavioral win.
- **User overrode: "everything in one sprint."** Accepted — it's a coherent unit (the whole efficiency pass), the project is small, and /sdd:plan doesn't time-box. Sprint 1 became all 11 stories / 24 ACs as a single 10-item checklist.

**Ordering decision.** Sequenced the 10 items by the spec's dependency chain: inventory (1) → load map (2) → Output Constraints (3, derived from the inventory) → reference/template/process-notes trims (4–6, driven by the load map) → uniform-inheritance audit (7) → behavior-preservation sweep (8) → zero-runtime check (9) → dogfood comparison (10). This resolves the spec's "order of operations" open issue.

**Honest flag raised.** Items 8–10 partly assess the whole sprint, and the dogfood (Item 10) compares this very planning chain against v2 — so the reduction number is an end-of-sprint read at build wrap-up. Noted to the user; not a blocker in autonomous mode.

**Deepening rounds:** 0. User said "generate the sprint" after confirming the list; no round run. smallProject close-default applied; no contradicting evidence had surfaced.

**Tag validation:** passed. Every item carries one tagged top-level checkbox `[PRD: ...]`; no `[unmapped]`. `docs/v2-verification.md` absent, so `[FB:]` prompting was skipped (sdd-plugin gate not active for v3).

**Open concerns:** "order of operations" resolved into the Sprint 1 ordering; "consolidation net-trim" narrowed to /sdd:build (Item 4); native-only-benchmark concern still open, lands at the Item 10 dogfood.

**Deferrals:** none surfaced.

---

# Process Notes — Sprint 1 (/sdd:build, v3)

## Session 2026-06-16

**Build mode.** Autonomous — one subagent per item, sequential dispatch, checkpoints every 3–4 items, surgical per-item spec/PRD loading. No per-item process notes; orchestrator writes once here at wrap-up.

**Commit posture.** No auto-commit (AGENTS.md overrides the generic SKILL instruction). All work left staged on disk for the user. Every subagent was instructed not to commit.

**Naming constraint held.** No shipped/persisted artifact, file, or rule label carries the "Caveman" name; the source toolkit is credited in prose only. PRD epic cross-references (e.g. `prd.md > Caveman Technique Mining`) are existing-heading pointers, allowed.

**Baseline resolution.** Command SKILLs/templates/references were never archived to `docs/archive/v2/` (that holds emitted artifacts), so git HEAD is the correct v2 baseline for SKILL/template/reference diffs. `docs/archive/v2/` is the baseline for emitted-artifact comparison.

**Item 4 steered off consolidation.** The load map (Item 2) showed references partition cleanly by phase, so any merge would raise load for a command loading only one of a pair, or yield no per-command saving. Realized trim was instead a clean per-command startup-load removal (`context-loading` dropped from `/sdd:plan`), which cannot raise any command's load. Resolves the "consolidation net-trim" open concern.

**sdd-guide net +205 words (honest).** Process Notes condensed (a reduction), but the new `## Output Constraints` section is a larger add. Deliberate one-time input cost that buys output savings on every turn — recorded in `docs/dogfood-comparison.md`.

**Dogfood verdict.** Input ~15–20% (concentrated in templates at −25% + a per-command startup-load drop) vs the ~23% guide-rail; output ~25–35% qualitative vs the ~32% guide-rail (confounded by project size and by artifacts predating the Output Constraints rules). Native-only mining lands in the neighborhood of both halves; the failure condition to pull the backlogged machinery is NOT met. Native-only stays.

**Incidental observation.** `plugins/sdd/.claude-plugin/plugin.json` still reads `2.0.0`; no Sprint 1 AC covers a version bump, so it was deliberately untouched. Logged in `docs/open-concerns.md` as a release-time action.

**Iteration candidates:** none. **Deferrals:** none new (machinery stays backlogged per scope).

[close-sprint-manifest]
- **Sprint:** 1 (v3 token-efficiency cycle — whole cycle in one sprint)
- **Closed:** 2026-06-16
- **Build mode:** autonomous
- **Items closed:** 10 / 10
- **ACs checked off in `docs/prd.md`:** 24 / 24
  - Item 1 — efficiency-techniques inventory: `tmca` `tmcb` `tmcc`
  - Item 2 — per-command load map: `lpma` `lpmb`
  - Item 3 — Output Constraints (terseness + anti-sycophancy): `trsa` `trsb` `asya` `asyb`
  - Item 4 — reference-stack trim: `rfsa` `rfsb` `rfsc`
  - Item 5 — template tightening: `tpla` `tplb`
  - Item 6 — process-notes leaning: `pnta` `pntb`
  - Item 7 — uniform inheritance audit: `inha`
  - Item 8 — behavior-preservation sweep: `prva` `prvb`
  - Item 9 — zero-runtime check: `vala` `valb`
  - Item 10 — dogfood comparison: `dfra` `dfrb` `dfrc`
- **Unmapped items:** none
- **`[FB: …]` items:** none → v2-verification step not applicable
- **Story splits:** none (no unfinished items)
- **Iteration candidates:** none
- **Artifacts created:** `docs/efficiency-techniques.md`, `docs/load-map.md`, `docs/dogfood-comparison.md`
- **Artifacts edited:** `plugins/sdd/skills/sdd-guide/SKILL.md`, 8 command SKILLs, 7 templates, 2 references, `docs/open-concerns.md`, `CLAUDE.md`, `AGENTS.md`, `docs/prd.md`, `docs/sprint-1.md`, `docs/project-state.json`
- **Commit posture:** uncommitted — all work staged for the user
- **Open concern logged:** `plugin.json` version still 2.0.0 → release-time bump
[/close-sprint-manifest]
