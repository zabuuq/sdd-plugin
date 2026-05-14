# Process Notes — Sprint 12

## Sprint planning (2026-05-14)

### Context

Sprint 11 closed the entire Multi-Session Resume epic (17 ACs `aaar`–`aabg`) — `/sdd:pause` and `/sdd:unpause` are fully implemented, resume-file cleanup is wired into the seven pause-able command SKILL.md files, and `docs/v2-verification.md` rows for FB-004 / FB-005 / FB-006 carry Shipped entries. The v2 chain is now end-to-end runnable for pause/unpause across sessions. Next-biggest visible gap: right-sizing — small projects still get the full deepening-round cadence regardless of their actual scale.

### PRD scan (post-Sprint-11)

- **Incomplete:** 81 ACs across 8 epics (counted via `grep '^  - \[ \] \`[a-z]{4}\`' docs/prd.md`):
  - Right-sizing wire-through (8 ACs) — `aabr`–`aaby`
  - `/sdd:prd`-only polish (9 ACs) — `aacd`–`aacl`
  - `/sdd:polish` re-open (3 ACs) — `aaev`–`aaex`
  - `/sdd:retro` at project close (6 ACs) — `aaey`, `aaez`, `aafb`–`aafe`
  - Automatic Backlog Generation (12 ACs) — `aafs`–`aagd`
  - Process Notes Growth remainder (9 ACs) — `aagf`–`aagn`
  - v1→v2 Migration (21 ACs) — `aago`–`aahi`
  - v2 Release Verification remaining (13 ACs) — `aahj`, `aahl`–`aaht`, `aahu`, `aahw`, `aahx`
- **Unvetted:** 0 (section present, empty). No `/sdd:refine` recommendation.

(Note: the Sprint 11 wrap-up projected 70 ACs remaining; actual count is 81. The pre-Sprint-11 baseline of "87 ACs" was an approximation. The grep count is authoritative going forward.)

### Open concerns scan

One open concern remains: "Soft acceptance criteria for MIT distribution" — target is `/sdd:refine`, not sprint-relevant. No new concerns surfaced.

### Proposed grouping

Agent proposed Right-Sizing Wire-Through (`aabr`–`aaby`, 8 ACs) as Sprint 12. Rationale captured:
- Single epic, fully specified — `references/right-sizing.md` is canonical and complete (signals, lifecycle, skippable beats per command, thinness signal, cap interaction).
- Touches every interview command: scope (write authority), discovery (read only), prd/spec/plan (read + mandated re-evaluation), refine/polish (read + permissive re-evaluation).
- Tangible outcome: small projects get a measurably tighter interview cadence — skipped designated Phase 1 beats, "no more rounds" default, sub-5-question rounds when thinness signal fires.
- Independent of other remaining epics. No PRD edits needed (the `/sdd:scope` relocation already shipped in Sprint 1 Item 1).
- Comfortable autonomous-sprint size — 8 ACs across roughly 1 SKILL.md write authority + cross-cutting reads/edits in 6 other commands + small extension to `references/deepening-rounds.md`. Similar shape to Sprint 11's Item 5 cleanup.

Why other candidates ranked lower:
- `/sdd:prd` polish (9 ACs) — single-file work, smaller; better as a follow-on or combined with another small epic.
- Process Notes Growth (9 ACs) — also good and similarly-sized; mechanically identical pattern across commands. Keep separate so each is a clear, demonstrable milestone.
- Auto Backlog Generation (12 ACs) — cross-cutting across 5 planning commands, larger lift, depends on backlog file conventions.
- v1→v2 Migration (21 ACs) — biggest, but lower priority until v2 is closer to GA.
- `/sdd:retro` at project close (6 ACs) — terminal command, better near v2 GA.
- `/sdd:polish` re-open (3 ACs) — small, could fit at the tail of another sprint.
- v2 Release Verification remaining (13 ACs) — mostly seeding/persistence ACs; can ride alongside other work.

### User response

"works for me" — accepted the proposal without modification.

### Build mode

Agent recommended autonomous, locked for the sprint, mirroring Sprints 5-11. User confirmed: "autonomous." Same rationale as prior sprints: pre-vetted ACs, fully-specified spec/reference resolutions, single-epic scope, well-understood cross-cutting pattern.

### Deepening rounds

Agent recommended against another round with reasoning + topic preview: pre-vetted ACs, canonical reference material already in place (`references/right-sizing.md`), single epic, well-understood cross-cutting pattern. Topics a next round would have covered (deferred to `/sdd:build` implementation context):
- Per-item grouping options — kept at 4 items (one detection-and-write authority, three cross-cutting reads/behaviors). Alternative was per-command split, but the uniform behavior across commands makes consolidated items cleaner.
- Concrete enumeration of which Phase 1 beats are "small-project-skippable" in each command — resolved in `references/right-sizing.md > ## Skippable Phase 1 beats per command`.
- The thinness-detection signal phrasing — resolved in `references/right-sizing.md > ## The thinness signal and sub-5-question rounds`.
- Whether `aaby` (any command may re-write `smallProject`) needs a guard against thrashing — best discovered at build time.

Context tier recommendation: `/clear` after sprint generation, then `/sdd:build`. The session carries the Sprint 11 build (5 subagents, multiple file edits) plus the Sprint 12 planning interview. Sprint 12 build benefits from a fresh window; `/sdd:build` re-entry loads CLAUDE.md and the new sprint file cleanly.

User accepted: "proceed."

### Sprint file generation

Sprint file written at `docs/sprint-12.md`. Four items, sequential, autonomous mode.

Final tag set:
- Item 1: `[PRD: aabr, aabs, aabt]` `[FB: FB-011]`
- Item 2: `[PRD: aabu, aaby]` `[FB: FB-011]`
- Item 3: `[PRD: aabv]` `[FB: FB-011]`
- Item 4: `[PRD: aabw, aabx]` `[FB: FB-011]`

All items carry `[PRD: ...]` — sprint validation per `aadw` passes.

### Notable decisions

- **`/sdd:discovery` exclusion from detection / re-evaluation.** Per `references/right-sizing.md > ## The smallProject field > Authoring lifecycle`, `/sdd:discovery` does NOT detect, re-evaluate, or write `smallProject`. The original PRD assigned detection to discovery; v2 moved it to `/sdd:scope` (a deliberate relocation captured in `spec.md > PRD Edits Required`). Item 2 has discovery READING the value (per AC `aabu`'s "every interview command") but never writing. This is consistent — discovery is open exploration, and a scale judgment at that stage is premature.
- **Permissive vs. mandated re-evaluation.** `references/right-sizing.md > ## The smallProject field > Authoring lifecycle` enumerates `/sdd:prd`, `/sdd:spec`, and `/sdd:plan` as mandated re-evaluators on startup. `/sdd:refine` and `/sdd:polish` are not explicitly enumerated. AC `aaby` says "each interview command may write..." — permission, not requirement. Sprint plan reflects this: scope/prd/spec/plan have mandated re-evaluation; refine/polish have permissive language ("may re-write if observed signals contradict"); discovery is read-only.
- **Item 4 implementation locus.** The end-of-round-default and sub-5-question-round behaviors are cross-cutting across every interview command's deepening flow. Centralizing the logic in `references/deepening-rounds.md` with cross-references to `references/right-sizing.md` keeps each SKILL.md slim and the rule single-sourced. The existing cross-reference at deepening-rounds.md line 38 is the anchor — extend it as needed. Subagent decides whether to add per-command pointers.
- **4-item granularity (vs. Sprint 11's 5).** Item 2 combines the cross-command read (`aabu`) with the per-command re-evaluation (`aaby`) because both involve project-state.json reads/writes in the same SKILL.md files. Splitting would mean two edit passes over the same 7 files. Combined into a single item, the subagent touches each SKILL.md once. Sprint 11's Item 5 used the same pattern.
- **No new design calls.** Sprint 12 is pure implementation. Every behavioral specification is already resolved in `references/right-sizing.md` and `references/deepening-rounds.md`. No `lastCommand` no-clobber–type design surprises expected.

### Build mode caveat

Same as every prior sprint: the cached marketplace plugin is still v1, so Sprint 12's edits to seven SKILL.md files plus `references/deepening-rounds.md` ship to disk but are not live as agent behavior until the maintainer reinstalls. First post-Sprint-12 invocation of any interview command will be the first real test of right-sizing behavior. The cache reinstall remains a deferred maintenance task across multiple sprints.

### v2-verification expected updates at Sprint 12 close

- **FB-011** (Right-size detection across interview commands with small-project hint propagated downstream) — Shipped: sprint 12, items 1-4.

### Post-Sprint-12 projection

- AC count remaining: 73 across 7 epics (81 − 8).
- Roughly 4-7 more sprints to v2 GA depending on bundling.
- Migration guide and verification documentation are the back-half work.
- PRD checkoff watch carried from Sprint 11 — close-sprint must propagate AC checkoffs to `docs/prd.md` immediately.

## Sprint build (2026-05-14)

### Item 1 — `/sdd:scope` `smallProject` detection and authoritative write

Subagent edited `plugins/sdd/skills/scope/SKILL.md`. Added two new sections:

- **`### Small-project signal observation (during Phase 1)`** inside the existing `## Interview — Phase 1: Mandatory Questions` block. Observes the six signals from `references/right-sizing.md > ## Signals for the heuristic judgment` without restating them; does not surface the judgment to the user during Phase 1.
- **`## End of Phase 1: Authoritative smallProject Write`** as a sibling top-level section between Phase 1 and the pre-existing `## Project Size Assessment`. Fires exactly once per `/sdd:scope` run, unconditionally, after the mandatory beats land and before any Phase 2 deepening or document generation. Cross-references the right-sizing reference for the heuristic + rule of thumb. Both branches handled (`true` for signals present; `false` when absent or strongly contradicted; omitting the key is acknowledged as equivalent-to-`false` but writing `false` is preferred for visibility).

**Subagent decision:** kept the new `## End of Phase 1: Authoritative smallProject Write` as a sibling of `## Project Size Assessment` rather than merging — the two are distinct concerns (`smallProject` = right-sizing heuristic; Project Size Assessment = phase-split call). Recorded so a future reader doesn't try to dedup them. Did not propagate the spec's leftover "tentatively flagged by /sdd:discovery" language — the right-sizing reference's authoring lifecycle is canonical and excludes discovery.

### Item 2 — Cross-command `smallProject` startup read + per-command re-evaluation

Subagent edited all seven interview command SKILL.md files. Each got a `smallProject` startup-read step placed immediately after the existing `lastCommand` state-update; each cross-references `references/right-sizing.md > ## The smallProject field` (with `> Authoring lifecycle` cited where re-evaluation is discussed). No signals or rule-of-thumb restated.

Per-command differences:
- `discovery/SKILL.md` — read-only, explicit "no detection, no re-evaluation, no write" rule citing the authoring-lifecycle exclusion.
- `scope/SKILL.md` — startup read added; one-line note that the end-of-Phase-1 authoritative write IS its re-evaluation step (Item 1's work unchanged).
- `prd/SKILL.md`, `spec/SKILL.md`, `plan/SKILL.md` — mandatory re-evaluation on startup against the signals using material on hand; confirm = no-op; flip writes `docs/project-state.json` + one-line entry to `process-notes-prd.md` / `process-notes-spec.md` / `process-notes-sprint-N.md` respectively, with brief rationale.
- `refine/SKILL.md`, `polish/SKILL.md` — permissive language: "may re-write `smallProject` if observed signals contradict the current value." Not mandated. Flip log target is `process-notes-sprint-N.md`.

**Subagent decision:** used numbered sub-steps (`1a`, `4a`, `Step 1a`) where the parent SKILL already had numbered startup sequences (scope/prd/spec); used a top-level `##` section in plan/refine/polish/discovery where state updates already lived at top level. For `/sdd:plan` the flip-log target is `process-notes-sprint-N.md` where N is the sprint this run is about to produce (`currentSprint + 1` or `1`) — noted explicitly so the file write at startup creates the right file if it doesn't yet exist.

### Item 3 — Small-project skippable Phase 1 beats

Subagent edited four SKILL.md files (`/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`). Each got a "Small-project right-sizing (conditional)" note inside its Phase 1 / mandatory-beats block:

- Cross-references `references/right-sizing.md > ## Skippable Phase 1 beats per command > ### \`/sdd:<command>\`` (matching the backtick-wrapped subsection names in the reference exactly, so a future link-checker resolves them).
- Uses the literal phrase "skippable, not required-skipped" so the conditionality is unambiguous and the agent's judgment to ask anyway is preserved.
- Beat lists are NOT restated inline anywhere (verified by grep).
- `/sdd:discovery`, `/sdd:refine`, `/sdd:polish` not edited — they have no skippable beats per the reference.

**Subagent decision:** in `/sdd:spec/SKILL.md` the conditional governs beats spread across multiple numbered steps (4, 5, 7, 11). The note was anchored in Step 4 (the file's labeled Phase 1 entry point) with explicit step-number forward references, avoiding inline beat-name restatement.

### Item 4 — Small-project deepening-round defaults

Subagent edited `plugins/sdd/skills/sdd-guide/references/deepening-rounds.md` only. No per-command SKILL.md edits needed — every interview command already defers to the reference for the deepening protocol, so right-sizing behaviors are inherited automatically. Reference-side single-sourcing preserved.

Three additions to the reference:
- **Cap-as-ceiling paragraph** (line 38, post-bullet-list): the 5/10 caps are a ceiling, not a target; right-sizing only moves the floor. Cross-references `references/right-sizing.md > ## Interaction with the question cap` for the canonical statement.
- **Right-sized round size (smallProject + thinness signal)** section: when `smallProject: true` AND the thinness signal fires on the just-completed round or transition, the next round is composed with fewer than 5 questions (typically 2-3). Cross-references `references/right-sizing.md > ## The thinness signal and sub-5-question rounds` for the signal definition (do not restate). Literal transition message: `"This is feeling well-covered. Want a quick 2-3 question pass to close gaps, or are you ready to proceed?"` (close paraphrase allowed). User retains the right to decline the round entirely.
- **Right-sized default direction (smallProject)** paragraph in the End-of-Round Recommendation section: when `smallProject: true`, the recommendation defaults to the close form ("I do not think we need another round…") UNLESS concrete evidence (genuine ambiguity, unresolved edge case, hidden assumption the user reacted to) in the just-completed round contradicts that default. **Form is preserved** — still definite, with reasoning, with topic-preview discipline. A bare prompt is still not allowed. Only the default direction shifts.

### Notable build observations

- **No `aaby` thrashing surfaced.** Sprint plan flagged "guards against `aaby` re-evaluation thrashing" as a build-time discovery item. Build did not surface a need — the per-command process-notes flip-log + "confirm = no-op" + "bias toward humility" + permissive (vs. mandated) language for `/sdd:refine`/`/sdd:polish` already form a natural anti-thrashing posture. No additional guard needed.
- **Reference cross-reference form converged.** All Item 2/3/4 edits use the same shape: `skills/sdd-guide/references/<file>.md > ## <subsection name>` (with backtick-wrapped command names where the reference's subsection headers use them). Consistent across 8 modified files. Good single-source discipline.
- **Caveat unchanged.** Cached marketplace plugin is still v1. Sprint 12's edits ship to disk but aren't live agent behavior until the maintainer reinstalls. First post-Sprint-12 interview-command run will be the first real exercise of right-sizing behavior end-to-end.

### Close-sprint actions

- PRD AC checkoffs propagated to `docs/prd.md`: `aabr`, `aabs`, `aabt`, `aabu`, `aabv`, `aabw`, `aabx`, `aaby` (8 ACs, entire Right-Sizing epic).
- `docs/v2-verification.md` FB-011 row updated: Shipped column populated `sprint 12, items 1-4`; Notes column populated with per-item summary. Verified column untouched (manual checkoff).
- No story splits (all items completed in full).
- No iteration-candidate markers raised during build.
- No tech-debt entries.
- No new open concerns.

### Anything notable?

- **Right-sizing landed clean in one sprint.** Eight ACs, eight files modified (seven SKILL.md + one reference), zero design surprises, zero per-command deepening edits needed. The cross-cutting nature of the change was absorbed by the reference layer.
- **The dogfood is paying off.** Sprint 12 verifies that the v2 architecture decision — references own "how," SKILL.md cross-references them, spec stays lean — composes well for cross-cutting behavior changes. Adding a similar cross-cutting behavior in v1 would have required restating in every SKILL.md.
- **Authoring-lifecycle clarity saved Item 2.** The right-sizing reference's explicit enumeration of which commands MUST re-evaluate (prd/spec/plan), which MAY (refine/polish), and which MUST NOT (discovery) collapsed what could have been a multi-round design conversation into mechanical wire-up. Worth noting for future cross-cutting features: enumerate command roles in the reference before sprint planning.

### Next-command recommendation

- **PRD state after close:** 73 ACs remaining across 7 epics. Unvetted section empty. Not all checked → not yet `/sdd:retro` territory.
- **Recommendation:** continue the sprint loop. `/clear`, then `/sdd:plan` to pick the next sprint. Natural next candidates (per Sprint 12's Notes section): `/sdd:prd`-only polish (`aacd`–`aacl`, 9 ACs), Process Notes Growth remainder (`aagf`–`aagn`, 9 ACs), `/sdd:polish` re-open (`aaev`–`aaex`, 3 ACs), or Automatic Backlog Generation (`aafs`–`aagd`, 12 ACs). `/sdd:plan` interview will pick.

### Close-sprint manifest

```
[close-sprint-manifest] timestamp: 2026-05-14T00:00:00Z
- PRD ACs checked: aabr, aabs, aabt, aabu, aabv, aabw, aabx, aaby
- Story splits: none
- Iteration candidate dispositions: none
- Tech-debt entries: 0 (informational)
- v2-verification rows updated: FB-011 (informational)
[/close-sprint-manifest]
```
