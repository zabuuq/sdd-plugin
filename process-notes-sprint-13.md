# Process Notes — Sprint 13

## Sprint planning (2026-05-14)

### Context

Sprint 12 closed the entire Right-Sizing epic (8 ACs `aabr`–`aaby`) — `/sdd:scope` writes the authoritative `smallProject` value at end of Phase 1, all seven interview SKILL.md files read it on startup with mandated re-eval in `/sdd:prd`/`/sdd:spec`/`/sdd:plan` and permissive re-eval in `/sdd:refine`/`/sdd:polish`, skippable Phase 1 beats are wired into `/sdd:scope`/`/sdd:prd`/`/sdd:spec`/`/sdd:plan`, and `references/deepening-rounds.md` carries the sub-5-question-round and close-direction defaults. `docs/v2-verification.md` FB-011 row Shipped column populated. v2 chain is now fully right-sized on disk (live behavior pending plugin reinstall, same caveat as every prior sprint).

Next-biggest visible gap: `/sdd:prd` itself — three quality-improvement stories sat in the PRD epic waiting for a sprint: pre-write AC quality check, phase-split flag at 10+ epics, and the Unvetted section with marker comment.

### PRD scan (post-Sprint-12)

- **Incomplete:** 73 ACs across 7 epics (counted via grep on `docs/prd.md`):
  - `/sdd:prd` Polish (9 ACs) — `aacd`–`aacl`
  - `/sdd:polish` re-open (3 ACs) — `aaev`–`aaex`
  - `/sdd:retro` at project close (6 ACs) — `aaey`, `aaez`, `aafb`–`aafe`
  - Automatic Backlog Generation (12 ACs) — `aafs`–`aagd`
  - Process Notes Growth remainder (9 ACs) — `aagf`–`aagn`
  - v1→v2 Migration (21 ACs) — `aago`–`aahi`
  - v2 Release Verification remaining (13 ACs) — `aahj`, `aahl`–`aaht`, `aahu`, `aahw`, `aahx`
- **Unvetted:** 0 (section present, empty). No `/sdd:refine` recommendation, no scope-creep flag.

### Open concerns scan

One open concern remains: "Soft acceptance criteria for MIT distribution" — target `/sdd:refine`, not sprint-relevant. No new concerns surfaced.

### Proposed grouping

Agent proposed `/sdd:prd` Polish (`aacd`–`aacl`, 9 ACs) as Sprint 13. Rationale captured:
- Three clean sub-stories, 3 ACs each: pre-write AC quality check, phase-split flag at 10+, Unvetted section + marker comment. Natural three-item shape.
- Single-file work in `plugins/sdd/skills/prd/SKILL.md`. Cleanest possible scope.
- All three sub-stories fully resolved in `docs/spec.md > /sdd:prd` and `references/living-documents.md > ## PRD Health Monitoring`. No design calls expected.
- Sprint 12 just finished cross-cutting work in 7 SKILL.md files; alternating to a single-file sprint amortizes contention on shared files and gives a focused, demonstrable milestone.
- Listed as the top natural next candidate in Sprint 12's notes.
- Tangible outcome: `/sdd:prd` produces measurably higher-quality PRDs end-to-end — every AC is testable, sprawling projects get a phase-split nudge, `/sdd:refine` has a defined Unvetted landing zone for items surfaced during sprints.

Why other candidates ranked lower:
- Process Notes Growth remainder (9 ACs) — mechanically uniform but cross-cutting across 4 SKILL.md files Sprint 12 just edited; deferring one sprint reduces churn risk.
- `/sdd:polish` re-open (3 ACs) — small enough to ride alongside another sprint; doesn't stand well alone.
- Auto Backlog Generation (12 ACs) — cross-cutting across 5 planning commands; bigger lift.
- v1→v2 Migration (21 ACs) — biggest, single-file, but lower priority until v2 is closer to GA.
- `/sdd:retro` at project close (6 ACs) — terminal-command work; better near v2 GA.
- v2 Release Verification remaining (~13 ACs) — seeding/persistence conventions; can ride alongside other work.

### User response

"works for me" — accepted the proposal without modification.

### Build mode

Agent recommended autonomous, locked for the sprint, mirroring Sprints 5-12. Rationale: pre-vetted ACs, fully-specified spec material, single-file scope, three independent sub-stories. User did not redirect. Mode locked as autonomous.

### Deepening rounds

Agent recommended against another round with reasoning + topic preview: pre-vetted ACs, canonical reference material already in place (`docs/spec.md > /sdd:prd` and `references/living-documents.md > ## PRD Health Monitoring`), single-file scope. Topics a next round would have covered (deferred to `/sdd:build` implementation context):
- Item granularity — kept at 3 items mirroring the three sub-stories. Alternative was one bundled item.
- AC-quality-check rewrite guardrails (against over-aggressive rewrites) — best discovered at build time.
- Phase-split-recommendation wording — `aach` already pins the explanation; close paraphrase fine at write time.
- Unvetted marker exact wording — `aacl` pins purpose-explained; wording flexible at write time.

User did not redirect. Sprint generation proceeded directly.

Context tier recommendation: `/clear` after sprint generation, then `/sdd:build`. The current session carries the Sprint 12 build (4 subagents, 8 file edits) plus the Sprint 13 planning interview. Sprint 13 build benefits from a fresh window — `/sdd:build` re-entry loads `CLAUDE.md` and the new sprint file cleanly.

### Sprint file generation

Sprint file written at `docs/sprint-13.md`. Three items, sequential, autonomous mode.

Final tag set:
- Item 1: `[PRD: aacd, aace, aacf]`
- Item 2: `[PRD: aacg, aach, aaci]`
- Item 3: `[PRD: aacj, aack, aacl]`

All items carry `[PRD: ...]` — sprint validation per `aadw` passes. No `[FB: ...]` tags — this epic is PRD-walkthrough-derived, not v1-feedback-derived; no v2-verification rows correspond to `/sdd:prd` Polish ACs.

### Notable decisions

- **Single-file sequencing.** All three items edit `plugins/sdd/skills/prd/SKILL.md`. Sequential subagents prevent edit collisions on the same file. Parallel would have been faster but riskier.
- **Item 3 vs. existing `docs/prd.md`.** This project's existing PRD already has an `## Unvetted` section with a marker comment at line 363. Item 3 affects future `/sdd:prd` runs — it does NOT require an edit to this project's PRD. Same for Items 1 and 2: they're command-behavior changes, not project-document edits. Sprint 13 modifies one file only: `plugins/sdd/skills/prd/SKILL.md`.
- **No FB mapping.** The `/sdd:prd` Polish epic is derived from the PRD walkthrough, not from the v1 feedback pile. None of `aacd`–`aacl` correspond to a row in `docs/v2-verification.md`. `/sdd:build` wrap-up writes no v2-verification row updates for this sprint.
- **3-item granularity (vs. Sprint 12's 4).** The three sub-stories (quality check, phase-split flag, Unvetted section) are independent of each other and align cleanly with the three PRD user stories. No reason to bundle or split further.
- **Existing prd/SKILL.md scope.** Did not pre-read `prd/SKILL.md` during planning; sprint-file generation per cached v1 `/sdd:sprint` does not require it. Subagents will read in full when they receive their items.

### Build mode caveat

Same as every prior sprint: the cached marketplace plugin is still v1, so Sprint 13's edits to `prd/SKILL.md` ship to disk but are not live as agent behavior until the maintainer reinstalls. First post-Sprint-13 `/sdd:prd` invocation will be the first real test of the new quality-check, phase-split, and Unvetted-section behaviors.

### v2-verification expected updates at Sprint 13 close

- None. No `[FB: ...]` tags on Sprint 13 items.

### Post-Sprint-13 projection

- AC count remaining: 64 across 6 epics (73 − 9).
- Roughly 4-6 more sprints to v2 GA depending on bundling.
- Process Notes Growth remainder (9 ACs) is the natural Sprint 14 candidate — wires `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:retro` to per-phase notes files. Mechanically uniform across 4-5 SKILL.md files.
- PRD checkoff watch carried from Sprints 11-12 — close-sprint must propagate AC checkoffs to `docs/prd.md` immediately.

## Build phase (2026-05-14)

### Mode

Autonomous, sequential. Three items in one file (`plugins/sdd/skills/prd/SKILL.md`); sequential subagents per item to prevent edit collisions.

### Open concerns at build start

One open concern remains: "Soft acceptance criteria for MIT distribution" — target `/sdd:refine`, not relevant to Sprint 13's PRD-polish work. Surfaced to context, no action.

### Item 1 — Pre-write AC quality check (`aacd`, `aace`, `aacf`)

- Subagent extended the thin "Acceptance criteria quality check" stanza in `## Document Generation` into a dedicated `### Pre-Write AC Quality Check` subsection.
- Names timing explicitly: exactly once per `/sdd:prd` run, immediately before `docs/prd.md` is written, after template population, not during AC drafting / deepening rounds / re-reads of existing PRD.
- Scopes the check to in-memory ACs about to be written; calls out that re-validation of on-disk ACs is `/sdd:refine`'s job.
- Each of the three checks (specific / observable / verifiable) gets a brief characterization plus an example for "specific" ("Page load completes in under 2 seconds" vs. "is fast").
- Rewrite-on-failure is in-place before file write — not after, not in a separate run.
- `## Important Reminders` line for AC testability left intact as a one-line reminder pointing at the mechanism.
- No issues, no deviations.

### Item 2 — Phase-split flag (`aacg`, `aach`, `aaci`)

- Subagent removed the loose `### Epic Count Monitoring` stanza from Phase 2 and replaced it with a one-line pointer to the new pre-generation location (keeps Phase 2 coherent — epic count is still monitored as requirements take shape, but the recommendation fires at the pre-generation gate, not in Phase 2).
- Added `### Phase-Split Recommendation` as the first ordered step in `## Document Generation`, placed before `### Pre-Write AC Quality Check`. Natural runtime order: phase-split flag → AC quality check → file write.
- Counts the in-memory epic set, sources the 10+ threshold from `references/living-documents.md > ## PRD Health Monitoring` (cross-reference, not restated).
- Recommendation message body explains phase-splitting as "breaking the project into smaller pieces, each going through the full SDD workflow independently (`/sdd:scope` through `/sdd:retro`)" — `aach` literal-or-close-paraphrase satisfied.
- Names the trade-off concretely: smaller cycles vs. longer coordination cost. Non-coercive.
- On user election to keep one phase, proceeds to AC quality check and writes the PRD as a single PRD. No re-prompt loop, no hard stop.
- **Design call flagged by subagent:** when the user *elects to split*, the new stanza stops generation on this run and points the user at re-running `/sdd:scope` for the first phase. The ACs only constrain the "keep one phase" path (non-blocking); the "elect to split" path was the subagent's call. Decision accepted — phase-splitting fundamentally means each phase goes through the full SDD workflow independently, so a partial PRD on the split path would be incoherent.

### Item 3 — Unvetted section + marker comment (`aacj`, `aack`, `aacl`)

- Subagent extended the existing `## Unvetted` bullet inside `**Template usage:**` from a one-line "leave empty" directive to a structured instruction: write the section header with no list items and no inline body content beyond a single HTML marker comment placed inside the section body.
- Marker-comment intent pinned (purpose, kinds of items, processor — `/sdd:refine`); exact wording left flexible at write time.
- Marker described as self-contained and stable across `/sdd:prd` re-runs and `/sdd:refine` runs.
- Matching `## Important Reminders` bullet updated so it no longer says "be empty" without naming the marker requirement.
- No issues, no deviations.

### CLAUDE.md / AGENTS.md updates

None. The three new mechanisms (pre-generation phase-split flag, pre-write AC quality check, Unvetted marker) are `/sdd:prd`-specific runtime behavior, not project-wide conventions. No new patterns to surface to the living documents.

### Open concerns updates

None added. No concerns resolved (the one open concern targets `/sdd:refine`, unaffected by this sprint's PRD-polish work).

### PRD checkoff propagation

Per Sprint 11/12 watch: nine ACs (`aacd`–`aacl`) checked off in `docs/prd.md` immediately on sprint close. Done before this notes section was written. The `/sdd:prd` Polish epic is now fully closed in the PRD.

### v2-verification.md updates

None. No `[FB: ...]` tags on any Sprint 13 item — the `/sdd:prd` Polish epic is PRD-walkthrough-derived, not v1-feedback-derived. Confirmed against the sprint plan; no rows touched.

### Sprint close summary

3/3 items complete. 9/9 ACs checked off in both the sprint file and `docs/prd.md`. Entire `/sdd:prd` Polish epic (`aacd`–`aacl`) closed in one sprint. All edits in one file: `plugins/sdd/skills/prd/SKILL.md`. No new CLAUDE.md / AGENTS.md updates. No new open concerns. No v2-verification rows touched.

### Caveat for next-PRD run

Cached marketplace plugin is still v1. Sprint 13's edits to `prd/SKILL.md` ship to disk but are not live as agent behavior until the maintainer reinstalls. First post-Sprint-13 `/sdd:prd` invocation against the reinstalled plugin will be the first real test of the three new mechanisms.

### Next sprint candidate

Process Notes Growth remainder (`aagf`–`aagn`, 9 ACs) is the natural Sprint 14 candidate per the planning projection — wires `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:retro` to per-phase notes files. Mechanically uniform across 4-5 SKILL.md files. The recommendation routes to `/sdd:plan` for Sprint 14 planning.
