# Sprint 4 — Process Notes

## Planning Session — 2026-05-13

### Context

Fourth v2 implementation sprint. Sprint 3 just closed: atomic rename sweep landed (sprint→plan, iterate→polish, reflect deleted), `/sdd:feedback` no-`lastCommand`-write bug fix landed, three new-command stubs (`discovery`, `pause`, `unpause`) landed, plugin manifest bumped to 2.0.0. The plugin now has v2-clean command paths.

Sprint 3 planning notes named the sprint 4 candidate explicitly: `MIGRATION-v1-to-v2.md` (17 ACs across 5 stories) + `docs/v2-verification.md` seeding (overdue from `/sdd:spec` finalization). This planning session adopts that scope plus the v2 verification-approach documentation that pairs naturally with the seeding work, and adds the archive INDEX file that completes Story 4 of the migration epic.

### PRD scan

- 13 epics in `docs/prd.md`, 206 ACs total, 0 formally checked off in the PRD file (Bug Fix 2 hasn't shipped, so `/sdd:build` doesn't auto-checkoff ACs yet — drift between sprint files and PRD is a known v2 gap, scheduled for a future sprint).
- 0 unvetted items.
- 1 open concern (`Soft acceptance criteria for MIT distribution`) targeted at `/sdd:refine`, not this sprint. No action.

### Open concerns processed

Read `docs/open-concerns.md`. The single Open item is targeted at `/sdd:refine`. Not in this sprint's scope. No update made to the file.

### Sprint theme proposed

**v2 release-prep artifacts.** Every release-required document that isn't yet written. Coherent grouping: each of the 4 items produces a discrete artifact required for v2 release; together they unblock release-readiness from the artifact side. Per-command behavior implementation (the larger lift) is the natural next phase but its prerequisites are now met.

Rationale for picking this grouping over alternatives:

- **Why not start `/sdd:build` wrap-up?** Wrap-up is a substantial epic — PRD AC auto-checkoff, story-splitting, the "anything notable?" prompt, close-sprint manifest write, v2-verification row updates, next-command recommendation logic. It's not a 1-sprint item. Doing release-prep first means when the wrap-up work lands, the artifacts it writes to (`docs/v2-verification.md`) already exist.
- **Why not start per-command behavior wiring?** Same rationale — those are big multi-sprint efforts. Getting the release-prep artifacts done first gives every later sprint a clean target (the verification audit file is ready to receive Shipped-column updates as work ships).
- **Why bundle these four items?** They all produce release-required documents and are independent of each other. Bundling them into one sprint lets autonomous mode dispatch all four in parallel.

### Proposed grouping

4 items, all parallel-safe (different files, no shared edits, no sequencing constraints):

1. `plugins/sdd/MIGRATION-v1-to-v2.md` — full migration guide content. 17 ACs (`aago` through `aagx`, `aagy`, `aagz`, `aaha`, `aahc`, `aahd`, `aahe`, `aahi`).
2. `docs/archive/v1/INDEX.md` — one-line pre-rename-snapshot note. 1 AC (`aahb`).
3. `docs/v2-verification.md` — sdd-plugin-only audit file seeded with FB-NNN rows per in-scope feedback item. 2 ACs (`aahj`, `aahn`).
4. v2 verification-approach documentation as a header section in `docs/v2-verification.md`. 4 ACs (`aahq`, `aahr`, `aahs`, `aaht`).

User confirmation: the planning was driven by the orchestrator under the user's "work without stopping for clarifying questions" directive. The sprint plan was presented in full as a single proposal; user did not redirect. If the user redirects post-generation, sprint-4.md can be edited before `/sdd:build` runs.

### Execution order decision

All four items are independent. No ordering constraint. Autonomous mode can dispatch four subagents in parallel, or run the smaller items inline and delegate Item 1 (the largest) to a subagent. Either pattern works.

### Build mode

**Autonomous**, matching sprints 1–3. Items are spec-mapped and mechanical; no deepening required mid-build. Build mode locked for sprint 4.

### Deepening rounds

None. The plan is spec-mapped and mechanical — every AC has a clear target artifact and a clear spec section to draw content from. No exploratory thread to chase.

### New concerns surfaced during planning

- **AC counts are heavily concentrated in Item 1 (17 ACs).** The migration guide is one cohesive document; splitting it into smaller items would create awkward fragments and multiple write events for the same file. Acknowledged trade-off: Item 1 is a "big" item from a verification standpoint, but a "natural unit" from a document standpoint. Mitigation: every AC has a dedicated checkbox in the item's acceptance-criteria list, so build verification against the AC list can be done section by section even though the file is written as a whole.
- **`docs/v2-verification.md` Pillar 1 statement (AC `aaht`) requires cross-reference to `docs/scope.md > What "done" looks like`.** Item 4 explicitly cross-references that. If `docs/scope.md`'s "done" definition is later revised, the Pillar 1 statement in `docs/v2-verification.md` must be checked for drift.
- **AC `aahh` (archive untouched) interacts with AC `aahb` (new INDEX file).** Item 2 explicitly notes: "Adding a NEW INDEX.md file inside the archive is explicitly required by AC `aahb` and does not contradict `aahh`." Item 2's verification confirms no existing archive file is modified. The maintainer should be aware of this distinction — the "archive untouched" rule applies to the v2 active-prose sweep not modifying existing v1 files, not to the new INDEX file that v2 explicitly adds.

None of these concerns rise to "Open" status — each has a documented resolution path inside the sprint items themselves. Not adding to `docs/open-concerns.md`.

### Tagging convention

- Items 1, 2, 3, 4 all carry real `[PRD: ...]` tags.
- No `[unmapped]` items in this sprint.
- No `[FB: ...]` tags. Sprint 4 creates the v2-verification.md file at Item 3, but the activation gate (file presence) is the v2-verification.md write behavior in `/sdd:build` wrap-up, not the seeding work. FB-row "Shipped" updates start firing in sprint 5+ when behavior implementation begins and `/sdd:build` wrap-up is in place.

### Decisions log

| Decision | Choice | Rationale |
|---|---|---|
| Sprint 4 theme | v2 release-prep artifacts | Anticipated sprint-3 candidate; unblocks release-readiness from artifact side before bigger per-command behavior sprints begin |
| Bundle migration guide into one item or split? | One item | The guide is one cohesive document; horizontal splits create awkward fragments and multiple write events. AC-count concentration in this item is acknowledged trade-off |
| Include archive INDEX as part of MIGRATION item or its own item? | Its own item (Item 2) | Different file, different scope, clearer per-item rollback |
| Include `/sdd:build` wrap-up implementation? | Defer | Substantial epic (PRD AC checkoff + story splits + manifest write + verification row updates + next-command logic); its own sprint |
| Include per-command behavior wiring (Interview Command Behaviors, Sprint Loop, etc.)? | Defer | Multi-sprint effort; needs its own coherent grouping after release-prep is done |
| Verification approach doc location | Header section in `docs/v2-verification.md` | Self-documenting artifact; documentation travels with the file it describes |
| Build mode | Autonomous | Three prior autonomous sprints landed clean; items are mechanical |
| Deepening rounds | None requested | Mechanical, spec-mapped plan; no exploratory thread |
| Note on user input | Planning ran under "no clarifying questions" directive | User can redirect post-generation by editing sprint-4.md before `/sdd:build` runs |

## Build Session — 2026-05-13

### Mode and dispatch

Autonomous build. Three subagents dispatched in parallel:

- **Subagent A:** Item 1 (`MIGRATION-v1-to-v2.md`).
- **Subagent B:** Item 2 (`docs/archive/v1/INDEX.md`).
- **Subagent C:** Items 3 + 4 combined (`docs/v2-verification.md` — header + seeded table).

Items 3 and 4 were merged into a single subagent dispatch despite the sprint plan listing them as parallel-safe. Reason: the sprint plan called them parallel because they were notionally distinct deliverables, but both write to the same file (`docs/v2-verification.md`). Running them through one subagent avoids a parallel-write conflict and lets the file be authored as one cohesive artifact (header above table). This is a minor deviation from the literal sprint plan; the ACs themselves are unchanged.

### Item 1 — MIGRATION-v1-to-v2.md

- File: `plugins/sdd/MIGRATION-v1-to-v2.md` (88 lines, 7 numbered sections).
- All 17 ACs (`aago`–`aahe`, `aahi`) satisfied. Verified by reading the file end-to-end and grepping for each v1 command name to confirm every occurrence serves a documented migration purpose (rename pair, table row, no-alias statement) — no prospective directives.
- Subagent judgment calls (additive, no contradiction with the spec):
  - Added a brief lead-in paragraph orienting the document for v1 users.
  - Added a sentence after the framing noting what carries over unchanged (PRD AC IDs, spec structure, sprint-tag conventions, backlog).
  - Noted in the `/sdd:discovery` section that running `/sdd:discovery` is optional for v1 projects carrying forward.
  - Used descriptive `##` headings rather than "Section 1, Section 2" labels.

### Item 2 — docs/archive/v1/INDEX.md

- File: `docs/archive/v1/INDEX.md` (3 lines: H1 + blank + one-line note).
- AC `aahb` satisfied (file exists; one-line "pre-rename snapshot" note present).
- AC `aahh` preserved: `git status docs/archive/v1/` shows only `INDEX.md` as untracked; no existing v1 archive file was modified.

### Items 3 + 4 — docs/v2-verification.md

- File: `docs/v2-verification.md` (71 lines: 2-section header + 6-column table with 44 rows).
- Item 4 ACs (`aahq`, `aahr`, `aahs`, `aaht`) satisfied in the header section.
- Item 3 ACs (`aahj`, `aahn`) satisfied in the table:
  - Every in-scope deliverable section from `docs/scope.md` maps to ≥1 row.
  - FB-IDs are three-digit-padded sequential FB-001 through FB-044, stable.
  - Every row's `Verified` column is `[ ]`. Every row's `Shipped` column is empty.
- Source assignment judgment (subagent's call, recorded for traceability):
  - **FB-001** → Event Planning App (per scope.md line 84 the back-to-back feedback bug is explicitly the Event-Planning case).
  - **FB-002** → fiver-gigs (PRD state-tracking non-split-item miss read as fiver-gigs-style multi-sprint friction).
  - **FB-007, FB-009, FB-010** → Event Planning App (interview-pacing pain — clarifying-question advancement, blank "continue?" prompts, surprise deepening rounds — read as the dominant Event Planning App friction signals).
  - **FB-008, FB-012** → fiver-gigs (question-count caps and active-prompting read as fiver-gigs pain).
  - **FB-014** → both Event Planning App and fiver-gigs (cross-project context-management friction).
  - **All remaining rows** → "sdd-plugin self-use" as the reasonable default for items that surfaced during dogfooding. Most v2 scope items came from plugin self-use, so this default applies broadly.
  - Source assignments are educated guesses where the discovery interview content is not directly accessible in the sprint context. Manual correction during the verification phase is expected and supported — the Source column is editable.
- Row-granularity judgment:
  - Bug fixes split into 2 rows (the two distinct bugs).
  - New commands split into 4 rows: `/sdd:prd`, `/sdd:pause`, `/sdd:unpause`, pause-file cleanup.
  - Behavioral rules across interview commands got 7 rows (one per named rule).
  - Onboard polish got 7 rows (one per bullet).
  - Sprint loop got 3 rows: `/sdd:plan`, sprint-mode memory, `/sdd:build` wrap-up.
  - `/sdd:retro` got 2 rows (project-close gating, cross-project-patterns step).
  - Feedback transfer got 4 rows (profile field, write-and-forward, inside-clone detection, failure handling).
  - Automatic backlog generation got 4 rows.
  - Process notes growth got 3 rows.
  - v1 → v2 migration got 5 rows (MIGRATION file, state table, resume renames, atomic-no-aliases, full-sweep + archive untouched + INDEX note).

### Living document updates

- **CLAUDE.md:** No updates. No new project conventions emerged from this sprint; the work followed existing markdown-only / no-runtime-code / no-auto-commit conventions.
- **AGENTS.md:** No updates. No new architectural decisions; the artifacts produced are exactly what the scope, PRD, and spec already anticipated.

### Open concerns

- No new concerns surfaced during the build. The single Open item (`Soft acceptance criteria for MIT distribution`) remains targeted at `/sdd:refine`; not in this sprint's scope and not touched.
- No concerns resolved by this build. The build produced release-prep artifacts; the in-flight concerns target later commands.

### AC counts

- Sprint 4 closes 24 ACs: 17 (Item 1: `aago`–`aahe`, `aahi`) + 1 (Item 2: `aahb`) + 2 (Item 3: `aahj`, `aahn`) + 4 (Item 4: `aahq`–`aaht`).
- Not yet closed in this sprint (deferred per sprint scope): `aahk`, `aahl`, `aahm`, `aahf`, `aahg`, `aahh`, `aaho`, `aahp`, `aahu`, `aahv`, `aahw`, `aahx`. These describe runtime command behaviors tied to `/sdd:build` wrap-up or to future plugin versions, with their structural prerequisites now met but the behaviors themselves shipping in later sprints.

### v2-verification.md activation gate

- The file now exists. `/sdd:build` wrap-up's auto-write behavior is activated by the file's presence, so once the wrap-up command is implemented, it will begin writing the Shipped column for sprint items carrying `[FB: ...]` tags. No FB-tagged items shipped in this sprint (intentional — sprint 4 is seeding-only). Sprint 5+ will begin populating the Shipped column when feedback-item implementation work begins.

### Closing state

- 4/4 items complete in `docs/sprint-4.md`. All AC checkboxes checked.
- Files added (uncommitted): `plugins/sdd/MIGRATION-v1-to-v2.md`, `docs/archive/v1/INDEX.md`, `docs/v2-verification.md`.
- Files modified (uncommitted): `docs/sprint-4.md`, `docs/project-state.json`, `process-notes-sprint-4.md`.
- No auto-commit per AGENTS.md convention.

### Next sprint recommendation

Sprint 4 closes the v2 release-prep artifact set. The natural next sprint is `/sdd:build` wrap-up implementation, which unlocks:

- Bug Fix 2 (`aaad`–`aaag`) — PRD AC auto-checkoff for non-split sprint items.
- `docs/v2-verification.md` Shipped-column auto-write behavior (`aahk`, `aahl`, `aahm`, `aahv`).
- Close-sprint manifest write (`[close-sprint-manifest]` block in process notes).
- "Anything notable?" wrap-up prompt + tech-debt capture.
- Next-command recommendation routing logic.

That's a substantial standalone sprint and may itself need to be split horizontally. Alternative candidates: per-command behavior implementation (Interview Command Behaviors, `/sdd:onboard` polish, Sprint Loop, etc.) — these can run in parallel or after wrap-up, depending on which dependencies the user prioritizes.
