# Process Notes — Sprint 18 (Planning)

## Planning session — 2026-05-14

### Proposed grouping (accepted as-is)

Sprint 18 — v1→v2 Migration Guide. 4 items, 21 ACs, sequential, autonomous build mode.

- Item 1: Author MIGRATION-v1-to-v2.md narrative sections — 13 ACs (`aago`, `aagp`, `aagq`, `aagv`, `aagw`, `aagx`, `aagy`, `aagz`, `aaha`, `aahc`, `aahd`, `aahe`, `aahi`).
- Item 2: Add state-aware migration table — 4 ACs (`aagr`, `aags`, `aagt`, `aagu`).
- Item 3: Verify archive index note — 1 AC (`aahb`). Already satisfied on disk; verify-and-check.
- Item 4: v1 command name sweep across codebase — 3 ACs (`aahf`, `aahg`, `aahh`).

Rationale: Item 1 lands the narrative shell; Item 2 layers the table (largest sub-deliverable, gets focused attention); Item 3 is trivial verify-and-check (already satisfied by `docs/archive/v1/INDEX.md` content); Item 4 runs last so sweep can validate against the new migration file as the legitimate exception locus.

User accepted the grouping without modification.

### Build mode

Autonomous — inherited from Sprint 17 default. Doc-authoring + grep sweep is well-spec'd, low-coupling between items. User confirmed.

### Deepening round 1 — started

Agent recommended a round because Item 2 (migration table) and Item 4 (codebase sweep) carry concrete ambiguities that `/sdd:build` would otherwise resolve on the fly. Topic preview:
1. Migration table column structure
2. Mid-reflect vs. post-reflect distinction (`aagt`)
3. Resume-file rename mapping (`aagw`)
4. Sweep scope for "live prose under `docs/`" (`aahg`)
5. "Migration-context references" exception scope

Context recommendation: continue (short conversation, sprint plan mostly captured, round adds 2-3 paragraphs).

User accepted the round.

#### Round 1 decisions

**Q1 — Migration table column schema (5 columns):**
`v1 lastCommand | Stay on (mid-flight) | Next v2 command (after completion) | One-time action | Notes`
User confirmed and explicitly raised the "stay on if not done with last command" column as something previously discussed. Adopted.

**Q2 — Mid-reflect vs. post-reflect content:**
- **Mid-reflect:** abandon the reflect (no v2 equivalent). Run `/sdd:build` if the sprint isn't closed (build's wrap-up absorbs reflect's responsibilities). Run `/sdd:plan` if the sprint was already closed before reflect started. Notes column explains the absorption.
- **Post-reflect:** run `/sdd:plan` to start the next sprint. Reflect-era artifacts persist as historical record.
User confirmed.

**Q3 — Resume-file rename pairs (full surface):**
- `docs/sprint-resume.md` → `docs/plan-resume.md`
- `docs/iterate-resume.md` → `docs/polish-resume.md`
No other renames (reflect was removed and wasn't pause-allowed; other commands kept their names). Content carries over unchanged across the rename. User confirmed scope is fully covered.

**Q4 — Sweep scope for `aahg` ("live prose"):**
- **Included:** `plugins/sdd/skills/**/*.md`, `plugins/sdd/README.md` (if present), `plugins/sdd/.claude-plugin/*.json`, `docs/discovery.md`, `docs/scope.md`, `docs/prd.md`, `docs/spec.md`, `docs/v2-verification.md`, `docs/backlog.md`, `docs/open-concerns.md`, `AGENTS.md`, repo-root `CLAUDE.md`, `docs/sdd-feedback.md`.
- **Excluded:** `docs/archive/v1/**` (per `aahh`), `docs/MIGRATION-v1-to-v2.md` (migration-context by definition), `docs/sprint-N.md` files (historical decision logs), `process-notes-*.md` files (append-only history).
User confirmed.

**Q5 — Migration-context exception scope:**
Legitimate v1-name appearances:
1. Historical/comparative prose ("v1 used X; v2 uses Y").
2. Quoted v1 feedback notes in `docs/sdd-feedback.md`.
3. Backlog entries referencing v1 origins.

Not legitimate: live behavioral instructions, current command-chain references, unqualified mentions implying v1 names are still active.

User confirmed.

#### End-of-round recommendation
Agent recommended closing — five spec-gap questions answered, remaining work is mechanical authoring. Topic preview of a hypothetical Round 2 (section order, tone, aahb timing, /sdd:onboard table row, version header): all styling decisions, not ambiguities. Context recommendation: continue tier — short conversation, decisions captured here, going straight to sprint file generation. User accepted close.

### Sprint file generation

Generated `docs/sprint-18.md`. 4 items, 21 ACs, sequential, autonomous build mode. No `[FB: ...]` tags (v1→v2 Migration epic is meta-infrastructure, not v1-feedback-derived). Activation caveat carries forward — cached marketplace plugin is still v1.

### Project state update

- `currentSprint`: 17 → 18.
- `buildMode`: autonomous (unchanged from Sprint 17).
- `lastCommand`: `/sdd:sprint`.
- `commandExplanationsShown.sprint`: already `true`; no change.
- Notes field updated to reflect Sprint 18 planning summary and post-sprint zero-unchecked-ACs projection.

### Open concerns

One open concern remains (`Soft acceptance criteria for MIT distribution`, target `/sdd:refine`). Not addressable in a migration-guide sprint. No new concerns surfaced during planning. No resolutions.

### Sprint planning summary

- **Items:** 4
- **ACs:** 21 (closes the v1→v2 Migration epic in full)
- **Build mode:** autonomous (locked for sprint)
- **Deepening rounds:** 1 round, 5 questions, all in topic preview from the round recommendation
- **Decisions captured:** migration table 5-column schema; mid-reflect/post-reflect content; resume-file rename pair list; sweep scope (included/excluded files); migration-context exception heuristic
- **Post-sprint projection:** zero unchecked ACs across the entire v2 PRD. All 5 v2 epics close. v2 is feature-complete pending release mechanics (version bumps, tag, marketplace cutover — out of sprint loop).

## Sprint 18 Item 4 — v1 command name sweep audit (2026-05-14)

### Alias-registration check (`aahf`)

- **SKILL.md frontmatter `name:` fields under `plugins/sdd/skills/*/SKILL.md`:** `plan`, `unpause`, `retro`, `build`, `pause`, `spec`, `polish`, `discovery`, `feedback`, `onboard`, `sdd-guide`, `refine`, `prd`, `scope`. None of `sprint`, `iterate`, `reflect` appear.
- **`plugins/sdd/commands/` directory:** not present (no glob match). No v1-named entries.
- **`plugins/sdd/.claude-plugin/plugin.json`:** declares plugin name `sdd`, no command-level entries. No v1 aliases.
- **Repo-root `.claude-plugin/marketplace.json`:** registers plugin `sdd` only. No command-level aliases.
- **Result:** PASS — no command alias for `/sdd:sprint`, `/sdd:iterate`, or `/sdd:reflect`.

### Per-pattern grep results

Sweep run across the locked included set (`plugins/sdd/skills/**/*.md`, `plugins/sdd/README.md`, `plugins/sdd/.claude-plugin/*.json`, `docs/discovery.md`, `docs/scope.md`, `docs/prd.md`, `docs/spec.md`, `docs/v2-verification.md`, `docs/backlog.md`, `docs/open-concerns.md`, `docs/sdd-feedback.md`, `AGENTS.md`, repo-root `CLAUDE.md`). Excluded: `docs/archive/v1/**`, `plugins/sdd/MIGRATION-v1-to-v2.md`, `docs/sprint-*.md`, `process-notes-*.md`.

**Pattern 1 — `/sdd:sprint`:**
- Total hits (pre-fix): 11. Legitimate: 4. Stale: 7. Stale post-fix: 0.
- Legitimate hits:
  - `docs/prd.md:305` — AC `aagp` text mandating migration-guide opening framing (migration-context).
  - `docs/prd.md:315` — AC `aagv` text mandating rename-pair documentation (migration-context).
  - `docs/prd.md:331` — AC `aahf` text itself (migration-context).
  - `docs/prd.md:371` — "What We're Building" prose: "renames `/sdd:sprint` → `/sdd:plan`" (historical/comparative).
  - `AGENTS.md:30` — "v2 also renames `/sdd:sprint` → `/sdd:plan`" (historical/comparative).
  - `docs/spec.md:843` — quoted MIGRATION opening framing (migration-context).
  - `docs/spec.md:855` — migration table row (migration-context).
  - `docs/sdd-feedback.md:73` — v1 feedback note listing v1 commands (quoted v1 feedback per heuristic #2).
  - `docs/sdd-feedback.md:79` — v1 feedback note (quoted v1 feedback per heuristic #2).
- Stale hits fixed: `docs/open-concerns.md:34, 39, 44, 49, 54, 84, 89` — seven "Resolved by: /sdd:sprint (sprint-1[/ planning])" lines rewritten to `/sdd:plan` per the locked fix mapping. These were live attributions in a current doc with no migration-context qualifier — heuristic flagged "unqualified mention implying v1 names are still active."

**Pattern 2 — `/sdd:iterate`:**
- Total hits (pre-fix): 6. Legitimate: 6. Stale: 0. Stale post-fix: 0.
- Legitimate hits:
  - `docs/prd.md:305` — AC `aagp` migration-context.
  - `docs/prd.md:315` — AC `aagv` migration-context.
  - `docs/prd.md:331` — AC `aahf` migration-context.
  - `docs/prd.md:371` — "What We're Building" historical/comparative.
  - `AGENTS.md:30` — historical/comparative.
  - `docs/spec.md:444` — "Renamed from `/sdd:iterate`" inside the `/sdd:polish` skill spec (rename declaration; migration-context).
  - `docs/spec.md:843` — quoted MIGRATION opening framing.
  - `docs/spec.md:857` — migration table row.

**Pattern 3 — `/sdd:reflect`:**
- Total hits (pre-fix): 14. Legitimate: 14. Stale: 0. Stale post-fix: 0.
- Legitimate hits:
  - `docs/open-concerns.md:60` — Resolution text explicitly framing the v1 → v2 change ("`/sdd:reflect` dissolves entirely in v2"); historical/comparative.
  - `docs/prd.md:5` — "Problem Statement" naming v1 bugs in `/sdd:reflect` that v2 fixes; historical/comparative.
  - `docs/prd.md:305, 310, 311, 331` — ACs `aagp`, `aags`, `aagt`, `aahf` — migration-context.
  - `docs/scope.md:15` — listed v1 bug ("`/sdd:reflect` PRD state-tracking gap") that v2 goals fix; historical/comparative.
  - `docs/scope.md:152` — describes migration-guide coverage of the dead `/sdd:reflect` two-case; migration-context.
  - `docs/sdd-feedback.md:53, 59, 65, 67, 83, 103` — six v1 feedback entries with `After command: /sdd:reflect` or in-note references; quoted v1 feedback per heuristic #2.
  - `docs/spec.md:291` — "reflect-template.md (REMOVED) — `/sdd:reflect` no longer exists"; migration-context.
  - `docs/spec.md:843` — quoted MIGRATION opening framing.
  - `docs/spec.md:858` — migration table row.
  - `docs/v2-verification.md:69` — FB-041 row describing migration-guide content ("handles dead `/sdd:reflect` two-case"); migration-context.

### After-fix re-grep results

- `/sdd:sprint` in scope: only legitimate hits remain (no stale "Resolved by" attributions in `docs/open-concerns.md`).
- `/sdd:iterate` in scope: only legitimate hits remain.
- `/sdd:reflect` in scope: only legitimate hits remain.
- `plugins/sdd/` (excluding `MIGRATION-v1-to-v2.md`): zero hits across all three patterns. All in-plugin v1-name occurrences are isolated to the migration guide.

### Archive untouched confirmation (`aahh`)

- No file under `docs/archive/v1/` was opened for write during this item. Item 3 verified the archive's INDEX.md note satisfies `aahb`; Item 4 left the directory untouched.

### Summary

All three ACs satisfied: `aahf` (no v1 aliases registered), `aahg` (live material uses v2 names; migration-context exceptions documented above), `aahh` (archive untouched).

---

## Sprint 18 build completion summary (2026-05-14)

Autonomous build run. Four items dispatched sequentially to subagents; orchestrator audited results and propagated checkoffs. No errors, no items deferred, no scope changes.

### Items

- **Item 1** — Audit-and-fill of `plugins/sdd/MIGRATION-v1-to-v2.md` narrative sections. Subagent verified all 13 ACs (`aago`, `aagp`, `aagq`, `aagv`, `aagw`, `aagx`, `aagy`, `aagz`, `aaha`, `aahc`, `aahd`, `aahe`, `aahi`) were already satisfied by the existing file content from a prior pass. No file edits required to the narrative; only PRD AC checkoffs and sprint status flip.
- **Item 2** — Rebuilt the state-aware migration table inside `plugins/sdd/MIGRATION-v1-to-v2.md` to the locked 5-column schema (`v1 lastCommand | Stay on (mid-flight) | Next v2 command (after completion) | One-time action | Notes`). Prior table was 3-column. 13 rows authored covering every v1 command name plus separate mid-reflect and post-reflect rows plus an unset-`lastCommand` row. `/sdd:pause`/`/sdd:unpause` rows omitted per item guidance (table focuses on v1→v2 migration only). ACs `aagr`, `aags`, `aagt`, `aagu` checked.
- **Item 3** — Verified `docs/archive/v1/INDEX.md` contains the satisfying one-line note about pre-rename snapshot of v1 planning artifacts. No file edit needed. AC `aahb` checked.
- **Item 4** — v1 command name sweep across the locked included file set. Three grep patterns, 31 total hits (11 `/sdd:sprint` + 6 `/sdd:iterate` + 14 `/sdd:reflect`). 24 legitimate (migration-context references in PRD ACs, MIGRATION guide quotations, spec migration content, AGENTS rename framing, quoted v1 feedback notes, scope historical bug references). 7 stale, all in `docs/open-concerns.md` (`Resolved by: /sdd:sprint (sprint-1)` attributions); fixed in-place to `/sdd:plan`. Post-fix re-grep: zero stale hits. Alias-registration check passed (no SKILL.md `name:` field, no marketplace.json entry, no `plugins/sdd/commands/` directory names a v1 command). Archive untouched. ACs `aahf`, `aahg`, `aahh` checked. Full audit recorded above.

### Sprint-level closeout

- **Items completed:** 4 of 4.
- **PRD ACs closed this sprint:** 21 (every AC in the `v1 → v2 Migration` epic).
- **PRD ACs unchecked across entire v2 PRD after Sprint 18:** 0. All 5 v2 epics close with this sprint.
- **v2-verification rows updated:** 0. No `[FB: ...]` tags on Sprint 18 items (v1→v2 Migration epic is GA-eve meta-infrastructure, not v1-feedback-derived). FB-041 in `docs/v2-verification.md` is the migration-guide tracker but is not mapped to a Sprint 18 item via `[FB]` tag; maintainer can verify it manually post-installation.
- **Living-documents updates:** None. `CLAUDE.md` and `AGENTS.md` already document the migration-guide location, the pure-markdown plugin posture, surgical context loading, and the v1→v2 rename framing. No new patterns surfaced.
- **Open concerns:** None resolved (the lone open concern — soft acceptance criteria for MIT distribution — targets `/sdd:refine`, not Sprint 18). None added.

### Notes for /sdd:retro

- Sprint 18 marks v2 feature-complete pending release mechanics (version bumps in `plugins/sdd/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`; git tag; marketplace cutover). None of those are PRD ACs.
- The "audit-and-fill" pattern (Item 1) where the migration file was already authored before Sprint 18 started suggests the planning↔build seam shifted some authoring work upstream into the planning phase. Worth a retro note: migration-guide authoring partly happened during `/sdd:plan` Round 1 deliberation rather than during `/sdd:build` proper. Net-positive for build velocity, but blurs the artifact provenance.
- The seven stale "Resolved by: /sdd:sprint" lines in `docs/open-concerns.md` were the only stale references found across the entire live codebase. Confidence that v2 is internally consistent on naming is now high.
