# Process Notes — Sprint 1 (v6)

## Planning session

- **Build mode:** autonomous (from profile `defaultSprintMode`; toggle kept). Confirmed, no re-pick.
- **PRD state at start:** 69 ACs, all unchecked; Unvetted empty → proceeded to planning (routing rule 3).
- **Proposed grouping (accepted):** Sprint 1 = the authoring front end + enabling foundations —
  Foundations (`fsch`, `fcek`, `fchn`, `fcas`, `ftpl`, `fgit`, `fgbl`, `ffea`), Discovery (12),
  Refine (7), Validate (6), Prototype (11), Lesson Capture (4), Checkpoint (5).
  Sprint 2 = the back half (Build, Resolve-PR, Retro) + migration cleanup (`farc`, `frem`).
  Milestone: brain-dump → refined `plan.md` → optional validate → walkable prototype, end-to-end.
- **Ordering decision:** foundations lead the checklist; the build-loop works items in issue-number
  order (maintainer's call). Foundations (`fsch`/`fchn`/`ftpl`/`fcas` + `markers.md`) are hard
  deps for the command SKILLs.

## Mid-planning discrepancy sweep (maintainer-requested)

The maintainer paused the deepening round to sweep `scope.md`/`prd.md`/`spec.md` for drift before
generating the sprint — rationale: the single-doc / single-session model exists to catch exactly
this, so fix it at the source. Found 8; resolutions below. Source of truth was confirmed with the
maintainer on each ambiguous item (no assumptions).

1. **`/sdd:resolve-pr` had no PRD coverage.** Spec introduced a whole vendored command (branch
   hygiene + feedback threads + stamping convention); PRD only had `brpf` naming the *personal*
   `resolve-pr-feedback` skill. **Resolution:** added a **Resolve-PR epic** to `prd.md` — new ACs
   `rpvc` (vendored command), `rpbh` (branch hygiene), `rpst` (stamping convention); moved/reworded
   `brpf` (feedback resolution) into it and repointed it at `/sdd:resolve-pr`. Updated spec's
   resolve-pr "Implements" line to `prd.md > Resolve-PR (rpvc, rpbh, brpf, rpst)`. Added a
   `/sdd:resolve-pr` row to the scope command table; fixed scope's "Done" + build prose to name the
   command, not the skill. Count fixed to "two anytime utilities" (checkpoint + resolve-pr) in scope
   and prd. **Note left for the maintainer:** `rpst` (stamping) is implemented by the *build-loop*
   but grouped under the Resolve-PR epic because it exists to serve resolve-pr's recognition — a
   grouping judgment call, movable to Build if preferred.
2. **`fcas`: context-management.md "change" vs "delete."** Spec deletes it outright. **Resolution:**
   maintainer confirmed delete. Reworded `fcas` (delete context-management.md; also folded in the
   full reference cascade — retire sprint-tags, add markers.md + build-loop.md, update
   living-documents/deepening-rounds/right-sizing) and scope's cascade note.
3. **`vslf`: PRD "auto-runs" vs spec "offer, ask-first."** Maintainer confirmed **ask-first** (a lot
   of prior discussion he didn't want lost). Reworded PRD `vslf` to offer/ask-first; retired the
   spec's "flag for wording tweak" note.
4. **`dnam`/`rfin`: draft/final as a separate marker vs version major digit.** Maintainer: **version
   numbering is the source of truth.** Reworded PRD `dnam` (draft = major `0`, `0.1`) and `rfin`
   (finalize = `0.x → 1.0` bump); updated scope's single-document note; retired the spec's alignment
   flags (inline note, Key Decisions tradeoff, Open Issues bullet).
5. **`ftpl` undercounted templates.** Maintainer initially said "keep discovery template," then
   corrected — it was a misread: discovery still runs, but everything lands in `plan.md` (where the
   version numbering lives), so there's no discovery.md and the discovery template **is** dropped
   (spec was right). Reworded `ftpl` to enumerate drop scope/prd/spec/sprint/discovery + add
   plan-template + keep/update retro-template.
6. **Two new reference files (`markers.md`, `build-loop.md`) had no AC.** Maintainer: **fold in.**
   Folded into `fcas` (see #2).
7. **`fcek` scope undercount.** AC said "six-command flow, including validate"; spec rekeys all 13
   `commandExplanationsShown` entries. Maintainer: **fold it in.** Reworded `fcek` to "full v6
   command set (chain + anytime commands)"; aligned scope's schema note.
8. **`scope.md` line 30 self-contradicting step numbers** (build "step 8" → 9; prototype "step 6" →
   7–8). **Resolution:** cleaned up.

**Cleared as already-consistent:** filename → `plan.md`, versioning walk, schema drop of
`currentSprint`/`buildMode`, archive's prototype exclusion, the two backlog deferrals, lesson-capture
excluding `validate` (consistent design choice across docs, not drift).

**Re-sweep result:** after edits, a second pass over the three docs found only stale skill-vs-command
references (scope "Done" line) and the `fcek`/schema-note undercount echo in scope — both fixed. All
remaining `resolve-pr-feedback` mentions are correct (they reference the personal skill the command
reuses; maintainer confirmed keep-as-is). No further drift.

## Deepening round 1 (sprint plan)

Four questions; decisions that shape the Sprint 1 checklist:

- **Q1 — Dependency ordering.** Foundations lead the checklist; the build-loop works items in
  issue-number order (maintainer's call). `fsch`/`fchn`/`ftpl`/`fcas` + `markers.md` are hard deps.
- **Q2 — Cross-cutting granularity.** `ffea` = one PR (add the `[CONCERN c#]` behavior to `sdd-guide`
  + `markers.md`; command SKILLs inherit it via sdd-guide — no per-command wiring). `fcas` = split
  into 4 issues, all tagged `[PRD: fcas]`: (1) delete `context-management.md` + retire
  `sprint-tags.md`; (2) add `markers.md` + `build-loop.md`; (3) `living-documents.md` schema-v2;
  (4) `deepening-rounds.md` trim + `right-sizing.md` remap. Sprint items may be finer than ACs.
- **Q3 — Verification bar (per PR).** Level (b): `claude plugin validate` + frontmatter, **plus** a
  consistency check that the SKILL's behavior matches its PRD AC and any reference contract it
  depends on (e.g., a marker-emitting SKILL matches `markers.md`). Full dogfood stays the
  end-of-cycle bar, not the per-PR gate. Sprint-item Verification lines written to this standard.
- **Q4 — Marker carry-forward.** Nothing is force-resolved at any step. Any marker (assumption /
  gap / concern), including a prototype `[GAP]` written into a finalized `plan.md`, carries forward
  through every step to `build`, where `bnod` surfaces it rather than guessing. No "resolve gaps"
  step added; carry-forward is the design (matches spec `rcnt` / "a plan can ship with open markers").

Round closed after Q4 — load-bearing risks (ordering, granularity, verification, carry-forward) all
resolved.

## Sprint cadence decision (overrides earlier grouping)

Maintainer decided: **one large sprint per version.** v6 ships as a single Sprint 1 containing all
72 ACs (all nine epics + Resolve-PR), not the earlier front-half/back-half two-sprint split. v7 will
later get its own single sprint. Updated `scope.md` (line 20 + sacred-constraint bullet) and `prd.md`
("What We're Building") to drop the "two sprints / front-half / back-half / sprint seam" framing;
capture-before-consume is now a within-sprint ordering constraint (capture in
discovery/refine/prototype/build lands before retro consumes). The `smallProject=false` judgment and
the Q1 foundations-first / issue-number ordering still govern within the single sprint.

## Sprint generated

`docs/sprint-1.md` written — 28 checklist items covering all 72 ACs, autonomous mode. Order:
foundations (schema v2, chain, `fcas`×4, templates, GitHub gate, CONCERN behavior) → authoring flow
(discovery/refine/validate/prototype) → lesson capture + checkpoint → build half (issues, build-loop,
resolve-pr, retro) → migration cleanup (`farc`, then `frem` last). `fcas` split into 4 items (all
`[PRD: fcas]`); `rpst` kept in the resolve-pr item (Item 25) per the approved grouping, with Item 24
noting the build-loop applies the stamp. Every Verification line written to the Q3 (b) bar
(`claude plugin validate` + SKILL-vs-AC/reference-contract consistency); full dogfood remains the
end-of-cycle bar.

**Pre-generation cross-doc check (maintainer-requested):** 72 AC IDs, all unique, no dupes; every AC
referenced in the spec; no dangling AC refs; every PRD epic maps to a spec `##` section. No drift
introduced by the resolve-pr epic, wording tweaks, or one-sprint cadence.

**Tag validation:** passed — 28/28 items tagged, all 72 ACs covered, zero phantom tags. `[FB: …]`
prompting skipped (`docs/v2-verification.md` absent). No `plan-resume.md` to clean up.

**Note on state file:** `docs/project-state.json` is still the v1 schema (with `currentSprint`/
`buildMode`) — the v2 migration is Sprint 1 Item 1, not yet built. Set `currentSprint: 1` for the v5
tooling running this build.


## Sprint 1 wrap-up (close-sprint)

### Decisions
- Built inline (orchestrator-direct edits) rather than per-item subagents, per the maintainer's global preference for inline build work; per-item commits preserved the audit trail.
- Adopted a group-branch workflow at the maintainer's direction: `v6` integration branch off `main`, five feature branches (foundations; discovery+refine; validate/prototype/lesson-capture/checkpoint; build/resolve-pr/retro/archive; remove-retired), one PR per group into `v6` (PRs #11-#15), human merge as the gate.
- `references/context-loading.md` deleted (maintainer decision, 2026-07-13): each v6 SKILL carries its own Loading list, so the central table was redundant. Raised and resolved via docs/open-concerns.md.
- `MIGRATION-v1-to-v2.md` deleted during item 28's sweep — entirely composed of instructions to run removed commands.
- plugin.json bumped 3.2.0 -> 4.0.0 with a v6 description (clean break = major bump).
- Item 1 migrated the live project-state.json to schema v2 mid-sprint (dogfood cost: a v5-style /sdd:build re-entry would have balked; accepted knowingly, sprint state carried in-session and in the sprint file).

### Pushback
- None of substance from either side; the maintainer redirected workflow (branching/PR structure) at sprint start and answered the context-loading.md question when asked.

### Surprises
- `deepening-rounds.md` had no between-rounds context-recommendation wiring left to trim — v3's efficiency pass had already removed it, making half of item 6 a no-op.
- A commit-splitting script bug in group 2 (str.index matched an inline backticked mention instead of a heading) corrupted refine/SKILL.md; caught before push, both commits reset and rebuilt cleanly.
- The five retired command directories survived `git rm -r` as empty dirs (untracked leftovers) and needed an explicit rmdir.

### Notable (maintainer)
- None reported at wrap-up.

[close-sprint-manifest] timestamp: 2026-07-14T00:00:00Z
- PRD ACs checked: fsch, fcek, fchn, fcas, ftpl, fgit, fgbl, ffea, ingm, ausd, invl, ingw, noin, dfsc, dfst, dfrq, draf, dnam, dasm, dgap, rwlk, rres, rlop, rnau, rcnt, rpsl, rfin, vopt, vslf, vscn, vbth, vdif, vkrc, pfid, pfps, pfsw, pweb, pnav, pcmp, pwal, plop, pfal, prvw, pmap, lcau, lcsp, lcsr, lcno, ckan, ckrd, ckpr, cknr, ckps, bgat, biss, blop, bchk, bmrg, bnod, bfan, rpvc, rpbh, brpf, rpst, rtls, rtpr, rtre, rttm, rtiq, rtdr, farc, frem
- Story splits: (none)
- Iteration candidate dispositions: (none)
- Tech-debt entries: 0 (informational)
- v2-verification rows updated: (none)
[/close-sprint-manifest]
