# Sprint 2

**Theme:** Shared behavior layer foundation — stand up the cross-cutting reference files, update existing references, and apply v2 behavioral additions to `sdd-guide/SKILL.md` so every subsequent per-command sprint can load from a v2-correct foundation.

**Tangible outcome:** All four NEW reference files exist with v2 content per spec; three existing reference files are updated to v2; `sdd-guide/SKILL.md` has the v2 behavioral additions; the plugin structurally validates with the v2 shared layer in place.

**Out of scope:** Atomic rename sweep (sprint 3 candidate — separate atomic op); per-command behavior implementation (sprints 3+); bug fixes; new skill stubs for `/sdd:discovery`, `/sdd:pause`, `/sdd:unpause` (wait until renames land); `MIGRATION-v1-to-v2.md`; `docs/v2-verification.md` seed. The plugin-level `plugins/sdd/CLAUDE.md` is already a v2-correct minimal loader pointer — no edit needed this sprint.

## Build Preferences
- **Build mode:** autonomous

## Execution order

All 8 items are independent (each touches a different file). Any order works. Build mode will dispatch them sequentially in order 1–8.

## Checklist

### Item 1: Create references/right-sizing.md (NEW)

- **PRD ref:** `prd.md > Interview Command Behaviors` (small-project stories) `[unmapped]`
- **Spec ref:** `spec.md > Reference: right-sizing.md (NEW)` and `spec.md > Right-Sizing Heuristic`
- **What to build:** Create `plugins/sdd/skills/sdd-guide/references/right-sizing.md` with the v2 right-sizing heuristic content owned by this file per spec. This is the canonical home for `smallProject` semantics, signal list, per-command skippable beats, and the thinness rule.
- **Acceptance criteria:**
  - [x] The file exists at `plugins/sdd/skills/sdd-guide/references/right-sizing.md`.
  - [x] The file documents `smallProject` field semantics: written authoritatively by `/sdd:scope` at end of Phase 1; re-evaluated by every subsequent interview command on its own startup; subsequent commands may update the value based on signals they observe.
  - [x] The file enumerates the heuristic signals: one primary feature, no external users, no multi-component architecture, no third-party integrations, brief or absent reference uploads, concise opening framing.
  - [x] The file enumerates per-command skippable Phase 1 beats when `smallProject: true` (covering `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan` at minimum).
  - [x] The file defines the thinness signal for sub-5-question rounds (short answers, deflections, agent's natural framing toward closure; 2+ of these triggers a 2-3-question round when `smallProject` is true).
  - [x] The file documents the interaction with the 5/10 question cap: cap stays; default round size drops below 5 when thinness signal fires.
  - [x] The file explicitly states the detection-placement decision: `/sdd:discovery` does NOT detect or write `smallProject`; `/sdd:scope` writes the authoritative initial value; subsequent commands re-evaluate.
- **Verification:**
  - Visual read: the file is internally coherent and stands on its own as a reference (someone loading just this file can understand the right-sizing model).
  - Cross-check: every claim made by `spec.md > Right-Sizing Heuristic` about content owned by this file is actually present here.
- **Status:** [x] Complete

---

### Item 2: Create references/pause-resume.md (NEW)

- **PRD ref:** `prd.md > Multi-Session Resume` `[unmapped]`
- **Spec ref:** `spec.md > Reference: pause-resume.md (NEW)` and `spec.md > Pause/Resume Quality Bar`
- **What to build:** Create `plugins/sdd/skills/sdd-guide/references/pause-resume.md` documenting the resume-file location convention, the required-sections schema, the per-section quality bar, distill-not-dump anti-patterns, the cleanup rule, and the per-round-summary consolidation with the same schema.
- **Acceptance criteria:**
  - [x] The file exists at `plugins/sdd/skills/sdd-guide/references/pause-resume.md`.
  - [x] The file documents the resume-file location convention: `docs/<command>-resume.md`, where `<command>` is the in-flight command name without the `/sdd:` prefix.
  - [x] The file specifies the required sections in this exact order: Status, Setup-already-done, Confirmed decisions, Round status, Pending decision, Files for next-session, Instructions for next-session.
  - [x] The file documents the per-section quality bar with enough specificity that a fresh zero-context model could be seeded into the same decision state as the paused conversation.
  - [x] The file lists distill-not-dump anti-patterns explicitly: no verbatim conversation, no agent reasoning trail, no re-explanation of plugin behavior, cross-reference don't restate.
  - [x] The file states the length target: 1–3 pages of markdown.
  - [x] The file documents the cleanup rule: the resumed command deletes its own resume file when the underlying interview completes its document generation step; missing resume file produces no error.
  - [x] The file states that per-round summary writes use this same schema and location convention (triggered when the three-tier model recommends `/clear`).
- **Verification:**
  - Visual read: the file reads as a complete reference; quality bar is concrete, not vague.
  - Cross-check: every claim made by `spec.md > Reference: pause-resume.md` about content owned by this file is present.
- **Status:** [x] Complete

---

### Item 3: Create references/sprint-tags.md (NEW)

- **PRD ref:** `prd.md > Bug Fixes` (PRD state-tracking), `prd.md > Sprint Loop`, `prd.md > v2 Release Verification` `[unmapped]`
- **Spec ref:** `spec.md > Reference: sprint-tags.md (NEW)`, `spec.md > PRD Acceptance Criteria IDs`, `spec.md > Sprint-Item Tags`, `spec.md > Iteration-Candidate Markers`, `spec.md > Close-Sprint Manifest`
- **What to build:** Create `plugins/sdd/skills/sdd-guide/references/sprint-tags.md` as the canonical home for every tag format and parser regex used in v2 sprint files and process-notes files. This is the most consequential reference file — `/sdd:plan` validation, `/sdd:build` wrap-up, and `/sdd:polish` re-open all read parser rules from here.
- **Acceptance criteria:**
  - [x] The file exists at `plugins/sdd/skills/sdd-guide/references/sprint-tags.md`.
  - [x] The file documents the PRD AC ID format `[a-z]{4}` (4-char lowercase alphabetic), assigned at write time by `/sdd:prd` and `/sdd:refine` with collision check, inline in backticks at the start of each AC line, stable across reorders.
  - [x] The file documents the `[PRD: id1, id2, ...]` sprint-item tag with a testable parser regex (comma-separated, multiple IDs allowed for items satisfying multiple ACs).
  - [x] The file documents the `[unmapped]` sprint-item tag for items not mapped to any PRD AC.
  - [x] The file documents the `[FB: FB-NNN]` sprint-item tag (sdd-plugin-only) with a testable parser regex (three-digit-padded IDs).
  - [x] The file documents the `[iteration-candidate]` process-notes marker: start-of-line marker + free-prose description until next blank line or next marker; optional inline `(item: abcd)` sprint-item reference; no severity field; fabrication prohibited.
  - [x] The file documents the `[close-sprint-manifest]` / `[/close-sprint-manifest]` block anchors with parser regex; manifest contains PRD ACs checked, story-split mappings, iteration-candidate dispositions, tech-debt entries, and v2-verification rows updated.
  - [x] The file documents the `[sprint-reopened]` process-notes marker emitted by `/sdd:polish` re-open.
  - [x] Every documented tag carries a testable regex (not pseudo-code).
- **Verification:**
  - Spot-check each regex by mentally applying it to an example tag from this very sprint file or from sprint-1.md and confirming the regex matches as intended.
  - Cross-check: every tag mentioned in spec.md is documented here with regex and example.
- **Status:** [x] Complete

---

### Item 4: Create references/context-management.md (NEW)

- **PRD ref:** `prd.md > Context Management` `[unmapped]`
- **Spec ref:** `spec.md > Reference: context-management.md (NEW)` and `spec.md > Three-Tier Between-Rounds Context Management`
- **What to build:** Create `plugins/sdd/skills/sdd-guide/references/context-management.md` defining the three-tier between-rounds recommendation model and its recognition heuristics.
- **Acceptance criteria:**
  - [x] The file exists at `plugins/sdd/skills/sdd-guide/references/context-management.md`.
  - [x] The file defines the three tiers explicitly: **continue**, **/compact**, **/clear**, with selection criteria for each.
  - [x] The file documents recognition signals: round size, artifact-conversation gap, superseded noise, token-usage approximation (rough proxies, no exact count).
  - [x] The file documents tier-mapping rules: continue when round was short and artifact already captures the round's value; /compact when round captured detail in conversation that the artifact did not but a compacted summary will carry it forward; /clear when round was long or heavy, artifact is rich and self-contained, and remaining conversation is dominated by superseded back-and-forth.
  - [x] The file documents failure-mode mitigation: user override upward always allowed; downward override carries a brief context-weight warning.
  - [x] The file documents `/clear` branch consolidation with pause-resume: agent writes a resume file (using `pause-resume.md` schema) before emitting the `/clear` recommendation.
  - [x] The file states the recommendation is delivered with reasoning in the same message; never a bare prompt.
  - [x] The file states the plugin does NOT programmatically invoke `/compact` or `/clear`; the user runs the command after receiving the recommendation.
- **Verification:**
  - Visual read: tier definitions are concrete enough that a future agent can apply them.
  - Cross-check: every claim made by `spec.md > Three-Tier Between-Rounds Context Management` about content owned by this file is present.
- **Status:** [x] Complete

---

### Item 5: Update references/deepening-rounds.md (v2 additions)

- **PRD ref:** `prd.md > Interview Command Behaviors` (deepening-round predictable-bounds + definite-recommendation stories) `[unmapped]`
- **Spec ref:** `spec.md > Reference: deepening-rounds.md (updated)`
- **What to build:** Update `plugins/sdd/skills/sdd-guide/references/deepening-rounds.md` to lock the v2 caps and recommendation framing. The v1 file already exists and covers Phase 1 / Phase 2 structure; this update tightens it.
- **Acceptance criteria:**
  - [x] The file states each deepening round is composed with 5 questions by default.
  - [x] The file states a deepening round may run up to 10 questions without further explanation.
  - [x] The file states past 10 questions, the agent emits a stated reason and obtains explicit user permission before posing the eleventh.
  - [x] The file documents the end-of-round recommendation framing literally: "I think we should do another round, because [reason]" or "I do not think we need another round, because [reason]."
  - [x] The file states topic preview is required when recommending continuation: an explicit list of topics the proposed next round would cover.
  - [x] The file states reasoning is required when recommending closure: may include a list of topics a next round would have covered, used to justify why those topics do not warrant a round.
  - [x] The recommendation must never be a bare prompt (e.g., "want to do another round?" is not allowed).
- **Verification:**
  - Visual read against current `deepening-rounds.md` content: confirm the v2 additions are present and the v1 content (Phase 1 vs Phase 2 structure) is preserved.
- **Status:** [x] Complete

---

### Item 6: Update references/context-loading.md (v2 loading rules)

- **PRD ref:** `prd.md > /sdd:discovery Command` (refs upload), `prd.md > Sprint Loop` (build re-entry), `prd.md > /sdd:retro at Project Close`, `prd.md > /sdd:onboard Polish` (cross-project pattern surfacing) `[unmapped]`
- **Spec ref:** `spec.md > Reference: context-loading.md (updated)`
- **What to build:** Update `plugins/sdd/skills/sdd-guide/references/context-loading.md` with the v2 loading additions. The v1 file already covers the threshold rule and existing commands; this update adds new rules and revises existing ones.
- **Acceptance criteria:**
  - [x] The file documents the `/sdd:discovery` loading rule: load `docs/refs/` if non-empty (no prompt if empty).
  - [x] The file documents the `/sdd:spec` loading rule: load `docs/refs/` at startup.
  - [x] The file documents the `/sdd:plan` loading rule: read the most recent `process-notes-sprint-N.md` to surface carry-forward items (iteration-candidate markers, tech-debt entries).
  - [x] The file documents the `/sdd:build` re-entry rule: in addition to v1's load order, look for `[close-sprint-manifest]` blocks when re-opening a sprint.
  - [x] The file documents the `/sdd:retro` loading rule: read `docs/sdd-cross-project-patterns.md` for prior pattern context.
  - [x] The file documents the `/sdd:onboard` loading rule: read both `~/.claude/sdd-user-profile.json` and `~/.claude/sdd-cross-project-patterns.md` when surfacing existing preferences.
  - [x] The 200-line threshold rule from v1 is preserved unchanged.
- **Verification:**
  - Visual read: every v2 addition above is reflected in the table or per-command rules; no v1 rule that should persist has been removed.
- **Status:** [x] Complete

---

### Item 7: Update references/living-documents.md (v2 update ordering + exclusions)

- **PRD ref:** `prd.md > Bug Fixes` (PRD state-tracking — `/refine` ordering relates to PRD updates), `prd.md > Sprint Loop` (`/refine` behavior) `[unmapped]`
- **Spec ref:** `spec.md > Reference: living-documents.md (updated)`
- **What to build:** Update `plugins/sdd/skills/sdd-guide/references/living-documents.md` with the revised `/sdd:refine` update ordering and explicit exclusions for `docs/v2-verification.md` and `docs/backlog.md`.
- **Acceptance criteria:**
  - [x] The file documents the revised `/sdd:refine` update ordering: PRD → spec → open sprint files (if affected) → AGENTS.md → CLAUDE.md.
  - [x] The file states each step is user-confirmed before proceeding.
  - [x] The file explicitly excludes `docs/v2-verification.md` from the living-documents chain, with a one-line reason ("manual audit trail, not updated by `/refine`").
  - [x] The file explicitly excludes `docs/backlog.md` from the living-documents chain, with a one-line reason ("append-only deferral log written during planning, never edited by `/refine`").
  - [x] The default stance is preserved unchanged from v1: resist scope/PRD/spec changes; new items go to backlog or the PRD's Unvetted section.
  - [x] The re-scoping trigger is preserved unchanged from v1.
  - [x] PRD health monitoring updated: 10+ epics triggers a phase-split recommendation (does not block PRD generation); 5+ unvetted items flags scope creep.
- **Verification:**
  - Visual read: the update ordering and exclusions are explicit; v1 content (default stance, re-scoping trigger) is preserved.
- **Status:** [x] Complete

---

### Item 8: Update sdd-guide/SKILL.md (v2 behavioral additions)

- **PRD ref:** `prd.md > Interview Command Behaviors` (clarifying-questions + handoff stories) `[unmapped]`
- **Spec ref:** `spec.md > Behavioral Layer: sdd-guide > sdd-guide Core (SKILL.md)` and `spec.md > End-of-Command Handoff`
- **What to build:** Add the four v2 behavioral additions to `plugins/sdd/skills/sdd-guide/SKILL.md`. Preserve existing v1 content (tone, interaction rules, guard rails, command chain, command explanations, process notes, state tracking, open concerns) unchanged.
- **Acceptance criteria:**
  - [x] The file documents the clarifying-questions-never-advance rule: when the user asks a clarifying question mid-interview, the agent's response addresses only the clarification; the agent does not record its own recommendation as the user's answer; the interview pointer advances only after the user provides an explicit answer to the question that was actually asked.
  - [x] The file documents the 5-suggested / 10-hard-cap question rule for deepening rounds, with a cross-reference to `references/deepening-rounds.md` for full detail.
  - [x] The file documents the definite end-of-round recommendation framing rule, with a cross-reference to `references/deepening-rounds.md` for the literal wording.
  - [x] The file includes the end-of-command handoff template inline: a two-line form (one-line outcome summary + the literal `Run /clear, then /sdd:<next>` sentence), with the first-handoff explanatory paragraph prepended once per user (tracked via `handoffWarningShown` on user profile).
  - [x] The handoff template enumerates the next-command map for every interview command and explicitly handles out-of-pattern commands (`/sdd:build`, `/sdd:retro`, `/sdd:pause`, `/sdd:unpause`, `/sdd:feedback`).
  - [x] All v1 content in sdd-guide/SKILL.md is preserved — no v1 rule, section, or guideline is removed by this update.
- **Verification:**
  - Visual diff against current `sdd-guide/SKILL.md`: confirm only additive changes (no deletions) and that every v2 addition above is present.
  - Cross-check against `spec.md > sdd-guide Core (SKILL.md)`: every bullet in the spec's v2-additions list is reflected in the file.
- **Status:** [x] Complete

---

## Notes

- This sprint is shared-layer-only. No per-command behavior is implemented; no command SKILL.md files are touched (except `sdd-guide/SKILL.md`, which IS the shared-behavior file by definition); no plugin manifest changes.
- All items carry `[unmapped]` tags because they create or update infrastructure that supports cross-cutting PRD ACs but does not directly satisfy any user-visible AC. The user-visible ACs (e.g., "agent emits the end-of-round recommendation literally") are satisfied in later sprints when individual command SKILLs read these references and behave accordingly.
- The plugin-level `plugins/sdd/CLAUDE.md` is already a v2-correct minimal loader pointer — no item needed for it this sprint.
- Sprint 3 candidate: atomic rename sweep + Bug Fix 1 + creating new skill stubs for `/sdd:discovery`, `/sdd:pause`, `/sdd:unpause`.
