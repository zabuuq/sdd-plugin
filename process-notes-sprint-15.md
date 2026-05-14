# Process Notes — Sprint 15

## Sprint planning (2026-05-14)

### Context

Sprint 14 closed the entire Process Notes Growth epic (9 ACs `aagf`–`aagn`) — per-phase process-notes file writes across scope/prd/spec SKILL.md (plan/build already correct since Sprint 11), `/sdd:retro` selective per-phase reading + silent-on-missing-file rule, and the append-only-within-phase rule in `sdd-guide/SKILL.md > Process Notes`. All Sprint-14 edits landed in 5 SKILL.md files. PRD checkoff propagated immediately on close. No `[FB: ...]` tags on Sprint-14 items; no `docs/v2-verification.md` rows touched. The append-only rule shipped in Sprint 14 governs this very file as of writing.

Next-biggest visible gaps per Sprint 14's projection: `/sdd:polish` re-open (3 ACs, small) and `/sdd:retro at Project Close` (6 ACs with `aafa` already shipped). Both lifecycle-adjacent; both single-file edits with full spec coverage. Pairing them keeps the v2 cycle progressing on lifecycle epics before tackling the larger Auto Backlog Generation (12 ACs, cross-cutting) and v1→v2 Migration (21 ACs).

### PRD scan (post-Sprint-14)

- **Incomplete:** 55 ACs across 5 epics:
  - `/sdd:polish` re-open (3 ACs) — `aaev`–`aaex`
  - `/sdd:retro` at project close (6 ACs) — `aaey`, `aaez`, `aafb`–`aafe` (`aafa` already done via `/sdd:build` wrap-up)
  - Automatic Backlog Generation (12 ACs) — `aafs`–`aagd`
  - v1→v2 Migration (21 ACs) — `aago`–`aahi`
  - v2 Release Verification remaining (13 ACs) — `aahj`, `aahl`–`aaht`, `aahu`, `aahw`, `aahx`
- **Unvetted:** 0 (section present, empty). No `/sdd:refine` recommendation, no scope-creep flag.

### Open concerns scan

One open concern remains: "Soft acceptance criteria for MIT distribution" — target `/sdd:refine`, not sprint-relevant. No new concerns surfaced from Sprint 14's build phase.

### Proposed grouping

Agent proposed **Lifecycle close-out** (`aaev`–`aaex` + `aaey`–`aafe` minus `aafa`, 9 ACs total) as Sprint 15. Rationale captured:
- Two epics that conceptually pair as end-of-sprint and end-of-project ceremonies. Both close cleanly with their respective sub-stories landing.
- Three clean sub-stories with disjoint single-file edits: (1) `/sdd:polish` re-open in `polish/SKILL.md`; (2) `/sdd:plan` retro-recommendation gate in `plan/SKILL.md` + cross-command grep verification; (3) `/sdd:retro` cross-project pattern capture in `retro/SKILL.md`.
- All ACs pre-vetted. Full spec coverage in place: `spec.md > /sdd:polish` (re-open escape hatch), `spec.md > /sdd:retro` (recommendation timing + cross-project pattern capture), `spec.md > Close-Sprint Manifest` (mechanism for re-open, lines 603-639), `references/sprint-tags.md > [close-sprint-manifest]` / `> [sprint-reopened]` (parsers).
- Mechanically uniform across the three items — each is a localized addition to an existing SKILL.md file.
- Tangible outcome: `/sdd:polish` re-open works for prematurely-closed sprints; `/sdd:plan` recommends `/sdd:retro` only at genuine project close; `/sdd:retro` captures cross-project patterns to user profile on a per-pattern confirmation basis.
- Listed as the top natural next candidates in Sprint 14's notes ("/sdd:polish re-open and Auto Backlog Generation").

Why other candidates ranked lower:
- `/sdd:polish` re-open alone (3 ACs) — too small for a standalone sprint; the project's been running 9-AC sprints.
- Auto Backlog Generation (12 ACs) — cross-cutting across 5 planning commands; bigger lift; pushes lifecycle close-out further out.
- `/sdd:retro` at project close alone (6 ACs) — pairs more naturally with polish re-open than with Auto Backlog or Migration.
- v1→v2 Migration (21 ACs) — lower priority until v2 nears GA; migration narrative should be written against final-state v2.
- v2 Release Verification remaining (13 ACs) — four separate user stories that don't pair as cleanly; better bundled near GA.

### User response

User directive in effect: work without stopping for clarifying questions. Agent presented the proposal with full rationale and proceeded directly to sprint-file generation. User did not redirect.

### Build mode

Agent recommended **autonomous**, locked for the sprint, mirroring Sprints 5-14. Rationale: pre-vetted ACs, fully-specified spec material, three independent sub-stories with disjoint single-file edits. Mode locked as autonomous.

### Deepening rounds

Agent recommended against another round with reasoning + topic preview: pre-vetted ACs, canonical reference material already in place (`spec.md > /sdd:polish`, `spec.md > /sdd:retro`, `spec.md > Close-Sprint Manifest`, `references/sprint-tags.md`). Topics a next round would have covered (deferred to `/sdd:build` implementation context):
- Item 1: exact placement of the manifest-detection branch relative to the existing Prerequisites section in `polish/SKILL.md` — discover at build time; the natural spot is before the "all items complete" check, but the existing file structure may suggest a cleaner integration.
- Item 1: whether the manifest parser's regex is copied inline or referenced from `references/sprint-tags.md` — reference, do not duplicate.
- Item 2: whether the PRD-state-check logic should be lifted into a shared reference file or duplicate `build/SKILL.md`'s check — discover at build time. Bias toward duplicating a few lines rather than introducing a new reference for one shared check.
- Item 2: how aggressive to be if a stale blanket-recommendation reference surfaces in an unexpected SKILL.md — keep mechanical, replace with gated form, leave substantive prose cleanup for a later pass.
- Item 3: project-name source for the pattern entry — `docs/project-state.json` field if present, project-directory basename as fallback. Confirm at build time.

User did not redirect. Sprint generation proceeded directly.

Context tier recommendation: `/clear` after sprint generation, then `/sdd:build`. The current session carries the full Sprint 14 build (one subagent + two direct edits across three items, plus sprint wrap-up) and the Sprint 15 planning interview. Sprint 15 build benefits from a fresh window — `/sdd:build` re-entry loads `CLAUDE.md` and the new sprint file cleanly per the context-loading reference.

### Sprint file generation

Sprint file written at `docs/sprint-15.md`. Three items, sequential, autonomous mode.

Final tag set:
- Item 1: `[PRD: aaev, aaew, aaex]`
- Item 2: `[PRD: aaey, aaez]`
- Item 3: `[PRD: aafb, aafc, aafd, aafe]`

All items carry `[PRD: ...]` — sprint validation per `aadw` passes. No `[FB: ...]` tags — both epics are PRD-walkthrough-derived (Sprint Loop polish re-open story; `/sdd:retro at Project Close` stories), not v1-feedback-derived; no v2-verification rows correspond to `aaev`–`aafe`.

### Notable decisions

- **Sequential despite disjoint file sets.** The three items have zero file overlap (Item 1: polish/SKILL.md; Item 2: plan/SKILL.md + cross-SKILL.md verification; Item 3: retro/SKILL.md). Parallel would be structurally safe. Sequential chosen for consistency with Sprints 5-14 and easier debugging. Worth re-evaluating in future sprints with similar shapes — parallel is a real option here.
- **Item 2 spans an edit + a cross-file verification.** The verification grep for `aaey` may surface zero edits (current state already invariant-satisfying) or surface incidental SKILL.md changes if blanket recommendations exist. Both outcomes satisfy the AC; the verification is part of the item's scope, not a separate item.
- **`aafa` already shipped via `/sdd:build` wrap-up.** Sprint 15 picks up only the `/sdd:plan` side (`aaez`) and the cross-command invariant (`aaey`). The retro epic's full close depends on `aafa` having already landed — confirmed in `docs/prd.md`.
- **No FB mapping.** Both epics are PRD-walkthrough-derived. None of `aaev`–`aafe` correspond to a row in `docs/v2-verification.md`. `/sdd:build` wrap-up writes no v2-verification row updates for this sprint.
- **3-item granularity.** Three sub-stories align cleanly with three PRD user stories. No reason to bundle or split further.
- **Spec coverage is dense for Item 1.** `spec.md > Close-Sprint Manifest` provides full re-open behavior steps 1-9, edge-case handling for `/sdd:refine`-modified ACs since close, multi-cycle handling, and a list of what re-open does NOT reverse. The SKILL.md edit can largely be a wire-up to the spec section + the sprint-tags.md parsers, not a re-derivation of the mechanism.
- **Sprint-15 builds on Sprint 14's edits.** Sprint 14 just landed selective per-phase reading + silent-on-missing-file behaviors in `retro/SKILL.md`. Sprint 15's Item 3 lands a new final interview step in the same file — fresh in disk, no merge conflicts expected.

### Build mode caveat

Same as every prior sprint: the cached marketplace plugin is still v1, so Sprint 15's edits to 3 SKILL.md files ship to disk but are not live as agent behavior until the maintainer reinstalls. First real test of `/sdd:polish` re-open requires a closed sprint and a follow-up `/sdd:polish` invocation against it (could be exercised on Sprint 15 itself after close). First real test of `/sdd:plan`'s gated retro recommendation comes on the next post-reinstall plan run. First real test of `/sdd:retro` cross-project capture requires a project-close retro.

### v2-verification expected updates at Sprint 15 close

- None. No `[FB: ...]` tags on Sprint 15 items.

### Post-Sprint-15 projection

- AC count remaining: 46 across 3 epics (55 − 9).
- Three of the original five remaining-at-Sprint-14-end epics close with this sprint: `/sdd:polish` re-open and `/sdd:retro at Project Close` close in Sprint 15; Process Notes Growth closed in Sprint 14.
- Remaining epics: Automatic Backlog Generation (12 ACs), v1→v2 Migration (21 ACs), v2 Release Verification remaining (13 ACs).
- Natural next-sprint candidates after Sprint 15: Auto Backlog Generation (12 ACs, cross-cutting across 5 planning commands) is the next-biggest visible gap; v2 Release Verification has four user stories that could be split or bundled; Migration guide is largest single-file write and best left until v2 nears GA.
- PRD checkoff watch carried from Sprints 11-14 — close-sprint must propagate AC checkoffs to `docs/prd.md` immediately.

## Build summary (2026-05-14 — autonomous, sequential)

Three items executed sequentially via three single-purpose subagent dispatches. Build mode locked autonomous per the sprint file. Orchestrator handled context loading, sprint-file checkoffs, PRD checkoff propagation, and the close-sprint manifest write.

### Item 1: `/sdd:polish` re-open mechanism — closed (`aaev`, `aaew`, `aaex`)

Single-file edit to `plugins/sdd/skills/polish/SKILL.md`. The subagent added a new **Step 7: Detect closed-sprint state (re-open escape hatch)** between the existing Step 6 (Load CLAUDE.md) and `## Prerequisites`, plus a new top-level **`## Re-Open Flow`** section between `## Prerequisites` and `## State Updates (immediate)`. The existing `## Prerequisites` block was prefixed with one clarifying sentence noting that it fires only on the open-sprint path; behavior is unchanged when no manifest is present.

The Re-Open Flow runs Steps A–F: ask the literal `Re-open the sprint?` prompt; exit-on-rejection with a re-open-required message; surface the manifest's checked ACs; ask `Which (if any) should be re-opened?`; reverse selected ACs (un-check in `docs/prd.md`, reverse splits per the manifest's `Story splits:` line, handle `/sdd:refine`-deleted ACs with an informational warning); append a `[sprint-reopened]` marker; then continue with the existing `## Behavior > Step 1: Read Open Concerns`. The "What re-open does NOT reverse" sub-section names iteration-candidate dispositions, tech-debt entries, and `docs/v2-verification.md` "Shipped" rows. All cross-references point at `docs/spec.md > Cross-Cutting Mechanisms > Close-Sprint Manifest > Re-open behavior` and `references/sprint-tags.md > [close-sprint-manifest]` / `> [sprint-reopened]` — no parser regexes or schema duplicated inline.

Decision: subagent used Step letters (A–F) rather than Step numbers for the Re-Open Flow sub-steps to avoid ambiguity with the Behavior section's existing numeric steps. Confirmed by orchestrator on verification re-read.

### Item 2: `/sdd:retro` recommendation timing — closed (`aaey`, `aaez`)

Subagent identified that `plan/SKILL.md`'s existing upstream `## PRD State Check` Rule 2 already gated the `/sdd:retro` recommendation on (all ACs checked) AND (Unvetted empty) — the exact condition `aaez` requires. Since `/sdd:plan` performs no PRD edits during its run, PRD state is fixed for the duration of the command and the routing decision computed at the upstream check is the same decision that would be computed at end-of-command.

Subagent chose **Option A** (label the existing upstream gate; do not duplicate to end-of-command). Edits to `plan/SKILL.md`:
- Added a rationale paragraph at the top of `## PRD State Check` explaining why the upstream placement is the authoritative gate (no PRD edits → fixed state → upstream is equivalent to end-of-command, with the recommendation emitted via the standard `## End-of-Command Handoff` template).
- Labeled Rule 2 with `(Satisfies aaez — ...)` and cross-referenced `build/SKILL.md > Step W11` as the parallel project-close gate satisfying `aafa`.
- Rewrote one sentence in `## End-of-Command Handoff > ### Next-command target` to name the upstream Rule 2 as the only path on which `/sdd:plan` recommends `/sdd:retro`, and to state explicitly that no additional end-of-command retro check fires.

Cross-command grep for `/sdd:retro` across `plugins/sdd/skills/*/SKILL.md` surfaced 21 matches across 8 files. All classified as either gated (build W11, plan Rule 2, build prerequisite line "or `/sdd:retro` if the project is complete") or context-only (retro's own definitions, workflow listings, cross-references). **Zero blanket sprint-boundary recommendations found.** `aaey` satisfied by current state — no edits to any other SKILL.md required.

Decision: Option A chosen over Option B (parallel end-of-command check) on the grounds that a duplicate check would be dead code on every non-Rule-2 path and would introduce parallel logic without strengthening any behavioral guarantee. The sprint item description literally said "at end-of-command" but the upstream check IS where the recommendation is emitted from (via the end-of-command handoff template) — so the AC's intent is satisfied. Documented inline with the cross-reference so future readers can trace `aaez` to its implementation.

### Item 3: `/sdd:retro` cross-project pattern capture — closed (`aafb`, `aafc`, `aafd`, `aafe`)

Single-file edit to `plugins/sdd/skills/retro/SKILL.md`. Inserted a new **Step 7: Cross-Project Pattern Capture** between the former Step 6 (Generate Retrospective Document) and former Step 7 (Final Open Concerns Resolution). Renumbered downstream: former Step 7 → Step 8 (Final Open Concerns Resolution), Step 8 → Step 9 (Update Project State), Step 9 → Step 10 (Wrap Up). Internal references updated (line 145 retro doc bullet, Important Reminders section).

The step asks one open-ended question: "Are there any patterns from this project you want to carry forward to future projects?" On a no-equivalent answer, it skips entirely — no file write. On user-named patterns, processes them one at a time with explicit per-pattern confirmation ("Capture this pattern as `[user's phrasing]`?"); only confirmed patterns are appended to `~/.claude/sdd-cross-project-patterns.md` in the format `- YYYY-MM-DD (project-name): User's chosen phrasing of the pattern.`. `project-name` resolution: read `docs/project-state.json` `name` field if present; fall back to project directory basename. File is created on first append; append-only across projects; never rewritten or reordered. Cross-reference points at `docs/spec.md > /sdd:retro` for format authority — entry format not re-specified inline beyond the literal template line.

Added one new Important Reminders bullet covering the step's ordering, skippability, and per-pattern confirmation invariants.

### Sprint-wide notes

- **Three subagent dispatches, no inline implementation by orchestrator.** Each item went to its own general-purpose subagent with a self-contained brief carrying the spec sections, sprint item description, AC list, verification criteria, and explicit constraints (markdown-only, no auto-commit, file scope). Orchestrator verified each edit on return by re-reading the modified file.
- **Zero deviations from sprint plan.** All three items landed in the intended files (`polish/SKILL.md`, `plan/SKILL.md`, `retro/SKILL.md`) with no cross-file scope creep. Item 2's cross-command grep verification surfaced no edits beyond `plan/SKILL.md`.
- **No `[FB: ...]` tags on any item.** Both epics (Sprint Loop polish re-open + `/sdd:retro at Project Close`) are PRD-walkthrough-derived, not v1-feedback-derived. `docs/v2-verification.md` is not touched at this sprint's close — zero rows updated, zero rows added.
- **No `[iteration-candidate]` markers written during build.** No real candidates observed during the three subagent runs.
- **No tech-debt entries.** All three edits are clean additions/labels with no shortcuts taken.
- **PRD checkoff propagated immediately on close.** All 9 ACs (`aaev`, `aaew`, `aaex`, `aaey`, `aaez`, `aafb`, `aafc`, `aafd`, `aafe`) flipped from `[ ]` to `[x]` in `docs/prd.md` at sprint close, matching Sprints 11-14 discipline.
- **Post-checkoff PRD state.** 46 ACs remain unchecked across 3 epics. Unvetted section empty. Per build/SKILL.md W11 routing: recommend `/sdd:plan` for the next sprint loop (neither project-close path nor refine path applies).
- **Open concerns.** No changes. The one open concern ("Soft acceptance criteria for MIT distribution") remains targeted at `/sdd:refine`; not sprint-relevant. No new concerns surfaced during the build.

### Verification recap (per-item)

All sprint-item verification criteria confirmed by orchestrator re-read post-edit:

- **Item 1.** Manifest detection (Step 7) fires before `## Prerequisites`; literal `Re-open the sprint?` prompt appears verbatim; cross-references to spec and sprint-tags reference present, no parsers/schemas inlined; "What re-open does NOT reverse" sub-section names iteration dispositions, tech-debt, and v2-verification rows; rejection path exits with re-open-required message and no file writes; multi-cycle clause notes most-recent manifest authoritative; open-sprint prerequisites still apply when no manifest present.
- **Item 2.** PRD state check exists with the two-conjunct gate (every AC checked AND Unvetted empty); `aaez` explicitly labeled in SKILL.md text (Rule 2 + Next-command target); no blanket retro recommendations remain across all SKILL.md; build W11 `aafa` gate untouched.
- **Item 3.** Pattern-capture step appears after Steps 3–6 and before Steps 8–10; one question at a time with per-pattern confirmation before any write; entry format matches spec verbatim including trailing period; file path is `~/.claude/sdd-cross-project-patterns.md` (not project-local); step skippable on no-equivalent answer with no file write; step numbering consistent (Step 1 → Step 10) with all cross-references updated.

[close-sprint-manifest] timestamp: 2026-05-14T16:05:51Z
- PRD ACs checked: aaev, aaew, aaex, aaey, aaez, aafb, aafc, aafd, aafe
- Story splits: (none)
- Iteration candidate dispositions: (none)
- Tech-debt entries: 0 (informational)
- v2-verification rows updated: (none) (informational)
[/close-sprint-manifest]
