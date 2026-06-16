# Sprint 9 — Process Notes

## Planning Session — 2026-05-13

### Context

Sprint 9 planning kicked off immediately after Sprint 8's wrap-up, same calendar day. Sprint 8 closed `/sdd:feedback` v2 (16 ACs: `aaaa`–`aaac` Bug Fix 1 + `aaff`–`aafr` Cross-Project Feedback Transfer); `feedback/SKILL.md` is feature-complete with dual-write, inside-clone detection, and single-warn-per-session failure handling. Sprint 8's process notes named Interview Command Behaviors + Context Management as the recommended next sprint with the caveat that the full 34 ACs deserved a focused pass.

Planning session ran under the user's `work without stopping for clarifying questions` directive — no `AskUserQuestion` checkpoints fired. Proposal was assembled and committed directly to `docs/sprint-9.md`. The user can redirect by editing the sprint file before `/sdd:build` runs.

### Why cross-cutting Interview Command Behaviors + Context Management next

Six reasons:

1. **The biggest cross-cutting unblock in the project.** AC `aaaj` says `/sdd:discovery` "applies every cross-cutting interview command behavior defined in the Interview Command Behaviors epic." `/sdd:discovery` is the new front-of-chain command — the v2 differentiator — but its implementation is blocked until the cross-cutting layer ships. Sprint 9 unblocks the next 1–2 sprints' worth of work.

2. **Sprint 8's process notes flagged this as the recommended next theme.** The Sprint 8 candidate-analysis table named "Interview Command Behaviors + Context Management" as the highest-leverage theme available; the only reason it wasn't Sprint 8 was the file-scope risk (34 ACs cross-cutting). Sprint 9 takes the bite now.

3. **Roughly half the work is already done in references.** `references/context-management.md` (141 lines, comprehensive) and the spec's End-of-Command Handoff resolution (lines 678–721, with full template and next-command map and out-of-pattern exceptions) substantially reduce Items 5 and 6's surface area. The remaining work is wiring, not authoring.

4. **Item-by-item is well-bounded.** Item 1 is a sdd-guide rule addition (3 ACs). Items 2 and 3 are deepening-rounds.md updates (3 + 4 ACs). Item 4 is another sdd-guide rule (4 ACs). Item 5 is a wire-through of an existing reference (7 ACs). Item 6 is a spec-resolved template install + wire (3 ACs). Each item touches one or two distinct concerns; none requires inventing a new abstraction.

5. **First multi-file sprint of the project — but managed.** Items 5 and 6 each touch six interview command SKILL.md files in addition to sdd-guide. This is a real departure from Sprints 5–8's single-file pattern. Risk is managed by (a) sequential dispatch with explicit file lists per item, (b) sdd-guide centralizing the heavy lifting (the per-command edits are thin wiring), (c) Items 1–4 establishing rules before Items 5 and 6 wire references.

6. **Mechanical-vs-exploratory: mechanical.** Every AC names a specific rule, literal phrasing, or behavioral seam. The PRD AC `aabn` for example specifies the exact recommendation phrasings ("I think we should do another round, because [reason]"). No design ambiguity at planning time. The 6-item decomposition is the only design call, and it follows the natural one-item-per-story seam.

### Sprint candidate analysis

Considered five candidate themes:

| Candidate | Size | Risk | Notes | Verdict |
|---|---|---|---|---|
| Cross-cutting ICB + Context Management (chosen) | 24 ACs (3+3+4+4+7+3) | Medium | First multi-file sprint; sdd-guide + deepening-rounds + 6 commands; biggest unblock (clears /sdd:discovery path) | **Selected** |
| Full Interview Command Behaviors + Context Management (34 ACs) | 34 ACs | High | Bundles scope-only (aabr–aabt) + right-size behavior (aabu–aaby) + PRD-only (aacd–aacl) | Defer — scope/PRD-only stories belong in command-specific sprints |
| /sdd:scope right-size detection + cross-command right-sizing | 8 ACs (3 + 5) | Medium | Depends on aabt's signal list deferred to spec; needs right-sizing.md content audit first | Defer one sprint — Sprint 9's cross-cutting layer is the precondition |
| /sdd:prd-only polish (AC quality, phase-split, Unvetted) | 9 ACs | Low | Single-file work in prd/SKILL.md | Defer — clean small standalone sprint |
| /sdd:discovery implementation | 10 ACs | Low | Blocked by aaaj on ICB cross-cutting | Defer one sprint — Sprint 9 unblocks |

**Why the chosen cross-cutting subset wins over the full 34 ACs:** The full epic mixes cross-cutting stories (apply to all 7 interview commands) with command-specific stories (`/sdd:scope`-only for aabr/aabs/aabt; `/sdd:prd`-only for aacd–aacl) and a right-size behavior story (`aabu`–`aaby`) that depends on `aabt`'s resolution. Bundling everything creates an artificial single-sprint scope that doesn't match the natural seams. Splitting cross-cutting from command-specific keeps Sprint 9 focused and leaves the command-specific stories for cleaner small sprints.

**Why the chosen Sprint 9 wins over `/sdd:scope` right-sizing:** Right-sizing depends on the cross-cutting layer being in place (every interview command needs the active-prompting and end-of-round-recommendation rules before `smallProject` can meaningfully right-size them). Sprint 9 must come first.

**Why not bundle in `/sdd:prd`-only polish:** The PRD-only stories (`aacd`–`aacl`) are 9 ACs of single-file work in `prd/SKILL.md`. Bundling diffuses Sprint 9's focus and adds a third concern (AC-quality check, phase-split threshold, Unvetted section) that has nothing to do with cross-cutting behavior.

### PRD scan

- 13 epics in `docs/prd.md`, 206 ACs total.
- **Checked ACs (post-Sprint-8):** 74 (21 from Sprint 5 + 19 from Sprint 6 + 18 from Sprint 7 + 16 from Sprint 8). Bug Fix 2 + v2-verification machinery checked an additional handful (`aaad`–`aaag`, `aahk`, `aahv`) during Sprint 5's wrap-up implementation.
- **Unchecked across remaining epics:** /sdd:discovery (10); Multi-Session Resume (17); Interview Command Behaviors cross-cutting (17 — Sprint 9 closes: aabh-aabj + aabk-aabm + aabn-aabq + aabz-aacc = 14, plus the small-project + right-size + prd-only that stay); Context Management (10 — Sprint 9 closes both stories: aacm-aacs + aact-aacv = 10); /sdd:polish re-open (3); /sdd:retro at Project Close (5); Automatic Backlog Generation (9); Process Notes Growth (10); v1→v2 Migration (13); v2 Release Verification (~9 still open).
- **Unvetted: empty** (line 363 of prd.md, unchanged).
- **Open concerns:** `docs/open-concerns.md` does not exist in this project. Skipped the open-concerns step (every command's startup protocol; the file simply isn't present here). Same as Sprint 8.
- **Historical-AC backfill gap:** Sprints 1–4 closed work pre-Bug-Fix-2; many ACs remain unchecked. Sprint 9 does not address the gap; remains maintainer PRD-hygiene.
- **Sprint 7 FB-tag gap:** Sprint 7 items map to FB-017 through FB-023 but were planned without `[FB:]` tags. Same status as Sprint 8 — candidate for `/sdd:polish` re-open or PRD-hygiene maintenance, not Sprint 9 work.

### Open concerns processed

`docs/open-concerns.md` does not exist in this project. No action.

### FB-tag mapping

Second sprint with `[FB: ...]` tags (Sprint 8 was first). Mapping from the v2-verification.md seed:

| Item | PRD ACs | FB tag(s) | v2-verification row Source(s) |
|---|---|---|---|
| Item 1 (clarifying-questions-don't-advance) | `aabh`, `aabi`, `aabj` | `FB-007` | Event Planning App, fiver-gigs |
| Item 2 (5/10 cap) | `aabk`, `aabl`, `aabm` | `FB-008` | fiver-gigs |
| Item 3 (end-of-round recommendation) | `aabn`, `aabo`, `aabp`, `aabq` | `FB-009`, `FB-010` | Event Planning App |
| Item 4 (active prompting) | `aabz`, `aaca`, `aacb`, `aacc` | `FB-012` | fiver-gigs |
| Item 5 (three-tier context wire) | `aacm`, `aacn`, `aaco`, `aacp`, `aacq`, `aacr`, `aacs` | `FB-014` | Event Planning App, fiver-gigs |
| Item 6 (default /clear handoff) | `aact`, `aacu`, `aacv` | `FB-015` | sdd-plugin self-use |

Item 3 carries two FB tags (`FB-009` and `FB-010` both map to the end-of-round-recommendation story). All other items carry one FB tag.

### Sprint item decomposition

Six items, mapped to the natural per-story seam:

- **Item 1: Clarifying-questions-don't-advance rule** (3 ACs, FB-007). New rule in `sdd-guide/SKILL.md` under Interaction Rules. Cross-cutting: applies to every interview command via sdd-guide's transitive load. No per-command edits.

- **Item 2: Bounded deepening rounds — 5 suggested / 10 hard cap** (3 ACs, FB-008). Update `references/deepening-rounds.md`'s Phase 2 section. Replaces the existing "4-5 questions" and "no cap" language with the 5-default / 10-silent / 11+-requires-permission rule.

- **Item 3: End-of-round recommendation with reasoning** (4 ACs, FB-009 + FB-010). Update `references/deepening-rounds.md`'s transition prompt to the structured "I think we should…" / "I do not think we need…" form with reasoning + topic list (continue branch) or optional avoided-topic list (close branch).

- **Item 4: Active prompting per beat / topic** (4 ACs, FB-012). New rule in `sdd-guide/SKILL.md` (adjacent to Item 1). Explicit invitation at end of each Phase 1 beat and each deepening-round topic; agent waits for user response before advancing; user input is processed as part of the current beat/topic.

- **Item 5: Three-tier between-rounds context recommendation — wire reference into commands** (7 ACs, FB-014). `references/context-management.md` already has the full 141-line model; Item 5 wires it into sdd-guide and the six existing interview command SKILL.md files so the recommendation actually fires at end-of-round. Order: content recommendation (Item 3) first, then context recommendation (Item 5). Both in the same end-of-round message group.

- **Item 6: Default /clear handoff between commands** (3 ACs, FB-015). Install the spec-resolved handoff template inline in `sdd-guide/SKILL.md` (standard form, first-handoff explanation, next-command map, out-of-pattern exceptions). Wire each of the six interview command SKILL.md files to emit the handoff at completion. Use `handoffWarningShown` (seeded by Sprint 7's `/sdd:onboard` work) to gate the first-handoff prepend.

### Out of scope (explicit)

- Small-project detection in `/sdd:scope` (`aabr`, `aabs`, `aabt`).
- Right-size behavior across interview commands (`aabu`–`aaby`).
- `/sdd:prd`-only stories — AC quality check (`aacd`–`aacf`), phase-split threshold (`aacg`–`aaci`), Unvetted section (`aacj`–`aacl`).
- `/sdd:discovery` command implementation (`aaah`–`aaaq`). Sprint 9 unblocks this.
- `/sdd:polish` re-open mechanism (`aaev`–`aaex`).
- Multi-Session Resume command implementations (`aaar`–`aabg`).
- Automatic Backlog Generation (`aafs`–`aagd`).
- Process Notes Growth (`aage`–`aagn`).
- v1→v2 Migration guide (`aago`–`aahi`).
- v2 Release Verification remaining ACs (~9).
- `/sdd:retro` at Project Close (`aaeq`–`aaeu`).
- Re-tagging Sprint 7's missing `[FB:]` entries.
- Historical-AC backfill from Sprints 1–4.

### Execution order decision

**Sequential.** Items 1, 4, 5, 6 all touch `sdd-guide/SKILL.md`. Items 2 and 3 both touch `deepening-rounds.md`. Items 5 and 6 each also touch the six existing interview command SKILL.md files (`scope`, `prd`, `spec`, `plan`, `refine`, `polish`). The edits are in different sections of the same files, but sequential dispatch prevents merge conflicts and keeps each subagent's mental model coherent.

Logical layering: Items 1–4 add foundational cross-cutting rules; Items 5–6 wire references and templates into the commands themselves. Item 5 builds on Item 3 (the context recommendation fires AFTER the content recommendation per `aacm`). Item 6 builds on Item 4 (the handoff is the unconditional end-of-command emission that completes the active-prompting cycle).

Same autonomous-sequential dispatch profile as Sprints 5–8, but with more files per item.

### Build mode

**Autonomous**, matching priors. Items are spec/reference-mapped: every AC has a clearly-defined target file and a clearly-defined source in the PRD + spec + references. No deepening required mid-build. Mode locked for Sprint 9.

The cached v1 `/sdd:sprint` Step 5 asks the user to pick build mode interactively. Per the no-clarifying-questions directive, this session defaulted to autonomous (matching Sprints 5–8) without re-asking. User can redirect by editing `buildMode` in `docs/project-state.json` before `/sdd:build` runs.

### Deepening rounds

None requested. Same call as Sprints 5–8: pre-vetted ACs with literal phrasing requirements (e.g., `aabn`'s exact recommendation strings), fully-specified spec/reference resolution for the bulk of the work, structurally well-understood cross-cutting work. The 6-item decomposition was the only design call, and it follows the natural one-item-per-story seam.

### Tagging convention

- Items 1–6 all carry real `[PRD: ...]` tags. No `[unmapped]` items.
- All six items carry `[FB: ...]` tags. **Second sprint with FB tags.** Item 3 carries two FB tags (FB-009 + FB-010); all others carry one.
- Sprint 5's close-sprint wrap-up logic (PRD checkoff + v2-verification row updates) will fire normally for these tags.

### New concerns surfaced during planning

- **Multi-file scope is new for the project.** Sprints 5–8 were single-file; Sprint 9 has two items (5 and 6) that each touch seven files (sdd-guide + six interview commands). Risk is real but managed via sequential dispatch with explicit file lists. Worth flagging for `/sdd:retro` later — does the autonomous-sequential pattern hold up for multi-file work as well as it did for single-file work?
- **Three-tier context recommendation reference is already complete but unwired.** `references/context-management.md` was written during the v2 spec/build phases but its consumers (the six interview commands) don't yet load it. Item 5 is essentially catching the wiring up to the content. Worth noting because the same pattern likely repeats elsewhere: spec/reference content written eagerly but not yet wired into commands. Future sprints should audit before assuming work is "shipped."
- **Item 6 depends on Sprint 7's `handoffWarningShown` seeding.** Sprint 7 added `handoffWarningShown: false` to the profile-creation step in `/sdd:onboard`. Item 6's first-handoff prepend gating reads that field. Existing user profiles (created before Sprint 7) may not have the field — the spec resolution at `docs/spec.md` lines 691–700 implies the agent should handle absent field as "show the heads-up" (treating absent as equivalent to `false`). Item 6's subagent should make this explicit.
- **`/sdd:discovery` skill file exists but command isn't implemented.** When wiring Item 5 and Item 6, the subagent should NOT touch `discovery/SKILL.md` even though it exists as a placeholder. The PRD ACs for `/sdd:discovery` (`aaah`–`aaaq`) are unchecked; that's a separate sprint blocked by this one. Sprint 9's Item 5 and Item 6 scope explicitly names the six implemented interview commands.

None of these rise to "Open concern" status. Each has a documented resolution in the sprint items themselves or is a forward-looking observation. `docs/open-concerns.md` doesn't exist in this project anyway.

### Decisions log

| Decision | Choice | Rationale |
|---|---|---|
| Sprint 9 theme | Cross-cutting Interview Command Behaviors + Context Management | Biggest cross-cutting unblock (clears /sdd:discovery path); roughly half the work already in references; mechanical character |
| Sprint scope | Cross-cutting subset only (24 ACs) | Full epic (34 ACs) bundles scope-only + PRD-only stories that don't fit the natural cross-cutting seam |
| Item decomposition | One item per PRD story (six items) | Natural seam; each item touches one or two concerns; no item invents a new abstraction |
| Item 5 wire strategy | Reference is loaded by each command (option b), not by sdd-guide's startup load (option a) | context-management.md is heavy and only relevant at end-of-round; loading it on every command startup wastes context |
| Item 6 template location | Inline in sdd-guide/SKILL.md (per spec resolution) | Spec already resolves the handoff template inline; following the spec's structural call |
| Multi-file scope | Accepted with sequential dispatch | First multi-file sprint; managed via per-item explicit file lists + autonomous-sequential subagent pattern |
| /sdd:discovery wiring | Excluded | discovery/SKILL.md is a placeholder; the command isn't implemented; that work is a separate sprint blocked by this one |
| Build mode | Autonomous | Matches Sprints 5–8; items are spec-mapped and mechanical; no-clarifying-questions directive defaulted without re-asking |
| Deepening rounds | None | Pre-vetted ACs with literal phrasing requirements, fully specified spec/reference resolution; no exploratory thread |
| `[FB: ...]` tagging | Yes — all six items | Second sprint with FB tags; Item 3 carries two FB tags (FB-009 + FB-010); Sprint 5's row-update logic fires normally |
| User input | None — no `AskUserQuestion` checkpoint | Per the no-clarifying-questions directive; proposal committed directly to sprint-9.md; user can redirect by editing the file before `/sdd:build` |

## Build Session — 2026-05-13

### Overview

Sprint 9 built autonomously in a single session (six items, sequential). All 24 PRD ACs closed. `[FB: ...]` rows updated for FB-007, FB-008, FB-009, FB-010, FB-012, FB-014, FB-015 in `docs/v2-verification.md` (`Shipped (sprint/item)` column populated; `Verified` boxes stay manual). No autonomous-mode checkpoint pause fired — the user's "work without stopping for clarifying questions" directive carried into build the same as into planning.

### Surprise: items 1, 2, partial 3, partial 6 were already in `sdd-guide.md` and `deepening-rounds.md`

When the build session loaded `sdd-guide/SKILL.md` and `references/deepening-rounds.md`, found that:

- **Item 1's clarifying-questions rule was already present** as Interaction Rule #4 in sdd-guide.md, covering all three ACs (`aabh`/`aabi`/`aabj`) in literal agent-actionable form.
- **Item 2's 5/10 cap was already present** in deepening-rounds.md Phase 2, with the "Past 10 questions, stop" + explicit permission gate matching `aabm` verbatim. The prior "no cap" language was already gone.
- **Item 3's end-of-round structured recommendation was already present** in deepening-rounds.md (literal continue/close forms with reasoning + topic preview / optional avoided-topic list), but the **Phase 2 transition prompt** at the top of the section still used a bare prompt ("Want another round to sharpen things, or ready to proceed?"). The build closed Item 3 by replacing that one bare prompt with a defer-to-structured-form directive — the structured form fires at every round-decision point, including the Phase 1 → Phase 2 transition.
- **Item 6's handoff template was already present** in sdd-guide.md (standard form + first-handoff explanation + next-command map + out-of-pattern exceptions). The build closed Item 6 by wiring the five not-yet-wired interview commands (`scope`, `prd`, `spec`, `refine`, `polish` — plan was already wired by Sprint 6) to emit the handoff at completion via their own `## End-of-Command Handoff` section pointing at the canonical template.

This is the pattern flagged in the planning notes: "spec/reference content written eagerly but not yet wired into commands." The Sprint 9 build did not need to author most of the rules — they were already authored. The work was the wiring. Items 4 and 5 were the only ones requiring substantive authoring (Item 4 added Interaction Rule #5 to sdd-guide.md; Item 5 added a new `## Between-Rounds Context Recommendation` section to sdd-guide.md, the load directive to each of the six commands, and the explicit end-of-round emission instruction in the four deepening-round commands).

### Items 1-4 done by orchestrator directly; items 5 and 6 dispatched to subagents

Items 1-4 were small enough (verification + one Phase 2 transition edit + one new sdd-guide rule) that the orchestrator did them directly without subagent dispatch. Items 5 and 6 each touched seven files (sdd-guide + six interview commands) and were appropriate for subagent dispatch. Subagent execution was sequential per the planning notes' explicit "sequential dispatch prevents merge conflicts" call — Item 5 first, then Item 6.

Both subagents reported clean completion. Orchestrator verified each via grep audits (no remaining bare prompts, no remaining "no cap" / "4-5 questions" language in command SKILL.md files, every command loads `context-management.md`, every command has an `## End-of-Command Handoff` section).

### Decisions made during build

| Decision | Choice | Rationale |
|---|---|---|
| Items 1-4 build path | Orchestrator-direct, no subagent | Three of the four were verification-only; Item 4 was a single new rule. Subagent overhead unwarranted. |
| Items 5, 6 build path | Subagent per item, sequential | Each touched seven files; sequential dispatch prevents merge conflicts per planning notes. |
| Item 3 Phase 2 transition fix | Replace bare prompt with defer-to-structured-form directive (not duplicate the form inline) | Keeps deepening-rounds.md as the single source for the recommendation; commands defer rather than duplicate. |
| Item 5 reference loading | Each command loads `context-management.md` directly (option b) | Per planning notes' wire-strategy decision; reference is heavy and only relevant at end-of-round. |
| `refine` and `polish` context-management wiring | Load the reference at startup + discretionary emit note at item-transition points | Neither command has formal deepening rounds; the reference is available if either grows them; emission is discretionary rather than mandatory. |
| `discovery` placeholder | Untouched | `discovery/SKILL.md` exists as a placeholder but the command isn't implemented; per planning notes, that's a separate sprint blocked by Sprint 9. |
| Autonomous checkpoint pauses | Not enforced | User's "no clarifying questions" directive translated to "no pauses for review either" — work flowed through to wrap-up. User can redirect via PR review or follow-up sprint. |
| Right-sizing reference drift | Logged, not addressed | `references/right-sizing.md` still says "4-5 questions" while `deepening-rounds.md` now says "5 default / up to 10". Right-sizing audit is OOS per the Sprint 9 sprint header — pair with the scope-detection + right-size sprint. |

### Items closed

- **Item 1** — Interaction Rule #4 in `sdd-guide.md` verified covers `aabh`/`aabi`/`aabj`. No edit needed. ✓
- **Item 2** — Phase 2 section of `deepening-rounds.md` verified covers `aabk`/`aabl`/`aabm`. No edit needed. ✓
- **Item 3** — Replaced the Phase 2 transition bare prompt at `deepening-rounds.md:21-25` with a defer-to-structured-form directive. End-of-round structured form already present and covers `aabn`/`aabo`/`aabp`/`aabq`. ✓
- **Item 4** — Added Interaction Rule #5 (active prompting) to `sdd-guide.md` after Rule #4. Covers Phase 1 beats and deepening-round topics, names invitations as illustrative, forbids advancement before user response, captures in-beat processing semantic. ✓
- **Item 5** — Subagent added `## Between-Rounds Context Recommendation` section to `sdd-guide.md` (lines 111-117). Added `context-management.md` load directive to all six interview command SKILL.md files. Four deepening-round commands (`scope`, `prd`, `spec`, `plan`) explicitly emit the three-tier recommendation after the content recommendation, in the same end-of-round message group. `refine` and `polish` load the reference with discretionary-emit notes. No bare prompts remain. ✓
- **Item 6** — Subagent added `## End-of-Command Handoff` section to `scope`, `prd`, `spec`, `refine`, `polish` (plan was already done in Sprint 6's `/sdd:plan` v2 work). Each section points at the canonical template in `sdd-guide.md`, names the next-command target, gives a one-line outcome-summary guideline, asserts unconditional emission. Out-of-pattern commands (`build`, `retro`, `pause`, `unpause`, `feedback`) and `discovery` untouched. ✓

### Verification cross-checks performed

- `grep "context-management"` across `plugins/sdd/skills/` — confirmed all six interview commands plus sdd-guide reference the file. `discovery` was checked separately and confirmed untouched.
- `grep "no cap on rounds|4-5 targeted questions|4-5 questions"` — confirmed clean in command SKILL.md files; the only remaining matches are in `references/right-sizing.md` (OOS for Sprint 9).
- `grep "Want another round to sharpen|or ready to proceed"` — confirmed no remaining bare prompts.
- `grep "^## End-of-Command Handoff"` — confirmed seven files have the section (sdd-guide + the six interview commands).

### v2-verification.md updates

Seven rows updated with `Shipped (sprint/item)` populated:

- FB-007 → sprint 9, item 1
- FB-008 → sprint 9, item 2
- FB-009 → sprint 9, item 3
- FB-010 → sprint 9, item 3
- FB-012 → sprint 9, item 4
- FB-014 → sprint 9, item 5
- FB-015 → sprint 9, item 6

`Verified` boxes stay manual per the maintainer's workflow (`/sdd:build` does not check Verified).

### PRD checkoff

Sprint-9.md's `[x]` checkboxes were updated as each item closed. PRD AC checkoff in `docs/prd.md` is a separate workflow step (the sprint file is the gating record for Sprint 9; the maintainer reconciles into the PRD per Bug Fix 2's PRD state-tracking flow during a later `/sdd:plan` PRD scan).

### New concerns surfaced during build

None that rise to "Open concern" status. Three forward-looking observations:

1. **The "wired vs. authored" gap was larger than planning anticipated.** Four of six items were partially or fully authored already; the build's actual work was wiring and verification. This is consistent with the planning note that "roughly half the work is already done in references" — but it was closer to two-thirds. Future planning should audit the "already done in reference but not wired" state explicitly when scoping a sprint.

2. **`references/right-sizing.md` drift on round-size language.** Deepening-rounds.md now says "5 default, up to 10"; right-sizing.md still says "4-5 questions" in two places. Sprint-9-OOS, but the right-size sprint candidate should fold this audit in.

3. **No autonomous-mode checkpoint pauses fired this session.** The "work without stopping for clarifying questions" directive read as a blanket no-pause directive, including the every-3-4-items autonomous checkpoint. Worth noting at `/sdd:retro` — does the user want the checkpoint to fire anyway as a status checkpoint (no input required, just visibility), or is the directive meant to suppress it entirely?

### Wrap-up state

- `docs/project-state.json` `lastCommand` updated to `/sdd:build`. `currentSprint` stays at 9 (sprint not yet rolled — that happens at the next `/sdd:plan`).
- `docs/v2-verification.md` `Shipped` column populated for the seven FB rows.
- `docs/sprint-9.md` — all six items show `Status: [x] Complete`; every AC checkbox is `[x]`.
- No commit performed (per AGENTS.md "Don't auto-commit during /sdd:build").

### Recommended next command

Sprint 9 closed all 24 in-scope ACs. The natural next moves (in priority order):

1. **`/sdd:discovery` implementation** (10 ACs `aaah`–`aaaq`) — Sprint 9 unblocks per `aaaj`. The biggest leverage move next.
2. **`/sdd:scope` right-size detection + cross-command right-sizing** (8 ACs `aabr`–`aaby`) — bundles right-sizing.md content audit (per drift observation 2 above) with cross-command application.
3. **`/sdd:prd`-only polish** (9 ACs `aacd`–`aacl`) — single-file work, low-risk small standalone sprint.

Maintainer decides at next `/sdd:plan`. Cached-marketplace plugin caveat continues — Sprint 9's cross-cutting behavior hits disk in `plugins/sdd/` but is not live as agent behavior until the maintainer reinstalls. First post-Sprint-9 dogfood of an interview command exercise (e.g., `/sdd:plan` or `/sdd:refine`) is the first production verification of the cross-cutting layer.
