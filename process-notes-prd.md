# Process Notes — /sdd:prd (v3)

## Session 1 — 2026-06-12

**Mode.** User delegated the interview: "go through and answer these questions yourself with what you think is best." So Phase 1/Phase 2 were run as agent-authored judgment calls rather than a live one-question-at-a-time interview. No deepening rounds run (0) — the scope was already well-developed and the user opted out of the interview.

**smallProject re-evaluation.** Confirmed `true` (no flip). Signals unchanged from scope: single feature (token efficiency), no external users, no multi-component architecture, no third-party integrations, concise framing. Phase 1 ran trimmed per right-sizing (skipped user-segmentation, standalone non-goals, competitive-landscape beats); AC generation kept in full.

**Central design decision — testable ACs vs. eyeballed measurement.** The scope explicitly measures success by inspection against `docs/archive/v2/` and treats the ~23%/~32% percentages as sanity-check guide-rails, not gates. A PRD needs testable ACs. Resolution: ACs are structural/observable — point at artifact diffs against the v2 archive, rule-by-rule preservation traces, load-map flags, and `claude plugin validate .` exit. The percentages survive as exactly one AC (`dfrc`) phrased as "record the observed reduction and note proximity to the guide-rails, no requirement to hit them." This keeps every AC verifiable without pretending to a precision the project disowns.

**Epic structure.** 4 epics: Caveman Technique Mining → Input-Footprint Reduction → Output Verbosity Reduction → Behavior Preservation & Verification. Merged what could have been two epics (behavior-preservation guard rail + dogfood verification) into one, since for a small project they're tightly coupled and the merge keeps the set terse. 4 epics is well under the 10-epic phase-split threshold — no phase-split recommendation fired.

**Behavior preservation promoted to a first-class epic.** The project's main risk is silently dropping a workflow step or a behavioral rule while trimming. Made that a guarded epic with hard gates (`prva` step-by-step vs. v2, `rfsc` rule-by-rule preservation trace, `vala`/`valb` validate + markdown-only) rather than leaving it implicit.

**Open concerns.** Both existing concerns (benchmark reachability → /sdd:build; per-command over-inclusion → /sdd:spec/plan) are not resolvable at PRD altitude and stayed deferred to their targets. Note: the over-inclusion concern is now partly addressed structurally — the PRD makes the load map (`lpma`/`lpmb`) a required artifact, so /sdd:spec/plan inherit a concrete deliverable for it. No new concerns surfaced.

**Non-goals.** Folded in per right-sizing (no standalone beat): no Caveman machinery, no instrumentation harness, no behavior/command/workflow changes, no GSD/Superpowers eval, no audience shift. First three trace to scope's "What's Explicitly Cut" + backlog; the machinery and GSD/Superpowers items are backlogged.

**Pushback.** None — user delegated the calls. The only real tension resolved was the testable-vs-eyeball one, resolved unilaterally toward structural ACs as above.
