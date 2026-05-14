# Project Retrospective

> Migrated from the hackathon plugin's `reflection.md`. The original was an AI-generated reflection produced at hackathon close — restructured here under the SDD `/sdd:retro` template. Original disclaimer preserved below.

> **Note:** This feedback is AI-generated and experimental. It's meant to surface useful observations, not to grade you. Take what resonates, ignore what doesn't. Your own sense of what you learned matters more than anything here.

## Project Metadata
- **Project:** Spec-Driven Development Plugin
- **Total sprints:** 1 (16 items, autonomous mode, plus 1 iteration cycle for distribution setup)
- **Date range:** 2026-04-03 to 2026-04-27
- **Date of retro:** 2026-04-27
- **Built using:** the hackathon-in-a-plugin curriculum (the predecessor of SDD itself)

## Process Patterns Across Sprints

### What the Process Notes Revealed
A consistent pattern across every planning phase: Jason drove the architecturally significant decisions himself, often surfacing them before the agent did. The open-concerns mechanism (introduced during `/spec`) came from him digging through prior session outputs and finding lost △ feedback. The `/plugin` → `/feedback` rename came from him spotting the naming collision with Claude Code's built-in `/plugin`. The 10+ epics / 5+ unvetted thresholds for PRD health came from him rejecting line count as too arbitrary. The surgical context loading for `/build` came from his auto-compaction experience on a parallel hackathon project. Two deepening rounds at every planning step, both productive every time.

### Planning Phase Assessment
- **Scope was strong on cuts.** "What's Explicitly Cut" named six real positions (hackathon framing, sycophantic tone, time-boxing, cross-agent portability as primary concern, GitHub issue template design, self-improvement automation), each with rationale. Articulating what you're explicitly *not* doing is harder than what you are.
- **Scope leaked into spec territory.** "Loose Implementation Notes" mixed `gh` CLI choice, file paths, and progressive CLAUDE.md generation strategy into scope. Small leak; in a team setting, this kind of bleed creates premature implementation commitment before requirements are nailed down.
- **PRD earned its expansion.** Testable checkboxes throughout. Three cross-cutting epics (Command Explanations, Living Documents, Process Notes) captured invariants that would otherwise scatter across feature epics. Explicit thresholds (10+ epics → phase-split, 5+ unvetted → scope creep). Decision to make the PRD itself double as progress tracker pays off in `/sprint` and `/refine`.
- **Soft acceptance criteria are a risk for an MIT distribution.** "Enough context to pick this up cold months later" relies on personal judgment — fine for self-use, weaker for outside contributors. Future projects shipping under MIT should anchor judgment criteria with examples or anti-examples.
- **Spec achieved full PRD↔spec traceability.** Every command section says "Implements `prd.md > [Epic]`" and means it. Five "Key Technical Decisions" each have rationale + tradeoff explicitly stated.
- **Spec/runtime duplication is a maintenance liability.** The 935-line spec partially overlaps with files that ended up *inside* the plugin (`context-loading.md`, `living-documents.md`). When the spec is also runtime, duplication compounds. Future versions: spec compresses to "what + why" and lets in-plugin reference files own the "how."

### Sprint Cadence
Single sprint, 16 items, autonomous mode with checkpoints every 3-4 items. The 8-12 item hackathon guideline was deliberately rejected — Jason wanted granular items with `/build` between each. Sequencing followed the natural dependency chain (shared core → manifests → commands in workflow order). Two items got split mid-planning (item 1 scaffolding, /build into step-by-step + autonomous) because they were too large for a single session. All 16 shipped.

### Workflow Friction
Post-sprint commands felt heavy. `/reflect` as two-phase + recommend-`/refine` + recommend-`/retro`, plus `/refine` as a separate compressed-interview step, was a lot of ceremony for a workflow Jason knew he'd run himself. A PRD-time criterion like "this command takes under two minutes when nothing notable happened" would have caught it then instead of three projects later. **The cleanest sign that in-flight mechanisms work: the only friction noticed was at boundaries.** Surgical context loading, `/feedback`, and the open-concerns mechanism earned their keep silently — only the meta-on-meta retrospective layer at sprint boundaries felt like overhead.

### Communication Patterns
Strong. Jason called out sycophancy directly when the agent slipped, and expanded the rule to include the AI's right to disagree — not something the agent had raised. Pushbacks were sharp but selective. He let agent recommendations stand on autonomous build mode and the 3-4 item checkpoint cadence. The spec covers stop-revert-revise but doesn't define what counts as a "clean state" for revert — a senior dev who's been bitten before could have pushed harder there. Pattern: not every decision needs interrogation, but worth noticing when one slides past unexamined.

## Product Assessment

### Outcome vs. Intent
Stated goal: deepen understanding of spec-driven development by building tooling for it. Achieved. The proof isn't the working plugin — it's that **understanding evolved during the planning**:
- The sprint concept emerged in `/prd` (Jason proposed it).
- The open-concerns mechanism emerged in `/spec` (discovered the gap by digging through old session outputs).
- The plugin self-improvement loop emerged in `/scope`.
Building the tool forced discovery of parts of the methodology, not just encoding what was already known. And now the tool is being used to redesign the tool — that closes the loop.

### Scope Changes
Disciplined. The "What we'd add with more time" list (GitHub integration, Jira/Linear, multi-agent, automated improvement) didn't creep into v1. Real adaptation captured in process notes: the GitHub API push froze and Jason pivoted to git CLI without re-scoping. The `/plugin` → `/feedback` rename was the only command-chain change of consequence post-PRD.

### Quality Assessment
- **Strengths:** PRD↔spec traceability, surgical context loading design with explicit rationale + tradeoff, open-concerns mechanism solving a real gap discovered through use, clean dependency-ordered checklist, process notes that capture every active-shaping moment.
- **Weaknesses:** Spec/runtime duplication, soft acceptance criteria for MIT distribution, post-sprint command ceremony.

### Technical Debt
Low. The architectural decisions are explicit enough that future refactors have a clear starting point. The biggest debt item is the spec/in-plugin duplication — addressable when the spec is next revised.

## SDD Workflow Performance

### What Worked
- **Surgical context loading for `/build`.** The standout. Has the design (per-command loading table), the rationale (auto-compaction experience on a parallel project), and the explicit tradeoff (narrower per-item context, mitigated by CLAUDE.md as architectural summary).
- **Open-concerns mechanism.** Carries unresolved questions across phase boundaries that would otherwise evaporate with conversation context. Validated through use — concerns from `/spec` were resolved as embedded acceptance criteria during `/checklist`.
- **`/feedback` command.** In-flow capture without interrupting current work. Used in this very project to flag the post-sprint friction that's now driving redesign.
- **Two deepening rounds at every planning step.** Productive every time.
- **PRD-as-progress-tracker.** Single source of truth for what's done vs. open vs. unvetted.

### What Didn't Work
- **Post-sprint command ceremony.** `/reflect` (two-phase) + recommend `/refine` + recommend `/retro`, plus `/refine` as a separate compressed-interview step, is a lot of structure for one person running through their own workflow.
- **Spec/runtime duplication.** Reference files inside the plugin restate spec content. When the plugin *is* the artifact, the spec compresses to "what + why" and the in-plugin files own the "how."

### Workflow Recommendations
- Collapse post-sprint commands into a single more flexible command. (Already underway — Jason is using SDD to redesign SDD.)
- Spec should compress when the runtime artifact restates the same content.
- For MIT distributions, anchor soft acceptance criteria with examples or anti-examples.
- Add a "this command takes under N minutes when nothing notable happened" criterion at PRD time for any command in the sprint loop.

## Plugin Feedback Summary

No formal `docs/sdd-feedback.md` was logged during this project — the friction observations above came directly out of `/reflect` and `/iterate` rather than the dedicated feedback command. The friction-at-boundaries pattern is the single biggest piece of feedback, and it's already being acted on through the SDD-redesigning-SDD loop.

### Feedback Themes
- **Boundary friction is overhead; in-flight friction is invisible.** The post-sprint commands are the one part of the workflow that interrupted flow noticeably. Everything that ran in-flight (context loading, `/feedback`, open-concerns) earned its keep without being noticed.

### How to Act on Feedback
The feedback is already being acted on. Jason is now using the SDD plugin itself to redesign the SDD plugin — the self-improvement loop sketched during `/scope`, actually running. The cleanest way to take this further: switch into the plugin's own repo (`zabuuq/sdd-plugin`), run `/sdd:scope` against the redesign, and let the workflow process its own friction.

## Open Concerns — Final Status

### Resolved During Project
- **Global user profile location** — Resolved during `/spec` → `~/.claude/sdd-user-profile.json`.
- **Per-project state file format** — Resolved during `/spec` → JSON (`docs/project-state.json`).
- **PRD size threshold** — Resolved during `/spec` → 10+ epics → phase-split, 5+ unvetted → scope creep.
- **Seed file format** — Resolved during `/spec` → structured markdown consumed by `/sdd:scope`.
- **Seed file detection** — Resolved during `/checklist` → convention is `docs/seed.md`. Embedded as Item 5 acceptance criterion.
- **Plugin-level CLAUDE.md content** — Resolved during `/checklist` → minimal pointer that loads sdd-guide. Embedded as Item 1 acceptance criterion.
- **Communication style enforcement** — Resolved during `/checklist` → sdd-guide core reads style from user profile and treats it as authoritative. Embedded as Item 2 acceptance criterion.

### Carried to Retro (Unresolved)
- **What counts as a "clean state" for `/build` revert.** Spec covers stop-revert-revise but doesn't define the revert anchor precisely. Worth pinning down before the next sprint loop.
- **Post-sprint command ceremony.** Acknowledged friction. Being addressed via the SDD-redesigning-SDD loop.
- **Spec/runtime duplication.** Acknowledged debt. To be addressed when the spec is next revised.

## Key Takeaways

### Top Wins
1. **Understanding evolved during planning.** The sprint concept, open-concerns mechanism, and self-improvement loop all emerged from doing the work, not from prior knowledge encoded into it.
2. **Surgical context loading and the open-concerns mechanism.** Both grounded in real pain (auto-compaction, lost △ feedback) and both validated through use in this very project.
3. **Discipline on scope.** "What we'd add with more time" stayed out of v1. Distribution shipped (`zabuuq/sdd-plugin`, validated on three downstream projects).

### Top Lessons
1. **Friction at boundaries gets noticed because it interrupts; friction in-flight gets noticed because it's *not* there.** The cleanest signal a mechanism is working is its absence from your conscious attention. Surgical loading, `/feedback`, open-concerns — all silent. Post-sprint ceremony — loud.
2. **For an MIT distribution, soft acceptance criteria need anchors.** Outside contributors can't apply your judgment.
3. **When the spec is also the runtime, duplication compounds.** Compress the spec to "what + why" and let the in-plugin reference files own the "how."

### If Starting Over
- Add a "fast-path when nothing notable happened" criterion to every sprint-loop command at PRD time, so the post-sprint ceremony gets pre-empted by a five-line skip path.
- Push how-decisions out of scope and into spec, even when the answer feels obvious — that discipline matters more in team settings than solo.
- Plan from the start that the spec compresses when the artifact is itself instructional (a plugin made of markdown).
- Make more of the pushbacks. The selective ones were sharp; the unmade ones (autonomous mode, 3-4 item checkpoint cadence, "clean state" definition) might have been worth interrogating too.

## Reflection

**What surprised most:** the friction around sprint reflection.

That answer landed three different ways in conversation: as the countermeasure that didn't pull its weight, as the planning decision worth pushing harder on next time, and as the surprise itself. Three angles, same observation — that's a real signal, not a passing thought.

What's interesting about *which* part felt heavy is that nothing in planning or build mechanics did. The in-flow parts of the workflow earned their keep. Only the meta-on-meta retrospective layer at sprint boundaries felt like overhead. That maps to a general pattern worth carrying forward: **boundary friction is conspicuous, in-flight friction is invisible**. The cleanest proof the in-flight mechanism works is that the boundary friction was flagged via `/feedback` itself.

The action is already in motion. The plugin is being redesigned using itself. That closes the self-improvement loop sketched during `/scope`.

## Milestone Completion (carried over)

- /scope: complete
- /prd: complete
- /spec: complete
- /checklist (now /sprint): complete (16/16)
- /build: 16/16 in autonomous mode
- /iterate: 1 cycle (distribution: repo split, marketplace publish, validated on 3 downstream projects)
- /reflect: complete
