# Process Notes — Sprint 16

## Planning (2026-05-14, /sdd:sprint via cached v1)

### Context entering planning
- Sprint 15 closed earlier the same day. Nine ACs (`aaev`–`aaex`, `aaey`–`aaez`, `aafb`–`aafe`) checked, closing the `/sdd:polish` re-open and `/sdd:retro at Project Close` epics.
- PRD state at planning time: 46 incomplete ACs across three remaining epics — Automatic Backlog Generation (12), v1→v2 Migration (21), v2 Release Verification remaining (13). Unvetted section empty. No `/sdd:refine` recommendation.

### Epic selection
Three candidates were on the table per Sprint 15's "natural next-sprint candidates" note:
- **Automatic Backlog Generation** (12 ACs) — cross-cutting across 5 planning commands; functionally the next-biggest visible gap.
- **v1→v2 Migration guide** (21 ACs) — single-file write; best written against final-state v2.
- **v2 Release Verification remaining** (13 ACs) — seeding hook in `/sdd:spec` + dogfood-approach docs.

**Picked Automatic Backlog Generation.** Rationale recorded inline in the sprint file's *Theme* / *Out of scope* sections:
- Migration is best last — written against final-state v2; v2 still has 25 non-migration ACs left, so the migration narrative would churn if written now.
- Release Verification is dogfood docs + a small seeding hook; lower-stakes; can bundle near GA.
- Backlog Generation is the functional gap a user would actually notice when running v2 commands today — it's a real user-facing mechanism with the spec already pinning every design call.

No pushback from the user on the epic choice; reply was "works for me."

### Item grouping
Two items, sequential:
1. **Backlog reference file + write triggers** (9 ACs `aafs`–`aaga`) — covers Story 1 (write trigger) and Story 2 (entry format) of the epic. One new `references/backlog.md` plus wiring into 5 SKILL.md (discovery, scope, prd, spec, plan).
2. **Artifact pointer mechanism** (3 ACs `aagb`–`aagd`) — covers Story 3 (artifacts point at backlog rather than restate). 5 template edits + same 5 SKILL.md getting an artifact-generation conditional check.

Considered splitting Item 1 further (reference-file write vs SKILL.md wiring) but rejected — the two changes are tightly coupled and reviewing them together is easier than reviewing them apart. Two items keeps the Sprint 14-15 cadence (small number of items, atomic edits).

Item 1 includes reconciling `/sdd:scope`'s existing project-split backlog write path (current `scope/SKILL.md` Steps 195-197 + the `### Backlog (if applicable)` section ~240-242) with the new defer-to-backlog prompt — the mechanism (file location, entry format) is unchanged; the user gating is the new addition. Flagged this in the item description so build doesn't surprise itself with the pre-existing scope text.

### Build mode
**Autonomous.** Matches Sprint 5-15. Both items are mechanical (5 SKILL.md edits + one reference file + template adjustments) with the spec resolving every design call. Step-by-step `/clear` between items would burn cycles for no review benefit. Build mode locked for the sprint.

Reply: "works for me."

### Deepening rounds
**Closed without deepening.** Recommendation: "I do not think we need another round, because the spec resolves every substantive design call and the remaining ambiguities are build-time judgment, not plan-time decisions."

Topics a round would have covered (logged in the sprint file's *Notes > Topics deferred to /sdd:build context*):
- Item 1: exact placement of the defer-prompt routing in each SKILL.md flow.
- Item 1: whether `references/backlog.md` should document a "first-write bootstrap" template for `docs/backlog.md`. (Decision: yes, include the one-line HTML comment marker; verify text at build.)
- Item 1: how cleanly `/sdd:scope`'s existing project-split write path reconciles with the new prompt routing — section-level rewrite vs localized edits.
- Item 2: section-heading text for the pointer block per template (template-author's call; pointer line is fixed).
- Item 2: whether the backlog-state check (file exists + `^### ` heading count) should be lifted to `references/backlog.md` for cross-reference rather than five duplicate inline checks. (Bias toward referencing.)

No user override; reply was "generate."

### Open concerns at planning time
- **Soft acceptance criteria for MIT distribution** — carried open concern targeting `/sdd:refine`. Surfaces naturally during Sprint 16 because backlog entries are exactly the judgment-based ACs the concern flags ("Trigger to revisit," "Why deferred" — both rely on author judgment). Not a sprint-16 item — it lands when the concern's target command (`/sdd:refine`) next runs against judgment-based ACs. Reaffirmed as deferred; no action.

### State writes
- `docs/sprint-16.md` written.
- `docs/project-state.json` updated: `currentSprint: 16`, `buildMode: autonomous`, `lastCommand: /sdd:sprint`. Notes field rewritten to reflect Sprint 16 plan.
- `docs/open-concerns.md`: no new concerns appended; no existing concerns resolved or deferred by sprint planning.

### Mode wrap-up
Sprint 16 is two items, sequential, autonomous, no deepening rounds, 12 ACs closing the Automatic Backlog Generation epic in full. Heaviest work in Item 1 (one new reference file + 5 SKILL.md edits). Item 2 is the artifact-side mirror (5 template edits + 5 same-SKILL.md edits for the conditional pointer check).

Cached marketplace caveat applies as usual — none of these edits change agent behavior until reinstall. First real exercise of the defer-to-backlog prompt requires a v2 planning command post-reinstall.

## Build summary (2026-05-14 — autonomous, sequential)

Two items executed sequentially via two single-purpose subagent dispatches. Build mode locked autonomous per the sprint file. Orchestrator handled context loading, sprint-file checkoffs, PRD checkoff propagation, and the close-sprint manifest write.

### Item 1: Backlog reference file + defer-to-backlog write triggers — closed (`aafs`, `aaft`, `aafu`, `aafv`, `aafw`, `aafx`, `aafy`, `aafz`, `aaga`)

One new file + 5 SKILL.md edits via one general-purpose subagent dispatch. New file `plugins/sdd/skills/sdd-guide/references/backlog.md` carries all 8 sections (Purpose / Entry format / Source field format / Write trigger / Writers / Append order and modification rule / Bootstrap / Parser note). The 6-field schema, the defer-vs-drop prompt shape, the three verbatim Source-field examples, and the parser regex (`^### ` heading scan) all live exclusively in this file. The cross-reference to `references/living-documents.md > Exclusions from the Living-Documents Chain` confirms `/sdd:refine` does not touch `docs/backlog.md`.

5 SKILL.md edits added `references/backlog.md` to each command's startup load list and inserted a short defer-routing block at the natural deferral anchor in each flow. Anchors chosen by the subagent:
- `discovery/SKILL.md` — new `## Deferrals` section between Phase 2 deepening and Process Notes.
- `scope/SKILL.md` — rewrote step 5 of `## Project Size Assessment` in place (the pre-existing project-split write path now routes through the prompt); `### Backlog (if applicable)` cross-references step 5 plus the reference file.
- `prd/SKILL.md` — new `### Deferrals` between Phase 2 (Requirements Organization) and Deepening Rounds. Includes a note clarifying that Unvetted-section items are NOT deferrals.
- `spec/SKILL.md` — new `### Step 11a: Deferrals` between Step 11 (Multi-Service) and Step 12 (Deepening). Clarifier names Open Issues as legitimately in-spec (so the prompt does not over-fire).
- `plan/SKILL.md` — new `### Step 4a: Deferrals` between Step 4 (User Decides) and Step 5 (Build Mode Choice). Clarifier states PRD Unvetted and existing backlog entries are not re-deferred.

None of the 5 SKILL.md files restate the 6-field entry schema, the prompt text, or the parser regex inline — all routing crosses-reference `references/backlog.md`.

Bootstrap marker comment chosen: `<!-- docs/backlog.md — append-only deferral log written by SDD planning commands. See plugins/sdd/skills/sdd-guide/references/backlog.md. -->`. The spec only required "HTML-style comment naming the file's purpose"; the back-reference to the canonical reference file was added so a future agent landing on the file cold finds the canonical context immediately.

Minor surface noted (informational, not an open concern): the `### Backlog (if applicable)` section in `scope/SKILL.md` is now slightly redundant with `## Project Size Assessment` step 5 — both describe the same prompt-gated write. Kept both because each is reachable from a different navigational entry point; a future cleanup pass could collapse them.

### Item 2: Planning artifacts emit backlog pointer — closed (`aagb`, `aagc`, `aagd`)

5 template edits + 5 SKILL.md edits via one general-purpose subagent dispatch. Each of the 5 planning-command templates now carries the conditional pointer-block placeholder with an HTML-comment guidance block above the literal line `Deferred items: see \`docs/backlog.md\`.`. Section headings chosen per artifact:
- `docs/scope.md` → `## What's Explicitly Cut` (kept existing heading)
- `docs/prd.md` → `## What We'd Add With More Time` (kept existing heading)
- `docs/spec.md` → `## Out of Scope` (new heading; `## Open Issues` left untouched and explicitly distinguished as NOT a deferred-items section)
- `docs/sprint-N.md` → `## Out of Scope` (new heading at end of file)
- `docs/discovery.md` → `## Deferred Topics` (new heading; distinct from existing `## Open Questions` which carries unresolved threads still in play for `/sdd:scope`)

Each of the 5 SKILL.md files (discovery, scope, prd, spec, plan) now performs the backlog-state check at artifact-generation time, cross-referencing `references/backlog.md > Parser note` rather than restating the regex. When `docs/backlog.md` is absent or has zero `^### ` entries, the writer omits both the heading and the body — no orphan headings, no empty sections.

Subagent flagged one judgment call as a potential follow-up concern: the templates carry the literal pointer line as visible body content (not commented out) so the writer can see the exact text it should produce. A naive template copy without performing the SKILL.md-prescribed backlog check would emit the line even with no backlog. Mitigation lives in the HTML-comment guidance above each placeholder plus the SKILL.md instructions. Orchestrator judged this acceptable — the template+SKILL coupling is the existing convention in the plugin, and the comment guidance is unambiguous. Recording here so it surfaces if a maintainer ever decides to harden the template pattern.

### Sprint-wide notes

- **Two subagent dispatches, no inline implementation by orchestrator.** Each item went to its own general-purpose subagent with a self-contained brief carrying the spec sections, sprint item description, AC list, verification criteria, and explicit constraints (markdown-only, no auto-commit, file scope, do-not-touch list). Orchestrator verified each subagent's report by spot-reading the modified files and grep-checking the no-inline-restatement invariants.
- **Zero deviations from sprint plan.** Both items landed in the intended files (1 new reference + 5 SKILL.md in Item 1; 5 templates + 5 SKILL.md in Item 2). No cross-file scope creep. The "topics deferred to /sdd:build context" enumerated in the sprint planning notes all resolved cleanly at build time: defer-prompt anchors picked per file; bootstrap marker text settled with back-reference to reference file; scope project-split reconciled via in-place step rewrite; pointer section headings chosen per artifact; backlog-state check lifted to `references/backlog.md > Parser note` and cross-referenced from each SKILL.md (per the planning-note bias toward referencing).
- **No `[FB: ...]` tags on any item.** The Automatic Backlog Generation epic is PRD-walkthrough-derived (Round 2 v2 spec interview), not v1-feedback-derived. `docs/v2-verification.md` is not touched at this sprint's close — zero rows updated, zero rows added.
- **No `[iteration-candidate]` markers written during build.** No real candidates observed across either subagent run.
- **No tech-debt entries.** All edits are clean additions or in-place rewrites with no shortcuts taken.
- **PRD checkoff propagated immediately on close.** All 12 ACs (`aafs`, `aaft`, `aafu`, `aafv`, `aafw`, `aafx`, `aafy`, `aafz`, `aaga`, `aagb`, `aagc`, `aagd`) flipped from `[ ]` to `[x]` in `docs/prd.md` at sprint close, matching Sprints 11-15 discipline.
- **Post-checkoff PRD state.** 34 ACs remain unchecked across 2 epics: v1→v2 Migration (21 ACs `aago`–`aahi`) and v2 Release Verification remaining (13 ACs `aahj`, `aahl`–`aaht`, `aahu`, `aahw`, `aahx`). Unvetted section empty. The Automatic Backlog Generation epic closed in full this sprint. Per `build/SKILL.md` W11 routing: project not complete, recommend `/sdd:plan` for the next sprint loop (neither project-close path nor refine path applies).
- **Open concerns.** No changes. The single open concern ("Soft acceptance criteria for MIT distribution") remains targeted at `/sdd:refine`; not sprint-relevant. The sprint surfaced no new concerns. The judgment-based ACs in `docs/backlog.md` entry fields (e.g., "Trigger to revisit," "Why deferred") are exactly the kind of soft criteria the open concern flags, so the concern is reaffirmed-as-deferred until the next `/sdd:refine` pass against judgment-based ACs.
- **Cached marketplace caveat.** All edits ship to disk but don't change live agent behavior until the maintainer reinstalls the cached marketplace plugin. First real exercise of the defer-to-backlog prompt requires running a v2 planning command post-reinstall.

### Verification recap (per-item)

All sprint-item verification criteria confirmed by orchestrator re-read post-edit:

- **Item 1.** `references/backlog.md` exists with all 7 enumerated sections + parser note; entry-format example matches `spec.md > Backlog Entry Format` field-for-field; defer-vs-drop prompt defined as the explicit gate (affirmative-only writes, drops to process-notes only); all 5 SKILL.md files load `references/backlog.md` on startup; all 5 SKILL.md files route deferral decisions through the prompt with a cross-reference to the reference file rather than restating it; `scope/SKILL.md`'s existing project-split write path reconciled (writes still land in `docs/backlog.md`, now go through the prompt); no SKILL.md restates the 6-field schema or the prompt text inline (verified by grep — only `references/backlog.md` carries the field-marker pattern `**Trigger to revisit:**` / `**Why deferred:**`).
- **Item 2.** All 5 templates contain the pointer-block placeholder (the literal line `Deferred items: see \`docs/backlog.md\`.` plus an HTML-comment guidance block above naming the conditional check); all 5 SKILL.md files perform the backlog-state check at artifact generation (file existence + `^### ` heading count via cross-reference to `references/backlog.md > Parser note`); conditional behavior confirmed in SKILL.md text — when the backlog is absent or empty, no pointer is emitted and the artifact's existing structure is preserved; pointer line uses the established phrasing verbatim across all 5 templates and occupies a single line.

[close-sprint-manifest] timestamp: 2026-05-14T20:50:00Z
- PRD ACs checked: aafs, aaft, aafu, aafv, aafw, aafx, aafy, aafz, aaga, aagb, aagc, aagd
- Story splits: (none)
- Iteration candidate dispositions: (none)
- Tech-debt entries: 0 (informational)
- v2-verification rows updated: (none) (informational — no [FB: ...] tags on any item this sprint)
[/close-sprint-manifest]
