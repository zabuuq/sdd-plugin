# Open Concerns

## Open

### Sweep model diverges from PRD Artifact Sweep AC (allow-list vs deny-list)
- **Source:** /sdd:spec — Deepening Round 1, Q2 (sweep model)
- **Target:** /sdd:refine
- **Concern:** `/sdd:spec` settled the artifact sweep as an **allow-list** of SDD-produced files (`docs/{scope,prd,spec,discovery,retro,open-concerns}.md`, `docs/sprint-*.md`, `docs/*-resume.md`, root `process-notes-*.md`), with a plan-gate *left-in-place* list for everything else. This contradicts the approved PRD Artifact Sweep wording, which describes a **deny-list** ("moves every item in `docs/` except the live carry-forward trio"). The observable changed. `/sdd:refine` should reconcile the PRD ACs to the allow-list model (and the new left-in-place plan output) before/around `/sdd:build`.
- **Update (`/sdd:plan`):** AC IDs were minted for all 20 PRD ACs during `/sdd:plan` (a one-off — normally `/sdd:prd`/`/sdd:refine` own ID assignment; the PRD shipped without them). The Artifact Sweep ACs are `aswa`–`aswe`. When `/sdd:refine` reconciles the allow-list/deny-list wording it will likely rewrite/split `aswa`; expect ID churn there (IDs are not recycled — `aswa` retires, new IDs issue). Sprint-1 Item 4 tags `aswa` and carries an inline note flagging this.

### `marketplace.json` version-field wording assumes a field that has never existed
- **Source:** /sdd:build — Sprint 1, Item 7 (manifest registration)
- **Target:** /sdd:refine
- **Concern:** Item 7's AC (`marketplace.json` version matches `plugin.json`) and CLAUDE.md ("plugin.json and marketplace.json versions move together") both assume `marketplace.json` carries a `version` field. It never has — across v1/v2/v3 the version is single-sourced in `plugins/sdd/.claude-plugin/plugin.json` and `marketplace.json` references the plugin via `source: ./plugins/sdd`. Build resolved this by keeping the established single-source model (bumped `plugin.json` to `3.1.0`, left `marketplace.json` versionless). `/sdd:refine` should reconcile the AC wording and the CLAUDE.md "move together" line to match reality (either document the single-source model, or decide to add an explicit marketplace version field).

## Resolved

### `currentSprint` reset-map off-by-one (reset to 1 vs 0)
- **Source:** /sdd:plan — startup sprint-number resolution
- **Target:** /sdd:build (Sprint 1, Item 5) + spec / `references/living-documents.md` reconciliation
- **Resolution:** Reconciled during Sprint 1. The reset map now sets `currentSprint → 0 (or omit)` in **both** `docs/spec.md > project-state.json Reset` and `references/living-documents.md > project-state.json Schema and Reset` (Item 1), and the `/sdd:archive` SKILL reset section applies `0`/omit (Item 5). Combined with `/sdd:plan`'s `currentSprint + 1` rule, the next cycle's first sprint is correctly `1`. Caught and fixed as a dogfood catch — the v4 manual seed exhibited the same off-by-one.

### Who increments the cycle number
- **Source:** /sdd:scope — Deepening Round 1, Q2 (version source)
- **Target:** /sdd:spec
- **Resolution:** `/sdd:archive` is the **sole owner** of `cycleNumber`. After archiving cycle N it sets `cycleNumber = N+1` as part of the live reset; `/sdd:scope` never touches the field. Single writer → no double-increment, no stale-number race. Bootstrap when the field is absent: assume `1` and ride the existing "advance past an existing `v{N}`" collision check (lands on the correct next version). See `docs/spec.md > Version Resolution`.

### Git step shape (PR confirmation, branch naming, no-`gh` fallback)
- **Source:** /sdd:scope — Phase 1, git-behavior beat
- **Target:** /sdd:spec
- **Resolution:** Branch `archive-v{N}` (resolved N); collision → auto-suffix (`-2`, …) + notify. PR opened via `gh pr create`. **No separate PR-open confirmation** — covered by the single upfront plan gate. Repo detection via `git rev-parse --is-inside-work-tree`; not a repo → file ops only. Order branch→commit→push→PR; on any git failure after the file archive, report the specific failing step and resulting state, leave the file archive (and any successful git work) in place, prompt how to proceed. Commit uses **tight staging** (only archive's own paths, not `git add -A`) plus a pre-flight warning if the tree has unrelated uncommitted changes. See `docs/spec.md > Git Integration`.

### Post-archive handoff / next-step
- **Source:** /sdd:scope — Phase 1, end-of-round close recommendation
- **Target:** /sdd:spec
- **Resolution:** `/sdd:archive` is **out-of-pattern** (with build/retro/pause), not an interview command — not bound by the interview-handoff template or `handoffWarningShown`. Emits a one-line outcome + the two-line handoff to `/sdd:scope` (`Run /clear, then /sdd:scope`), with a conditional PR line. `/sdd:archive` is added to the "Out-of-pattern commands" list in `sdd-guide/SKILL.md`. See `docs/spec.md > Handoff`.

### Which project-state.json fields reset vs. carry forward
- **Source:** /sdd:scope — Deepening Round 1, Q1 (carry-forward) + Loose Implementation Notes
- **Target:** /sdd:spec
- **Resolution:** Field-level reset map defined: `version` carry; `cycleNumber` bump to N+1; `currentSprint` reset to 1; `buildMode` carry; `smallProject` reset to null; `lastCommand` reset to `"archive"`; `commandExplanationsShown` normalized (drop stale `sprint`/`iterate`/`reflect`, add `archive`, flip all to false **except `archive`** which is once-per-project); `notes` rewritten to a fresh seed line. Snapshot taken byte-identical **before** the reset. See `docs/spec.md > project-state.json Reset`.

### INDEX.md exact contents and format
- **Source:** /sdd:prd — Archive Index story
- **Target:** /sdd:spec
- **Resolution:** Auto-generated, structural-but-factual template matching the established `docs/archive/vN/INDEX.md` skeleton: `# docs/archive/v{N}/` title → static intro → `## Contents` (auto-enumerated from files actually swept, grouped; `project-state.json` + living-doc snapshots called out with `lastCommand`) → `## Cycle summary` (factual metadata bullets: cycle N, `Archived: <date>`, lastCommand, sprints, artifact count — no hand-authored narrative). See `docs/spec.md > INDEX.md Generation`.

## Deferred
