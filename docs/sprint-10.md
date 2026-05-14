# Sprint 10

**Theme:** `/sdd:discovery` command implementation. Sprint 9 unblocked this work per AC `aaaj` (the cross-cutting Interview Command Behaviors that `/sdd:discovery` inherits). This sprint replaces the 29-line stub at `plugins/sdd/skills/discovery/SKILL.md` with a real interview command — Phase 1 mandatory beats, optional `docs/refs/` reading, deepening rounds via the shared rules, real-time process-notes append, and `docs/discovery.md` generation with overwrite confirmation. 10 ACs (`aaah`–`aaaq`) close.

**Tangible outcome:** After this sprint, a user running the v2 chain on a new project can invoke `/sdd:onboard → /sdd:discovery → /sdd:scope` and `/sdd:discovery` runs as a real interview command. It reads optional reference files from `docs/refs/`, runs Phase 1 mandatory beats, offers deepening rounds with the cross-cutting rules (clarifying-questions-don't-advance, 5/10 bounded rounds, end-of-round recommendation with reasoning, active prompting, three-tier context recommendation), appends to `process-notes-discovery.md` in real time, writes `docs/discovery.md` at the end (with overwrite confirmation if the file already exists), and emits the standard `/clear` handoff to `/sdd:scope`. After `docs/scope.md` exists, subsequent runs read `docs/discovery.md` but never write to it. The chain becomes runnable end-to-end through discovery → scope.

**Out of scope:**
- Small-project detection (`aabr`–`aaby`). Per spec Beat 4 + open-concerns resolution, detection relocated from `/sdd:discovery` to `/sdd:scope`; `/sdd:discovery` does not detect or write `smallProject`. The cross-command right-size *reading* behavior (`aabu`–`aaby`) is a separate sprint.
- `/sdd:prd`-only stories (`aacd`–`aacl`). Single-command work in `prd/SKILL.md`; separate small sprint.
- Multi-Session Resume command implementations (`aaar`–`aabg`). `/sdd:pause` and `/sdd:unpause` SKILL.md files exist as stubs; separate sprint.
- `/sdd:polish` re-open mechanism (`aaev`–`aaex`). Different command. Separate sprint.
- Automatic Backlog Generation (`aafs`–`aagd`). Cross-cutting across 5 commands including `/sdd:discovery`; separate sprint.
- Process Notes Growth (`aage`–`aagn`). The cross-cutting AC `aage` (`/sdd:discovery` writes to `process-notes-discovery.md`) is closed by Item 5 of this sprint as a side effect, but the rest of the epic — wiring the per-phase split into `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, plus `/sdd:retro` selective reading — is separate.
- v1→v2 Migration guide (`aago`–`aahi`). Single-file `MIGRATION-v1-to-v2.md` work; separate sprint.
- v2 Release Verification remaining ACs. Separate work.
- `/sdd:retro` at Project Close (`aaey`–`aafe`). Separate sprint.
- Historical-AC backfill from Sprints 1–4 + Sprint 7's missing `[FB:]` tags (FB-017 through FB-023). Maintainer PRD-hygiene.

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** All seven items edit `plugins/sdd/skills/discovery/SKILL.md`; Item 6 additionally creates `plugins/sdd/skills/sdd-guide/templates/discovery-template.md`. Build order follows the command's runtime sequence so each subagent sees a coherent partial state:

1. Item 1 (prerequisite check) — first thing the skill does on invocation
2. Item 2 (read-only check after `/sdd:scope`) — second startup guard
3. Item 3 (`docs/refs/` reading) — startup acknowledgment after guards pass
4. Item 4 (Phase 1 beats + interview shell) — core command body, replaces the stub message
5. Item 5 (`process-notes-discovery.md` real-time append) — wired through the interview body from Item 4
6. Item 6 (`docs/discovery.md` generation + template + overwrite confirmation) — end of command
7. Item 7 (cross-cutting behavior inheritance verification) — verification step across the assembled command

Items 1, 2, 3 are short startup-guard additions. Item 4 is the largest and reshapes the file's body. Items 5 and 6 wire end-of-beat and end-of-command writes. Item 7 is verification, not a new edit — it confirms inheritance from sdd-guide and may file follow-ups if any cross-cutting rule fails to fire.

## Checklist

### Item 1: `/sdd:onboard` prerequisite check `[PRD: aaah]` `[FB: FB-003]`

- **PRD ref:** `prd.md > /sdd:discovery Command` (exploration story) `[PRD: aaah]`
- **Spec ref:** `spec.md > /sdd:discovery (NEW)` (loads + behavior — startup guard)
- **What to build:** At the start of `/sdd:discovery`'s behavior in `plugins/sdd/skills/discovery/SKILL.md`, before any interview work or file reads, check whether `~/.claude/sdd-user-profile.json` exists. If absent, emit a message instructing the user to run `/sdd:onboard` first and exit cleanly. Do not write any files, do not modify `docs/project-state.json` beyond the standard `lastCommand` startup update, do not perform an interview.

  Pattern lives in sdd-guide's Guard Rails section: "Every command checks its prerequisites before doing any real work. If prerequisites are not met, name the command the user should run instead and stop." Match the existing prerequisite-check phrasing convention from other interview commands (`scope`, `prd`, `spec`).
- **Acceptance criteria:**
  - [x] `aaah` `/sdd:discovery` exits with a "run `/sdd:onboard` first" message and writes nothing when `~/.claude/sdd-user-profile.json` does not exist.
- **Verification:**
  - Re-read the updated `discovery/SKILL.md` and confirm the prerequisite check is the first non-state-update action in the Behavior section.
  - Confirm the exit message names `/sdd:onboard` literally.
  - Confirm the exit path writes no files — including no `docs/discovery.md`, no `process-notes-discovery.md` entry, no other `docs/` writes. The `lastCommand` state-update happens before the prereq check per sdd-guide's state-tracking rule, which is correct.
- **Status:** [x] Complete

---

### Item 2: Read-only mode after `/sdd:scope` exists `[PRD: aaaq]` `[FB: FB-003]`

- **PRD ref:** `prd.md > /sdd:discovery Command` (write `discovery.md` story) `[PRD: aaaq]`
- **Spec ref:** `spec.md > /sdd:discovery (NEW)` (lifecycle — "Once `docs/scope.md` exists, subsequent commands read but never write `docs/discovery.md`")
- **What to build:** In `discovery/SKILL.md`, after the `/sdd:onboard` prerequisite check passes, check whether `docs/scope.md` exists. If it does, the command's behavior changes: it may be invoked to surface or re-read `docs/discovery.md` for context, but it must not run a new interview that would overwrite `docs/discovery.md`. Emit a message naming `docs/scope.md` as the reason and exit — or, if the user explicitly wants to re-do discovery, require them to confirm and then proceed with the standard overwrite-confirmation flow (Item 6 handles the overwrite confirmation itself).

  This AC is about behavior after the project has moved past discovery, not about preventing legitimate re-runs. The cleanest implementation: when `docs/scope.md` exists, emit `docs/scope.md exists; discovery is closed for this project. /sdd:discovery is now read-only — see docs/discovery.md for the captured context.` and exit. If the user genuinely wants to overwrite (e.g., the project has pivoted), they delete `docs/scope.md` first or take a different escape hatch.

  Cross-reference: the PRD's overwrite confirmation in `aaap` covers the case where `docs/discovery.md` exists but `docs/scope.md` does not yet — that's mid-flow re-run. `aaaq` covers the case where the project has structurally moved past discovery.
- **Acceptance criteria:**
  - [x] `aaaq` Once `docs/scope.md` exists in the project, subsequent commands read `docs/discovery.md` but do not write to it.
- **Verification:**
  - Re-read the updated `discovery/SKILL.md` and confirm the `docs/scope.md`-exists branch exits without writing `docs/discovery.md`.
  - Confirm the exit message references `docs/scope.md` as the reason discovery is read-only.
  - Confirm the `lastCommand` state-update still happens (sdd-guide's state-tracking rule is not bypassed by this guard).
- **Status:** [x] Complete

---

### Item 3: `docs/refs/` reading at startup `[PRD: aaal, aaam, aaan]` `[FB: FB-013]`

- **PRD ref:** `prd.md > /sdd:discovery Command` (file uploads story) `[PRD: aaal, aaam, aaan]`
- **Spec ref:** `spec.md > File Uploads (docs/refs/)` (shared reference directory, /sdd:discovery as PRD-required reader) + `spec.md > /sdd:discovery (NEW)` (one-line acknowledgment format on non-empty)
- **What to build:** In `discovery/SKILL.md`, after the prerequisite and read-only guards pass and before opening Phase 1 of the interview, check whether `docs/refs/` exists and contains any files.

  - **When `docs/refs/` is non-empty:** Read the files. Emit a one-line acknowledgment: `Read N files from docs/refs/: [filename1, filename2, ...]. I'll reference them as we go.` No content summaries — the acknowledgment is a registration of the files, not a recap. The agent then proceeds into Phase 1, free to reference the files' content during the interview without requiring the user to re-state it.
  - **When `docs/refs/` is empty or missing:** Proceed directly into Phase 1 with no prompt, no nudge, no "you can upload files here" message. The directory is user-managed between commands per spec Beat 8.

  Implementation note: prior knowledge of the file contents lets the agent fold reference material into Phase 1 question framing naturally — e.g., if the user uploaded an existing PRD, the agent doesn't ask basic "what is this?" questions that the PRD already answers; it asks deeper questions instead.
- **Acceptance criteria:**
  - [x] `aaal` `/sdd:discovery` has a designated upload location it reads before opening Phase 1 of the interview (exact path is `docs/refs/` per spec Beat 8).
  - [x] `aaam` When the designated upload location contains files, `/sdd:discovery` reads them as context and may reference their content during the interview without the user re-stating that content.
  - [x] `aaan` When the designated upload location is empty, `/sdd:discovery` proceeds directly into the Phase 1 interview without prompting the user to upload.
- **Verification:**
  - Re-read the updated `discovery/SKILL.md` and confirm `docs/refs/` is named as the read location.
  - Confirm the read happens before Phase 1 opens, not partway through.
  - Confirm the one-line acknowledgment format matches spec Beat 8 exactly (literal "Read N files from docs/refs/: [filename1, ...]. I'll reference them as we go.").
  - Confirm the empty-directory path emits no prompt — the silent-proceed behavior is essential to `aaan`.
- **Status:** [x] Complete

---

### Item 4: Phase 1 beats + interview shell `[PRD: aaai]` `[FB: FB-003]`

- **PRD ref:** `prd.md > /sdd:discovery Command` (exploration story) `[PRD: aaai]`
- **Spec ref:** `spec.md > /sdd:discovery (NEW)` (loads + behavior — interview structure) + `references/deepening-rounds.md` (Phase 1 + Phase 2 pattern)
- **What to build:** Replace the stub body of `plugins/sdd/skills/discovery/SKILL.md` with a real interview shell. The interview follows the SDD pattern documented in `references/deepening-rounds.md`: Phase 1 mandatory beats first, then Phase 2 deepening rounds offered via the standard end-of-round recommendation framing inherited from sdd-guide.

  **Phase 1 beat design** (the subagent decides, guided by these constraints):
  - The job of `/sdd:discovery` is "what is this?" — open exploration before structural decisions. Beats should surface the project's nature, not its boundaries (boundaries are `/sdd:scope`'s job) or its requirements (`/sdd:prd`'s job) or its architecture (`/sdd:spec`'s job).
  - Beats are mandatory in Phase 1 — every project gets the same baseline coverage. Right-size detection happens in `/sdd:scope` (per spec Beat 4 relocation), not here.
  - Reasonable beat candidates: the idea ("what are you trying to build, in your own words?"), the why ("what makes this worth doing?"), the audience ("who is this for? — feel free to skip if you're the only user"), prior art and inspiration ("what existing things does this resemble or borrow from?"), constraints ("what limits or non-negotiables are in play — time, tech stack, scope?"), starting context ("what context do you already have — existing code, docs, sketches?"), and a brief "anything else I should know?" close before transitioning to Phase 2.
  - Each beat respects sdd-guide's Interaction Rules — one question at a time, free-form (no multiple-choice), adaptive flow (skip beats the user already covered), active prompting at end of beat (Rule 5).
  - The agent may reference `docs/refs/` content (from Item 3) when framing beat questions — e.g., if an existing PRD or seed file already covers "what makes this worth doing," skip or adapt that beat.

  **Phase 2 deepening rounds** are inherited from sdd-guide's transitive load of `references/deepening-rounds.md`: 5-suggested / 10-hard cap, definite end-of-round recommendation with topic preview, three-tier context recommendation after the content recommendation, all per the existing rules.

  **Stub removal:** The current stub message (`/sdd:discovery is registered for the v2 chain but not yet fully implemented. Run /sdd:scope to proceed for now.`) is deleted. The Status section in the frontmatter description is updated to reflect that the command is implemented.

  **End-of-command handoff** is inherited from sdd-guide's `## End-of-Command Handoff` section — the standard handoff template fires at completion, naming `/sdd:scope` as the next command per the next-command map in sdd-guide. The interview command set already includes `/sdd:discovery` per sdd-guide line 121, so no edit to sdd-guide is required.
- **Acceptance criteria:**
  - [x] `aaai` `/sdd:discovery` runs an interview using the SDD interview pattern: Phase 1 mandatory beats followed by deepening rounds offered via the standard end-of-round recommendation framing.
- **Verification:**
  - Re-read the updated `discovery/SKILL.md` and confirm Phase 1 beats are named and mandatory.
  - Confirm Phase 2 deepening rounds are offered using the structured recommendation framing from `references/deepening-rounds.md` (inherited from sdd-guide load).
  - Confirm the stub message is removed; the file's frontmatter `description` no longer says "Stub in this release."
  - Confirm beat framing respects one-question-at-a-time, free-form, and active-prompting rules (cross-cutting from sdd-guide).
  - Confirm the command exits via the standard end-of-command handoff (inherited from sdd-guide), naming `/sdd:scope` as the next command.
- **Status:** [x] Complete

---

### Item 5: `process-notes-discovery.md` real-time append `[PRD: aaak]` `[FB: FB-003, FB-037]`

- **PRD ref:** `prd.md > /sdd:discovery Command` (exploration story) `[PRD: aaak]`
- **Spec ref:** `spec.md > Process Notes` (per-phase notes files) + `references/process-notes.md` if present, otherwise sdd-guide's Process Notes section
- **What to build:** Wire `discovery/SKILL.md` to append to `process-notes-discovery.md` in the project root in real time during the interview. Real-time means: as decisions are made, pushback is given, struggles surface, or pivots happen — not as a single end-of-command summary dump.

  What to capture (per sdd-guide's Process Notes section):
  - Decisions made and the rationale behind them.
  - Pushback from either side (user or agent) and how it was resolved.
  - Questions that came up and what made them difficult.
  - Struggles, pivots, and anything that shifted direction.

  Implementation: at every Phase 1 beat close, every deepening-round close, and every notable mid-interview moment, append a markdown entry to `process-notes-discovery.md` with a timestamp or beat marker. Avoid writing verbatim conversation — the file captures the human-side decisions, not a transcript.

  This AC (`aaak`) is part of the `/sdd:discovery` story but also satisfies the cross-cutting Process Notes Growth AC `aage` (`/sdd:discovery` writes to `process-notes-discovery.md`) as a side effect. The rest of the Process Notes Growth epic — wiring the per-phase split into other planning commands and `/sdd:retro` selective reading — remains separate.
- **Acceptance criteria:**
  - [x] `aaak` `/sdd:discovery` appends to `process-notes-discovery.md` in real time during the interview rather than writing a single end-of-command summary.
- **Verification:**
  - Re-read the updated `discovery/SKILL.md` and confirm the real-time append behavior is described in the interview body, not as an end-of-command step.
  - Confirm the file path is `process-notes-discovery.md` in the project root (not in `docs/`, not in the plugin tree).
  - Confirm the captured content matches sdd-guide's Process Notes guidance (decisions, pushback, struggles, pivots — not verbatim conversation).
  - Side-effect note: AC `aage` (Process Notes Growth epic) is closed by this item — `/sdd:discovery` writes to `process-notes-discovery.md`. The implementing command should update `aage`'s checkbox during sprint wrap-up.
- **Status:** [x] Complete

---

### Item 6: `docs/discovery.md` generation + template + overwrite confirmation `[PRD: aaao, aaap]` `[FB: FB-003]`

- **PRD ref:** `prd.md > /sdd:discovery Command` (write `discovery.md` story) `[PRD: aaao, aaap]`
- **Spec ref:** `spec.md > /sdd:discovery (NEW)` (writes `docs/discovery.md` at end; requires explicit confirmation before overwriting) + `spec.md > templates/discovery-template.md (NEW)` reference
- **What to build:** Two parts.

  1. **Create the template** at `plugins/sdd/skills/sdd-guide/templates/discovery-template.md`. The template defines the output schema for `docs/discovery.md` per spec line 286. Sections should reflect the "what is this?" framing — examples: project name and one-line summary, the idea (longer-form), why it matters, audience, prior art and inspiration, constraints, starting context, open questions for `/sdd:scope`. The subagent designs the specific section list, consistent with the Phase 1 beats from Item 4 (the template's sections should align with the beats — each beat surfaces content for a corresponding section). Keep it lean — the artifact is a context-grounding document for downstream commands, not a comprehensive PRD-style write-up.

  2. **Wire generation in `discovery/SKILL.md`.** At the end of the interview (after Phase 2 deepening rounds close), generate `docs/discovery.md` from the template using content captured in Phase 1 and any deepening rounds. Before writing:
     - **If `docs/discovery.md` does not exist:** write the file.
     - **If `docs/discovery.md` already exists:** emit an explicit overwrite confirmation prompt. The prompt must clearly state that the existing file will be overwritten and require explicit user confirmation before proceeding. On user confirmation, overwrite. On user rejection, exit without writing and tell the user what happened.

  The overwrite confirmation in `aaap` is distinct from the `aaaq` read-only mode in Item 2: `aaap` covers mid-flow re-runs (discovery file exists, scope file does not yet); `aaaq` covers post-scope state. Item 2's `docs/scope.md`-exists guard fires first; if it doesn't exit, Item 6's overwrite confirmation handles the case where `docs/discovery.md` exists but `docs/scope.md` does not.
- **Acceptance criteria:**
  - [x] `aaao` `/sdd:discovery` writes `docs/discovery.md` at the end of its interview.
  - [x] `aaap` When `docs/discovery.md` already exists at `/sdd:discovery` startup, `/sdd:discovery` requires explicit user confirmation before overwriting it.
- **Verification:**
  - Confirm `plugins/sdd/skills/sdd-guide/templates/discovery-template.md` exists with a section structure aligned to Phase 1 beats from Item 4.
  - Re-read the updated `discovery/SKILL.md` and confirm the end-of-interview write step is present.
  - Confirm the overwrite-confirmation path is explicit — emits a prompt, waits for affirmative confirmation, proceeds or aborts based on response.
  - Confirm the no-overwrite-on-rejection path emits a message stating why the file wasn't written.
  - Confirm `aaap`'s check fires *before* the write — not after — so the user actually has a chance to refuse.
- **Status:** [x] Complete

---

### Item 7: Cross-cutting behavior inheritance verification `[PRD: aaaj]` `[FB: FB-003]`

- **PRD ref:** `prd.md > /sdd:discovery Command` (exploration story) `[PRD: aaaj]`
- **Spec ref:** `spec.md > Cross-Cutting Mechanisms` (every interview command inherits the cross-cutting behaviors from sdd-guide)
- **What to build:** This item is verification, not new edits — unless gaps surface. After Items 1–6 land, audit `discovery/SKILL.md` to confirm every cross-cutting interview-command behavior from the Interview Command Behaviors epic fires correctly in `/sdd:discovery`. The behaviors are documented in sdd-guide and inherit transitively via sdd-guide's startup load (Item 4 already names the deepening-rounds reference; sdd-guide's `## Interaction Rules`, `## Deepening Rounds`, `## Between-Rounds Context Recommendation`, and `## End-of-Command Handoff` are loaded automatically).

  Checklist:
  - **Clarifying questions don't advance the interview** (`aabh`–`aabj`): the rule lives in sdd-guide Interaction Rule #4. Inherited automatically. Verify by re-reading `discovery/SKILL.md` and confirming no command-specific text overrides or contradicts the rule.
  - **5/10 bounded deepening rounds** (`aabk`–`aabm`): documented in `references/deepening-rounds.md` Phase 2. Inherited via the deepening-rounds load. Verify the same way.
  - **End-of-round recommendation with reasoning** (`aabn`–`aabq`): documented in `references/deepening-rounds.md`. Inherited. Verify the Phase 2 transition in `discovery/SKILL.md` doesn't emit a bare prompt that contradicts the structured form.
  - **Active prompting at end of beat / topic** (`aabz`–`aacc`): sdd-guide Interaction Rule #5. Inherited. Verify Phase 1 beats and Phase 2 topic closes invite additional input.
  - **Three-tier context recommendation** (`aacm`–`aacs`): sdd-guide `## Between-Rounds Context Recommendation`. Inherited; sdd-guide line 121's interview command set names `/sdd:discovery`, so the recommendation fires at end-of-round. Confirm `references/context-management.md` is loaded by `discovery/SKILL.md` per the Sprint 9 wiring pattern (the other six interview commands have an explicit load directive). If missing, add it.
  - **End-of-command handoff** (`aact`–`aacv`): sdd-guide `## End-of-Command Handoff`. Inherited. Verify the handoff fires at completion, names `/sdd:scope` per the next-command map (sdd-guide line 148), and uses the standard form. The first-handoff-explanation gating via `handoffWarningShown` is already canonical in sdd-guide.

  If any cross-cutting behavior fails to fire (e.g., a missing reference load), file the fix as part of this item — don't punt to a follow-up sprint. The whole point of `aaaj` is the inheritance is real, not theoretical.
- **Acceptance criteria:**
  - [x] `aaaj` `/sdd:discovery` applies every cross-cutting interview command behavior defined in the Interview Command Behaviors epic.
- **Verification:**
  - Re-read `discovery/SKILL.md` end-to-end and confirm every cross-cutting rule listed in the "What to build" checklist is present in inherited or explicit form.
  - Confirm `references/context-management.md` is loaded by `discovery/SKILL.md` (the Sprint 9 wiring pattern — explicit load directive, matching the other six interview commands).
  - Confirm `references/deepening-rounds.md` is loaded (already required by Item 4).
  - Confirm sdd-guide line 121's interview command set includes `/sdd:discovery` (it does — no edit needed unless missing).
  - Side-effect check: if any cross-cutting reference (deepening-rounds, context-management) needs an edit to support `/sdd:discovery` specifically, file that edit as part of Item 7 rather than deferring.
- **Status:** [x] Complete

---

## Notes

- All seven items edit `plugins/sdd/skills/discovery/SKILL.md`. Item 6 additionally creates `plugins/sdd/skills/sdd-guide/templates/discovery-template.md`. Sequential execution is mandatory — every item edits the same primary file.
- Sprint 10 closes 10 PRD ACs: `aaah` (Item 1), `aaai` (Item 4), `aaaj` (Item 7), `aaak` (Item 5), `aaal`–`aaan` (Item 3), `aaao`–`aaap` (Item 6), `aaaq` (Item 2). The 10-AC count matches the entire `/sdd:discovery Command` epic in the PRD.
- **Side effect on `aage` (Process Notes Growth):** Item 5 implements `/sdd:discovery`'s write to `process-notes-discovery.md`, which is also the AC `aage` text. `/sdd:build` wrap-up should check off `aage` alongside `aaak` during PRD checkoff. The rest of the Process Notes Growth epic stays out of scope.
- **No `[FB:]` mapping for most items.** The `/sdd:discovery` command itself is FB-003 (the headline new command per `docs/v2-verification.md`). FB-013 (file uploads during `/sdd:discovery`) maps to Item 3. The other items don't map cleanly to specific FB rows — they're implementation pieces of FB-003. `/sdd:build` wrap-up writes `Shipped (sprint/item)` updates to `docs/v2-verification.md` for FB-003 and FB-013 when the sprint closes; the per-item `[FB: ...]` tags above name FB-003-adjacent for items that are squarely part of the headline command and FB-037-adjacent for the process-notes side effect (FB-037 covers per-phase process notes generally, of which `/sdd:discovery`'s write is one part).
- **Cached marketplace plugin is still v1.** Same caveat as every prior sprint — Sprint 10's edits ship to disk in `plugins/sdd/skills/discovery/`, but they aren't live as agent behavior until the maintainer reinstalls. First post-Sprint-10 invocation of `/sdd:discovery` will be the first verification of the implementation in production.
- **First-ever Phase 1 beat design.** Sprint 10 is the first sprint in this project where the implementing subagent designs Phase 1 beats from scratch — prior beat designs (in `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:onboard`) inherited from v1. The subagent's beat choices for Item 4 should be reviewed during the verification pass — the beats should surface project nature without overlapping `/sdd:scope`'s boundary-drawing or `/sdd:prd`'s requirements.
- **No deepening rounds requested.** Same rationale as Sprints 5–9: pre-vetted ACs, fully-specified spec/reference resolution for the supporting mechanisms (`docs/refs/`, cross-cutting interview behaviors, handoff template), structurally well-understood single-command scope. The Phase 1 beat design is the only genuine open design call and is deferred to `/sdd:build` implementation context.
- **Natural next-sprint candidates after Sprint 10:**
  - **Multi-Session Resume** (`aaar`–`aabg`, 17 ACs). `/sdd:pause` and `/sdd:unpause` SKILL.md stubs exist; full implementation pairs with the resume-file schema in `references/pause-resume.md` (already in place from prior spec work).
  - **Right-sizing wire-through** (`aabr`–`aaby`, 8 ACs). `/sdd:scope` detection (3 ACs) + cross-command right-sizing (5 ACs) — touches every interview command including the freshly-implemented `/sdd:discovery`.
  - **`/sdd:prd`-only polish** (`aacd`–`aacl`, 9 ACs). AC quality check, phase-split threshold, Unvetted section. Single-file work in `prd/SKILL.md`.
  - **`/sdd:retro` at Project Close** (`aaey`–`aafe`, 6 ACs). Recommendation gating + cross-project pattern capture.
  - **v1→v2 Migration guide** (`aago`–`aahi`, 21 ACs). Single `MIGRATION-v1-to-v2.md` file. Becomes more valuable as v2 nears GA.
  - **Automatic Backlog Generation** (`aafs`–`aagd`, 12 ACs). Cross-cutting across 5 planning commands including `/sdd:discovery`.
  - **Process Notes Growth remainder** (`aagf`–`aagn`, 9 remaining ACs after Sprint 10 closes `aage`). Cross-cutting per-phase wiring + `/sdd:retro` selective reading + append-only rule.
  - **`/sdd:polish` re-open mechanism** (`aaev`–`aaex`, 3 ACs).
  - **v2 Release Verification remaining** (~12 ACs across seeding, manual checkoff convention, approach documentation, persistence).
- **PRD AC ID conventions check:** Sprint 10 closes 10 IDs `aaah`–`aaaq` — the entire `/sdd:discovery Command` epic. None collide with Sprints 1–9's closed IDs. PRD's Unvetted section is empty.
