# Open Concerns

## Open

_None. All concerns resolved or carried to `docs/retro.md` at project close (`/sdd:retro`, 2026-06-16)._

## Resolved

### Plugin manifest version still reads 2.0.0 while v3 is the active cycle
- **Source:** /sdd:build Sprint 1 Item 9 — zero-runtime check (incidental observation)
- **Target:** project close / release step (not a Sprint 1 AC)
- **Resolution (/sdd:retro):** Carried to `docs/retro.md` (Open Concerns — Final Status) as a tracked **release-time action**, not a defect. `plugins/sdd/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` must move together to the v3 version at ship time. No behavioral effect before publish; the bump is the responsibility of the release step that ships v3.

### Can native-only technique-mining actually reach half the Caveman benchmark?
- **Source:** /sdd:scope — Phase 1, Beat 4 (goals) — native-only target
- **Target (was):** /sdd:build (dogfood time)
- **Resolution (/sdd:build, Item 10):** Qualitative verdict — **in the neighborhood, does not fall short.** Realized input win is ~15–20% (concentrated in templates at −25% plus a per-command startup-load drop) against the ~23% guide-rail; output mechanism (`## Output Constraints`) is now in place and structurally sound, ~25–35% qualitative estimate against the ~32% guide-rail (confounded by project size and by the artifacts predating the rules — see `docs/dogfood-comparison.md`). The failure condition that would trigger pulling the backlogged machinery is **not** met; native-only stays. Recommend a fresh chain run under the new Output Constraints to confirm the output half empirically.

### Does consolidating a reference produce a net per-command trim, or can it raise some commands' load?
- **Source:** /sdd:spec — Open Issues — reference-stack consolidation
- **Target (was):** /sdd:build (Sprint 1 Item 4)
- **Resolution (/sdd:build, Item 4):** Moot in practice — **no consolidation was performed.** The load map (Item 2) showed the references partition cleanly by phase, so any merge would raise load for a command that loads only one of the pair, or yield no per-command saving (recorded in `docs/load-map.md`). The realized trim was instead a clean per-command startup-load removal (`context-loading` dropped from `/sdd:plan`), which cannot raise any command's load. The net-trim guard the concern asked for was applied and steered the work away from consolidation.

### What is the order of operations across the two artifacts and the trims?
- **Source:** /sdd:spec — Open Issues — sequencing
- **Target (was):** /sdd:plan
- **Resolution (/sdd:plan):** Resolved by the Sprint 1 ordering. All v3 work is one sprint, sequenced by the dependency chain: Item 1 (inventory) → Item 2 (load map) → Item 3 (output rules, derived from the inventory) → Items 4–6 (reference/template/process-notes trims, driven by the load map) → Items 7–10 (verification + dogfood). The rule-inventory audit trail is hosted in `docs/load-map.md` and written across Items 4 and 8.

### Which references/templates are over-included per command?
- **Source:** /sdd:scope — Phase 1, Loose Implementation Notes
- **Target (was):** /sdd:spec (identify the trim list), /sdd:plan (sequence the work)
- **Resolution (/sdd:spec):** Resolved at the design level. The spec designs `docs/load-map.md` as the artifact that enumerates, for every `/sdd:*` command, the references it loads at startup and flags each **load-justified** or **over-included** (`lpma`/`lpmb`). The per-file `references/` review outcome (`rfsa`) and at-least-one-removal requirement (`rfsb`) hang off it. The actual enumeration is profiling work for `/sdd:plan` sequencing and `/sdd:build` execution — the *mechanism* that resolves the concern is now specified, the *list* is produced when the load map is built.

## Deferred
