# Process Notes — Sprint 1 (v4 cycle)

Real-time notes for `/sdd:plan`. Append-only. Categories: decisions, pushback, difficult questions, pivots.

## /sdd:plan

### Decisions
- **smallProject re-evaluation:** confirmed `true` (no-op). Single new command (`/sdd:archive`), solo self-improvement cycle, no external users, no multi-component architecture, no core third-party integration (git/gh are incidental CLIs). Standard small-project signals hold; left value alone.
- **Sprint mode:** autonomous, resolved from profile `defaultSprintMode: "autonomous"` (resolution rule 1 — no mode question asked). Toggle confirmed: keep `defaultSprintMode` set to autonomous (no-op profile write). `buildMode: "autonomous"` already recorded in project-state.json. Mode locked for the sprint.
- **Sprint shape:** one sprint, 8 items, building `/sdd:archive` end-to-end. Right-sizing skipped the multi-sprint-sequencing and dependency-graph Phase 1 beats (smallProject). User explicitly confirmed "everything in one sprint." Mirrors v3, which ran its whole cycle in a single sprint.
- **Item split:** items 2–6 author different sections of the single new `skills/archive/SKILL.md`, split along PRD-story lines so each carries its own `[PRD: ...]` tag (same pattern v3 used splitting work across sdd-guide). Items 7 (manifest/version bump) and 8 (verification) are `[unmapped]` — infra/verification, not product ACs.

### Difficult questions
- **Missing PRD AC IDs (hard blocker for the write).** `docs/prd.md` has 20 ACs but none carry the 4-char IDs that the project's convention (and v3's PRD) use and that the sprint-tag validator needs for `[PRD: abcd]` tags. `/sdd:plan` is forbidden from minting AC IDs (owned by `/sdd:prd`/`/sdd:refine`). Surfaced to user; they chose "plan now, fix IDs before write" — run planning to completion, then either step to `/sdd:refine` or add IDs to the PRD before `docs/sprint-1.md` is written.

### Pushback
- User checked whether I'd confused "updating the plugin" with "running the plugin on a project." Clarified: no confusion — this is the v4 self-development (dogfood) cycle; `/sdd:plan` produces the sprint that drives source edits under `plugins/sdd/`. The AC-ID gap is a plain incompleteness in `docs/prd.md`, orthogonal to that distinction.

### Pivots
- (none)

### Deepening
- Zero deepening rounds. At the Phase 1 → Phase 2 transition I recommended closing (smallProject default-to-close, no contradicting evidence): the spec is a detailed self-contained design doc and the grouping maps 1:1 onto its sections. Topics a round would have covered — branch-collision-suffix vs directory-advance-N readability, `notes` seed-line wording, hidden deps among same-file items 2–6 — are all pinned in the spec's Open Issues and resolvable at build time. Context recommendation: continue (light remaining work; `/clear` belongs at the end-of-command handoff). User accepted closing.

### Decisions (write phase)
- **Sprint number = 1.** Live `project-state.json` had `currentSprint: 1` but no `docs/sprint-*.md` existed — first v4 sprint. Produced `docs/sprint-1.md` and kept `currentSprint: 1` (Step 8: set to the new sprint number). Did NOT follow Step 7's literal `currentSprint + 1 = 2`, because the live value was a mis-seed (see open concern below).
- **AC IDs minted in `/sdd:plan` (one-off).** Per the user's "fix IDs before write" choice (which authorized adding IDs to `docs/prd.md`), assigned 20 stable 4-char IDs: `rcsa/rcsb/rcsc` (Run Control & Safety), `aswa`–`aswe` (Artifact Sweep), `ldpa/ldpb/ldpc` (Living-Doc Preservation), `aixa/aixb/aixc` (Archive Index), `drsa/drsb/drsc` (Directory Reset), `gita/gitb/gitc` (Git Integration). Normally `/sdd:prd`/`/sdd:refine` own this; flagged in open concerns that `/sdd:refine` reconciliation may churn `aswa`.
- **Tag mapping.** Each AC mapped to exactly one item; items 1, 7, 8 are `[unmapped]` (groundwork / manifest-version / verification). Tag validation passed before write. `[FB: ...]` prompting skipped — `docs/v2-verification.md` does not exist in the v4 working dir.

### Difficult questions (cont.)
- **`currentSprint` reset-map off-by-one.** Discovered while resolving the sprint number: the spec's reset map says `currentSprint → 1`, but with `/sdd:plan`'s `+1` rule that yields `2` as a new cycle's first sprint. Should reset to `0`/omit. Logged as an open concern for Sprint 1 Item 5 (and spec/`living-documents.md` reconciliation). Classic dogfood catch — the v4 reset map has the same bug the manual v4 seed exhibited.

### Open concern carried
- Sweep allow-list (spec) vs deny-list (PRD) divergence remains open, targeted at `/sdd:refine` before `/sdd:build`. `/sdd:plan` cannot resolve it. If the user routes the AC-ID fix through `/sdd:refine`, that same run can reconcile the divergence.

## /sdd:build — Sprint Close (wrap-up)

Autonomous build of all 8 items. Each item dispatched to a subagent with surgically-loaded spec/PRD sections; orchestrator verified each result against its ACs before checkoff. Items 2–6 authored `plugins/sdd/skills/archive/SKILL.md` section-by-section (scaffold/pre-flight → plan gate → execute → reset → git+handoff), dispatched sequentially to avoid same-file conflicts.

### Decisions
- **No-commit-during-build honored throughout.** Every subagent was explicitly instructed not to commit/branch (AGENTS.md "Clean state = last user commit") — overriding the generic build-skill "commit when done." Commit/push/PR happened only at sprint close on explicit user request.
- **Off-by-one reconciled (Items 1 + 5).** `currentSprint` reset changed from `1` to `0 (or omit)` in **both** `docs/spec.md` and `references/living-documents.md`, then applied in the SKILL's reset section. Resolves the open concern; next cycle's first `/sdd:plan` correctly yields sprint 1.
- **Built `aswa` to the spec's allow-list model, not the PRD's deny-list wording.** Item 4 sweep moves the explicit allow-list; the PRD `aswa` deny-list phrasing remains flagged for `/sdd:refine` (open concern unchanged).
- **Repo detection placed in pre-flight (Item 6).** So the Plan Gate (Item 3) can conditionally show branch/PR lines; the Git Integration section reuses the same detection result.
- **Handoff section added to the SKILL in Item 6.** No item explicitly owned the SKILL's completion-handoff emission; folded it into the terminal git item as the natural close.
- **Version bump = 3.1.0 (minor), user decision.** Not the major `4.0.0` the v1/v2/v3 pattern implied — maintainer chose a minor bump for an additive new command.

### Pushback
- (none from the user during build; the orchestrator pushed back on one ambiguity — see surprises.)

### Surprises
- **`marketplace.json` has never carried a `version` field.** Item 7's AC ("marketplace.json version matches plugin.json") and CLAUDE.md ("versions move together") both assume a marketplace version field that has not existed across v1–v3 (version is single-sourced in `plugin.json`, referenced via `source`). Resolved by keeping the established single-source model; the AC/CLAUDE.md wording is a new open concern for `/sdd:refine`. Flagged to the user, who set the version explicitly.
- **`plugin.json` has no `commands` array.** Commands are auto-discovered from `skills/<name>/SKILL.md`; "registering" `archive` required no manifest list edit — just the SKILL.md existing.
- **Verification was a hand-executed disposable walkthrough.** Since `/sdd:archive` cannot be self-invoked (`disable-model-invocation: true`) and the real dogfood is deferred to cycle close, all 20 ACs (`rcsa`–`gitc`) were exercised by manually running the documented procedure on throwaway temp-dir fixtures (incl. a disposable git repo for the git ACs). Observed results recorded; real dogfood (live `/sdd:archive` on this project) remains the eventual cycle-close proof.

### Notable (W8)
- Nothing additional raised at the "anything notable?" prompt; user moved to ship.

### Tech debt
- (none)

### Discards
- (none)

[close-sprint-manifest] timestamp: 2026-06-17T23:13:03Z
- PRD ACs checked: rcsa, rcsb, rcsc, aswa, aswb, aswc, aswd, aswe, ldpa, ldpb, ldpc, aixa, aixb, aixc, drsa, drsb, drsc, gita, gitb, gitc
- Story splits: (none)
- Iteration candidate dispositions: (none)
- Tech-debt entries: 0 (informational)
- v2-verification rows updated: (none)
[/close-sprint-manifest]
