# SDD v3 — Scope

## Idea

v3 is a token-efficiency cycle for the SDD plugin. The plugin's behavior stays the same; the goal is to make it **cheaper to run** — fewer input tokens consumed by the plugin's own machinery, and fewer output tokens spent by the agent in conversation.

Two prongs:

1. **Shrink the plugin's input footprint** — the per-command sdd-guide + reference-file load grew substantially in v2. Trim that stack, tighten verbose templates, and lean out real-time process-notes guidance (without losing decision history).
2. **Cut agent output verbosity** — terser responses; remove sycophancy padding ("great idea," "that's a great question") and over-explanation.

The approach is **native technique-mining**: study the Caveman plugin (https://github.com/juliusbrussee/caveman), extract its compression rules and constraint-skill techniques, and reimplement them as native markdown inside sdd-guide. Forking or downloading Caveman to study it is in-bounds. The plugin's **zero-runtime, pure-markdown posture is preserved** — no hook, no scripts, no MCP middleware in v3.

## Who It's For

The SDD plugin's own users — unchanged from v2. That's Jason plus the local tech-group members who run SDD on their own projects. The plugin continues to dogfood on itself. Efficiency gains land for that same group; v3 introduces no audience shift.

## Inspiration & References

- **Caveman** (https://github.com/juliusbrussee/caveman) — primary reference and benchmark. A token-efficiency toolkit: a presentation-layer constraint skill, a Claude Code auto-activation hook, `caveman-compress` (memory-file compressor, ~46% input cut), and `caveman-shrink` (MCP middleware that compresses tool descriptions). Reported ~65% output-token reduction with accuracy preserved; MIT, standalone, composable. v3 mines its *techniques* (the constraint rules and compression heuristics that port to markdown), not its machinery.
- **SDD v2** (archived at `docs/archive/v2/`) — the negative reference. v2's grown per-command load, verbose templates, and real-time process notes are the bloat v3 trims against.
- **Not references for v3:** Get Shit Done and Superpowers (deferred — see `docs/backlog.md`).

## Goals

- **Reduce the plugin's input-token footprint** by roughly **half the Caveman benchmark (~23%)** — trimming the per-command sdd-guide + reference load, verbose templates, and real-time process-notes overhead.
- **Reduce agent output verbosity** by roughly **half the Caveman benchmark (~32%)** — terser responses, sycophancy padding removed.
- **Preserve behavior and accuracy** — the commands still produce correct, complete artifacts (scope, PRD, spec, sprint, retro) and no workflow step is lost. Efficiency never comes at the cost of correctness.
- **Stay zero-runtime, pure markdown** — all efficiency gains expressed as markdown instructions; no hook, script, or MCP dependency added.

The "half the Caveman benchmark" rationale: the full Caveman figures lean on its MCP middleware and compressor scripts (the machinery v3 deliberately excludes), so native-only technique-mining targets roughly half. The percentages are **sanity-check guide-rails, not contractual gates** — see measurement below.

## What "Done" Looks Like

v3 is shipped when:

- Every command's `SKILL.md` and the reference stack have been reviewed and trimmed for input footprint — the v2-grown per-command load is visibly leaner.
- Terseness and anti-sycophancy rules are baked into sdd-guide (the Caveman constraint-skill techniques), so every command inherits them.
- Verbose templates are tightened and real-time process-notes guidance is leaner, without losing decision history.
- A full dogfood run of the chain on a real small project visibly produces leaner input-load and output than v2 — **eyeballed against `docs/archive/v2/`**, with the ~23% / ~32% halves as a sanity check (no rigorous token-counting harness).
- Behavior is preserved: correct, complete artifacts; no workflow step lost.
- The plugin still passes `claude plugin validate .` and remains zero-runtime pure markdown.

## What's Explicitly Cut

Deferred items: see `docs/backlog.md`.

## Loose Implementation Notes

- Caveman's value to v3 is split: the **constraint skill** (presentation-layer rules) ports cleanly into sdd-guide as markdown; the **hook**, **`caveman-compress`**, and **`caveman-shrink` MCP middleware** are executable machinery and are out of scope for v3 (backlogged for future research).
- Likely highest-leverage input-footprint targets: the per-command sdd-guide + reference re-load on every command startup, the reference stack itself, and verbose templates. Worth profiling which references actually need to load per command vs. which are over-included.
- Anti-sycophancy and terseness are partly already stated in sdd-guide's Tone section; v3 likely sharpens and centralizes these rather than inventing them from scratch.
- Measurement is by inspection against the archived v2 artifacts, not instrumentation — keep this lightweight, consistent with the efficiency spine.
