# docs/archive/v3/

This directory contains a snapshot of v3 planning, sprint, and process artifacts, archived at the start of the next cycle. Files here are historical and are not modified by later cycles.

## Contents

- `scope.md`, `prd.md`, `spec.md` — v3 planning artifacts (the token-efficiency trim-plan).
- `open-concerns.md` — v3 open-concerns ledger at cycle close (all resolved or carried to `retro.md`).
- `retro.md` — v3 project-close retrospective.
- `sprint-1.md` — the single v3 sprint file.
- `process-notes-scope.md`, `process-notes-prd.md`, `process-notes-spec.md`, `process-notes-sprint-1.md` — per-phase process notes (v3 split notes per phase rather than combining).
- `dogfood-comparison.md` — v3 Sprint 1 verification record (v3-vs-v2 input-footprint and output-verbosity comparison; ~23%/~32% guide-rail verdict; native-only conclusion).
- `efficiency-techniques.md` — v3 technique inventory (compression/constraint techniques studied, with port-to-markdown verdicts).
- `load-map.md` — v3 per-command load map and the rule-inventory audit trail proving no behavioral rule was dropped by a trim.
- `project-state.json` — final v3 project state (currentSprint 1, lastCommand `/sdd:retro`).
- `backlog.md`, `sdd-feedback.md` — **snapshots** of the cross-cycle living documents as they stood at v3 close. The live copies remain in `docs/` and carry forward into the next cycle.

## Cycle summary

v3 was a token-efficiency pass over v2: trim the plugin's input footprint and add output-verbosity constraints (`## Output Constraints`) without changing behavior. Single sprint; closed feature-complete pending release mechanics (the manifest version bump to 3.0.0). See `retro.md` for the full retrospective and `dogfood-comparison.md` for the realized-vs-target efficiency verdict.
