# Sprint 1

The whole v6 cycle in one sprint (the maintainer's cadence is one large sprint per version): the clean-break rewrite of SDD's front end into the brainstorm-first, single-`plan.md` flow, plus the autonomous build half. Items are ordered by dependency, foundations first, and the build-loop works them in issue-number order. The load-bearing rule: the shared contracts every command leans on — `project-state.json` schema v2, the updated `sdd-guide` chain, the `markers.md` and `build-loop.md` reference files, and `plan-template.md` — land before any command that consumes them, and the retired commands (`scope`/`prd`/`spec`/`plan`/`polish`) are deleted last, only once the new flow is proven. End state: `discovery → refine → [validate] → prototype → build → retro` plus `checkpoint` and `resolve-pr` run end-to-end on this repo, each SKILL passing `claude plugin validate .` and consistent with its PRD AC(s) and reference contracts, ready for the north-star dogfood (brain-dump → working prototype in ~a day).

Out of scope: the deferred automated third-party AI validation, GitHub-native PR/branch-hygiene automation, and the older cross-cycle items — see `docs/backlog.md`.

## Build Preferences
- **Build mode:** autonomous

## Checklist

### Item 1 — project-state.json schema v2 + explanation-key rekey
- [x] Migrate `docs/project-state.json` to schema v2: bump `version` to 2, add a `settings` object (`prototypeFidelity: null`), drop `currentSprint` and `buildMode`, keep `smallProject` top-level, and rekey `commandExplanationsShown` to the full v6 command set [PRD: fsch, fcek]
- **PRD ref:** `prd.md > Foundations & Migration`
- **Spec ref:** `spec.md > Foundations & Migration > project-state.json schema (v2)`
- **What to build:** Change the on-disk `docs/project-state.json` to the v6 shape per the spec's schema block: `version: 2`; add `settings` (persisted user choices, seeded `{ "prototypeFidelity": null }`); remove the now-vestigial `currentSprint` and `buildMode`; keep `smallProject` at top level (machine-owned, AI-derived); rekey `commandExplanationsShown` to `discovery, refine, validate, prototype, build, retro, checkpoint, resolve-pr, onboard, pause, unpause, feedback, archive`, each `false`. Do not duplicate `plan.md`'s version into state — maturity lives only in the document.
- **Acceptance criteria:** (from `prd.md > Foundations & Migration`)
  - [x] `fsch` — `docs/project-state.json` gains persisted settings (prototype fidelity at minimum).
  - [x] `fcek` — The `commandExplanationsShown` key set is rekeyed to the full v6 command set — the six chain commands plus the anytime commands (`checkpoint`, `resolve-pr`, `pause`, `unpause`, `feedback`, `archive`, `onboard`).
- **Verification:** `claude plugin validate .` passes. The written state file parses as valid JSON and matches the spec's schema-v2 block field-for-field: `settings.prototypeFidelity` present, `currentSprint`/`buildMode` absent, `smallProject` top-level, and `commandExplanationsShown` keys equal to the v6 set.

---

### Item 2 — sdd-guide command chain + remove between-rounds section
- [x] Update `skills/sdd-guide/SKILL.md`: command chain → `discovery → refine → validate → prototype → build → retro` (validate optional); update the interview-command set and end-of-command handoff next-command map; remove the "Between-Rounds Context Recommendation" section entirely [PRD: fchn]
- **PRD ref:** `prd.md > Foundations & Migration`
- **Spec ref:** `spec.md > Foundations & Migration > Shared-behavior cascade`
- **What to build:** In `skills/sdd-guide/SKILL.md`, replace the six-plus-command v5 chain with the v6 chain `discovery → refine → validate → prototype → build → retro`, marking `validate` optional. Update the interview-command list and the End-of-Command Handoff next-command map to the v6 commands. Delete the entire "Between-Rounds Context Recommendation" section — v6 drops between-rounds auto-suggest; context is user-driven via `/sdd:checkpoint`.
- **Acceptance criteria:** (from `prd.md > Foundations & Migration`)
  - [x] `fchn` — `sdd-guide`'s command chain is updated to `discovery → refine → validate → prototype → build → retro`, with `validate` marked optional.
- **Verification:** `claude plugin validate .` passes. The chain string and handoff next-command map in `sdd-guide/SKILL.md` match the spec; a grep confirms the Between-Rounds Context Recommendation section is gone with no dangling references to it.

---

### Item 3 — delete context-management.md + retire sprint-tags.md
- [x] Delete `references/context-management.md` and `references/sprint-tags.md`; remove or repoint any loader references so nothing loads the deleted files [PRD: fcas]
- **PRD ref:** `prd.md > Foundations & Migration`
- **Spec ref:** `spec.md > Foundations & Migration > Shared-behavior cascade`, `spec.md > File Structure`
- **What to build:** Delete `plugins/sdd/skills/sdd-guide/references/context-management.md` (the whole between-rounds three-tier auto-suggest model v6 removes) and `references/sprint-tags.md` (obsolete; `markers.md` replaces it — created in Item 4). Sweep the command SKILLs and `sdd-guide` for any "load `context-management.md`" / "load `sprint-tags.md`" instructions and remove them. The surviving one-liner ("context is user-driven; use `/sdd:checkpoint`") lives in checkpoint's SKILL.md (Item 22), not here.
- **Acceptance criteria:** (from `prd.md > Foundations & Migration`, `fcas`)
  - [x] `fcas` (part) — `context-management.md` is deleted outright and `sprint-tags.md` is retired.
- **Verification:** `claude plugin validate .` passes. Both files are gone; a repo-wide grep finds no surviving loader references to either.

---

### Item 4 — add markers.md + build-loop.md reference contracts
- [x] Create `references/markers.md` (the inline-marker parser contract) and `references/build-loop.md` (the vendored loop skeleton), each per its full spec design section [PRD: fcas]
- **PRD ref:** `prd.md > Foundations & Migration`
- **Spec ref:** `spec.md > Markers`, `spec.md > Build-Loop Skeleton`
- **What to build:** Create `references/markers.md` as the single parser contract for inline markers per spec `## Markers`: the three types (`[ASSUMPTION a#]`, `[GAP g#]`, `[CONCERN c#]`), the optional `PROPOSED` form, the stateless round-local ID rules, the scan regex `\[(ASSUMPTION|GAP|CONCERN) ([agc]\d+):`, the resolve-loop rules (resolve→replace→strip→re-scan; never auto-strip), and the writers/resolvers table. Create `references/build-loop.md` as the vendored loop skeleton per spec `## Build-Loop Skeleton`: `plan → make → separate checker → bounded retry (≤2) → compound`, with the build (durable, git worktree, stop-on-twice-failed) vs prototype (disposable, no git, `[GAP]`-and-continue) specialization table including the PR/branch stamping row.
- **Acceptance criteria:** (from `prd.md > Foundations & Migration`, `fcas`)
  - [x] `fcas` (part) — `markers.md` and `build-loop.md` are added as new reference files.
- **Verification:** `claude plugin validate .` passes. `markers.md`'s syntax, ID rules, and scan regex match spec `## Markers`; `build-loop.md`'s skeleton and specialization table match spec `## Build-Loop Skeleton`.

---

### Item 5 — living-documents.md schema-v2 + archive reset map
- [x] Update `references/living-documents.md` with the schema-v2 shape and the updated archive reset map (drop `currentSprint`/`buildMode`; carry `settings`; reset `smallProject` to null; `docs/learnings/` never swept) [PRD: fcas]
- **PRD ref:** `prd.md > Foundations & Migration`
- **Spec ref:** `spec.md > Foundations & Migration > project-state.json schema (v2)`, `spec.md > Foundations & Migration > Shared-behavior cascade`
- **What to build:** In `references/living-documents.md`, document the v2 `project-state.json` shape (the same block Item 1 writes to disk) and update the archive reset map: drop `currentSprint`/`buildMode`, carry `settings` forward, reset `smallProject` to null, rekey `commandExplanationsShown`, and add `docs/learnings/` to the never-swept carry-forward set alongside `backlog.md`, `sdd-feedback.md`, and `project-state.json`.
- **Acceptance criteria:** (from `prd.md > Foundations & Migration`, `fcas`)
  - [x] `fcas` (part) — `living-documents.md` is updated for the schema change.
- **Verification:** `claude plugin validate .` passes. The schema block and reset map in `living-documents.md` match the spec; `docs/learnings/` is listed as never-swept carry-forward.

---

### Item 6 — deepening-rounds.md trim + right-sizing.md remap
- [x] Trim `references/deepening-rounds.md` of its context-recommendation coupling; remap `references/right-sizing.md` (scope→discovery as `smallProject` writer; re-evaluators → refine/validate; skippable-beat lists → v6 commands) [PRD: fcas]
- **PRD ref:** `prd.md > Foundations & Migration`
- **Spec ref:** `spec.md > Foundations & Migration > Shared-behavior cascade`
- **What to build:** In `references/deepening-rounds.md`, remove the coupling to the deleted between-rounds context recommendation (the "emit the three-tier context recommendation after the content recommendation" wiring), keeping the deepening mechanics themselves. In `references/right-sizing.md`, mechanically remap the v5 command roles to v6: `discovery` inherits scope's role as the `smallProject` writer; the re-evaluator set becomes the surviving v6 interview commands (`refine`, `validate`); the skippable Phase-1-beat lists remap from the old commands to the v6 commands.
- **Acceptance criteria:** (from `prd.md > Foundations & Migration`, `fcas`)
  - [x] `fcas` (part) — `deepening-rounds.md` is trimmed of its context-recommendation coupling and `right-sizing.md` is remapped (scope→discovery as the `smallProject` writer).
- **Verification:** `claude plugin validate .` passes. `deepening-rounds.md` no longer references the deleted context tiers; `right-sizing.md`'s writer, re-evaluator, and skippable-beat lists name only v6 commands.

---

### Item 7 — templates: drop old, add plan-template.md, keep/update retro-template
- [x] Delete the scope/prd/spec/sprint/discovery templates; create `templates/plan-template.md` (single planning document per the spec's section order); keep and streamline `templates/retro-template.md` [PRD: ftpl]
- **PRD ref:** `prd.md > Foundations & Migration`
- **Spec ref:** `spec.md > Document Model — plan.md > Template`, `spec.md > File Structure`
- **What to build:** Delete `templates/` entries for scope, prd, spec, sprint, and discovery. Create `templates/plan-template.md` with the spec's section order: a top `**Version:** 0.1` line, then `# [Project] — Plan`, `## Overview`, `## Goals & Success`, `## Requirements` (epic → story → acceptance criteria with stable 4-char AC IDs), `## Architecture` (stack table with doc URLs, file structure, data flow, component sections), `## Key Decisions` (decision | rationale | tradeoff), `## Non-Goals` (+ conditional `docs/backlog.md` pointer); markers live inline, no roll-up section. Keep `templates/retro-template.md`, streamlined to the three-part v6 retro (drop the long-form structured-retro prose).
- **Acceptance criteria:** (from `prd.md > Foundations & Migration`)
  - [x] `ftpl` — The templates drop scope/prd/spec/sprint/discovery, add the single planning-document template (`plan-template.md`), and keep/update the retro template.
- **Verification:** `claude plugin validate .` passes. The old five templates are gone; `plan-template.md`'s section order matches spec `## Document Model — plan.md > Template`; `retro-template.md` is streamlined and carries no long-form structured-retro prose.

---

### Item 8 — onboard GitHub hard gate
- [x] Update `skills/onboard/SKILL.md` to verify `gh` is authenticated and a repository exists, and hard-block v6 (not warn) when either check fails [PRD: fgit, fgbl]
- **PRD ref:** `prd.md > Foundations & Migration`
- **Spec ref:** `spec.md > Foundations & Migration > GitHub gate (onboard)`
- **What to build:** In `skills/onboard/SKILL.md`, add a startup verification that `gh` is authenticated and a Git repository exists. On either failure, hard-block: v6 does not start and `/sdd:discovery` cannot proceed — a block with a clear remediation message, not a warning that lets the flow continue.
- **Acceptance criteria:** (from `prd.md > Foundations & Migration`)
  - [x] `fgit` — `/sdd:onboard` verifies that `gh` is authenticated and a repository exists.
  - [x] `fgbl` — When either check fails, `/sdd:onboard` hard-blocks: v6 does not start and `/sdd:discovery` cannot proceed. It is a block, not a warning.
- **Verification:** `claude plugin validate .` passes. Behavior matches the spec: with `gh` unauthenticated or no repo, onboard blocks and discovery is refused; with both satisfied, onboard proceeds.

---

### Item 9 — feasibility/viability pushback (CONCERN behavior)
- [x] Add cross-cutting feasibility/viability pushback to `sdd-guide`: any command from `discovery` onward can raise it, written into `plan.md` as `[CONCERN c#: …]` markers so it survives clear/compact [PRD: ffea]
- **PRD ref:** `prd.md > Foundations & Migration`
- **Spec ref:** `spec.md > Foundations & Migration > Feasibility/viability pushback`, `spec.md > Markers`
- **What to build:** Add to `skills/sdd-guide/SKILL.md` a cross-cutting behavior: from `discovery` onward, the AI may raise feasibility/viability pushback ("the market may not justify this"; "this is high-effort — a lighter version?") at any time, not gated to a single step. Pushback that needs to persist is written into `plan.md` as a `[CONCERN c#: …]` marker per `references/markers.md`, so it survives a clear/compact. Because it lives in `sdd-guide`, every command inherits it — no per-command wiring.
- **Acceptance criteria:** (from `prd.md > Foundations & Migration`)
  - [x] `ffea` — Feasibility/viability pushback is available as cross-cutting behavior any command from `discovery` onward can raise when warranted; it is not gated to a single step.
- **Verification:** `claude plugin validate .` passes. `sdd-guide` documents the CONCERN-raising behavior and references `markers.md` for the marker form; the behavior is defined once in `sdd-guide` (inherited), not duplicated per command.

---

### Item 10 — Discovery: ingest & inventory
- [ ] Rewrite the ingest half of `skills/discovery/SKILL.md`: accept text/images/audio, transcribe audio in-flow, emit a source inventory and block for user response before drafting, and run a full brainstorm when there's nothing to ingest [PRD: ingm, ausd, invl, ingw, noin]
- **PRD ref:** `prd.md > Discovery`
- **Spec ref:** `spec.md > Discovery > Ingest & inventory`
- **What to build:** In `skills/discovery/SKILL.md`, build the ingest step: accept text, images, and audio (`ingm`); transcribe audio in-flow via the `transcribe` skill / faster-whisper and use the transcript as interview context (`ausd`); before drafting, emit an inventory listing every source found — files under `docs/refs/`, pasted material, in-flow transcriptions (`invl`) — then block and wait for an explicit user response (`ingw`). When there is nothing to ingest (empty/absent `docs/refs/` and no pasted material), skip the inventory and enter a full brainstorm interview as a first-class path, not an error (`noin`).
- **Acceptance criteria:** (from `prd.md > Discovery`)
  - [ ] `ingm` — `/sdd:discovery` accepts text, images, and audio as ingest input.
  - [ ] `ausd` — Audio sources are transcribed in-flow (via the `transcribe` skill / faster-whisper) rather than rejected; the transcript is used as interview context.
  - [ ] `invl` — Before drafting, the command emits an inventory listing every source it found.
  - [ ] `ingw` — The command blocks after presenting the inventory and waits for an explicit user response before it begins drafting.
  - [ ] `noin` — With nothing to ingest, the command skips the inventory and enters a full brainstorm interview as a first-class path, not an error state.
- **Verification:** `claude plugin validate .` passes. Behavior matches spec `Ingest & inventory`: drive a mixed-source run (confirm inventory lists refs + pasted + transcription, then waits); drive an empty run (no `docs/refs/`, no paste) and confirm it enters the brainstorm path with no inventory and no error.

---

### Item 11 — Discovery: data-first pre-draft interview
- [ ] Build the data-first interview in `skills/discovery/SKILL.md`: search the provided material before each question, state what was found and accept confirm/decline/add, using standard interview mechanics [PRD: dfsc, dfst, dfrq]
- **PRD ref:** `prd.md > Discovery`
- **Spec ref:** `spec.md > Discovery > Data-first pre-draft interview`, `references/deepening-rounds.md`
- **What to build:** For each interview question, first search the provided material for an answer (`dfsc`). When the material answers it, ask the question *and* state what was found, accepting a confirm / decline / add response; when it doesn't, ask fresh (`dfst`). Use standard interview mechanics — one question at a time, a default of ~5 questions with a hard cap of 10, then the deepening-round recommendation (`dfrq`) — reusing `references/deepening-rounds.md`.
- **Acceptance criteria:** (from `prd.md > Discovery`)
  - [ ] `dfsc` — For each interview question, the command first searches the provided material for an answer before asking.
  - [ ] `dfst` — When the material answers a question, the command asks it *and* states what it found, accepting confirm / decline / add; when it doesn't, the command asks fresh.
  - [ ] `dfrq` — The pre-draft interview uses standard interview mechanics: one at a time, ~5 default / 10 hard cap, followed by the deepening-round recommendation.
- **Verification:** `claude plugin validate .` passes. Behavior matches spec `Data-first pre-draft interview` and the `deepening-rounds.md` contract (one-at-a-time, cap, recommendation); a question the material answers is asked-with-finding, one it doesn't is asked fresh.

---

### Item 12 — Discovery: auto-draft plan.md
- [ ] Build the auto-draft step: produce `docs/plan.md` at `Version: 0.1` from `plan-template.md`, tagging every assumption, gap, and feasibility concern inline as markers [PRD: draf, dnam, dasm, dgap]
- **PRD ref:** `prd.md > Discovery`
- **Spec ref:** `spec.md > Discovery > Auto-draft`, `spec.md > Document Model — plan.md`, `spec.md > Markers`
- **What to build:** From the interview, produce `docs/plan.md` written to disk at `Version: 0.1` using `plan-template.md` (`draf`). The file carries its final name from creation (never "draft"); draft status is the version major digit `0` (`dnam`). Tag every AI assumption inline as `[ASSUMPTION a#: …]` (`dasm`), every identified gap as `[GAP g#: …]` (`dgap`), and any feasibility concern as `[CONCERN c#: …]`, all per `references/markers.md`.
- **Acceptance criteria:** (from `prd.md > Discovery`)
  - [ ] `draf` — The command produces one planning document capturing intent, requirements, and architecture, written to disk.
  - [ ] `dnam` — The document file carries its final name from creation; draft-vs-final status is the version major digit inside the file (major `0` = draft), written as a draft (`0.1`) at this stage.
  - [ ] `dasm` — Every assumption the AI makes is tagged inline with a machine-findable marker carrying a short ID (`[ASSUMPTION a3: …]`).
  - [ ] `dgap` — Every gap the AI identifies is tagged inline with a machine-findable marker carrying a short ID (`[GAP g2: …]`).
- **Verification:** `claude plugin validate .` passes. A discovery run writes `docs/plan.md` at `Version: 0.1`, section order matching `plan-template.md`, with inline `[ASSUMPTION …]`/`[GAP …]`/`[CONCERN …]` markers whose syntax matches `markers.md`.

---

### Item 13 — Refine: marker walk
- [ ] Rewrite the walk in `skills/refine/SKILL.md`: scan `plan.md` for markers, present one at a time in document order, resolve in place and strip, re-scan until none remain resolvable, and never remove a marker autonomously [PRD: rwlk, rres, rlop, rnau]
- **PRD ref:** `prd.md > Refine`
- **Spec ref:** `spec.md > Refine > Marker walk (step 4)`, `spec.md > Markers > Resolve-loop rules`
- **What to build:** Rewrite `skills/refine/SKILL.md` (clean break; no old mid-chain refine behavior carries over). Scan `plan.md` for markers and present them one at a time in document order (`rwlk`). Resolving replaces the marked text in place and strips the marker with no breadcrumb (`rres`). Re-scan and repeat until none remain resolvable; a marker added mid-loop (including a prototype `[GAP]`) is caught on a later re-scan (`rlop`). Never remove a marker on the AI's own initiative — only explicit user resolution removes one (`rnau`).
- **Acceptance criteria:** (from `prd.md > Refine`)
  - [ ] `rwlk` — `/sdd:refine` scans the document for markers and presents them one at a time in document order.
  - [ ] `rres` — Resolving a marker replaces the marked text in place with settled content and strips the marker, leaving no breadcrumb.
  - [ ] `rlop` — After each resolution the command re-scans and repeats until no markers remain except those explicitly left tagged; a marker added mid-loop is picked up on a later re-scan.
  - [ ] `rnau` — The command never removes a marker on its own; a marker is removed only through explicit user resolution.
- **Verification:** `claude plugin validate .` passes. Behavior matches `markers.md` resolve-loop rules: drive a walk that resolves some markers (confirm in-place replacement, marker stripped, no breadcrumb), leaves some, and picks up one added mid-loop on re-scan; confirm no marker is removed without explicit resolution.

---

### Item 14 — Refine: carry-forward + proposed solutions
- [ ] Build carry-forward: an unresolvable marker stays tagged and carries to `prototype`; the AI may append a `PROPOSED` solution but must keep the marker [PRD: rcnt, rpsl]
- **PRD ref:** `prd.md > Refine`
- **Spec ref:** `spec.md > Refine > Marker walk (step 4)`, `spec.md > Markers > Resolve-loop rules`
- **What to build:** In `skills/refine/SKILL.md`, when a marker can't be resolved during refine, leave it tagged in the document so it carries forward to `prototype` (`rcnt`). The AI may append a proposed solution next to the unresolved marker (the `PROPOSED` form) but only if it also keeps the marker in place (`rpsl`). Nothing is force-decided.
- **Acceptance criteria:** (from `prd.md > Refine`)
  - [ ] `rcnt` — When a marker can't be resolved during refine, it stays tagged in the document and carries forward to `prototype`.
  - [ ] `rpsl` — The AI may append a proposed solution next to an unresolved marker, but only if it also keeps the marker in place.
- **Verification:** `claude plugin validate .` passes. An unresolvable marker survives the refine pass still tagged; when a `PROPOSED` note is appended, the marker remains (matches `markers.md`).

---

### Item 15 — Refine: finalize
- [ ] Build finalize: drop draft status by bumping the version major `0.x → 1.0` in place, with no separate final file [PRD: rfin]
- **PRD ref:** `prd.md > Refine`
- **Spec ref:** `spec.md > Refine > Finalize (step 5)`, `spec.md > Document Model — plan.md > Versioning`
- **What to build:** In `skills/refine/SKILL.md`, the finalize step drops draft status by bumping the version major from `0.x` to `1.0` and updating the same file in place — no separate "final" file is created. The version number is the single source of truth for maturity.
- **Acceptance criteria:** (from `prd.md > Refine`)
  - [ ] `rfin` — At the end of the review pass the command finalizes by bumping the version major (`0.x → 1.0`, draft → final) and updates the same file in place — no separate "final" file is created.
- **Verification:** `claude plugin validate .` passes. Finalize bumps `plan.md` to `1.0` in place; no sibling final file appears; the version walk matches spec Versioning.

---

### Item 16 — Validate: optional, user-initiated command
- [ ] Create `skills/validate/SKILL.md` as an optional, user-initiated command; the chain proceeds `refine → prototype` whether or not it ran [PRD: vopt]
- **PRD ref:** `prd.md > Validate (optional)`
- **Spec ref:** `spec.md > Validate`
- **What to build:** Create `skills/validate/SKILL.md`. It's optional and user-initiated: the chain can go from `refine` straight to `prototype` whether or not `validate` has run, and running it is the maintainer's choice.
- **Acceptance criteria:** (from `prd.md > Validate (optional)`)
  - [ ] `vopt` — `/sdd:validate` is optional: the flow can proceed from `refine` to `prototype` whether or not `validate` has run, and running it is user-initiated.
- **Verification:** `claude plugin validate .` passes. `prototype` is reachable without `validate` having run; `validate` only runs when the user invokes it.

---

### Item 17 — Validate: self-critique + external files + reconciliation
- [ ] Build validate's three beats: offer (ask-first) a self-critique written into `docs/validation/`; gather all files there (both full-copy and suggestion-list forms); walk the differences one at a time with keep/remove/change, bumping to `2.x` [PRD: vslf, vscn, vbth, vdif, vkrc]
- **PRD ref:** `prd.md > Validate (optional)`
- **Spec ref:** `spec.md > Validate > Three beats`, `spec.md > Validate > Source-agnostic drop-zone`
- **What to build:** Beat 1 — offer to run Claude's adversarial self-critique (ask first, do not auto-run); on yes, write the findings as a validation file into `docs/validation/`, uniform with external sources; on no, skip (`vslf`). Beat 2 — scan `docs/validation/`; any file there is a validation file, no naming convention (`vscn`); handle both a full modified copy of the document (extract changes by diff against `plan.md`) and a suggestion-list file (`vbth`). Beat 3 — present the differences between the validations and `plan.md` one at a time (`vdif`); for each, accept keep / remove / change and apply in place (`vkrc`); walk carried-over markers in the same pass; bump the version to `2.x`; anything unresolvable becomes a carry-forward marker.
- **Acceptance criteria:** (from `prd.md > Validate (optional)`)
  - [ ] `vslf` — On start, the command offers to run its own adversarial self-critique — it asks first and does not auto-run; on decline it goes straight to gathering external validation files.
  - [ ] `vscn` — The command scans `docs/validation/` for external validation files; any file present is treated as a validation file, no naming convention required.
  - [ ] `vbth` — The command handles both a full modified copy of the document (changes extracted by diff) and a file that only lists suggestions.
  - [ ] `vdif` — The command presents the differences between the validations and the document one at a time.
  - [ ] `vkrc` — For each difference, the command accepts a keep / remove / change decision and applies it to the document in place.
- **Verification:** `claude plugin validate .` passes. Behavior matches spec `Three beats`: self-critique is offered (ask-first) and, if accepted, dropped as a file into `docs/validation/`; both a full-copy and a suggestion-list validation file are handled; differences are walked one at a time with keep/remove/change; `plan.md` ends at `2.x` with any unresolved item left as a carry-forward marker.

---

### Item 18 — Prototype: persisted fidelity
- [ ] Create `skills/prototype/SKILL.md` fidelity handling: ask hi-fi vs lo-fi on first run, persist to `settings.prototypeFidelity`, reuse without re-asking, switch only on explicit request [PRD: pfid, pfps, pfsw]
- **PRD ref:** `prd.md > Prototype`
- **Spec ref:** `spec.md > Prototype > Fidelity (persisted)`, `spec.md > Foundations & Migration > project-state.json schema (v2)`
- **What to build:** In `skills/prototype/SKILL.md`, on the first prototype run in a project ask hi-fi (clickable) vs lo-fi (screenshots) (`pfid`); persist the answer to `project-state.json` under `settings.prototypeFidelity` and reuse it on later runs without re-asking (`pfps`); switch the persisted choice only when the maintainer explicitly asks, never re-surfacing it each run (`pfsw`).
- **Acceptance criteria:** (from `prd.md > Prototype`)
  - [ ] `pfid` — On the first prototype run in a project, the command asks hi-fi (clickable) vs lo-fi (screenshots).
  - [ ] `pfps` — The choice is persisted to `docs/project-state.json` and reused on later runs without re-asking.
  - [ ] `pfsw` — The maintainer can switch the persisted fidelity by explicitly asking; the command does not re-surface the choice each run.
- **Verification:** `claude plugin validate .` passes. First run asks and writes `settings.prototypeFidelity`; a later run reuses it silently; an explicit switch request updates it, and no run re-prompts on its own.

---

### Item 19 — Prototype: build & navigation
- [ ] Build the prototype in plain HTML/CSS/JS at `prototype/` (walled off), author navigation path(s) from `plan.md` only, and write them to `prototype/navigation-paths.md` surfaced to the maintainer [PRD: pweb, pnav, pcmp, pwal]
- **PRD ref:** `prd.md > Prototype`
- **Spec ref:** `spec.md > Prototype > Build & navigation`
- **What to build:** In `skills/prototype/SKILL.md`, produce the prototype in plain HTML/CSS/JS (`pweb`), output to `prototype/` at the repo root and walled off from any real build — nothing marks it as a foundation (`pwal`). Author navigation path(s) from `plan.md` and nothing else (`pnav`), producing one or more paths as needed to navigate the prototype; write them to a companion `prototype/navigation-paths.md` (not into `plan.md`) and surface them to the maintainer (`pcmp`).
- **Acceptance criteria:** (from `prd.md > Prototype`)
  - [ ] `pweb` — The prototype is built in plain HTML/CSS/JS.
  - [ ] `pnav` — The command authors the navigation path(s) from the source-of-truth document and nothing else.
  - [ ] `pcmp` — The navigation paths are written to a companion file paired with the prototype (not into the source-of-truth document) and surfaced to the maintainer.
  - [ ] `pwal` — The prototype's code is kept walled off from any real build; nothing marks it as a foundation for the final product.
- **Verification:** `claude plugin validate .` passes. Behavior matches spec `Build & navigation`: prototype output is plain HTML/CSS/JS under `prototype/`, nav paths derive only from `plan.md` and land in `prototype/navigation-paths.md`, and nothing labels the prototype code as a build foundation.

---

### Item 20 — Prototype: loop, gap-marking, review, issue map
- [ ] Reuse the loop skeleton (no git) to build pages; on a twice-failed page write `[GAP: …]` into `plan.md` and continue; walk each page along the nav paths against the document; optionally emit a page→issue mapping [PRD: plop, pfal, prvw, pmap]
- **PRD ref:** `prd.md > Prototype`
- **Spec ref:** `spec.md > Prototype > Loop behavior`, `spec.md > Prototype > Build & navigation`, `spec.md > Build-Loop Skeleton`
- **What to build:** In `skills/prototype/SKILL.md`, reuse the vendored loop skeleton (plan → make → separate checker → bounded retry ≤2) without git machinery (`plop`). When a page fails its checker after the bounded retry, write a `[GAP: …]` marker into `plan.md` (per `markers.md`) and continue building the rest rather than halting (`pfal`). The prototype-review step walks each page along the navigation paths and checks it against the document (`prvw`). Optionally produce a page→issue mapping that seeds the real build issues (`pmap`).
- **Acceptance criteria:** (from `prd.md > Prototype`)
  - [ ] `plop` — The command reuses the build-loop skeleton (plan → make → separate-checker → bounded retry ≤2) without the git machinery.
  - [ ] `pfal` — When a page fails its checker after the bounded retry, the command writes a `[GAP: …]` marker into the document and continues building the rest of the prototype rather than halting.
  - [ ] `prvw` — The prototype-review step walks each page along the navigation paths and checks it against the document.
  - [ ] `pmap` — The command can optionally produce a page→issue mapping that seeds the real build issues.
- **Verification:** `claude plugin validate .` passes. Behavior matches `build-loop.md` prototype specialization: a twice-failed page writes a `[GAP]` into `plan.md` and the loop continues; review walks the nav paths against the document; the optional page→issue mapping is produced on request.

---

### Item 21 — Lesson capture (automatic)
- [ ] Add automatic lesson capture: detect lesson-worthy events and write them to `docs/learnings/` unprompted, active during discovery/refine/prototype/build, surfaced at retro; leave the ad-hoc "record globally now" move manual [PRD: lcau, lcsp, lcsr, lcno]
- **PRD ref:** `prd.md > Lesson Capture`
- **Spec ref:** `spec.md > Lesson Capture`
- **What to build:** Add cross-cutting lesson capture (in `sdd-guide` so the relevant commands inherit it): the AI detects lesson-worthy events — lost-context / redundant-question events, repeated roadblocks, course corrections, reusable designs/practices/preferences — and writes them to `docs/learnings/` without prompting (`lcau`). Capture is active during doc creation (`discovery`, `refine`) and item build (`prototype`, `build`) (`lcsp`); captured lessons surface at `/sdd:retro` for promotion (`lcsr`). The maintainer's ad-hoc "record this globally now" move stays a manual action — not implemented or watched for by the plugin (`lcno`). `docs/learnings/` is a permanent carry-forward store, never swept by archive.
- **Acceptance criteria:** (from `prd.md > Lesson Capture`)
  - [ ] `lcau` — The AI detects lesson-worthy events and writes them to `docs/learnings/` without prompting.
  - [ ] `lcsp` — Automatic capture is active during doc creation (`discovery`, `refine`) and item build (`prototype`, `build`).
  - [ ] `lcsr` — Captured lessons surface at `/sdd:retro` for promotion.
  - [ ] `lcno` — The maintainer's ad-hoc "record this globally right now" move is left as a manual action, not implemented or watched for.
- **Verification:** `claude plugin validate .` passes. The capture behavior is defined in `sdd-guide` and active across `discovery`/`refine`/`prototype`/`build`; a simulated lesson-worthy event writes a `docs/learnings/` entry unprompted; the entry is available to retro; no "record globally now" watcher exists.

---

### Item 22 — Checkpoint
- [ ] Create `skills/checkpoint/SKILL.md`: an anytime command that reads the session and prints a tailored `/compact <instructions>` string, writes no file, and never runs compact itself [PRD: ckan, ckrd, ckpr, cknr, ckps]
- **PRD ref:** `prd.md > Checkpoint`
- **Spec ref:** `spec.md > Checkpoint`
- **What to build:** Create `skills/checkpoint/SKILL.md`. It runs anytime, independent of chain position (`ckan`); reads the session and emits a tailored instruction string for Claude Code's `/compact <instructions>` form, naming what to preserve (`ckrd`); prints the string to chat and writes no file (`ckpr`); never runs `/compact` itself (`cknr`); and is distinct from `/sdd:pause` — it writes no resume file (`ckps`). This is the on-demand replacement for the removed between-rounds auto-suggest.
- **Acceptance criteria:** (from `prd.md > Checkpoint`)
  - [ ] `ckan` — `/sdd:checkpoint` runs anytime, independent of chain position.
  - [ ] `ckrd` — The command reads the session and emits a tailored instruction string for Claude Code's `/compact <instructions>` form, naming what to preserve.
  - [ ] `ckpr` — The instruction string is printed to chat; the command writes no file.
  - [ ] `cknr` — The command never runs `/compact` itself.
  - [ ] `ckps` — The command is distinct from `/sdd:pause`: it does not write a resume file.
- **Verification:** `claude plugin validate .` passes. Invoking checkpoint prints a `/compact <instructions>` string naming what to preserve, writes no file, and never invokes compact; it produces no resume file.

---

### Item 23 — Build: issues from refined work
- [ ] Rewrite `skills/build/SKILL.md` issue-creation: keep `docs/backlog.md` as the local, unrefined holding area (never acted on), and turn a refined item into a GitHub issue with title, description, ACs lifted from `plan.md`, a link back, and labels [PRD: bgat, biss]
- **PRD ref:** `prd.md > Build`
- **Spec ref:** `spec.md > Build > Issues from refined work`
- **What to build:** In `skills/build/SKILL.md`, keep `docs/backlog.md` as the local, unrefined holding area that build agents never act on directly (`bgat`). Turn a refined item into a GitHub issue carrying a title, a description, acceptance criteria lifted (not authored) from `plan.md` by AC ID, a link back to `plan.md`, and labels (`biss`).
- **Acceptance criteria:** (from `prd.md > Build`)
  - [ ] `bgat` — `docs/backlog.md` remains the local, unrefined holding area and is never itself acted on by build agents.
  - [ ] `biss` — A refined item becomes a GitHub issue carrying a title, a description, acceptance criteria, a link back to the document, and labels.
- **Verification:** `claude plugin validate .` passes. Backlog items are never turned into issues by build; a refined item yields an issue with all five parts, ACs lifted from `plan.md` by ID with a link back.

---

### Item 24 — Build: build-loop
- [ ] Build the loop with git: one issue → one branch → one PR in its own worktree with agents fanning out; a separate checker per PR; never auto-merge; no decisions mid-build [PRD: blop, bchk, bmrg, bnod, bfan]
- **PRD ref:** `prd.md > Build`
- **Spec ref:** `spec.md > Build > Build-loop`, `spec.md > Build-Loop Skeleton`
- **What to build:** In `skills/build/SKILL.md`, run the vendored loop skeleton (`references/build-loop.md`) with git machinery: one issue → one branch → one PR (`blop`), each item in its own git worktree with multiple agents fanning out across issues in parallel (`bfan`). Each PR is checked by a separate agent before it reaches the maintainer (`bchk`). Never auto-merge; wait on the maintainer's review (`bmrg`). Make no decisions mid-build — an under-specified issue is surfaced, not guessed; required decisions are expected to already live in `plan.md` and the issue (`bnod`). Apply the PR/branch stamping convention from `build-loop.md` (the `sdd` label + `sdd/<issue>-<slug>` prefix); the stamping AC (`rpst`) is tracked in Item 25.
- **Acceptance criteria:** (from `prd.md > Build`)
  - [ ] `blop` — `/sdd:build` runs the build-loop such that one issue maps to one branch and one pull request.
  - [ ] `bchk` — Each PR is checked by a separate agent before it reaches the maintainer.
  - [ ] `bmrg` — The build-loop never auto-merges a PR; it waits on the maintainer's review.
  - [ ] `bnod` — The build-loop makes no decisions mid-build; an under-specified issue is surfaced, not guessed.
  - [ ] `bfan` — Multiple agents can work different issues in parallel, each in its own worktree.
- **Verification:** `claude plugin validate .` passes. Behavior matches `build-loop.md` build specialization: one issue → one branch → one PR in its own worktree; a separate checker runs before maintainer review; PRs are never auto-merged; an under-specified issue is surfaced rather than guessed.

---

### Item 25 — Resolve-PR command
- [ ] Create `skills/resolve-pr/SKILL.md`: a vendored anytime command that recognizes its own PRs via the stamp, deletes branches of closed/merged PRs, and works open-PR review threads in bounded rounds [PRD: rpvc, rpbh, brpf, rpst]
- **PRD ref:** `prd.md > Resolve-PR`
- **Spec ref:** `spec.md > Resolve-PR`, `spec.md > Build > Build-loop` (stamping), `spec.md > Build-Loop Skeleton`
- **What to build:** Create `skills/resolve-pr/SKILL.md` as a vendored anytime SDD command — distinct from the personal `resolve-pr-feedback` skill, whose thread-resolution logic it reuses — runnable independent of chain position (`rpvc`). Branch hygiene: find PRs it created (recognized via the `sdd` label + `sdd/<issue>-<slug>` prefix) that are now closed or merged, and delete their local and remote branches (`rpbh`). Feedback resolution: find its still-open PRs carrying review comments and work each thread (fix → reply → resolve → push) in bounded rounds (`brpf`). The stamping convention (`rpst`) is applied by the build-loop (Item 24) and consumed here for recognition.
- **Acceptance criteria:** (from `prd.md > Resolve-PR`)
  - [ ] `rpvc` — `/sdd:resolve-pr` is a vendored anytime SDD command — distinct from the personal `resolve-pr-feedback` skill, whose logic it reuses — runnable independent of chain position.
  - [ ] `rpbh` — Branch hygiene: the command finds PRs it created that are now closed or merged and deletes their local and remote branches.
  - [ ] `brpf` — Feedback resolution: after the maintainer leaves review comments, `/sdd:resolve-pr` works each open-PR review thread (fix → reply → resolve → push) in bounded rounds.
  - [ ] `rpst` — The build-loop stamps its PRs and branches (an `sdd` label plus an `sdd/<issue>-<slug>` prefix) so `/sdd:resolve-pr` can recognize its own.
- **Verification:** `claude plugin validate .` passes. The command recognizes its own PRs via the stamp; on a closed/merged stamped PR it deletes the local + remote branches; on an open stamped PR with review comments it works each thread in bounded rounds; unstamped PRs are ignored.

---

### Item 26 — Retro
- [ ] Rewrite `skills/retro/SKILL.md`, streamlined to three parts: list `docs/learnings/` lessons and promote per-lesson with routing, report dump-to-prototype session count and issue-queue status, and drop the long-form structured-retro prose [PRD: rtls, rtpr, rtre, rttm, rtiq, rtdr]
- **PRD ref:** `prd.md > Retro`
- **Spec ref:** `spec.md > Retro`
- **What to build:** Rewrite `skills/retro/SKILL.md`, streamlined to three parts and dropping the long-form structured-retro prose (`rtdr`). List the lessons captured to `docs/learnings/` during the project (`rtls`). Per lesson, prompt to promote it and suggest routing — plugin-level lessons to `/sdd:feedback`, global lessons to `~/.claude` (`rtpr`) — and actively recommend against re-promoting a lesson already present in global config (`rtre`). Report a rough dump-to-prototype timing as a session count, not a stopwatch (`rttm`), and the current issue-queue status (`rtiq`).
- **Acceptance criteria:** (from `prd.md > Retro`)
  - [ ] `rtls` — `/sdd:retro` lists the lessons captured to `docs/learnings/` during the project.
  - [ ] `rtpr` — For each lesson, the command prompts to promote it and suggests routing (plugin-level → `/sdd:feedback`, global → `~/.claude`).
  - [ ] `rtre` — The command actively recommends against re-promoting a lesson already present in global config.
  - [ ] `rttm` — The command reports a rough dump-to-prototype timing as a session count, not a stopwatch time.
  - [ ] `rtiq` — The command reports the current issue-queue status.
  - [ ] `rtdr` — The retro omits the old long-form structured-retro prose.
- **Verification:** `claude plugin validate .` passes. Retro lists `docs/learnings/` lessons, prompts per-lesson promotion with routing, guards against re-promoting an already-global lesson, reports a session-count timing and issue-queue status, and carries no long-form structured-retro prose.

---

### Item 27 — Archive adaptation
- [ ] Update `skills/archive/SKILL.md` for the v6 structure: sweep `plan.md` and `docs/validation/`, drop the `sprint-*.md` and `discovery.md` sweeps, grow the never-swept set with `docs/learnings/`, leave `prototype/` untouched, and repoint the handoff to `/sdd:discovery` [PRD: farc]
- **PRD ref:** `prd.md > Foundations & Migration`
- **Spec ref:** `spec.md > Foundations & Migration > Archive adaptation`
- **What to build:** In `skills/archive/SKILL.md`, adapt the sweep to v6: allow-list `plan.md` in place of the scope/prd/spec trio; add `docs/validation/`; drop the dead `sprint-*.md` glob and drop `discovery.md`; keep `retro.md`, `open-concerns.md`, `*-resume.md`, and root `process-notes-*.md`. Grow the never-swept carry-forward set to include `docs/learnings/` alongside `backlog.md`, `sdd-feedback.md`, `project-state.json`. `prototype/` sits at the repo root outside `docs/`, so archive never touches it — no special exclusion logic needed. Repoint the handoff from `/clear` + `/sdd:scope` to `/clear` + `/sdd:discovery`. Update the reset map: drop `currentSprint`/`buildMode`, carry `settings`, rekey `commandExplanationsShown`.
- **Acceptance criteria:** (from `prd.md > Foundations & Migration`)
  - [ ] `farc` — `/sdd:archive` sweeps the single document (not the scope/prd/spec trio), sweeps `docs/validation/`, drops the dead `sprint-*.md` glob, excludes disposable prototype code from the versioned archive, and repoints its handoff from `/clear` + `/sdd:scope` to `/sdd:discovery`.
- **Verification:** `claude plugin validate .` passes. The sweep allow-list, never-swept set, and reset map match spec `Archive adaptation`; the handoff names `/sdd:discovery`; a dry run confirms `plan.md` + `docs/validation/` are swept, `sprint-*.md`/`discovery.md` are not, and `prototype/` and `docs/learnings/` are untouched.

---

### Item 28 — Remove retired commands
- [ ] Delete the `scope`, `prd`, `spec`, `plan`, and `polish` skill directories (last, once the new flow is proven), keeping `onboard`, `pause`, `unpause`, `feedback`, `archive` [PRD: frem]
- **PRD ref:** `prd.md > Foundations & Migration`
- **Spec ref:** `spec.md > Foundations & Migration > Command removals`, `spec.md > File Structure`
- **What to build:** Delete the `plugins/sdd/skills/scope`, `prd`, `spec`, `plan`, and `polish` directories. Keep `onboard`, `pause`, `unpause`, `feedback`, and `archive`. (The new `validate`/`prototype`/`checkpoint`/`resolve-pr` commands are added and `discovery`/`refine`/`build`/`retro` rewritten by earlier items.) Do this last, after the new flow is proven, so nothing is removed before its replacement works. Sweep for and remove any lingering references to the deleted commands.
- **Acceptance criteria:** (from `prd.md > Foundations & Migration`)
  - [ ] `frem` — The commands `scope`, `prd`, `spec`, `plan`, and `polish` are removed; `onboard`, `pause`, `unpause`, `feedback`, and `archive` are kept.
- **Verification:** `claude plugin validate .` passes with the five command directories gone and the plugin still structurally valid; a repo-wide grep finds no dangling references to the removed commands.

---

## Out of Scope

Deferred items: see `docs/backlog.md`.
