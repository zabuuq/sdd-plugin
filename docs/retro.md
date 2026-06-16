# Project Retrospective

## Project Metadata
- **Project:** SDD v3 — token-efficiency pass
- **Total sprints:** 1 (whole cycle delivered in a single sprint)
- **Date range:** 2026-06-11 to 2026-06-16
- **Date of retro:** 2026-06-16

## Process Patterns Across Sprints

### What the Process Notes Revealed
A single, coherent pattern runs through every phase: **decisive framing up front, then heavy delegation to agent judgment.** The user shaped the cycle at the two moments that mattered — the native-only-vs-Caveman-machinery call in scope, and the single-sprint override in plan — and delegated the rest (PRD and spec were run as agent-authored judgment calls at the user's explicit request: "answer these yourself with what you think is best"). Zero deepening rounds were run in any phase; the small-project close-default fired cleanly each time with no contradicting evidence. The notes also show consistent, honest self-flagging by the agent: the sdd-guide net +205-word cost, the input win landing under the guide-rail, and the dogfood number being an end-of-sprint read were all surfaced rather than smoothed over.

### Planning Phase Assessment
Scope → PRD → spec set the cycle up well. Discovery was skipped (correctly — a small self-improvement cycle). The central planning tension was real and well-resolved: the scope measures success by eyeball against `docs/archive/v2/` with ~23%/~32% as sanity-check guide-rails, but a PRD needs testable ACs — resolved by making every AC structural/observable (artifact diffs, rule-by-rule preservation traces, load-map flags, `claude plugin validate .` exit) and letting exactly one AC (`dfrc`) carry the percentages as "record and note proximity, no requirement to hit." Behavior preservation was promoted to a first-class guarded epic, which matched the project's primary risk (silently dropping a step or rule while trimming). The user confirmed the chain felt right-sized and that delegation was a deliberate speed-and-trust choice, not a symptom of the chain being too heavy.

### Sprint Cadence
The agent proposed a three-sprint split (analysis foundation → trims → verification); the user overrode it to a single sprint of all 10 items / 24 ACs, run autonomously. Confirmed in retro as the right call — and as part of a broader pattern: the user has been pushing more (or all) work into single sprints across multiple projects lately, and it has consistently worked out well. For a small, coherent, single-feature cycle with a clear dependency chain, one sprint avoided artificial seams without costing follow-ability.

### Workflow Friction
Effectively none. The user reported the chain felt right-sized for a small dogfood cycle. The thin PRD/spec phases were thin by the user's choice (delegation for speed), not because the steps were busywork. No phase felt like going through the motions.

### Communication Patterns
Worked well. Because the user delegated most phases, the high-value communication moments were the few real decision points — and the agent surfaced those clearly enough for the user to weigh in where it mattered (native-only, single-sprint). One forward-looking caveat the user raised: the new `## Output Constraints` (terseness + anti-sycophancy) rules did **not** govern most of this cycle, so the live communication change they introduce is still unproven — "this update is going to change things, so we will see."

## Product Assessment

### Outcome vs. Intent
Matches intent. The goal was "cheaper to run, same behavior," targeted at roughly half the Caveman benchmark (~23% input / ~32% output). The dogfood landed at **~15–20% input** (concentrated in templates at −25% plus a per-command startup-load drop) and **~25–35% output**, with the input number under the guide-rail. The user set no firm expectation going in — "any improvement is good" — and the guide-rails were always sanity checks, not gates. Native-only technique-mining held; the failure condition that would have triggered pulling the backlogged executable machinery was not met.

### Scope Changes
Two notable changes, both confirmed as the right calls: (1) **consolidation was dropped entirely** — the load map showed references partition cleanly by phase, so any merge would raise load for a command that loads only one of a pair; the realized trim was instead a clean per-command startup-load removal (`context-loading` dropped from `/sdd:plan`), which cannot raise any command's load; (2) the **plugin version bump was deliberately left out of scope** (no Sprint 1 AC covered it). The user did not want consolidation pushed harder.

### Quality Assessment
Good, per the user. All 24 ACs satisfied, all 10 items closed, `claude plugin validate .` passing, zero-runtime markdown posture intact. Three analysis artifacts produced (`efficiency-techniques.md`, `load-map.md`, `dogfood-comparison.md`) carrying the verification trail.

### Technical Debt
Contained — nothing beyond two known items: (1) `plugins/sdd/.claude-plugin/plugin.json` still reads `2.0.0` (a release-time bump, tracked in open concerns); (2) the output-terseness rules are unverified in live use because the cycle's own artifacts predate them. Both acceptable given the project goals.

## SDD Workflow Performance

### What Worked
- **Right-sizing held end to end.** Small-project detection fired at scope and was re-confirmed (no flip) at every subsequent phase; deepening rounds were correctly zero throughout.
- **Delegation-friendly.** The workflow accommodated the user delegating PRD/spec to agent judgment without losing rigor — ACs stayed testable, the preservation epic stayed guarded.
- **Honest self-flagging.** The agent consistently surfaced costs and shortfalls (the +205-word sdd-guide delta, the sub-guide-rail input win) instead of hiding them.
- **Single-sprint flexibility.** `/sdd:plan` not time-boxing let the whole coherent efficiency pass run as one sprint when the user asked for it.

### What Didn't Work
- Nothing surfaced as friction. The one genuine limitation is structural, not a workflow fault: efficiency was judged by inspection against v2 with no instrumentation, so the headline reduction numbers are qualitative estimates, and the output half remains empirically unconfirmed under the new rules.

### Workflow Recommendations
- **Confirm the output half empirically.** Run a fresh chain under the now-live `## Output Constraints` and compare against v2 to validate the ~25–35% output estimate with the rules actually in force.
- **Offer delegation as a first-class, opt-in mode.** The user wants "agent drafts, I veto" available more often — but explicitly *not* as the default. Worth a future cycle.
- **Keep leaning into larger sprints where the unit is coherent.** Validated again here and across the user's other recent projects.

## Plugin Feedback Summary

`docs/sdd-feedback.md` holds 20 entries spanning three projects (Event Planning App, fiver-gigs, sdd-plugin), logged April–May 2026. v3 was deliberately scoped to token efficiency only, so none of this feedback was acted on in this cycle — it remains input for a future one.

### Feedback Themes
- **Already addressed in v1/v2 (now shipped behavior)** — right-sizing small projects (`references/right-sizing.md`); clarifying questions must not advance the interview (sdd-guide Rule 4); deepening-round topic previews; context management between rounds (`references/context-management.md`); a `/discovery` command (and the scope→discovery / prd→scope rename); multi-session resume via `/sdd:pause` + `/sdd:unpause`; auto-maintained backlog (`docs/backlog.md` + `references/backlog.md`).
- **Still open — workflow-capability candidates for a future cycle:**
  - File uploads during scope and later phases (reference docs scanned into planning).
  - `/sdd:feedback` clobbers `lastCommand`, so consecutive feedback entries lose the real "after command" context.
  - New utility commands: `--help` on every command, `/sdd:sdd` environment setup, `/sdd:backlog` manual add, `/sdd:viability` idea stress-test.
  - `/sdd:reflect` (now `/sdd:refine`) PRD state-tracking gap — fully-complete non-split items never check off their PRD criteria, so the PRD reads incomplete while sprints show done.
  - Sprint-mode preference: "use same mode as last sprint" / "always use this mode."
  - Cross-project feedback transfer — move feedback from a consuming project into the plugin project, runnable from either.

### How to Act on Feedback
This repo *is* the plugin source, so the usual "switch to the plugin project" step is already satisfied. The path forward is a **future SDD cycle** (start at `/sdd:scope` or `/sdd:prd`) targeting the still-open items above, with `docs/sdd-feedback.md` as the input. The open items cluster naturally into a "workflow capability" cycle — new commands plus the feedback/reflect gaps — a clean successor to v3's efficiency pass.

## Open Concerns — Final Status

### Resolved During Retro
- **Plugin manifest version still reads `2.0.0` while v3 is the active cycle** — carried to retro as a tracked **release-time action**, not a defect. `plugins/sdd/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` must move together to the v3 version at ship time (next bump TBD at release). No Sprint 1 AC covered it, so it was correctly left untouched during build. It matters going forward only at the moment v3 is published; it has no effect on behavior before then.

### Carried to Retro (Unresolved)
- None. All entries resolved or accounted for.

### Previously Resolved (during the project)
- **Can native-only technique-mining reach half the Caveman benchmark?** — Resolved at `/sdd:build` (Item 10): in the neighborhood, does not fall short; native-only stays; the trigger to pull the backlogged machinery was not met.
- **Does consolidating a reference produce a net per-command trim?** — Resolved at `/sdd:build` (Item 4): moot — no consolidation performed; references partition cleanly by phase, so the realized trim was a per-command startup-load removal instead.
- **Order of operations across the two artifacts and the trims?** — Resolved at `/sdd:plan`: the single-sprint dependency ordering (inventory → load map → output rules → trims → verification).
- **Which references/templates are over-included per command?** — Resolved at `/sdd:spec`: designed `docs/load-map.md` as the enumerating/​flagging artifact; the list itself was produced when the load map was built.

## Key Takeaways

### Top Wins
1. **The native-only bet paid off.** Meaningful reductions (~15–20% input, ~25–35% output) achieved with zero added runtime surface — the plugin stays pure markdown, validates clean, and never needed the deferred executable machinery.
2. **Right-sizing plus delegation produced a fast, clean cycle.** Small-project detection held throughout, the user delegated the thin phases for speed, and the whole pass shipped in one sprint without losing rigor or dropping a behavioral rule.
3. **Behavior preservation was guarded, not hoped for.** Making it a first-class epic with rule-by-rule preservation traces meant the trim never silently dropped a workflow step or rule.

### Top Lessons
1. **Eyeball-against-v2 measurement gives direction, not precision.** It was the right non-instrumentation call for a small cycle, but it leaves the output half unconfirmed under the rules that now actually govern — a fresh chain run is needed to close that.
2. **Delegation is a tool the user wants on tap, but not on by default.** "Agent drafts, I veto" worked well here for speed; it should be an explicit opt-in, not the standard mode.
3. **A one-time input cost can be the right trade for recurring output savings.** The +205-word sdd-guide delta (driven by the new `## Output Constraints`) is paid once per load but buys terser output on every turn — worth flagging honestly rather than hiding.

### If Starting Over
Little would change. The single defensible "do it differently" is sequencing the dogfood: because v3's own artifacts predate the Output Constraints, the output reduction could only be estimated, not measured. A cleaner design would land the terseness rules first, then run a fresh chain under them as the dogfood baseline — confirming the output half empirically instead of qualitatively. Everything else — native-only, single sprint, delegated middle phases, guarded preservation epic — would be repeated.
</content>
</invoke>
