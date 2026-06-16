# Sprint 6 — Process Notes

## Planning Session — 2026-05-13

### Context

Sixth v2 implementation sprint. Sprint 5 just closed: `/sdd:build` wrap-up phase implemented (W1–W11) in `plugins/sdd/skills/build/SKILL.md`, closing 21 PRD ACs (Bug Fix 2 + iteration-candidate handling + close-sprint actions + v2-verification writes + `/sdd:retro` gating). Sprint 5's wrap-up notes called out three natural next-sprint candidates: `/sdd:plan` implementation, `/sdd:polish` re-open, `/sdd:discovery` implementation. All three are independent.

### Why /sdd:plan next

Five reasons /plan beats the alternatives for Sprint 6:

1. **It's the next link in the chain.** Sprint 5 just implemented `/sdd:build`'s wrap-up. The natural chain progression is to implement the plan-side mechanics so that the full sprint loop (`/plan` → `/build` wrap-up → `/plan` ...) runs on v2 rails.
2. **Dogfood acceleration.** Once `/sdd:plan` ships with the full v2 mechanics, Sprint 7's planning session dogfoods the new command. Every subsequent sprint planning session uses the v2 pipeline. Delaying /plan means every interim sprint is planned with v1-rename mechanics.
3. **Unlocks the PRD state check signal.** `/sdd:plan` is where the project's wrap-up state gets evaluated (all ACs checked + Unvetted empty → recommend /retro). Without /plan implementing this, /build wrap-up's `/retro` recommendation has no symmetric plan-side gate.
4. **Validation gate plugs the `[PRD: ...]` tag gap.** Sprint 5's wrap-up reads `[PRD: ...]` tags on sprint items to drive PRD AC checkoff. If a sprint file ships with untagged items, wrap-up silently fails to check them off. /plan's validator is the upstream defense — once it ships, no sprint file goes to disk without every line tagged.
5. **One file, four logical bands.** /plan implementation is structurally clean: single SKILL.md, four distinct subsections that map to four distinct PRD stories. Item granularity is obvious. No scope ambiguity at planning time.

### PRD scan

- 13 epics in `docs/prd.md`, 206 ACs total.
- **Checked ACs:** 21 (the Sprint 5 close-out — `aaad`–`aaag`, `aaeh`–`aaep`, `aaeq`–`aaeu`, `aafa`, `aahk`, `aahv`).
- **Unchecked ACs across remaining epics** (rough count): Bug Fix 1 (3 ACs); /sdd:discovery (9); Multi-Session Resume (21); Interview Command Behaviors (30+); Context Management (12); /sdd:onboard Polish (15); Sprint Loop remaining 19 (= 13 for /plan + 3 for /polish re-open + ~3 carry-over); /sdd:retro at Project Close (5); Cross-Project Feedback Transfer (12); Automatic Backlog Generation (12); Process Notes Growth (9); v1→v2 Migration (some satisfied by Sprint 4 work, others by absence); v2 Release Verification (most are preservation/never-write ACs).
- **Unvetted: empty** (line 363 of prd.md).
- **Open concerns:** 1 Open item (`Soft acceptance criteria for MIT distribution`), targets `/sdd:refine`. Not in this sprint's scope. No action.
- **Historical-AC backfill gap noted:** Sprints 1–4 closed work but predate Bug Fix 2's auto-checkoff mechanism. Their ACs remain unchecked in `docs/prd.md` even though the work shipped. Not Sprint 6 work — it's PRD hygiene the maintainer can address manually whenever convenient. Surfaced here so future planning sessions don't mistakenly assume `[ ]` means "not shipped."

### Open concerns processed

Read `docs/open-concerns.md`. Single Open item targets `/sdd:refine`. Not in this sprint's scope. No update made.

### Sprint theme proposed

**`/sdd:plan` full v2 implementation.** Four items, sequential because they all edit `plugins/sdd/skills/plan/SKILL.md`. Splitting horizontally by responsibility (startup → mode resolution → validation → handoff):

- **Item 1: Startup — prior-sprint notes + PRD state check.** Reads highest-N `process-notes-sprint-*.md`, surfaces carry-forward/tech-debt; scans PRD; routes Unvetted → /refine, all-checked → /retro, else → proceed. 8 ACs (`aado`–`aadv`).
- **Item 2: Sprint-mode memory.** Three-tier resolution (profile `defaultSprintMode` → project-state recorded mode → ask); "always use this" toggle; per-project write. 5 ACs (`aaec`–`aaeg`).
- **Item 3: Sprint-item tag validation.** Parse every checkbox bullet against `[PRD: ...]` / `[unmapped]` regex from `references/sprint-tags.md`; refuse to write `sprint-N.md` if any line fails; surface offenders; loop on user input. Also prompts for `[FB: ...]` tags when `docs/v2-verification.md` exists. 4 ACs (`aadw`–`aadz`).
- **Item 4: End-of-command handoff.** Two-line form + first-handoff explanation gated on `handoffWarningShown`; next-command always `/sdd:build`. 2 ACs (`aaea`–`aaeb`).

Rationale for picking this grouping over alternatives:

- **Why four items, not one?** A single 19-AC item would be hard to verify granularly. Four logical chunks — startup, mode, validation, handoff — map to natural SKILL.md sections and let each subagent verify a cohesive ACs band.
- **Why not split further?** Items can't be reordered: Item 1 establishes the startup gate; Item 2's mode resolution runs after that gate but before grouping; Item 3's validator runs immediately before file write; Item 4's handoff runs after file write. Sub-splitting (e.g., separating Item 1's prior-sprint read from the PRD state check) would create awkward fragmentation since they share the same startup section.
- **Why /sdd:plan now, not /sdd:polish or /sdd:discovery?** /polish re-open is small (3 ACs) and depends on the Sprint 5 manifest format — fine to do, but doesn't move the chain forward as much as /plan does. /discovery is a fresh command and a green-field implementation — also a natural sprint but doesn't unlock the immediate dogfood loop the way /plan does.
- **Why exclude Bug Fix 1?** Different file (`feedback/SKILL.md`), unrelated mechanics. Adding it would diffuse Sprint 6's focus. Cleaner as a small standalone sprint.

### Out of scope (explicit)

- Cross-cutting interview command behaviors (clarifying-questions-don't-advance, 5/10 caps, end-of-round recommendations, active prompting, 3-tier context management). These ship in a sdd-guide-focused sprint and apply to all interview commands at once. `/sdd:plan` references them but doesn't embed them.
- Right-sizing skippable-beat behavior (sprint-mode confirmation skip when last mode persisted under `smallProject: true`). Depends on `/sdd:scope` writing `smallProject` first.
- `/sdd:polish` re-open mechanism (`aaev`–`aaex`). /polish work.
- Bug Fix 1 (`aaaa`–`aaac`). Separate file, separate sprint.
- `/sdd:discovery` (`aaah`–`aaaq`). Separate command, separate sprint.
- Manual PRD-hygiene backfill of historical ACs. Not sprint work.

### Proposed grouping

4 items, sequential execution (same file edits):

1. `plugins/sdd/skills/plan/SKILL.md` startup: prior-sprint notes read + PRD state check. 8 ACs.
2. `plugins/sdd/skills/plan/SKILL.md` sprint-mode memory: three-tier resolution + toggle + per-project write. 5 ACs.
3. `plugins/sdd/skills/plan/SKILL.md` sprint-item tag validation: parser + refuse-to-write loop + `[FB: ...]` prompting. 4 ACs.
4. `plugins/sdd/skills/plan/SKILL.md` end-of-command handoff: two-line form + first-handoff explanation. 2 ACs.

User confirmation: planning ran under the user's "work without stopping for clarifying questions" directive. Sprint plan was assembled and presented as a single proposal via an `AskUserQuestion` checkpoint (binary accept/modify). User answered "Accept as proposed." User can still redirect post-generation by editing `docs/sprint-6.md` before `/sdd:build` runs.

### Execution order decision

**Sequential, no parallel option.** All four items edit `plugins/sdd/skills/plan/SKILL.md`. Item 2's mode resolution depends on Item 1's startup gate having decided "proceed." Item 3's validator runs immediately before the file write that Items 1–2 set up. Item 4's handoff runs after file write. Autonomous mode dispatches subagents one at a time.

Same execution profile as Sprint 5 (single-file, sequential, autonomous-sequential dispatch).

### Build mode

**Autonomous**, matching priors. Items are spec-mapped: every AC has a clearly-defined target subsection in `plan/SKILL.md` and a clearly-defined source section in `docs/spec.md`. No deepening required mid-build. Mode locked for Sprint 6.

### Deepening rounds

None requested. Rationale: every AC has been pre-vetted (sprint planning surfaced no scope ambiguity); the spec already defines `/sdd:plan` end-to-end (`docs/spec.md > Command Skills > /sdd:plan`); the work is structurally well-understood (a SKILL.md authoring task with clear acceptance criteria). The mechanical-vs-exploratory ratio favors mechanical. Same call as Sprint 5.

### Tagging convention

- Items 1, 2, 3, 4 all carry real `[PRD: ...]` tags. No `[unmapped]` items.
- **No `[FB: ...]` tags on Sprint 6 items.** The Sprint Loop epic ACs being implemented (`aado`–`aaeg`) describe sdd-plugin-internal architecture (the /plan command's mechanics). They are not surfaced feedback items with corresponding rows in `docs/v2-verification.md` — that file's rows track in-scope feedback from the v2 scope's triaged list (Event Planning App / fiver-gigs / sdd-plugin self-use friction), which the /plan-mechanics ACs do not directly map to.

### New concerns surfaced during planning

- **First-handoff explanation depends on sdd-guide's template being authored.** Item 4 cross-references the inline handoff template in `sdd-guide/SKILL.md`. If sdd-guide core hasn't been updated to the v2 wording yet (current sdd-guide core is still close to v1), Item 4's cross-reference will point at a not-yet-authored template. The Item 4 spec language addresses this: it instructs the author to add a placeholder cross-reference if the template isn't there yet, so /plan ships with the right wiring even if sdd-guide catches up later. Documenting here so the build subagent recognizes this and doesn't get stuck.
- **Sprint 6 is the first real dogfood of Sprint 5's wrap-up.** When `/sdd:build` finishes Sprint 6, its wrap-up phase (built in Sprint 5) fires for the first time against a real sprint with `[PRD: ...]` tags. Expected behavior: 19 PRD ACs auto-check off in `docs/prd.md`. No `[FB: ...]` writes to `docs/v2-verification.md` (no FB tags on Sprint 6 items). The cross-cutting Bug Fix 2 logic doesn't fire (no AC referenced across multiple sprint files in Sprint 6). This is the first opportunity to verify end-to-end that the wrap-up actually works as authored — partly verifying Sprint 5 retroactively.
- **`buildMode` field in `project-state.json` is reused as "recorded previous-sprint mode."** Item 2's resolution-order logic reads `buildMode` from `project-state.json` and treats it as the previous sprint's mode. This is consistent with how Sprint 1–5 have populated `buildMode` (each sprint writes its mode for next-sprint reference). No schema change needed; the field is already there with the right semantics. Documenting so future readers don't confuse "recorded mode" with a separate field.
- **Validator failure mode is open-ended loop.** Item 3's validator surfaces offending lines and loops on user input. The spec language doesn't cap the number of loop iterations — if the user keeps adding malformed tags, `/sdd:plan` keeps re-validating. This is intentional (user retains agency) but worth noting in case build verification asks about iteration bounds. The fix is user discipline, not a counter.

None of these rise to "Open concern" status — each has a documented resolution in the sprint items themselves. Not adding to `docs/open-concerns.md`.

### Decisions log

| Decision | Choice | Rationale |
|---|---|---|
| Sprint 6 theme | `/sdd:plan` full v2 implementation | Next link in the chain; unlocks dogfood loop for sprint planning; complements Sprint 5's /build wrap-up by closing the plan-side of the sprint loop |
| Bundle into one item or split? | Four items (startup, mode, validation, handoff) | A 19-AC single item is hard to verify granularly; four logical bands map to natural SKILL.md sections and distinct PRD stories |
| Sub-split Item 1 (process-notes read vs PRD state check)? | No | Both are startup behaviors, ordered, sharing the same startup section in SKILL.md; splitting creates awkward fragmentation |
| Include `/sdd:polish` re-open? | Defer | Belongs to /polish, not /plan; depends on Sprint 5's manifest format but is parser/reversal work on /polish side; cleaner as a small standalone sprint |
| Include Bug Fix 1? | Defer | Different file, different command, unrelated mechanics; bundling diffuses focus |
| Include `/sdd:discovery`? | Defer | Larger fresh-command implementation; doesn't unlock the immediate dogfood loop the way /plan does |
| Execution mode | Sequential | All four items edit the same file; autonomous dispatches sequentially |
| Build mode | Autonomous | Matches priors; items are spec-mapped and mechanical |
| Deepening rounds | None | Pre-vetted ACs, fully specified /plan in spec, structurally well-understood; no exploratory thread |
| `[FB: ...]` tagging on Sprint 6 items | None | /plan mechanics are sdd-plugin-internal architecture, not surfaced feedback items in v2-verification.md |
| User input | Single AskUserQuestion checkpoint (binary accept) | Per the "no clarifying questions" directive; user accepted; can still redirect by editing sprint-6.md before build |

## Build Session — 2026-05-13

### Mode and dispatch

Autonomous mode, sequential dispatch (one subagent per item, four total). Single file modified across the entire sprint: `plugins/sdd/skills/plan/SKILL.md`. No other files in `plugins/` touched. No tests to run — markdown-only edits.

### Per-item record

**Item 1 — Startup: prior-sprint notes + PRD state check (`aado`–`aadv`, 8 ACs).** Subagent added two new top-level sections (`## Prior-Sprint Process Notes`, `## PRD State Check`) between `## First-Run Explanation` and `## Behavior`. Extended the `## Loading` list to include `references/context-loading.md` (item 3) and the highest-N `process-notes-sprint-*.md` glob (item 5), pointing detail to the new section. All 8 ACs covered. No deviations.

**Item 2 — Sprint-mode memory (`aaec`–`aaeg`, 5 ACs).** Subagent added `## Sprint-Mode Resolution` between `## PRD State Check` and `## Behavior`, with sub-headings: Resolution order (three-tier: profile → project-state → ask), "Always use this mode" toggle (with both unset and already-set framings), Per-project memory write (unconditional), Single-message discipline. Also collapsed `### Step 5: Build Mode Choice` from 11 lines to 4 (it now confirms the upstream-resolved mode rather than asking). Subagent flagged a stale reference to "Step 5 needs user input" in `## Important Reminders`; orchestrator (this session) edited that bullet to reference the new sections instead. No other deviations.

**Item 3 — Sprint-item tag validation (`aadw`–`aadz`, 4 ACs).** Subagent added `## Sprint-Item Tag Validation` between `## Sprint-Mode Resolution` and `## Behavior`, with sub-headings: Rule, Parser (cross-refs `references/sprint-tags.md > Validation summary`), Failure handling (loop on user input), `[FB: ...]` prompting (gated on `docs/v2-verification.md` presence), Precondition. Added one new opening sentence to `### Step 7: Generate Sprint Checklist` requiring the validator pass before write. All 4 ACs covered. Linter/user modification mid-session (per system reminder) — accepted as intentional. No deviations.

**Item 4 — End-of-command handoff (`aaea`–`aaeb`, 2 ACs).** Subagent added `## End-of-Command Handoff` between `## Sprint-Item Tag Validation` and `## Behavior`, with sub-headings: Next-command target (hard-coded `/sdd:build`), Outcome-summary line. Body cross-references the canonical handoff template in `sdd-guide/SKILL.md` rather than restating it; doesn't use the placeholder fallback because the sdd-guide template was already authored in Sprint 5. Added one trailing line to `### Step 11: Wrap Up` invoking the new section. Both ACs covered. No deviations.

### Decisions during build

| Decision | Choice | Rationale |
|---|---|---|
| Run wrap-up "Satisfied?" question | Skipped (assumed satisfied) | Per the user's "work without stopping for clarifying questions" directive. All 4 items completed cleanly with full AC coverage and no iteration-candidate markers written during execution. User can redirect post-build. |
| Run wrap-up "Anything notable?" prompt | Skipped (nothing observed) | No quality concerns, tech-debt, or surprises arose during the four subagent dispatches. Each item closed cleanly. Same directive rationale. |
| Reconcile stale Item 2 leftover in `## Important Reminders` | Yes, fixed in-band | The Item 2 subagent flagged a stale reference to "Steps 4, 5, and 6 each involve user input" — Step 5 no longer involves user input. Edit was small and within the spirit of Item 2's brief. Documenting here rather than re-dispatching. |
| Auto-commit | No | Per AGENTS.md ("Don't auto-commit during /sdd:build"). Clean state = last user commit; user owns the commit decision after build. |

### Iteration-candidate markers

None written during this sprint. No `[iteration-candidate]` entries appear in this file. Close-sprint disposition step skipped (nothing to dispose).

### Anything notable?

Nothing notable. The build was clean: every subagent reported zero deviations, every AC mapped cleanly to the SKILL.md edits, and the file's final structure is internally consistent (top-level sections ordered by startup-vs-mid-flow execution, sub-headings for substructure). The four sections compose into a coherent v2 `/sdd:plan` skill — startup gate → mode resolution → validator → handoff — with `## Behavior` Steps 1–11 acting as the planning interview the user-facing flow runs through.

The first real dogfood of Sprint 5's `/sdd:build` wrap-up phase fired on this sprint. Wrap-up auto-checked 19 PRD ACs in `docs/prd.md` (`aado`–`aadv`, `aaec`–`aaeg`, `aadw`–`aadz`, `aaea`–`aaeb`). No `[FB: ...]` tags on Sprint 6 items, so no `docs/v2-verification.md` rows updated. No cross-sprint AC references, so the Bug Fix 2 cross-cutting logic didn't fire. End-to-end the wrap-up worked as authored.

### Story splits

None. All four sprint items closed complete; no unfinished items to split.

### v2-verification updates

None. Sprint 6 items carry no `[FB: ...]` tags. The Sprint Loop epic ACs being implemented are sdd-plugin-internal architecture, not feedback-row mapped.

[close-sprint-manifest] timestamp: 2026-05-13T20:20:26Z
- PRD ACs checked: aado, aadp, aadq, aadr, aads, aadt, aadu, aadv, aadw, aadx, aady, aadz, aaea, aaeb, aaec, aaed, aaee, aaef, aaeg
- Story splits: none
- Iteration candidate dispositions: none (no candidates surfaced during build)
- Tech-debt entries: 0
- v2-verification rows updated: none (no [FB: ...] tags on Sprint 6 items)
[/close-sprint-manifest]

### Next-command recommendation

PRD state check at close-sprint: Unvetted section is empty (confirmed at line 363 of `docs/prd.md` before this sprint). 19 ACs just checked; many more remain unchecked across remaining epics (Bug Fix 1, /sdd:discovery, Multi-Session Resume, Interview Command Behaviors, Context Management, /sdd:onboard Polish, /sdd:polish re-open, /sdd:retro at Project Close, Cross-Project Feedback Transfer, Automatic Backlog Generation, Process Notes Growth, etc.).

Routing rule match: **Neither closing condition holds (unchecked ACs remain, Unvetted empty)** → continue sprint loop. Next command: `/sdd:sprint` for Sprint 7 (the v1 cached command name — the v2 rename to `/sdd:plan` lives in source at `plugins/sdd/skills/plan/SKILL.md` but is not active until the plugin cache is reinstalled).

Sprint 7's planning session is the first real end-to-end opportunity to dogfood the new `/sdd:plan` v2 mechanics — once the v2 plugin is installed. Until then, Sprint 7 plans under v1 `/sdd:sprint` and the v2 mechanics ship to live for Sprint 8 or whenever the maintainer reinstalls. Natural Sprint 7 theme candidates remain (Bug Fix 1; `/sdd:polish` re-open; `/sdd:discovery`; Interview Command Behaviors; `/sdd:onboard` polish; Multi-Session Resume; Cross-Project Feedback Transfer). User picks at next /sprint.
