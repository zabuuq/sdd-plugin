# Process Notes — SDD v3 (Scope)

v2 cycle artifacts archived to `docs/archive/v2/` at v3 start (user directive: archive all v2 data, not just scope). Cross-cycle living docs (`docs/backlog.md`, `docs/sdd-feedback.md`) kept live in `docs/` as the v3 seed; snapshots also placed in the archive.

## /sdd:scope (v3)

### Cycle framing
- **Startup oddity:** `/sdd:scope` invoked on a repo holding a *completed* v2 cycle (lastCommand `/sdd:retro`, sprints 1–18 done). Confirmed with user before overwriting: intent is a new **v3** cycle. User chose "archive all v2 data." Executed full archive + fresh project-state.

### Beat 1 — The idea
- **v3 spine = token efficiency.** Not the capability expansion the backlog implied (share-back, new commands, GSD/Superpowers eval). A deliberate pivot to making the plugin cheaper to run.
- **Drivers (user's words):**
  - Newer Claude models make token efficiency matter more; user wants to run at *higher effort levels* without exhausting the token budget.
  - The plugin returns a lot of unnecessary context — sycophantic padding ("that's a great job/idea"), restating, over-explanation.
- **Two prongs identified:**
  1. Reduce the plugin's own input-token footprint (per-command SKILL + reference-file load — v2 grew this stack substantially; verbose templates; real-time process notes; re-read artifacts).
  2. Reduce agent *output* verbosity (terse responses, kill sycophancy).
- **Benchmark target:** the Caveman plugin (https://github.com/juliusbrussee/caveman). User wants SDD to run at comparable efficiency.
  - Caveman = presentation-layer constraint skill + Claude Code hook (auto-activation flag) + `caveman-compress` memory-file compressor (~46% input cut) + `caveman-shrink` MCP middleware (compresses tool descriptions). ~65% output-token reduction, accuracy preserved. MIT, standalone, composable.
- **Strategic option raised by user:** apply efficiency *rules natively* to the SDD plugin AND/OR use Caveman *in conjunction* (compose at runtime) rather than purely copying. Native-vs-dependency boundary is an open scope decision (plugin currently has zero runtime deps — pure markdown).
- **DECIDED — Native only (option a).** v3 bakes terseness rules into sdd-guide and trims the reference stack; Caveman is a benchmark/inspiration, not a dependency. Rationale: preserves the plugin's zero-dependency, pure-markdown posture — no external runtime, no install step, no tech-group coordination. Caveman is studied for *what* to compress, not wired in to do the compressing.
- **DECIDED — backlog firmly out.** Deferred v3 backlog items (share-back, new commands, GSD/Superpowers eval) are out of scope for v3. Token efficiency is the sole spine. Items stay in `docs/backlog.md` for a future cycle; not folded into v3.
- **Refinement on Caveman boundary.** User wants to pull as much from Caveman as possible — incl. forking/downloading the repo to do so. Clarified the line: Caveman = constraint skill (markdown-portable) + hook + `caveman-compress` script + `caveman-shrink` MCP middleware (executable machinery). **DECIDED — v3 mines techniques and reimplements them natively** (constraint rules + compression heuristics → sdd-guide markdown); leaves the hook/MCP/scripts behind to preserve the pure-markdown, zero-runtime posture. Forking/downloading Caveman to *study* it is in-bounds for v3.
- **DEFERRED to backlog** — "Pull Caveman's executable machinery into SDD (hook/MCP/scripts)." User explicitly asked to add it and to research feasibility later. Entry written to `docs/backlog.md`.

### Beat 2 — Who it's for
- **Audience = SDD plugin's own users, unchanged from v2.** No audience shift in v3. Efficiency gains benefit the same group (Jason + tech-group members running SDD on their projects); plugin continues to dogfood on itself. No new persona work needed.

### Beat 4 — Goals
- **Target = half the Caveman benchmark.** User's reasoning: hitting the *full* Caveman numbers almost certainly requires the MCP/machinery (deferred), so native-only technique-mining should aim for ~half.
  - **~23% input-token reduction** (½ of Caveman's ~46%) — plugin's own footprint: per-command sdd-guide + reference load, verbose templates, real-time process notes.
  - **~32% output-token reduction** (½ of Caveman's ~65%) — terser responses, sycophancy padding removed.
  - **Behavior/accuracy preserved** — same artifacts, same workflow quality; efficiency must not degrade the correctness of scope/PRD/spec/sprint outputs.
  - Targets are guide-rails, not contractual; the "by half" framing is the user's stated bar.
- **Measurement = eyeball against v2.** No rigorous token-counting harness. Judge leanness by inspection vs. the v2 artifacts (now in `docs/archive/v2/`); the ~23%/~32% percentages are a sanity check, not a gate. Real goal: meaningfully leaner without breaking behavior.

### Beats 3, 5, 6 — answered by agent at user's request
User delegated the remaining beats ("you'll have a better idea of what done should be; I trust your answers"). Agent-proposed, user did not veto:

- **Beat 3 — Inspiration/references.** Caveman = primary reference (benchmark + technique source); already covered. GSD/Superpowers explicitly NOT references for v3 (deferred). v2 is the negative reference — the bloat being trimmed against.
- **Beat 5 — Done.** v3 is shipped when:
  - Every command SKILL.md + reference stack reviewed and trimmed for input footprint (the v2-grown per-command load is leaner).
  - Terseness + anti-sycophancy rules baked into sdd-guide (Caveman constraint-skill techniques), inherited by all commands.
  - Verbose templates tightened; real-time process-notes guidance leaner without losing decision history.
  - A full dogfood run of the chain on a real small project is visibly leaner in input-load + output vs v2 (eyeballed against `docs/archive/v2/`, ~23%/~32% sanity check).
  - Behavior preserved: correct, complete artifacts; no workflow step lost.
  - Still passes `claude plugin validate .`; still zero-runtime pure markdown.
- **Beat 6 — Solo/team.** Solo (Jason), same as v2. Keeps doc depth lean — itself aligned with the efficiency spine.

### Phase 1 close — smallProject judgment
Clearly a **small project**: single cohesive feature (token efficiency), solo, self-improvement dogfooding on itself, short cycle, no external paying customers, no multi-subsystem build. Signals strongly present, none contradicted → `smallProject: true`. Written to `docs/project-state.json`.

### Project Size Assessment
Appropriately sized for a single scope-to-ship cycle. No split.

### Phase 2 — deepening rounds
**Zero rounds run.** Agent recommended closure at the Phase 1→2 transition (small-project default + scope genuinely crisp); user accepted ("generate"). Topics a round would have covered — which commands to trim first, how aggressive terseness can get before hurting clarity, dogfood-verification sequencing — are implementation-altitude and belong in `/sdd:prd` and `/sdd:plan`, not scope. Context recommendation: continue (moderate session, no superseded noise, scope.md captures the value).

### Document generation
`docs/scope.md` generated from template and approved by user on first pass — no revision iterations requested. Backlog pointer block ("What's Explicitly Cut") emitted because `docs/backlog.md` has entries.

### Open concerns seeded
Created `docs/open-concerns.md` with two Open entries: (1) whether native-only can hit half the Caveman benchmark (verify at dogfood; trigger for the backlogged machinery item if it falls short); (2) which references/templates are over-included per command (the core trim list — for /sdd:spec and /sdd:plan to enumerate).

### Wrap-up
Deleted `docs/scope-resume.md` (resumed-command cleanup). `lastCommand` stays "scope". Handoff to `/sdd:prd` emitted.
