# Process Notes — /sdd:spec (v3)

## Session 2026-06-12

**Right-sizing.** smallProject re-evaluated at startup, confirmed `true` (single self-improvement feature, solo + small known group, no new architecture, no integrations, concise framing). No-op, no flip. Standard small-project skips applied: architecture-overview, cross-cutting-concerns, and deployment-topology beats skipped — all N/A for a zero-runtime markdown plugin anyway.

**Adapted Phase 1.** The conventional spec beats (stack, storage, auth, integrations, frontend, deployment topology, multi-service) are settled or N/A — stack is unchanged from v2, fixed by scope/PRD. Phase 1 collapsed to the genuinely open design questions for v3.

**Key decisions:**

- **Spec is a trim-plan design doc**, not concrete edits — follows v2's delta-and-resolution posture. Names what/why/where; line-edits left to `/sdd:plan` and `/sdd:build`. Jason chose this directly over the "lock in concrete decisions up front" alternative.
- **Analysis artifacts as standalone `docs/` files**, not spec sections. Agent recommended this when asked; reasoning: they're cited as deliverables, must persist for the verification trail, and keeping them out of the spec preserves the design-doc posture. Two files: `docs/efficiency-techniques.md` (inventory) and `docs/load-map.md` (trim driver).
- **Naming constraint (Jason-initiated).** No shipped or persisted artifact carries "Caveman" as a label — Jason wants the additions separated from the source toolkit in naming. Source credited in prose only. Artifacts named by function. Recorded as a Key Technical Decision and as a naming rule at the top of the spec. PRD epic cross-references still cite the PRD heading verbatim (a pointer, not a label).
- **Output Constraints as a new dedicated sdd-guide section** (`## Output Constraints`), absorbing and sharpening the existing Tone "no sycophancy" line; Tone line becomes a pointer. Chosen over expanding Tone in place — satisfies `trsa`'s "named section" requirement and centralizes per `trsb`.
- **Rule-inventory check** is the preservation mechanism for `rfsc`/`prva`: record named rules before a trim, confirm each present after, audit trail in `docs/load-map.md`. Jason accepted as proposed ("Works for me").
- **Dogfood target = v3's own planning chain** (this scope/PRD/spec run), compared against `docs/archive/v2/`. Jason chose this over a fresh throwaway project — no disposable work, already a real small project, matches the "eyeball vs v2" non-instrumentation posture.

**Pushback / friction:** none substantive. Jason's answers were decisive and short; one "yes" to an either/or (Output Constraints placement) was resolved by the agent taking the position (new dedicated section) consistent with the terseness spine rather than re-asking.

**Deepening rounds:** 0. Recommended closure at the Phase 1 → Phase 2 transition (smallProject close-default, no contradicting evidence). A round would have covered rule-inventory edge cases (mechanism already agreed), trim aggressiveness, per-command terseness carve-outs (`inha` forbids them), and template/process-notes specifics (all `/sdd:plan`-altitude). Jason accepted closure ("generate").

**Open concerns handled:** over-inclusion concern (targeted at /sdd:spec) resolved into the `docs/load-map.md` design. Native-only-benchmark concern left open, deferred to dogfood (`/sdd:build`) as designed. Two new spec Open Issues surfaced (consolidation net-effect per command; artifact/trim order of operations) — added to open-concerns.

**CLAUDE.md / AGENTS.md:** merged, not overwritten. Updated active cycle to v3, generalized the spec-posture note to cover the trim-plan form, added the two new artifacts and the Output Constraints decision, repointed the dogfood/verification language from v2-verification to the v3 mechanisms.
