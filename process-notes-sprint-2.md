# Sprint 2 — Process Notes

## Planning Session — 2026-05-13

### Context

Second v2 implementation sprint, still using v1 `/sdd:sprint` (the v2 `/sdd:plan` rename + behavior upgrade is itself scheduled for sprint 3). Sprint 1 closed with PRD foundation complete: 206 unique AC IDs backfilled, three spec-mandated PRD edits applied, alphanumeric AC-ID slips scrubbed from spec, five foundation open-concerns resolved. All 206 PRD ACs are still unchecked because sprint 1 was pure meta-work; sprint 2 begins actual v2 implementation.

### PRD scan

- 13 epics in `docs/prd.md`, 206 ACs total, 0 checked off.
- 0 unvetted items (Unvetted section contains only the marker comment).
- No `/sdd:refine` recommendation surfaced — nothing in the Unvetted section to vet.

### Open concerns processed

Only one concern remains in `## Open`: "Soft acceptance criteria for MIT distribution" — targeted at `/sdd:refine`, not `/sdd:sprint`. Surfaced but not resolved (correct disposition; nothing for sprint planning to do with it).

### Sprint theme proposed

**Shared behavior layer foundation.** Stand up the cross-cutting reference files and `sdd-guide/SKILL.md` v2 additions so every subsequent per-command sprint can load from a v2-correct foundation. Deliberately additive — no renames, no per-command behavior, no bug fixes in this sprint.

Rationale: dependency analysis on the spec shows every command-behavior implementation depends on (a) one or more of the four NEW reference files, (b) v2 updates to the three existing reference files, or (c) v2 behavioral additions to `sdd-guide/SKILL.md`. Standing this layer up first means subsequent sprints can implement per-command behavior against stable infrastructure rather than continuously building infra-as-needed.

### Proposed grouping

8 items, all single-file edits or creations, all independent (each touches a different file):

1. Create `references/right-sizing.md` (NEW).
2. Create `references/pause-resume.md` (NEW).
3. Create `references/sprint-tags.md` (NEW) — most consequential; contains every tag format and parser regex used in v2.
4. Create `references/context-management.md` (NEW) — three-tier between-rounds model.
5. Update `references/deepening-rounds.md` — lock 5/10 caps + topic-preview framing.
6. Update `references/context-loading.md` — add v2 loading rules.
7. Update `references/living-documents.md` — revised `/refine` update ordering + exclusions for v2-verification.md and backlog.md.
8. Update `sdd-guide/SKILL.md` — clarifying-questions-don't-advance + end-of-command handoff template.

Out of scope (deferred to sprint 3 candidate): atomic rename sweep, Bug Fix 1 (`/sdd:feedback` lastCommand), new skill stubs.

Plugin-level `plugins/sdd/CLAUDE.md` was checked: already a v2-correct minimal loader pointer. No item needed.

User accepted the grouping as-is — single-word "works" response. No edits, removals, or restructuring requested.

### Build mode

User chose **autonomous**. Items are small scoped file creations/edits with clear acceptance criteria. Sprint 1 ran autonomous successfully; same shape of work warrants the same mode.

Build mode locked for sprint 2. No mid-sprint mode switching.

### Deepening rounds

None requested. User's decisive one-word answers ("works", "autonomous") signaled readiness to proceed. Sprint plan went straight to generation.

### New concerns surfaced during planning

None.

### Tagging convention

All 8 items carry `[unmapped]` in their PRD ref because they implement infrastructure that supports cross-cutting PRD ACs but does not directly satisfy any user-visible AC. The user-visible ACs (e.g., "agent emits the end-of-round recommendation literally", "/sdd:scope detects small-project signals during Phase 1") are satisfied in later sprints when individual command SKILLs read these references and behave accordingly. This mirrors sprint 1's `[unmapped]` housekeeping items.

### Decisions log

| Decision | Choice | Rationale |
|---|---|---|
| Sprint 2 theme | Shared behavior layer foundation | Every per-command sprint depends on this layer; standing it up first unblocks everything |
| Include atomic rename sweep? | No — defer to sprint 3 | Rename sweep is its own atomic operation; bundling it would expand sprint 2 surface |
| Include Bug Fix 1? | No — defer to sprint 3 | Small standalone fix; fits more naturally alongside the rename + skill stubs |
| Include Bug Fix 2? | No — defer to later sprint | Depends on `/sdd:build` wrap-up phase implementation |
| Include `plugins/sdd/CLAUDE.md`? | No — already v2-correct | File already exists as a minimal loader pointer per spec |
| Tag format for items | `[unmapped]` for all 8 | Infrastructure work that doesn't directly satisfy user-visible ACs |
| Build mode | Autonomous | Same shape of work as sprint 1; same mode |
| Deepening rounds | None requested | User decisive; plan was clean |


## Build Session — 2026-05-13

### Mode and dispatch

Autonomous build, eight items dispatched as subagents. All eight items are single-file create/update operations on independent files; no inter-item dependencies. Dispatched in two parallel batches of four (1-4 NEW reference files, then 5-8 existing-file updates) for speed; checkpoint pauses skipped per the user's instruction to work without stopping.

### What was built

| Item | File | Outcome |
|---|---|---|
| 1 | plugins/sdd/skills/sdd-guide/references/right-sizing.md (NEW) | 110 lines. Documents smallProject semantics, six-signal heuristic, per-command skippable beats, thinness signal, detection-placement decision. |
| 2 | plugins/sdd/skills/sdd-guide/references/pause-resume.md (NEW) | ~85 lines. Resume-file location convention, seven required sections in exact order, per-section quality bar (table), distill-not-dump rules, cleanup, per-round-summary consolidation. |
| 3 | plugins/sdd/skills/sdd-guide/references/sprint-tags.md (NEW) | 263 lines. Every v2 tag format with regex: PRD AC IDs, [PRD: ...], [unmapped], [FB: ...], [iteration-candidate], [close-sprint-manifest] / [/close-sprint-manifest], [sprint-reopened]. Regexes mentally validated against canonical examples and actual sprint-2.md content. |
| 4 | plugins/sdd/skills/sdd-guide/references/context-management.md (NEW) | 171 lines. Three-tier model (continue / /compact / /clear), recognition signals with practical thresholds, tier-mapping rules, failure-mode mitigation, /clear branch consolidation with pause-resume. |
| 5 | plugins/sdd/skills/sdd-guide/references/deepening-rounds.md (UPDATED) | 41 → 57 lines. Locked 5/10 question caps, 11+ permission gate, two literal recommendation forms, topic-preview required for continuation, reasoning required for closure, bare prompts disallowed. Phase 1 preserved untouched. |
| 6 | plugins/sdd/skills/sdd-guide/references/context-loading.md (UPDATED) | 11 → 13 table rows. Renamed sprint→plan and iterate→polish; removed reflect; added discovery, pause, unpause; revised onboard / spec / plan / build / polish / retro entries. Added re-opening-a-sprint subsection to re-entry. 200-line threshold + CLAUDE.md exemption preserved verbatim. |
| 7 | plugins/sdd/skills/sdd-guide/references/living-documents.md (UPDATED) | Update ordering revised to PRD → spec → open sprint files (if affected) → AGENTS.md → CLAUDE.md, each step user-confirmed. New Exclusions section for docs/v2-verification.md and docs/backlog.md. PRD health monitoring tightened (10+ epics non-blocking). Default Stance and Re-Scoping Trigger preserved unchanged. |
| 8 | plugins/sdd/skills/sdd-guide/SKILL.md (UPDATED) | 94 → 152 lines. Added interaction rule 4 (clarifying-questions-never-advance), new Deepening Rounds section (5/10 caps + recommendation framing cross-referencing deepening-rounds.md), new End-of-Command Handoff section with standard form / first-handoff explanation / next-command map / out-of-pattern handling. Updated Command Chain, Command Explanations, and Process Notes to v2 command names. All v1 sections preserved. |

### Observations

- Spec is the only source needed to drive each item. Each subagent received the relevant spec subsection verbatim plus the item's acceptance criteria; no subagent needed to read the full spec.md, full prd.md, or each other's output. Surgical context loading worked exactly as designed.
- The renames carried through cleanly. context-loading.md, living-documents.md, and sdd-guide/SKILL.md all touched command-name references; the rename guidance in each subagent's prompt was followed without ambiguity.
- Sprint-tags.md is the largest file and the most regex-dense. Subagent mentally exercised every regex against the canonical examples and against [unmapped] in sprint-2.md itself before finalizing — caught no errors, no rework needed.

### Iteration-candidate markers

None. No real iteration candidates surfaced during build. (Per spec, fabrication is prohibited — the absence is correct.)

### Surprises / pivots / pushback

None.

### CLAUDE.md / AGENTS.md updates

None. Build revealed no new project-wide patterns or conventions. Existing CLAUDE.md and AGENTS.md correctly describe the work that was done; no edits warranted.

### Open-concerns delta

No change. The one Open concern ("Soft acceptance criteria for MIT distribution") is targeted at /sdd:refine and unrelated to this sprint's work.

## Wrap-Up — 2026-05-13

### PRD AC checkoff

None applicable. All 8 sprint items carry [unmapped] tags — they implement infrastructure that supports cross-cutting PRD ACs but does not directly satisfy any user-visible AC. Per spec close-sprint logic: parse each closed item's [PRD: ...] tag and update docs/prd.md accordingly; with no [PRD: ...] tags on any item, nothing to check off.

### Story splits

None. All 8 items finished within their original scope.

### Anything notable

Not surfaced for user response yet — pending the wrap-up question to the user (next message).

### v2-verification row updates

None applicable. No item carries a [FB: ...] tag. docs/v2-verification.md does not exist yet (per spec, it is seeded at /sdd:spec finalization — which has not been re-run since v2 spec was finalized; the seeding is itself a future-sprint task or part of the /sdd:spec implementation sprint).

### Next-command recommendation

Sprint complete; 8/8 items done. Next: per CLAUDE.md / spec, sprint 3 candidate is the atomic rename sweep + Bug Fix 1 + new skill stubs for /sdd:discovery, /sdd:pause, /sdd:unpause. Run /sdd:sprint (still v1 — /sdd:plan is the rename target, scheduled for sprint 3) to plan sprint 3.

### Close-sprint manifest

[close-sprint-manifest] timestamp: 2026-05-13T17:30:00Z
- PRD ACs checked: (none — all 8 items were [unmapped])
- Story splits: (none)
- Iteration candidate dispositions: (none — no in-flight candidates surfaced during build)
- Tech-debt entries: 0 (informational)
- v2-verification rows updated: (none — no [FB: ...] tags on any item; file not yet seeded)
[/close-sprint-manifest]

