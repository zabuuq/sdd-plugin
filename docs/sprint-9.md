# Sprint 9

**Theme:** Cross-cutting Interview Command Behaviors + Context Management. Six items, sequential, primarily editing `plugins/sdd/skills/sdd-guide/SKILL.md` and `plugins/sdd/skills/sdd-guide/references/deepening-rounds.md`, with selective wiring into the six existing interview command SKILL.md files (`scope`, `prd`, `spec`, `plan`, `refine`, `polish`). Closes the cross-cutting subset of Interview Command Behaviors (clarifying questions don't advance, bounded rounds 5/10, end-of-round recommendation with reasoning, active prompting) plus both Context Management stories (three-tier between-rounds recommendation, default `/clear` handoff between commands). This is the unblocker for `/sdd:discovery` per AC `aaaj`.

**Tangible outcome:** After this sprint, the SDD plugin's six existing interview commands behave consistently across the cross-cutting interview rules. A mid-interview clarifying question doesn't advance the pointer. Deepening rounds default to five questions, cap at ten without explanation, and require explicit user permission to exceed ten. Every deepening round ends with the structured "I think we should…" / "I do not think we need…" recommendation plus reasoning and a topic list. Every Phase 1 beat and deepening-round topic ends with an explicit "anything else?" invitation. After every deepening round, the agent emits a separate three-tier context recommendation (continue / `/compact` / `/clear`) with reasoning. Every interview command ends with a `/clear` handoff message naming the next command, and first-time users see the heads-up paragraph (gated by `handoffWarningShown` in the user profile). 24 PRD ACs close: `aabh`–`aabj`, `aabk`–`aabm`, `aabn`–`aabq`, `aabz`–`aacc`, `aacm`–`aacs`, `aact`–`aacv`.

**Out of scope:**
- Small-project detection in `/sdd:scope` (`aabr`, `aabs`, `aabt`). Scope-only story, needs `references/right-sizing.md` content audit; pair with right-size behavior in a separate sprint.
- Right-size behavior across interview commands (`aabu`–`aaby`). Depends on `aabt`'s signal-list resolution; pair with the scope detection sprint.
- `/sdd:prd`-only stories — AC quality check (`aacd`–`aacf`), phase-split threshold (`aacg`–`aaci`), Unvetted section (`aacj`–`aacl`). All single-file work in `prd/SKILL.md`; cleaner as a separate small sprint.
- `/sdd:discovery` command implementation (`aaah`–`aaaq`). Blocked by Interview Command Behaviors per AC `aaaj`; this sprint unblocks it for the next sprint.
- `/sdd:polish` re-open mechanism (`aaev`–`aaex`). Different file, different command. Separate sprint.
- Multi-Session Resume command implementations (`aaar`–`aabg`). The `/sdd:pause` and `/sdd:unpause` skill files exist on disk but the ACs are unchecked — that's PRD-hygiene-vs-implementation work for a separate sprint.
- Automatic Backlog Generation (`aafs`–`aagd`). Cross-cutting across 5 commands; separate sprint.
- Process Notes Growth (`aage`–`aagn`). Cross-cutting; separate sprint.
- v1→v2 Migration guide (`aago`–`aahi`). Single-file `MIGRATION-v1-to-v2.md` work; separate sprint.
- v2 Release Verification remaining ACs (~9 still open). Separate work.
- `/sdd:retro` at Project Close (`aaeq`–`aaeu`). Separate sprint.
- Re-tagging Sprint 7's missing `[FB:]` entries (FB-017 through FB-023). Maintainer PRD-hygiene.
- Historical-AC backfill from Sprints 1–4 work shipped pre-Bug-Fix-2. Maintainer PRD-hygiene.

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** Items 1, 4, 5, 6 all touch `plugins/sdd/skills/sdd-guide/SKILL.md`. Items 2 and 3 both touch `plugins/sdd/skills/sdd-guide/references/deepening-rounds.md`. Items 5 and 6 also each touch the six existing interview command SKILL.md files (`scope`, `prd`, `spec`, `plan`, `refine`, `polish`) — Item 5 wires the between-rounds context recommendation, Item 6 wires the end-of-command handoff. The edits are in different sections of the same files, but sequential dispatch prevents merge conflicts and keeps each subagent's mental model coherent.

Item 5 builds naturally on Item 3 (the context recommendation fires AFTER the content recommendation per `aacm`). Item 6 builds on Item 4 (the handoff is the unconditional end-of-command emission that active-prompting rounds out). Items 1 and 2 are foundational rules that 5 and 6 don't depend on directly, but coming first keeps the rule set assembled in one logical pass.

## Checklist

### Item 1: Clarifying-questions-don't-advance rule `[PRD: aabh, aabi, aabj]` `[FB: FB-007]`

- **PRD ref:** `prd.md > Interview Command Behaviors` (clarifying-questions-don't-advance story) `[PRD: aabh, aabi, aabj]`
- **Spec ref:** `spec.md > Cross-Cutting Mechanisms` (interview-command-behavior posture inherited from PRD; PRD ACs are pre-vetted and specific enough that no separate spec resolution exists)
- **What to build:** Add a new section to `plugins/sdd/skills/sdd-guide/SKILL.md` under or adjacent to **Interaction Rules** documenting how the agent handles mid-interview clarifying questions. The rule is cross-cutting and applies to every interview command via sdd-guide's transitive load on each `/sdd:*` invocation.

  The rule must state:
  1. **When the user asks a clarifying question mid-interview, the agent's response addresses only the clarification.** It does not move the currently-open question forward, does not pose a new question, does not advance to the next beat or topic. (`aabh`)
  2. **The agent does not write its own recommendation into the conversation record as if it were the user's answer.** Specifically: the agent must not phrase a clarification answer in a way that implies the user has accepted that answer, and must not log a recommendation as the user's accepted decision. The interview pointer holds. (`aabi`)
  3. **The interview pointer advances only after the user provides an explicit answer to the question that was actually asked.** A user response that is itself a question, or that goes off on a tangent, does not constitute an answer. The agent re-poses (or restates) the open question after addressing the clarification, then waits. (`aabj`)

  Write the rule as agent-actionable phrasing (the rules above are good starting language). Phrase it for cross-cutting application — every interview command inherits this rule because every interview command loads sdd-guide on startup.

  Avoid duplicating the rule into individual command SKILL.md files. The whole point of sdd-guide is to be the cross-cutting rule home; a per-command duplicate would drift.
- **Acceptance criteria:**
  - [x] `aabh` When the user asks a clarifying question mid-interview, the agent's response addresses only the clarification and does not change the currently-open question.
  - [x] `aabi` The agent does not write its own recommendation into the conversation record as the user's answer to the currently-open question.
  - [x] `aabj` The interview pointer advances to the next question only after the user provides an explicit answer to the question that was actually asked.
- **Verification:**
  - Re-read the updated `plugins/sdd/skills/sdd-guide/SKILL.md` and confirm the new section explicitly forbids advancement on a clarifying question.
  - Confirm the rule names the three behaviors (`aabh`/`aabi`/`aabj`) in literal agent-actionable terms.
  - Confirm the rule is positioned as cross-cutting (applies to every interview command), not scoped to one command.
  - Confirm no individual command SKILL.md was edited — the rule's home is sdd-guide.
- **Status:** [x] Complete — rule landed at `plugins/sdd/skills/sdd-guide/SKILL.md` Interaction Rule #4; covers all three ACs as cross-cutting agent-actionable text; no per-command edit.

---

### Item 2: Bounded deepening rounds — 5 suggested / 10 hard cap `[PRD: aabk, aabl, aabm]` `[FB: FB-008]`

- **PRD ref:** `prd.md > Interview Command Behaviors` (bounded-rounds story) `[PRD: aabk, aabl, aabm]`
- **Spec ref:** `spec.md > Cross-Cutting Mechanisms` (deepening-rounds detail inherited from PRD; resolution lives in `references/deepening-rounds.md`)
- **What to build:** Update `plugins/sdd/skills/sdd-guide/references/deepening-rounds.md`'s **Phase 2 — Deepening Rounds** section. The current text says "Generate 4-5 targeted questions" and "There is no cap" — both contradict the v2 PRD.

  Replace with:
  1. **Default round composition is five questions.** Each deepening round is composed by the agent with five questions by default. (`aabk`)
  2. **A round may run up to ten questions without further explanation.** Between five and ten, the agent does not need to justify additional questions; the round can extend organically based on user responses or threading. (`aabl`)
  3. **Past ten questions in a single round requires an explicit stated reason plus explicit user permission.** Before posing the eleventh question in a round, the agent emits a stated reason for needing more questions and explicitly asks for the user's permission to continue past the cap. The agent waits for the user's affirmative response before posing question eleven. (`aabm`)

  The 5-default / 10-cap rule applies to a single round, not to the entire deepening phase. The user is still free to elect another round after one closes — the cap is per-round.

  Remove the existing "There is no cap" sentence. It directly contradicts `aabm`. Keep the rest of the Phase 2 framing (round purpose, question targeting, one-at-a-time rule) intact; only replace the question-count and cap portions.
- **Acceptance criteria:**
  - [x] `aabk` Each deepening round is composed by the agent with five questions by default.
  - [x] `aabl` A deepening round may run up to ten questions without further explanation from the agent.
  - [x] `aabm` Past ten questions in a single round, the agent emits a stated reason for needing more questions and obtains explicit user permission before posing the eleventh.
- **Verification:**
  - Re-read the updated `deepening-rounds.md` and confirm "five questions by default" appears verbatim or in equivalent agent-actionable phrasing.
  - Confirm the 5/10 distinction is explicit: 5 default, up to 10 silent, 11+ requires reason + permission.
  - Confirm the prior "no cap" language is gone.
  - Confirm the cap is named as per-round, not per-deepening-phase.
- **Status:** [x] Complete — Phase 2 section in `deepening-rounds.md` carries "5 questions by default", "up to 10 questions without further explanation", explicit reason + permission gate before question 11; cap framed per-round ("in a round").

---

### Item 3: End-of-round recommendation with reasoning `[PRD: aabn, aabo, aabp, aabq]` `[FB: FB-009, FB-010]`

- **PRD ref:** `prd.md > Interview Command Behaviors` (end-of-round-recommendation story) `[PRD: aabn, aabo, aabp, aabq]`
- **Spec ref:** `spec.md > Cross-Cutting Mechanisms` (resolution inherited from PRD; literal phrasing required per AC `aabn`)
- **What to build:** Update `plugins/sdd/skills/sdd-guide/references/deepening-rounds.md`'s end-of-round transition. The current text reads: *"I've got enough to generate your [document name]. Want another round to sharpen things, or ready to proceed?"* This is a bare prompt that does not commit to a definite recommendation, contradicting `aabn`.

  Replace with the structured form:
  1. **At the end of every deepening round, the agent emits one of exactly two recommendations:** (`aabn`)
     - `I think we should do another round, because [reason]`, OR
     - `I do not think we need another round, because [reason]`
     
     The phrasing is literal (per the PRD AC). The `[reason]` is required, not optional.
  2. **The recommendation includes the agent's reasoning in the same message.** Not in a follow-up. Not after the user asks. The reasoning lands with the recommendation. (`aabo`)
  3. **When the recommendation is to continue (do another round), the reasoning includes an explicit list of topics the proposed next round would cover.** Topic list is part of the reasoning, in the same message. (`aabp`)
  4. **When the recommendation is to close (no more rounds), the reasoning may include a list of topics a next round *would have* covered, used to justify why those topics do not warrant a round.** This is optional but commonly useful — naming what the agent considered and rejected. (`aabq`)

  The recommendation is followed by the user's choice. If the user accepts the close, the document is generated (the existing Phase 2 transition logic continues). If the user accepts another round (or overrides a close-recommendation), the next round begins.

  This recommendation is the **content** recommendation (about whether to do another round). Sprint 9 Item 5 layers the **separate context recommendation** (continue/`/compact`/`/clear`) on top — both fire at end-of-round but address different questions (more content vs. context management).

  Keep the open-ended "the user can do as many rounds as they want" beat from the existing text, but reframe it so the structured recommendation fires every round, not just the first.
- **Acceptance criteria:**
  - [x] `aabn` At the end of every deepening round, the agent emits one of: "I think we should do another round, because [reason]" or "I do not think we need another round, because [reason]."
  - [x] `aabo` The recommendation includes the agent's reasoning in the same message.
  - [x] `aabp` When the recommendation is to continue, the reasoning includes an explicit list of topics the proposed next round would cover.
  - [x] `aabq` When the recommendation is to close, the reasoning may include a list of topics a next round would have covered, used to justify why those topics do not warrant a round.
- **Verification:**
  - Re-read the updated `deepening-rounds.md` and confirm the two literal recommendation forms are present verbatim.
  - Confirm the rule requires reasoning in the same message (not in a follow-up message).
  - Confirm the continue-branch documents topic-list inclusion.
  - Confirm the close-branch documents the optional avoided-topic list.
  - Confirm the prior bare prompt ("Want another round to sharpen things, or ready to proceed?") is replaced.
  - Confirm the recommendation is positioned as separate from Item 5's context recommendation (these are two adjacent but independent recommendations).
- **Status:** [x] Complete — End-of-round recommendation section in `deepening-rounds.md` carries both literal forms with required reasoning, topic-list inclusion on continue, optional avoided-topic list on close. Phase 2 transition reworded to fire the same structured recommendation (no bare prompt remains). Independence from Item 5's context recommendation is preserved in `references/context-management.md`.

---

### Item 4: Active prompting per beat / topic `[PRD: aabz, aaca, aacb, aacc]` `[FB: FB-012]`

- **PRD ref:** `prd.md > Interview Command Behaviors` (active-prompting story) `[PRD: aabz, aaca, aacb, aacc]`
- **Spec ref:** `spec.md > Cross-Cutting Mechanisms` (resolution inherited from PRD)
- **What to build:** Add a new rule to `plugins/sdd/skills/sdd-guide/SKILL.md` (under Interaction Rules, adjacent to or after Item 1's clarifying-questions rule). The rule applies to every interview command via sdd-guide's transitive load.

  The rule must state:
  1. **At the end of each Phase 1 beat, the agent emits an explicit invitation for additional user input.** Suggested phrasings: "anything else on this?", "did I miss anything?", "anything else you'd add before we move on?" or equivalent open-ended prompts. (`aabz`)
  2. **At the end of each deepening-round topic, the agent emits the same explicit invitation.** Same phrasings. (`aaca`)
  3. **The agent does not advance to the next beat or topic before the user has had an opportunity to respond to the invitation.** "Opportunity to respond" means the agent waits for a user message after emitting the invitation. The user may respond with content (which the agent then processes) or with an explicit "no, move on" (which advances the pointer). (`aacb`)
  4. **When the user provides additional input in response to the invitation, the agent processes that input as part of the current beat or topic before advancing.** The input is not deferred to a later beat; it becomes part of the current beat's record. (`aacc`)

  Write the rule as cross-cutting agent guidance. Reference Phase 1 (mandatory questions from `deepening-rounds.md`) and Phase 2 (deepening rounds) explicitly so the agent knows both phases are covered.

  Suggested location: at the end of the Interaction Rules section in sdd-guide.md, after Item 1's clarifying-questions rule. Or in a small standalone reference file (`references/active-prompting.md`) loaded by sdd-guide — the choice is a judgment call. Recommendation: inline in sdd-guide.md, since the rule is short (4 ACs of behavior) and doesn't warrant a reference file's overhead. Keep references for content-rich modules (deepening-rounds, context-management, right-sizing).
- **Acceptance criteria:**
  - [x] `aabz` At the end of each Phase 1 beat, the agent emits an explicit invitation for additional input (e.g., "anything else on this?", "did I miss anything?", or equivalent open-ended prompt).
  - [x] `aaca` At the end of each deepening-round topic, the agent emits the same explicit invitation.
  - [x] `aacb` The agent does not advance to the next beat or topic before the user has had an opportunity to respond to the invitation.
  - [x] `aacc` When the user provides additional input in response to the invitation, the agent processes that input as part of the current beat or topic before advancing.
- **Verification:**
  - Re-read the updated `plugins/sdd/skills/sdd-guide/SKILL.md` and confirm the active-prompting rule is present.
  - Confirm the rule names both Phase 1 beats and deepening-round topics.
  - Confirm the rule explicitly forbids advancement before the user has had an opportunity to respond.
  - Confirm the rule documents the "additional input processed as part of the current beat" semantics.
  - Confirm example phrasings are illustrative ("e.g.", or equivalent), not enumerated as the only allowed phrasings.
- **Status:** [x] Complete — active-prompting rule added as Interaction Rule #5 in `plugins/sdd/skills/sdd-guide/SKILL.md`. Covers Phase 1 + deepening-round topics, forbids advancement before user response, names in-beat processing semantics, treats phrasings as illustrative.

---

### Item 5: Three-tier between-rounds context recommendation — wire reference into commands `[PRD: aacm, aacn, aaco, aacp, aacq, aacr, aacs]` `[FB: FB-014]`

- **PRD ref:** `prd.md > Context Management` (three-tier between-rounds story) `[PRD: aacm, aacn, aaco, aacp, aacq, aacr, aacs]`
- **Spec ref:** `spec.md > Cross-Cutting Mechanisms > Three-Tier Between-Rounds Context Management` (lines 653–676; references `references/context-management.md`)
- **What to build:** `plugins/sdd/skills/sdd-guide/references/context-management.md` already documents the three-tier model in full (141 lines): tier definitions, triggers, recommendation message forms, recognition signals, tier-mapping rules, failure-mode mitigation, `/clear`-branch consolidation with pause-resume, and examples. The content side is done; Item 5's work is **wiring** that reference into the commands so the recommendation actually fires.

  Two parts:
  1. **Add the load directive to sdd-guide.md** so every command that loads sdd-guide transitively gains access to `context-management.md`. Either: (a) instruct sdd-guide to load `context-management.md` on startup, OR (b) add a behavioral pointer in sdd-guide.md naming `references/context-management.md` as the canonical home for the between-rounds recommendation and instructing each interview command to load it at its deepening-round entry point. Option (b) is more efficient (the reference is heavy and only relevant at end-of-round); recommend (b) unless the subagent finds a cleaner shape.
  2. **Audit each of the six existing interview command SKILL.md files** (`scope/SKILL.md`, `prd/SKILL.md`, `spec/SKILL.md`, `plan/SKILL.md`, `refine/SKILL.md`, `polish/SKILL.md`) and ensure each:
     - Loads `references/context-management.md` at the appropriate point (probably alongside its existing load of `deepening-rounds.md`).
     - Emits the three-tier recommendation at the end of every deepening round, **separate from** Item 3's content recommendation. The order is: content recommendation first (Item 3), then the context recommendation (this item). Both in the same end-of-round message group.
     - Does not phrase the recommendation as a bare prompt ("want to compact?" or equivalent). The recommendation names a tier with reasoning. (`aacq`)
     - Does not programmatically invoke `/compact` or `/clear` — both are user-run. (`aacr`)
  3. **Confirm `aacs` is satisfied** by `references/context-management.md` actually containing the recognition-signal resolution (lines 55–85 of that file — Round size, Artifact-conversation gap, Superseded noise, Token-usage approximation). The PRD AC `aacs` defers signals to `/sdd:spec`; the spec resolved the deferral by pointing at this reference file. Item 5 confirms the wire-through is complete.

  Avoid duplicating `context-management.md`'s content into sdd-guide.md or individual command SKILL.md files. The reference owns the "how"; the SKILL.md files own the "when and where." (Project convention from CLAUDE.md: do not duplicate reference-file content into the spec — same principle applies to SKILL.md files.)
- **Acceptance criteria:**
  - [x] `aacm` At the end of every deepening round, after the standard end-of-round recommendation about whether to continue, the agent emits a separate recommendation choosing one of three tiers: continue, `/compact`, or `/clear`.
  - [x] `aacn` The agent recommends the **continue** tier when the round was short and the in-progress artifact already captures the round's value.
  - [x] `aaco` The agent recommends the **/compact** tier when the round captured detail in conversation that the artifact did not, but a compacted summary will carry that detail forward usefully.
  - [x] `aacp` The agent recommends the **/clear** tier when the round was long or heavy, the artifact is rich and self-contained, and the remaining conversation is dominated by superseded back-and-forth.
  - [x] `aacq` The context-management recommendation is delivered with reasoning in the same message and is never phrased as a bare prompt (e.g., "want to compact?" is not allowed).
  - [x] `aacr` The plugin does not programmatically invoke `/compact` or `/clear`; the user runs the command after receiving the recommendation.
  - [x] `aacs` Recognition signals supporting the recommendation (round length, artifact-vs-conversation coverage, noise ratio) are deferred to `/sdd:spec`.
- **Verification:**
  - Confirm `context-management.md` is referenced from sdd-guide.md or directly loaded by each of the six interview command SKILL.md files at the appropriate point (end-of-round).
  - Re-read each of the six interview command SKILL.md files and confirm they emit the three-tier recommendation at end-of-round, separate from the content recommendation.
  - Confirm the order is content recommendation (Item 3) first, then context recommendation (this item).
  - Confirm no command rewrites or duplicates `context-management.md`'s content inline; the reference is the single source.
  - Confirm `aacs`'s deferral is closed by the reference file's existing recognition-signal section (round size / artifact-conversation gap / superseded noise / token-usage approximation).
  - Confirm the failure-mode mitigation (no programmatic `/compact` or `/clear`, always definite with reasoning, no bare prompts) is enforced by the reference's existing content (lines 104–112 of context-management.md).
- **Status:** [x] Complete — `## Between-Rounds Context Recommendation` section added to `sdd-guide.md` after `## Deepening Rounds`; `context-management.md` load directive added to all six interview command SKILL.md files (`scope`, `prd`, `spec`, `plan`, `refine`, `polish`); the four deepening-round commands explicitly emit the three-tier recommendation after the end-of-round content recommendation, in the same message group; all bare prompts removed; reference owns the "how", commands own "when/where".

---

### Item 6: Default /clear handoff between commands `[PRD: aact, aacu, aacv]` `[FB: FB-015]`

- **PRD ref:** `prd.md > Context Management` (default-`/clear`-handoff story) `[PRD: aact, aacu, aacv]`
- **Spec ref:** `spec.md > Cross-Cutting Mechanisms > End-of-Command Handoff` (lines 678–721; template lives inline in `sdd-guide/SKILL.md` per spec, with next-command map and out-of-pattern exceptions resolved)
- **What to build:** Implement the end-of-command handoff template inline in `plugins/sdd/skills/sdd-guide/SKILL.md` (per spec's resolution) and wire each interview command to emit the handoff at completion.

  Three parts:
  1. **Add the handoff template to sdd-guide.md.** Copy the spec's resolution (lines 684–700 of `docs/spec.md`):
     - **Standard form** — one-line outcome summary, then `Run /clear, then /sdd:[next-command] to continue.`
     - **First-handoff explanation prepend** — the heads-up paragraph about what `/clear` does, prepended once per user.
     - **First-handoff tracking** — gated by `handoffWarningShown` in `~/.claude/sdd-user-profile.json`; flipped from `false` to `true` by the first interview command that emits a handoff. Sprint 7 already seeds `handoffWarningShown: false` during `/sdd:onboard`, so the field exists in newly-created profiles.
     - **Next-command map** — the table from spec lines 702–714.
     - **Out-of-pattern exceptions** — `/sdd:build`, `/sdd:retro`, `/sdd:pause`, `/sdd:unpause`, `/sdd:feedback` per spec lines 716–720.
  2. **Wire each of the six interview command SKILL.md files** (`scope`, `prd`, `spec`, `plan`, `refine`, `polish`) to emit the handoff at completion. Each command's wrap-up step (or final step) should reference the handoff template in sdd-guide and emit the standard form (with first-handoff prepend on first invocation). The emission is unconditional per `aacu` — no context-weight heuristic skips it.
  3. **Verify exact wording is implemented per the spec resolution** (`aacv` says wording is deferred to `/sdd:spec`; the spec resolved it). The standard form's literal text plus the first-handoff explanation's literal text must match the spec exactly.

  Out of pattern: `/sdd:build`, `/sdd:retro`, `/sdd:pause`, `/sdd:unpause`, `/sdd:feedback` do not emit the standard handoff. Sprint 9's "wire each interview command" scope covers the six interview commands only.

  Don't pre-emptively touch `/sdd:discovery` (skill file exists but the command isn't implemented per `aaah`–`aaaq`; that's a separate sprint blocked by this one). When `/sdd:discovery` ships, it inherits the handoff via sdd-guide's template and the same wiring pattern.
- **Acceptance criteria:**
  - [x] `aact` At the end of every interview command, the agent emits a handoff message containing both the instruction to run `/clear` and the name of the recommended next command.
  - [x] `aacu` The handoff message is emitted unconditionally — no context-weight heuristic causes it to be skipped.
  - [x] `aacv` Exact handoff message wording is deferred to `/sdd:spec`.
- **Verification:**
  - Confirm the handoff template is documented in sdd-guide.md (standard form + first-handoff explanation + next-command map + out-of-pattern exceptions).
  - Confirm the first-handoff prepend gating logic is tied to `handoffWarningShown` per the spec.
  - Confirm each of the six interview command SKILL.md files (`scope`, `prd`, `spec`, `plan`, `refine`, `polish`) emits the handoff at completion. The emission is at the end of the command, unconditional.
  - Confirm the literal text of the standard form and the first-handoff explanation match `docs/spec.md` lines 684–700 verbatim.
  - Confirm `aacv` is closed: the spec resolution (the inline template) satisfies the AC's deferral to `/sdd:spec`.
  - Confirm `/sdd:build`, `/sdd:retro`, `/sdd:pause`, `/sdd:unpause`, `/sdd:feedback` are NOT modified to emit the handoff (they're out-of-pattern per spec).
  - Confirm `/sdd:discovery` is not touched in this sprint (its implementation is a separate sprint blocked by this one).
- **Status:** [x] Complete — canonical handoff template lives in `sdd-guide.md > ## End-of-Command Handoff` (standard form, first-handoff explanation gated by `handoffWarningShown`, next-command map, out-of-pattern exceptions). All six interview command SKILL.md files (`scope`, `prd`, `spec`, `plan`, `refine`, `polish`) have their own `## End-of-Command Handoff` section pointing at the canonical template, naming the next-command target, giving a one-line outcome-summary guideline, and asserting unconditional emission. Out-of-pattern commands and `/sdd:discovery` untouched.

---

## Notes

- All six items work cross-cutting across the six existing interview commands. Sequential execution is mandatory: items 1–4 add foundational rules to sdd-guide and deepening-rounds; items 5–6 wire references and templates into the commands themselves.
- Sprint 9 closes 24 PRD ACs: 3 (Item 1: `aabh`–`aabj`) + 3 (Item 2: `aabk`–`aabm`) + 4 (Item 3: `aabn`–`aabq`) + 4 (Item 4: `aabz`–`aacc`) + 7 (Item 5: `aacm`–`aacs`) + 3 (Item 6: `aact`–`aacv`).
- Sprint 9 is the **second sprint with `[FB:]` tags** (after Sprint 8's first). FB-007 (Item 1), FB-008 (Item 2), FB-009 + FB-010 (Item 3), FB-012 (Item 4), FB-014 (Item 5), FB-015 (Item 6) each get `Shipped (sprint/item)` row updates in `docs/v2-verification.md` at close-sprint.
- **The biggest cross-cutting unblock in the project.** AC `aaaj` says `/sdd:discovery` "applies every cross-cutting interview command behavior defined in the Interview Command Behaviors epic." Sprint 9 closes the cross-cutting subset of that epic (the parts that apply to all interview commands, not the scope-only or PRD-only stories). With Sprint 9 landed, `/sdd:discovery` can be implemented in a subsequent sprint without inheriting an unfinished cross-cutting layer.
- **Multi-file scope departs from the Sprints 5–8 single-file pattern.** Items 5 and 6 each touch six interview command SKILL.md files in addition to sdd-guide. This is the project's first multi-file sprint by design — the cross-cutting nature of the work makes a single-file decomposition impossible without leaving the work half-done. Risk is managed by sequential dispatch, surgical edits (each subagent gets the explicit file list and what to add per file), and the fact that sdd-guide centralizes the heavy lifting (the per-command edits are thin wiring).
- **The cached marketplace plugin is still v1.** Same caveat as Sprints 5–8. The new cross-cutting behaviors ship to disk in `plugins/sdd/skills/sdd-guide/` and the command SKILL.md files, but they aren't live as agent behavior until the maintainer reinstalls. First post-Sprint-9 dogfood of a real interview command exercise (e.g., `/sdd:scope` or `/sdd:plan`) will be the first verification of the cross-cutting layer in production.
- **First `/sdd:plan` v2 dogfood is still pending plugin reinstall** — same caveat as Sprints 6–8. Sprint 9 planning ran on cached v1 `/sdd:sprint`.
- **After Sprint 9: natural next-sprint candidates remain.**
  - **`/sdd:scope` right-size detection + cross-command right-sizing** (`aabr`–`aaby`, 8 ACs). Sprint 9 ships the cross-cutting interview behaviors; this candidate sprint adds the right-size signal flow on top.
  - **`/sdd:discovery` implementation** (`aaah`–`aaaq`, 10 ACs). Sprint 9 unblocks this per `aaaj`. New command, new SKILL.md, but inherits the cross-cutting rules.
  - **`/sdd:prd`-only polish** — AC quality check (3 ACs), phase-split threshold (3 ACs), Unvetted section (3 ACs). 9 ACs in `prd/SKILL.md`.
  - **Multi-Session Resume** (`aaar`–`aabg`, 17 ACs). `/sdd:pause` and `/sdd:unpause` skill files exist on disk but ACs are unchecked.
  - **Automatic Backlog Generation** (9 ACs cross-cutting across 5 commands).
  - **Process Notes Growth** (10 ACs cross-cutting).
  - **v1→v2 Migration guide** (`aago`–`aahi`, 13 ACs, single `MIGRATION-v1-to-v2.md`).
  - **`/sdd:polish` re-open** (`aaev`–`aaex`, 3 ACs).
  - **`/sdd:retro` at Project Close** (5 ACs).
  - **v2 Release Verification remaining** (~9 ACs).
  - The maintainer chooses at next `/sdd:plan` (or cached `/sdd:sprint`).
- **PRD AC ID conventions check:** Sprint 9 uses 24 IDs across `aabh`–`aacv` (with gaps for the deferred stories). None collide with Sprints 5–8's checked IDs. The PRD's Unvetted section remains empty — no refinement needed before Sprint 9 build.
- **No deepening rounds requested.** Same rationale as Sprints 5–8: pre-vetted ACs with literal phrasing requirements, fully-specified spec/reference resolution for the bulk of the work, structurally well-understood cross-cutting work. The 6-item decomposition is the only real design call; it follows the natural seams (one item per story, with Context Management's two stories getting one item each).
