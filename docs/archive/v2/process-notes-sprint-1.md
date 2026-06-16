# Sprint 1 — Process Notes

## Planning Session — 2026-05-12

### Context

First v2 implementation sprint. Planned via the v1 `/sdd:sprint` command (the v2 `/sdd:plan` rename + behavior upgrade is itself part of v2 work, scheduled for a later sprint). Acknowledged the dogfooding bootstrap up front: v1 sprint command in use because the v2 plan command does not yet exist; v1 sprint command does not validate `[PRD: ...]` tags, does not enforce 4-char AC IDs, does not read prior process-notes-spec.md, does not check Unvetted section. None of those gaps were fatal — sprint 1 contents are simple enough that the v1 command's interview-and-decompose flow was sufficient.

### Sprint Theme

PRD foundation — make `docs/prd.md` internally consistent with v2 spec and AC-ID-tagged so every subsequent sprint can validly use `[PRD: id1, id2]` tags.

Tangible outcome: `docs/prd.md` is fully v2-consistent, every AC carries a stable 4-char alphabetic ID, the five foundation open-concerns are resolved, alphanumeric AC-ID slips scrubbed from `docs/spec.md`.

Deliberately out of scope: plugin filesystem changes (skill renames, new SKILL.md files), v2 command behavior implementation, bug fixes. Those are deferred to subsequent sprints whose ordering will be decided at sprint-2 planning time.

### Proposed grouping (initial)

Six items in a logical sequence:
1. PRD edit: relocate smallProject detection from `/sdd:discovery` to `/sdd:scope`.
2. PRD edit: add new v2 profile-field seeding story to `/sdd:onboard Polish`.
3. PRD edit: name `~/.claude/sdd-cross-project-patterns.md` in `/sdd:retro` AC.
4. AC ID backfill: assign 4-char alphabetic IDs to every AC in `docs/prd.md`.
5. Open-concerns reconciliation: move five resolved concerns to Resolved.

(Plus scrub of `a7kp`-style alphanumeric examples in `docs/spec.md`, which got folded into item 4 during the deepening round.)

User accepted the grouping as-is. No edits, removals, or restructuring requested.

### Build mode

User chose **autonomous**. Rationale offered alongside the choice: items are small, scoped markdown edits with clear acceptance criteria; autonomous would let `/build` rip through all items in one pass with a single review checkpoint, which fits the work better than step-by-step's per-item `/clear` cycles.

Build mode locked for sprint 1. No mid-sprint mode switching.

### Deepening Round 1

User requested one round. Five questions answered, all resolving edges in the proposal:

1. **Q1: Item ordering and AC-ID assignment for newly-added ACs.** Answer: **A (edits-then-backfill).** Items 1–3 add new ACs without IDs; item 4 sweeps and assigns IDs to all ACs (including the ones items 1–3 just added). Cleaner mechanically (one ID-generation pass; no collision-check against partial state). Intermediate states are ID-less, which is acceptable because autonomous mode reviews the final state only.

2. **Q2: Item 1 placement decision.** Answer: **B (new /sdd:scope-scoped story under Interview Command Behaviors).** Keeps the existing cross-cutting small-project story (about *reading* `smallProject`) intact and gives `/sdd:scope`'s *detection* responsibility its own story. Cleaner separation than appending detection ACs to the cross-cutting story.

3. **Q3: Item 2 placement decision.** Answer: **A (new dedicated story for v2 profile-field seeding).** No existing `/sdd:onboard Polish` story enumerates the full schema; `defaultSprintMode` has no natural existing home. Single new v2-seeding story keeps the edit surgical and readable.

4. **Q4: Item 4 generation mechanism.** Answer: **A (LLM-driven inline file rewrite).** `/build` reads `docs/prd.md`, generates IDs as it walks the ACs (tracking assigned IDs in working memory), writes the whole file back via one atomic Write. Matches how the runtime `/sdd:prd` will work; no script artifact in the repo.

5. **Q5: Item 5 reconciliation — spec.md slip scrub scope.** Answer: **A (full resolution — sprint 1 also scrubs `a7kp`-style examples in `docs/spec.md`).** Folded into item 4's scope. Eliminates the open concern's "no example carries the alphanumeric form into production" clause completely instead of leaving it as a lingering thread.

End-of-round recommendation: no further rounds. All five named edges resolved; remaining work (exact AC text for new stories, verification routines) is mechanical and routine. User accepted recommendation.

### New concerns surfaced during planning

None. Planning resolved five proposal edges to concrete choices; no new ambiguities introduced.

### Decisions log

| Decision | Choice | Rationale |
|---|---|---|
| Sprint 1 theme | PRD foundation only | Spec mandates these edits before any v2 implementation sprint begins; pure markdown work; no dogfooding paradoxes |
| What to exclude from sprint 1 | Plugin filesystem renames, v2 command behavior, bug fixes | Each is a separate concern that doesn't share the PRD-edit thematic |
| Item 1 PRD placement | New /sdd:scope-scoped story under Interview Command Behaviors | Cleaner separation of detection (scoped) from reading (cross-cutting) |
| Item 2 PRD placement | New dedicated v2-profile-field-seeding story | `defaultSprintMode` has no existing home; one story is surgical |
| Item 3 PRD placement | Edit existing AC in-place under /sdd:retro at Project Close | Spec-mandated literal replacement |
| Item ordering vs. AC-ID assignment | Edits-then-backfill (item 4 sweeps after items 1–3) | One ID-generation pass; cleaner final state |
| ID generation mechanism | LLM-driven single-pass file rewrite | Matches runtime `/sdd:prd` pattern; no script artifact |
| Spec.md alphanumeric scrub | Sprint 1 does it (folded into item 4) | Resolves open concern fully; ~30-second edit |
| Build mode | Autonomous | Small scoped edits; per-item `/clear` cycles are overkill |

## Build Session — 2026-05-13

### Mode and orchestration

Autonomous mode. Items 1–3 dispatched to subagents sequentially (not in parallel, despite being "logically independent" per the sprint file — all three edit `docs/prd.md`, so concurrent file writes would race). Item 4 dispatched as a single subagent (both halves — PRD AC-ID backfill and `docs/spec.md` alphanumeric scrub — folded into one dispatch since they're conceptually one operation). Item 5 (open-concerns reconciliation) executed directly by the orchestrator without a subagent — pure structured move-between-sections, the full file was already in orchestrator context, subagent dispatch would have been ceremony.

The first-handoff checkpoint pause normally specified by autonomous mode was skipped because the user explicitly asked for the build to run without stopping. Verification ran via independent grep-and-read after each subagent reported back, instead of via a user-facing checkpoint.

### Per-item summary

**Item 1 (Relocate smallProject detection):** Subagent removed the small-project story from `### /sdd:discovery Command` and added a new `*(applies to /sdd:scope only)*`-scoped story under `### Interview Command Behaviors` covering Phase 1 detection, end-of-Phase-1 authoritative write, and signals-from-`references/right-sizing.md`. The cross-cutting reading story was left intact. Line-count drift matched prediction (382 lines after edit).

**Item 2 (v2 profile-field seeding story):** Subagent added one new `/sdd:onboard Polish` story at the end of the epic enumerating `handoffWarningShown: false` and `defaultSprintMode: null` as profile-seeding ACs. Subagent noted the prompt under-counted preexisting stories (six estimated; actually seven), which is fine — no existing stories were modified. Line-count delta was +4 (one blank + one story-lead + two ACs).

**Item 3 (cross-project pattern file name):** Single one-line in-place AC replacement. Subagent confirmed `cross-project-patterns.md` appears exactly once and the "deferred to /sdd:spec" phrasing no longer appears in the /sdd:retro AC body.

**Item 4 (AC ID backfill + spec.md scrub):** Highest-stakes item of the sprint. Subagent used sequential alphabetic ID assignment starting at `aaaa` and ending at `aahx` for the 206th and final AC. Atomic single-pass file rewrite via Write tool (not Edit) — read entire PRD, transform line-by-line, write back. Uniqueness guaranteed by sequential strategy (zero collisions among 206 IDs drawn from a 457K space). For the spec scrub: 7 distinct edits replacing `a7kp`/`b3mq`/`c1xw` with valid alphabetic examples (`abcd`, `mxqr`, `pvtb`, `znyg`), the confused base-26/alphabetic-clarification cluster collapsed to one clean line, and the now-redundant correction note removed entirely. Orchestrator verified independently: 206 indented AC lines, 206 ID-prefixed AC lines, 206 unique IDs (208 total `[a-z]{4}` matches minus `true` × 4 and `null` × 3 = 206); zero matches for `a7kp`/`b3mq`/`c1xw` in spec.md.

**Item 5 (open-concerns reconciliation):** Five concerns moved from Open to Resolved (one-time PRD AC ID backfill; PRD edit for smallProject detection relocation; new profile fields; cross-project pattern file name; AC-ID notation form). Each got a `Resolved by: /sdd:sprint (sprint-1)` line plus a specific resolution note naming the responsible sprint-1 item. "Soft acceptance criteria for MIT distribution" stayed in Open. Deferred section untouched. The order in the Resolved section places the five new entries first, preserving "most recently resolved at top" implicit ordering.

### Decisions and deviations during build

- **Subagent dispatch for items 1–3 sequential, not parallel.** Sprint file said items 1–3 were "independent" and could run in any order; orchestrator interpreted "independent" as logical (no item depends on another's output) rather than concurrent (safe to run in parallel). All three edit the same file (`docs/prd.md`); concurrent writes would corrupt state. Sequential dispatch was the correct read.
- **Item 5 executed directly, no subagent.** Build mode is "autonomous" but the orchestrator made a judgment call: housekeeping operations where the file is small, the orchestrator already has full context, and the operation is purely structural move-between-sections do not benefit from subagent ceremony. Faster + lower error surface. Worth noting as a pattern for future autonomous sprints — pure-housekeeping items can run direct.
- **Item 4 stakes warranted extra-careful subagent prompt.** Prompt enumerated exact line-by-line spec.md edits (with target line numbers), specified deterministic sequential ID assignment with a precomputed final-ID check (`aahx` for the 206th), and required the subagent to report back uniqueness count + first/last IDs + sample AC lines for orchestrator verification. Verification ran independently via grep before marking the item complete.
- **`true` and `null` are 4-char lowercase alphabetic strings.** When verifying uniqueness via `grep -oE \`[a-z]{4}\` | sort -u`, raw count came back at 208, not 206. The 2-token discrepancy is `true` (literal JSON value, appears in PRD body in backticks) and `null` (same). Subtracting those non-ID matches gives the expected 206 unique IDs. Future PRD-validation regexes should constrain to AC-line context (`^  - \[ \] \`...\``) rather than file-wide backtick-matching to avoid this false-positive class.

### CLAUDE.md / AGENTS.md updates

None. No new project-wide patterns or conventions emerged during this build. The sequential-ID backfill strategy was a one-time operation; the runtime `/sdd:prd` ID-generation behavior (collision check, opaque IDs) is already specified in spec.md and unchanged.

### New concerns surfaced during build

None. Five concerns moved from Open to Resolved; no new ambiguities or unanswered questions surfaced during execution.

### Sprint outcome

All 5 items complete. All 19 acceptance criteria across the 5 items satisfied. `docs/prd.md` is now fully v2-consistent and AC-ID-tagged (206 stable IDs across the file). `docs/spec.md` is scrubbed of alphanumeric AC-ID slips. `docs/open-concerns.md` has the five foundation concerns resolved; only "Soft acceptance criteria for MIT distribution" remains in Open. Sprint 2 onward can rely on a stable, v2-consistent PRD and use `[PRD: id1, id2]` tag format against backfilled IDs.
