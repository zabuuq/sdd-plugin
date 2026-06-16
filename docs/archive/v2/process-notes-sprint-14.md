# Process Notes — Sprint 14

## Sprint planning (2026-05-14)

### Context

Sprint 13 closed the entire `/sdd:prd` Polish epic (9 ACs `aacd`–`aacl`) — pre-write AC quality check, phase-split flag at 10+ epics, and the Unvetted section with marker comment. All Sprint-13 edits landed in `plugins/sdd/skills/prd/SKILL.md`. PRD checkoff propagated immediately on close. No `[FB: ...]` tags on Sprint-13 items; no `docs/v2-verification.md` rows touched.

Next-biggest visible gap per Sprint 13's projection: Process Notes Growth remainder (`aagf`–`aagn`, 9 ACs). Mechanically uniform across 4-5 phase SKILL.md files plus targeted edits to `retro/SKILL.md` and `sdd-guide/SKILL.md`. Pre-vetted; spec material already in place.

### PRD scan (post-Sprint-13)

- **Incomplete:** 64 ACs across 6 epics:
  - `/sdd:polish` re-open (3 ACs) — `aaev`–`aaex`
  - `/sdd:retro` at project close (6 ACs) — `aaey`, `aaez`, `aafb`–`aafe`
  - Automatic Backlog Generation (12 ACs) — `aafs`–`aagd`
  - Process Notes Growth remainder (9 ACs) — `aagf`–`aagn`
  - v1→v2 Migration (21 ACs) — `aago`–`aahi`
  - v2 Release Verification remaining (13 ACs) — `aahj`, `aahl`–`aaht`, `aahu`, `aahw`, `aahx`
- **Unvetted:** 0 (section present, empty). No `/sdd:refine` recommendation, no scope-creep flag.

### Open concerns scan

One open concern remains: "Soft acceptance criteria for MIT distribution" — target `/sdd:refine`, not sprint-relevant. No new concerns surfaced from Sprint 13's build phase.

### Proposed grouping

Agent proposed **Process Notes Growth remainder** (`aagf`–`aagn`, 9 ACs) as Sprint 14, mirroring Sprint 13's projection. Rationale captured:
- Three clean sub-stories with disjoint file sets: (1) per-phase notes wire-up across 4-5 phase SKILL.md files; (2) `/sdd:retro` selective reading in `retro/SKILL.md`; (3) append-only rule in `sdd-guide/SKILL.md`. Zero file overlap — parallel would be structurally safe, but sequential is consistent with Sprint 5-13 pattern.
- All ACs pre-vetted. Spec coverage in place (`spec.md > Architecture Overview > Data Flow` for per-phase architecture; `spec.md > /sdd:retro` for selective reading + silent missing-file behavior).
- Mechanically uniform across phase commands — Item 1 is a wire-up pattern repeated four times.
- Tangible outcome: every planning phase has a dedicated process-notes file the phase's command owns end-to-end; `/sdd:retro` reads selectively rather than concatenating; append-only invariant is explicit in `sdd-guide` so the convention can't drift.
- Listed as the top natural next candidate in Sprint 13's notes.

Why other candidates ranked lower:
- `/sdd:polish` re-open (3 ACs) — small enough to ride alongside another sprint; doesn't stand well alone.
- Auto Backlog Generation (12 ACs) — cross-cutting across 5 planning commands; bigger lift than Process Notes Growth.
- `/sdd:retro` at project close (6 ACs) — terminal-command work; better near v2 GA.
- v1→v2 Migration (21 ACs) — biggest, single-file, but lower priority until v2 nears GA.
- v2 Release Verification remaining (~13 ACs) — seeding / persistence / manual-checkoff conventions; can ride alongside other work.

### User response

User directive in effect: work without stopping for clarifying questions. Agent presented the proposal with full rationale and proceeded to sprint-file generation. User did not redirect.

### Build mode

Agent recommended **autonomous**, locked for the sprint, mirroring Sprints 5-13. Rationale: pre-vetted ACs, fully-specified spec material, three independent sub-stories with disjoint file sets. Mode locked as autonomous.

### Deepening rounds

Agent recommended against another round with reasoning + topic preview: pre-vetted ACs, canonical reference material already in place (`docs/spec.md > Architecture Overview > Data Flow` and `docs/spec.md > /sdd:retro`), uniform per-phase pattern. Topics a next round would have covered (deferred to `/sdd:build` implementation context):
- How aggressive to be with stray `process-notes.md` references in surrounding prose (Item 1) — mechanical change only; leave substantive prose cleanup for a later pass.
- Whether the append-only rule lands in sdd-guide alone or also in a new reference file (Item 3) — sdd-guide alone is cleaner; a new reference file would be overkill for a single rule.
- Whether `/sdd:build` already writes to `process-notes-sprint-N.md` by convention (it does, since Sprint 11) or requires explicit instruction edits — verify at build time.
- Whether `plan/SKILL.md` exists yet as a separate file or whether the v2 rename has not yet materialized — verify at build time; if not yet authored, edit whichever SKILL.md currently performs sprint-planning duties.

User did not redirect. Sprint generation proceeded directly.

Context tier recommendation: `/clear` after sprint generation, then `/sdd:build`. The current session carries the full Sprint 13 build (three subagents, 1 file edited across three passes, plus sprint wrap-up) plus the Sprint 14 planning interview. Sprint 14 build benefits from a fresh window — `/sdd:build` re-entry loads `CLAUDE.md` and the new sprint file cleanly.

### Sprint file generation

Sprint file written at `docs/sprint-14.md`. Three items, sequential, autonomous mode.

Final tag set:
- Item 1: `[PRD: aagf, aagg, aagh, aagi, aagj]`
- Item 2: `[PRD: aagk, aagl]`
- Item 3: `[PRD: aagm, aagn]`

All items carry `[PRD: ...]` — sprint validation per `aadw` passes. No `[FB: ...]` tags — Process Notes Growth is PRD-walkthrough-derived, not v1-feedback-derived; no v2-verification rows correspond to `aagf`–`aagn`.

### Notable decisions

- **Sequential despite disjoint file sets.** The three items have zero file overlap (Item 1: scope/prd/spec/plan/build SKILL.md files; Item 2: retro/SKILL.md; Item 3: sdd-guide/SKILL.md). Parallel would be structurally safe and slightly faster. Sequential chosen for consistency with the Sprint 5-13 pattern and easier debugging if anything goes sideways. Worth re-evaluating in future sprints with similar shapes — parallel is a real option here.
- **Item 1 spans 4-5 SKILL.md files inside a single item.** Edits are mechanical (rename write target), so a single subagent should handle the whole item in one pass. AC verification is per-command, not per-edit, so the item's success criteria are clearly bounded.
- **No FB mapping.** Process Notes Growth is PRD-walkthrough-derived. None of `aagf`–`aagn` correspond to a row in `docs/v2-verification.md`. `/sdd:build` wrap-up writes no v2-verification row updates for this sprint.
- **3-item granularity.** The three sub-stories (per-phase writes, selective retro reading, append-only rule) align cleanly with the three PRD user stories in the Process Notes Growth epic. No reason to bundle or split further.
- **Existing prd/SKILL.md is already mid-flight.** Sprint 13 just edited `prd/SKILL.md` to add three new sections in `## Document Generation`. Item 1 of this sprint edits the same file (the `## Wrap-Up > ### Append to Process Notes` subsection — changes `process-notes.md` → `process-notes-prd.md`). Pre-existing structure in `prd/SKILL.md` is well-known to the agent.

### Build mode caveat

Same as every prior sprint: the cached marketplace plugin is still v1, so Sprint 14's edits to 6 SKILL.md files ship to disk but are not live as agent behavior until the maintainer reinstalls. First post-Sprint-14 `/sdd:retro` will be the first real test of the selective-reading + silent-missing-file behaviors; first post-Sprint-14 `/sdd:scope`/`/sdd:prd`/`/sdd:spec`/`/sdd:plan` runs will be the first real tests of per-phase writes.

### v2-verification expected updates at Sprint 14 close

- None. No `[FB: ...]` tags on Sprint 14 items.

### Post-Sprint-14 projection

- AC count remaining: 55 across 5 epics (64 − 9).
- Roughly 4-5 more sprints to v2 GA depending on bundling.
- `/sdd:polish` re-open (3 ACs) and Auto Backlog Generation (12 ACs) are the next two natural candidates — re-open is small and could ride with another sprint; Auto Backlog is bigger and cross-cutting.
- PRD checkoff watch carried from Sprints 11-13 — close-sprint must propagate AC checkoffs to `docs/prd.md` immediately.

## Sprint build (2026-05-14) — autonomous

Build mode: autonomous. Three items, sequential. PRD checkoff propagated to `docs/prd.md` per-item, immediately after each item's checklist update.

### Item 1: Per-phase process-notes file writes `[PRD: aagf, aagg, aagh, aagi, aagj]`

Dispatched as a single subagent (item spans multiple files, edits are mechanical). Pre-survey identified three SKILL.md files with stale `process-notes.md` write targets needing per-phase replacement; `/sdd:discovery`, `/sdd:plan`, `/sdd:build` already correct from Sprint 11 onward.

Files edited:
- `plugins/sdd/skills/scope/SKILL.md` — line 254 wrap-up write target changed from `process-notes.md` to `process-notes-scope.md`; added "create on first append" line at 261 mirroring discovery convention.
- `plugins/sdd/skills/prd/SKILL.md` — line 154 wrap-up write target changed from `process-notes.md` to `process-notes-prd.md`; added "create on first append" line at 161. Pre-existing line 41 (smallProject re-eval flip) already referenced `process-notes-prd.md` from earlier work.
- `plugins/sdd/skills/spec/SKILL.md` — line 231 wrap-up write target changed from `process-notes.md` to `process-notes-spec.md`; added "create on first append" line at 239. Pre-existing line 47 (smallProject re-eval flip) already referenced `process-notes-spec.md`.

Already-correct (verified, not re-edited): `discovery/SKILL.md` writes to `process-notes-discovery.md` (lines 134, 153, 157, shipped via `aage`); `plan/SKILL.md` writes to `process-notes-sprint-N.md` (line 295); `build/SKILL.md` writes to `process-notes-sprint-N.md` (lines 180, 426).

Post-edit grep cross-check: no planning command (discovery/scope/prd/spec/plan) shares a write target with another planning command; `/sdd:plan` and `/sdd:build` share `process-notes-sprint-N.md` by design (allowed per `aagi`); the only remaining bare `process-notes.md` references in `plugins/sdd/skills/**` were the consumer-side reads in `retro/SKILL.md` (lines 18, 77) — flagged by the subagent as Item 2 territory, deliberately not touched.

Notable observations from the subagent:
- The "create on first append" sentence pattern is now duplicated across `discovery`, `scope`, `prd`, `spec` SKILL.md files. Not a duplication problem today (~one sentence each), but if the convention later moves into a shared reference file, these four become cleanup candidates. No new open concern needed — call-out for future awareness only.
- `sdd-guide/SKILL.md` lines 70-71 already documented the per-phase / per-sprint split correctly from prior work; no edits needed there. Suggests the convention was settled at the sdd-guide level before the individual SKILL.md write targets caught up — Sprint 14 Item 1 brings the runtime files into alignment with the already-stated convention.

PRD checkoff propagated: `aagf`–`aagj` checked in `docs/prd.md` immediately after sprint-14.md checklist update.

### Item 2: `/sdd:retro` selective process-notes reading `[PRD: aagk, aagl]`

Done directly by the orchestrator — single-file edit (`retro/SKILL.md`), context already loaded, dispatching a subagent would have been pure overhead. Trade-off captured for future sprints: single-file edits inside the orchestrator's loaded context are faster than dispatch; multi-file or context-heavy items still benefit from dispatch.

Edits to `retro/SKILL.md`:
- **Loading section.** Removed `process-notes*.md` from the startup-loading set (was item 4 in the loading list). Re-numbered subsequent items. Added explicit skip note under `**Skips:**`: "`process-notes-*.md` files at startup. Process-notes files are read **per-phase, during the interview** (see Step 2), not concatenated at startup."
- **Prerequisite at line 31.** Replaced the bare `process-notes*.md` glob with an explicit enumeration of per-phase + per-sprint file names. Kept the sanity check that *some* process-notes file exists (so retro isn't run on a project that ran nothing), but reworded the error message to "Run at least one planning phase or sprint cycle..." and clarified that the file is still read selectively in Step 2.
- **Step 2 (lines 73-87) — full rewrite.** Replaced "Review All Process Notes" (which described concatenated reading) with "Per-Phase Process Notes Reading (during interview)." New Step 2: process-notes files are read one phase at a time during the interview blocks below, not at startup. Per-phase mapping table names each file. Missing-file handling explicitly stated: "proceed silently. Do not emit a warning. Do not ask the user about the absence. Treat it as 'this phase produced no notes' and move on."
- **Step 3 Topics 1 and 2.** Added per-topic read instructions: Topic 1 (Planning quality) reads `process-notes-discovery.md`, `-scope.md`, `-prd.md`, `-spec.md` as separate reads when that topic comes up; Topic 2 (Sprint cadence) reads each `process-notes-sprint-N.md` as a separate read in order. Both topic blocks reference the per-phase missing-file rule from Step 2.

Verification grep: no stale `process-notes.md` references remain in `retro/SKILL.md` after the edits. All references are either the glob `process-notes-*.md` (in the skip-at-startup note) or specific per-phase file names.

PRD checkoff propagated: `aagk`, `aagl` checked in `docs/prd.md` immediately after sprint-14.md checklist update.

### Item 3: Append-only process-notes discipline `[PRD: aagm, aagn]`

Done directly by the orchestrator — minimal addition to existing `sdd-guide/SKILL.md > ## Process Notes` section, no need to dispatch.

Edit to `sdd-guide/SKILL.md`: added two short paragraphs after the existing "Write notes in real time..." line (line 79), before the `## State Tracking` section.

- **Append-only within a phase** (lands `aagm`). Stated as: each phase's command writes real-time entries in the order they happen and does not modify or remove its own entries after writing. End-of-phase state is the accumulated real-time entries — nothing pruned, edited, or summarized in place by the writing command. Adds the rationale that the history stays intact for `/sdd:retro` and later review.
- **Mid-phase summarization** (lands `aagn`). Stated as: mid-phase summarization for multi-session continuity belongs to `/sdd:pause`, which writes a structured resume file (`docs/<command>-resume.md`) per `references/pause-resume.md`. Phase commands do not produce mid-phase summaries by editing their own process-notes files. Adds the operational hint: when scrollback fades, the user runs `/sdd:pause`, then `/clear`, then `/sdd:unpause`.

Cross-references `references/pause-resume.md` by name without restating its schema, distillation rules, or quality bar — verification AC ("no duplication") satisfied.

PRD checkoff propagated: `aagm`, `aagn` checked in `docs/prd.md` immediately after sprint-14.md checklist update.

### Notable decisions and observations across the sprint

- **Dispatch granularity.** Item 1 (5-file edit) was dispatched to a subagent and benefited from the dispatch. Items 2 and 3 (single-file edits) were done directly by the orchestrator — context was already loaded and dispatch would have added overhead without parallelism. Rule of thumb for future sprints: dispatch when the item touches multiple files or needs context the orchestrator doesn't already hold; do it inline when the file is open and the edit is localized.
- **Convention-vs.-runtime gap.** Items 1 and 2 closed a longstanding gap between sdd-guide's documented per-phase convention (lines 70-71, settled at the sdd-guide level earlier) and the runtime SKILL.md files that still wrote to `process-notes.md`. The lesson: when sdd-guide changes a convention, audit the runtime files immediately. Sprint 14's Items 1-2 effectively performed that audit.
- **No FB mapping** — none of `aagf`–`aagn` map to v2-verification rows. No `[FB: ...]` tags on Sprint 14 items. Sprint-14 close writes no rows to `docs/v2-verification.md`.
- **No `[iteration-candidate]` markers written** during build. All three items closed cleanly; no follow-up candidates surfaced.
- **No new open concerns** surfaced from the build. The single open concern ("Soft acceptance criteria for MIT distribution") remains as-is — not addressed by this sprint, not made worse.
- **CLAUDE.md / AGENTS.md** — no updates needed. AGENTS.md already documents the per-phase process-notes split as an architecture decision; Sprint 14 brings the runtime files into alignment with the documented decision rather than introducing new conventions.

### Sprint-close summary

- 3 of 3 items complete.
- 9 of 9 PRD ACs checked: `aagf`, `aagg`, `aagh`, `aagi`, `aagj`, `aagk`, `aagl`, `aagm`, `aagn`.
- Process Notes Growth epic closes entirely with this sprint (`aage` shipped earlier via `/sdd:discovery`).
- Files modified across the sprint: `plugins/sdd/skills/scope/SKILL.md`, `plugins/sdd/skills/prd/SKILL.md`, `plugins/sdd/skills/spec/SKILL.md`, `plugins/sdd/skills/retro/SKILL.md`, `plugins/sdd/skills/sdd-guide/SKILL.md`, `docs/sprint-14.md`, `docs/prd.md`.
- `docs/v2-verification.md` unchanged (no FB mapping).
- `docs/open-concerns.md` unchanged.
- Cached marketplace plugin is still v1, so these edits ship to disk but are not live as agent behavior until the maintainer reinstalls. First real test of the new behaviors comes on the next post-reinstall `/sdd:scope`/`/sdd:prd`/`/sdd:spec`/`/sdd:retro` runs.

### Post-Sprint-14 state

- 55 ACs remaining across 5 epics: `/sdd:polish` re-open (3), `/sdd:retro` at project close (6), Auto Backlog Generation (12), v1→v2 Migration (21), v2 Release Verification remaining (13).
- Natural next-sprint candidates per Sprint 13's projection still apply: `/sdd:polish` re-open (small, could ride alongside another sprint) or Auto Backlog Generation (bigger, cross-cutting across 5 planning commands).
