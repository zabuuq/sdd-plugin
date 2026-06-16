# Sprint 5 — Process Notes

## Planning Session — 2026-05-13

### Context

Fifth v2 implementation sprint. Sprint 4 just closed: `MIGRATION-v1-to-v2.md`, `docs/archive/v1/INDEX.md`, and `docs/v2-verification.md` (seeded + verification-approach header) all landed. v2 release-prep artifact set is complete. The natural next sprint, called out in sprint 4's wrap-up notes, is **`/sdd:build` wrap-up phase implementation** — the single most-blocking deliverable still on the ground.

### Why /sdd:build wrap-up next

Five downstream items are gated on the wrap-up phase being implemented:

1. **Bug Fix 2** (`aaad`–`aaag`): PRD AC auto-checkoff for closed sprint items happens inside the wrap-up. Without wrap-up, the PRD never reflects what's actually been shipped, and the drift between sprint files and PRD compounds with every sprint.
2. **`docs/v2-verification.md` Shipped-column writes** (`aahk`, `aahv`): the FB-row auto-update fires inside the wrap-up. Sprint 4 seeded the file; without wrap-up, the Shipped column stays empty forever.
3. **`/sdd:retro` project-close gating** (`aafa`): /retro gets recommended only when the PRD's final AC checks off, and that AC checkoff happens inside the wrap-up.
4. **"One cohesive close-out"** (Pillar 3 of the scope's "done" definition): the wrap-up phase IS the cohesive close-out. Until it exists, the post-sprint experience still feels like multiple ceremonies.
5. **Close-sprint manifest** (spec.md > Close-Sprint Manifest): the structured block written by the wrap-up is what `/sdd:polish` re-open reads. /polish re-open is a separate sprint, but its prerequisite is the manifest format being produced here.

Sprint 4's note called out that this is "a substantial standalone sprint and may itself need to split." Confirmed during this planning pass: 3 items, 21 ACs total, all editing the same SKILL.md file.

### PRD scan

- 13 epics in `docs/prd.md`, 206 ACs total. PRD state: most ACs still unchecked (Bug Fix 2 hasn't shipped → no auto-checkoff yet; the v2 sprint-file-to-PRD drift is exactly the friction Bug Fix 2 is being built to resolve).
- Manual count of remaining work: Bug Fix 2 (4 ACs); /sdd:discovery (9); Multi-Session Resume (21); Interview Command Behaviors (30+); Context Management (12); /sdd:onboard Polish (15); Sprint Loop (27, of which ~21 are this sprint and ~6 are /sdd:plan + /sdd:polish); /sdd:retro at Project Close (8); Cross-Project Feedback Transfer (12); Automatic Backlog Generation (12); Process Notes Growth (7); v1→v2 Migration (3 unimplemented runtime ACs: `aahf`, `aahg`, `aahh` — partially satisfied; verify by code state); v2 Release Verification (9 unimplemented runtime/preservation ACs).
- **Unvetted: empty** (line 363 of prd.md).
- **Open concerns:** 1 Open item (`Soft acceptance criteria for MIT distribution`), targets `/sdd:refine`, not this sprint. No action.

### Open concerns processed

Read `docs/open-concerns.md`. Single Open item targets `/sdd:refine`. Not in this sprint's scope. No update made.

### Sprint theme proposed

**`/sdd:build` wrap-up phase implementation.** Three items, sequential because they all edit `plugins/sdd/skills/build/SKILL.md`. Splitting horizontally by responsibility:

- **Item 1: Wrap-up entry + iteration-candidate handling.** The satisfaction question, the surfacing of `[iteration-candidate]` markers, the per-candidate disposition prompts, the satisfied/iterate branching. 9 ACs (`aaeh`–`aaep`).
- **Item 2: Close-sprint actions.** PRD AC checkoff (Bug Fix 2 + cross-cutting handling), story splits, "anything notable?", process notes write, close-sprint-manifest block. 8 ACs (`aaad`–`aaag`, `aaeq`–`aaet`).
- **Item 3: Next-command routing + v2-verification writes + /retro gating.** The final wrap-up output: write FB-row Shipped columns (gated on file presence), recommend next command based on PRD state. 4 ACs (`aaeu`, `aafa`, `aahk`, `aahv`).

Rationale for picking this grouping over alternatives:

- **Why three items, not one?** A single 21-AC item would be hard to verify granularly. Three logical chunks — entry, actions, output — let the build agent verify each ACs band cohesively.
- **Why not split further?** Items can't be reordered: Item 2 requires Item 1's wrap-up entry point to exist; Item 3 routes Item 2's outputs. Sub-splitting Item 2 (e.g., separating PRD checkoff from story splits) would create awkward fragmentation — both are close-sprint actions that share the same loop over closed items.
- **Why /sdd:build wrap-up now, not /sdd:plan?** /sdd:plan does plan-side validation (`[PRD: ...]` tag presence, PRD state check, sprint-mode memory). It's important and will follow this sprint. But /build wrap-up unlocks Bug Fix 2 and the v2-verification write behavior, which are the audit-trail mechanics. Without those, every future sprint has to manually reconcile PRD checkoff and Shipped-column writes. Doing /build wrap-up first means Sprint 6 can dogfood the wrap-up against itself.
- **Why exclude /sdd:polish re-open?** `aaev`, `aaew`, `aaex` belong to /sdd:polish, not /sdd:build. /polish re-open reads the `[close-sprint-manifest]` block this sprint produces, but the parsing/reversal logic is /polish-side. Cleaner as a separate (small) sprint.

### Out of scope (explicit)

- `/sdd:polish` re-open (`aaev`, `aaew`, `aaex`) — /polish work.
- `/sdd:plan` implementation (`aado`–`aaeg`) — separate command, separate sprint.
- Other interview command behaviors (clarifying-questions-don't-advance, deepening-round caps, etc.) — `/sdd:build` is not an interview command.
- `aaho`, `aahp`, `aahu`, `aahw`, `aahx` — negation/preservation ACs about v2-verification.md never-written / never-gated / sdd-plugin-only / never-deleted / future-version-pattern. Satisfied by absence. No active work.
- `aahl`, `aahm` — structural / spec-deferral ACs already satisfied by sprint 4's seeded table and the spec itself.

### Proposed grouping

3 items, sequential execution (same file edits):

1. `plugins/sdd/skills/build/SKILL.md` wrap-up entry + iteration-candidate handling. 9 ACs.
2. `plugins/sdd/skills/build/SKILL.md` close-sprint actions (PRD AC checkoff + story splits + "anything notable?" + process notes + manifest). 8 ACs.
3. `plugins/sdd/skills/build/SKILL.md` next-command routing + v2-verification writes + /retro gating. 4 ACs.

User confirmation: planning ran under the user's "work without stopping for clarifying questions" directive. Sprint plan was assembled and presented as a single proposal. User can redirect post-generation by editing `docs/sprint-5.md` before `/sdd:build` runs.

### Execution order decision

**Sequential, no parallel option.** All three items edit `plugins/sdd/skills/build/SKILL.md`. Item 2 builds on Item 1's wrap-up entry-point section; Item 3 builds on Item 2's close-sprint manifest output field. Autonomous mode dispatches subagents one at a time.

This is a different execution profile from sprint 4 (where all four items were on different files and ran in parallel). Sprint 5's autonomous mode is autonomous-sequential rather than autonomous-parallel. Same mode name, different shape — the dispatch pattern adapts to the items.

### Build mode

**Autonomous**, matching priors. Items are spec-mapped: every AC has a clearly-defined target in the SKILL.md and a clearly-defined source section in spec.md. No deepening required mid-build. Mode locked for sprint 5.

### Deepening rounds

None requested. Rationale: every AC has been pre-vetted (sprint planning surfaced no scope ambiguity); the spec already defines the wrap-up phase end to end; and the work is structurally well-understood (a SKILL.md authoring task with clear acceptance criteria). The mechanical-vs-exploratory ratio favors mechanical.

### Tagging convention

- Items 1, 2, 3 all carry real `[PRD: ...]` tags. No `[unmapped]` items.
- **No `[FB: ...]` tags on Sprint 5 items, despite Bug Fix 2 (`aaad`–`aaag`) mapping to v2-verification row FB-002.** Reason: Sprint 5 IS the bootstrap that implements the wrap-up that processes `[FB: ...]` tags. The tag-handling code is being built in this sprint; tagging this sprint with the very tag it implements would require the wrap-up to dogfood itself before it exists. After Sprint 5 ships, a manual one-time note on FB-002 will record "Shipped: sprint 5, item 2" by hand. From Sprint 6 onward, the wrap-up does the auto-write.

### New concerns surfaced during planning

- **Self-bootstrap problem on Sprint 5 itself.** Wrap-up handling of `[FB: ...]` tags ships in Sprint 5. Sprint 5 cannot dogfood that handling on Sprint 5 — by the time `/sdd:build` runs against Sprint 5 and reaches its own end, the new wrap-up code is present in the SKILL.md but not yet exercised. First real dogfood happens when /build runs against Sprint 6 (or whatever sprint runs next). Sprint 5's wrap-up may itself be partially manual or skipped — the existing SKILL.md's "Sprint complete" terminus fires, and the new wrap-up phase doesn't execute until the SKILL.md edits are themselves loaded into a build session that follows them. This is normal bootstrap-cycle behavior; documenting it so future readers don't mistake "Sprint 5 didn't trigger wrap-up" for a bug.
- **Cross-cutting AC handling correctness depends on scanning all sprint files.** Item 2's Bug Fix 2 cross-cutting logic must scan every `docs/sprint-*.md` file to determine whether all contributing items have closed. This is fine in practice (sprint files are append-only and read cheap) but does require an unambiguous parser for `[PRD: id1, id2]` tag references across files. The parser is already specified in `references/sprint-tags.md`; Item 2 should explicitly reference that parser, not re-implement.
- **Story-split AC ID generation needs to collision-check.** New ACs introduced by splits get fresh 4-char IDs. The PRD currently has IDs `aaaa` through `aahx`. Item 2 must specify that new IDs are generated by sampling from `[a-z]{4}` and collision-checking against the existing PRD before settling. The spec's `Cross-Cutting Mechanisms > PRD Acceptance Criteria IDs` section already defines this; Item 2 should reference it.
- **Verification of the wrap-up phase itself is by SKILL.md re-read, not by execution.** Markdown-only plugin, no runtime. The build agent verifies the SKILL.md spec language satisfies each AC by reading the file. End-to-end verification fires only when /build runs against a real sprint. This is consistent with `docs/spec.md > Testing Strategy` (no automated tests; verification is structural + dogfood).

None of these rise to "Open concern" status — each has a documented resolution in the sprint items themselves. Not adding to `docs/open-concerns.md`.

### Decisions log

| Decision | Choice | Rationale |
|---|---|---|
| Sprint 5 theme | `/sdd:build` wrap-up phase implementation | Highest-blocking remaining v2 deliverable; unlocks Bug Fix 2, v2-verification writes, /retro gating, and the "cohesive close-out" Pillar 3 |
| Bundle into one item or split? | Three items (entry, actions, output) | A 21-AC single item is hard to verify granularly; three logical bands map to natural SKILL.md sections |
| Sub-split Item 2 (PRD checkoff vs story splits)? | No | Both are close-sprint actions over the same item-closed loop; splitting them creates awkward fragmentation |
| Include /sdd:polish re-open? | Defer | Belongs to /polish, not /build; reads the manifest this sprint produces but parses on /polish side |
| Include /sdd:plan implementation? | Defer | Separate command; planning side of the sprint loop; depends on no Sprint 5 outputs |
| Execution mode for parallel-vs-sequential? | Sequential | All three items edit the same file; autonomous mode dispatches sequentially |
| Build mode | Autonomous | Matches priors; items are spec-mapped and mechanical |
| Deepening rounds | None | Pre-vetted ACs, fully specified wrap-up phase, structurally well-understood; no exploratory thread |
| `[FB: ...]` tagging on Sprint 5 items | None | Self-bootstrap: the tag-handling code ships in this sprint, so this sprint can't be tagged with it; manual FB-002 note will be added after the SKILL.md edits ship |
| Note on user input | Planning ran under "no clarifying questions" directive | User can redirect by editing sprint-5.md before /sdd:build runs |

## Sprint Close — Wrap-Up Notes — 2026-05-13

Sprint 5 closed 3/3 items, all sequential, all editing a single file: `plugins/sdd/skills/build/SKILL.md`. Build mode: autonomous. Dispatched three subagents in order; each subagent reported success and the orchestrator verified the file state between dispatches. Final wrap-up phase shape: W1 → W11, monotonically numbered, all under the single `## Wrap-Up Phase` heading.

### What landed

- **Item 1** (W1–W5): satisfaction question + iteration-candidate handling. Closed 9 ACs (`aaeh`–`aaep`).
- **Item 2** (W6–W10 originally; W6–W8 + W10 after Item 3's renumber): PRD AC checkoff, story splits, "anything notable?", process-notes write with `[close-sprint-manifest]` block. Closed 8 ACs (`aaad`–`aaag`, `aaeq`–`aaet`) plus the unnumbered manifest AC.
- **Item 3** (inserts W9; renames previous-W9 → W10 and previous-W10 → W11): v2-verification row updates (activation-gated on file presence), next-command recommendation. Closed 4 ACs (`aaeu`, `aafa`, `aahk`, `aahv`) plus 2 unnumbered ACs (Verified-column never-written; End-of-Command Handoff template wording).

Reframed step-by-step Step 2 and autonomous Step 4 to route into wrap-up rather than emit their old terminal "Sprint complete" messages. Removed the duplicate `process-notes-sprint-N.md` write that autonomous Step 4 used to perform — W10 is now the single source of truth.

### Decisions and rationale

- **Renumber W9 → W10 rather than W9a-suffix.** Single monotonic sequence reads cleaner than fractional suffixes. Required updating in-document cross-references (autonomous Step 4 description, "No Per-Item Process Notes" note, and W8/W9 forward-references); all resolved.
- **Sprint-file marking for story splits.** Author chose to leave unfinished `[ ]` items as-is rather than annotating with a split note. The `[close-sprint-manifest]` block is the canonical record of where work moved. Documented inline in W7 so future readers understand the choice.
- **Tech-debt formatting in process notes.** Author may use either `### Tech-debt` header + sub-list OR `[tech-debt]` line markers per entry. Both stated as human-readable and machine-locatable for `/sdd:plan`'s carry-forward read. No single canonical format mandated — explicit author choice in the SKILL.md.
- **Step 4 / W10 deduplication.** Removed Step 4's process-notes write bullet; replaced with explicit prose stating that W10 is the single source of truth. Step 4 retains its user-facing reporting bullets (totals, what was built, issues) and the open-concerns update.

### Pushback

- **Subagent-to-subagent baton-pass via "concerns for next item" reporting.** Item 2's subagent self-flagged that Item 3 would need to either re-order the v2-verification step before manifest assembly OR overwrite the manifest post-write. Item 3's subagent took the cleaner re-order path. The structured concerns-handoff saved a round-trip.
- **No user-side pushback.** User invoked `/sdd:build` autonomously with explicit "no clarifying questions" directive; no interactive corrections during build.

### Surprises

- **None mid-sprint.** Each item landed on first dispatch. The Item 3 renumber was self-contained.
- **Bootstrap dogfood is partial.** This wrap-up section is itself a meta-dogfood: the orchestrator followed the new wrap-up phase logic (defined in the SKILL.md edits that just landed) against this very sprint, even though the currently-loaded `/sdd:build` plugin is still the cached pre-edit version. First real end-to-end dogfood will fire when `/sdd:build` runs against sprint 6 with the updated SKILL.md loaded.

### Notable items (W8 prompt)

The W8 "anything notable?" prompt is normally answered interactively. For this autonomous run under the no-stop directive, the orchestrator made the reasonable call: **assumed nothing notable** beyond what is captured in this process-notes section. The user is free to append observations to this file before moving to `/sdd:plan` if anything surfaces. Documented as an intentional deviation, not a missed step.

### Iteration candidate dispositions

W1's scan of this file (before wrap-up appended this section) returned **zero** `[iteration-candidate]` entries. W4 (per-candidate disposition) was skipped. No dispositions to record.

### Tech-debt entries

(none)

### Discard decisions

(none)

### Bootstrap note — manual back-fill required

Sprint 5 Item 2 implements Bug Fix 2 (`aaad`–`aaag`), which corresponds to row `FB-002` in `docs/v2-verification.md`. Because the wrap-up that populates the Shipped column was being built **in this sprint**, sprint 5 items do not carry `[FB: ...]` tags (planning decisions log entry above). The maintainer should manually append `sprint 5, item 2` to `FB-002`'s Shipped column in `docs/v2-verification.md` before declaring the v2-verification audit trail complete for FB-002. This is a one-time bootstrap deviation; sprint 6+ will exercise the auto-write mechanism for real.

### [close-sprint-manifest]

```
[close-sprint-manifest] timestamp: 2026-05-13T00:00:00Z
- PRD ACs checked: aaad, aaae, aaaf, aaag, aaeh, aaei, aaej, aaek, aael, aaem, aaen, aaeo, aaep, aaeq, aaer, aaes, aaet, aaeu, aafa, aahk, aahv
- Story splits: (none)
- Iteration candidate dispositions: (none)
- Tech-debt entries: 0 (informational)
- v2-verification rows updated: (none) — sprint 5 carries no [FB: ...] tags by design (bootstrap sprint); manual back-fill required for FB-002
[/close-sprint-manifest]
```
