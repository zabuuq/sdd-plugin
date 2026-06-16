# Sprint 3

**Theme:** Atomic rename sweep + Bug Fix 1 + new-command stubs. Mechanical v1→v2 transition so subsequent sprints can implement per-command behavior on stable v2 skill paths.

**Tangible outcome:** `claude plugin validate .` passes against the v2 command surface. Skill directories at `plugins/sdd/skills/` are the v2 set (`onboard`, `discovery`, `scope`, `prd`, `spec`, `plan`, `build`, `polish`, `refine`, `retro`, `pause`, `unpause`, `feedback`, plus `sdd-guide`). v1 command names appear only in `docs/archive/v1/` and explicit migration-context references. Plugin manifest reads version 2.0.0. `/sdd:feedback` no longer clobbers `lastCommand`.

**Out of scope:** full implementation of `/sdd:discovery`, `/sdd:pause`, `/sdd:unpause` (stubs only this sprint); `MIGRATION-v1-to-v2.md` (its own coherent sprint — five stories, ~17 ACs); Bug Fix 2 (`/sdd:build` wrap-up PRD checkoff — depends on `/sdd:build` wrap-up implementation); per-command v2 behavior for `/sdd:onboard`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:build`, `/sdd:polish`, `/sdd:refine`, `/sdd:retro`; `docs/v2-verification.md` seeding (deferred — was supposed to fire at `/sdd:spec` finalization but the v2 spec phase has been completed without it; will need a one-off seed item in a future sprint or hand-seeded).

## Build Preferences
- **Build mode:** autonomous

## Execution order

Item 1 (skill-directory rename) **must complete before** Item 2 (active-prose sweep). Item 1 changes file paths; Item 2 changes content across many files including the renamed ones. Running them in parallel would conflict.

Items 3–7 are independent of each other and of Item 2 (they touch different files) and may run in parallel once Item 1 has finished.

## Checklist

### Item 1: Atomic skill-directory rename + reflect deletion

- **PRD ref:** `prd.md > v1 → v2 Migration` (no-aliases story) `[PRD: aahf]`
- **Spec ref:** `spec.md > File Structure`, `spec.md > Removed from v1`, `spec.md > Atomic Rename Sweep`
- **What to build:** Rename `plugins/sdd/skills/sprint/` → `plan/`. Rename `plugins/sdd/skills/iterate/` → `polish/`. Delete `plugins/sdd/skills/reflect/` and `plugins/sdd/skills/sdd-guide/templates/reflect-template.md`. Update the SKILL.md files inside the renamed directories so every internal reference to `/sdd:sprint` becomes `/sdd:plan`, every internal reference to `/sdd:iterate` becomes `/sdd:polish`, and every reference to `/sdd:reflect` is removed or replaced with the correct v2 routing (per `docs/spec.md > /sdd:build` and `> /sdd:polish` — `/sdd:reflect`'s work absorbed into build wrap-up + retro). Inside the renamed SKILL.md files, also update the YAML frontmatter `name:` field (`name: sprint` → `name: plan`; `name: iterate` → `name: polish`) and the command-title `# /sdd:sprint` heading → `# /sdd:plan`, etc. Use `git mv` so the rename preserves history.
- **Acceptance criteria:**
  - [x] `plugins/sdd/skills/sprint/` no longer exists as a directory.
  - [x] `plugins/sdd/skills/plan/SKILL.md` exists.
  - [x] `plugins/sdd/skills/iterate/` no longer exists as a directory.
  - [x] `plugins/sdd/skills/polish/SKILL.md` exists.
  - [x] `plugins/sdd/skills/reflect/` no longer exists.
  - [x] `plugins/sdd/skills/sdd-guide/templates/reflect-template.md` no longer exists.
  - [x] The YAML frontmatter `name:` field in `plan/SKILL.md` is `plan` (not `sprint`).
  - [x] The YAML frontmatter `name:` field in `polish/SKILL.md` is `polish` (not `iterate`).
  - [x] Inside `plan/SKILL.md`, every `/sdd:sprint` reference has been updated to `/sdd:plan`.
  - [x] Inside `polish/SKILL.md`, every `/sdd:iterate` reference has been updated to `/sdd:polish`.
  - [x] Inside `plan/SKILL.md` and `polish/SKILL.md`, every reference to `/sdd:reflect` is either removed or replaced with the correct v2 next-command (per spec).
  - [x] `git mv` (or equivalent rename-preserving operation) was used so commit history follows the rename rather than recording a delete+add.
- **Verification:**
  - Directory listing: `plugins/sdd/skills/` contains `plan/` and `polish/` but no `sprint/`, `iterate/`, or `reflect/`.
  - Grep `plugins/sdd/skills/plan/SKILL.md` for `/sdd:sprint` and `/sdd:reflect` → both return zero matches. Grep for `/sdd:plan` → returns multiple matches.
  - Grep `plugins/sdd/skills/polish/SKILL.md` for `/sdd:iterate` and `/sdd:reflect` → both return zero matches.
  - Frontmatter visual check on `plan/SKILL.md` and `polish/SKILL.md` confirms `name:` is correct.
- **Status:** [x] Complete

---

### Item 2: Active-prose v1→v2 name sweep

- **PRD ref:** `prd.md > v1 → v2 Migration` (active-material consistency) `[PRD: aahg, aahh]`
- **Spec ref:** `spec.md > Atomic Rename Sweep`
- **What to build:** Sweep every reference to `/sdd:sprint`, `/sdd:iterate`, and `/sdd:reflect` in active material, replacing with the v2 equivalent or removing references to `/sdd:reflect` (which no longer exists). Active material is:
  - `README.md` (at repo root)
  - `AGENTS.md`
  - `CLAUDE.md` (root) and `plugins/sdd/CLAUDE.md`
  - All `plugins/sdd/skills/*/SKILL.md` files NOT renamed by Item 1 (i.e., `onboard`, `scope`, `prd`, `spec`, `build`, `refine`, `retro`, `feedback`, `sdd-guide`) — these typically reference v1 names in handoff text, next-command lines, or command-chain prose. The `plan/` and `polish/` SKILL.md files are handled by Item 1.
  - All `plugins/sdd/skills/sdd-guide/references/*.md` files
  - All `plugins/sdd/skills/sdd-guide/templates/*.md` files
  - Live prose under `docs/`: `docs/scope.md`, `docs/prd.md`, `docs/spec.md`, `docs/open-concerns.md`, `docs/sdd-feedback.md`, `docs/sprint-1.md`, `docs/sprint-2.md`, `docs/sprint-3.md` (this file itself — but its references like "Item 1 renames sprint→plan" are explicit migration context and may stay), and `docs/backlog.md` if it exists.
  - Process-notes files at repo root: `process-notes.md` (legacy), `process-notes-sprint-1.md`, `process-notes-sprint-2.md`. Note: `process-notes-sprint-1.md` and `process-notes-sprint-2.md` are historical records of work that happened under v1 `/sdd:sprint` naming; references to that historical fact are explicit migration context and may stay. Use judgment: when prose says "during /sdd:sprint we did X" describing what historically happened, that's allowed; when prose says "/sdd:sprint will do X" prospectively, that should change.
- **NOT touched (preserve verbatim):**
  - `docs/archive/v1/` — entire directory left untouched.
  - `docs/archive/submission-draft.md` — historical document.
  - Explicit migration-context references in sprint files or process notes (e.g., "sprint 3 renames sprint→plan and iterate→polish" — that *names* the rename, doesn't reference v1 commands prospectively).
- **Acceptance criteria:**
  - [x] After this item, `grep -r "/sdd:sprint" plugins/ docs/ README.md AGENTS.md CLAUDE.md` returns matches only in: `docs/archive/v1/` (untouched), explicit migration-context phrasing, and historical-event descriptions in process notes about what already happened.
  - [x] After this item, `grep -r "/sdd:iterate" plugins/ docs/ README.md AGENTS.md CLAUDE.md` returns matches only in the same allow-list.
  - [x] After this item, `grep -r "/sdd:reflect" plugins/ docs/ README.md AGENTS.md CLAUDE.md` returns matches only in the same allow-list. (`/sdd:reflect` has no v2 equivalent — references are deleted or replaced with the correct v2 routing per spec, e.g., "absorbed into `/sdd:build` wrap-up + `/sdd:retro`".)
  - [x] `docs/archive/v1/` is byte-for-byte identical to its state before this item ran (no files modified or deleted).
  - [x] README.md documents the v2 command chain explicitly (or its existing command list reflects v2 names).
  - [x] AGENTS.md's "Current cycle (v2)" section and prose elsewhere use v2 command names.
  - [x] Every SKILL.md outside Item 1's scope that previously referenced v1 names has been updated.
- **Verification:**
  - Run the three greps listed above; inspect each remaining match and confirm it falls within the allow-list (archive, explicit migration context, historical fact).
  - Spot-read README.md's command-chain section: every named command is a v2 command.
- **Status:** [x] Complete

---

### Item 3: Plugin manifest version bump to 2.0.0

- **PRD ref:** none `[unmapped]`
- **Spec ref:** `spec.md > Runtime & Deployment` (version bump line)
- **What to build:** Update `plugins/sdd/.claude-plugin/plugin.json` so `version` reads `2.0.0`. Check `.claude-plugin/marketplace.json` for any version field; per current state of the file there is no top-level version, but verify the plugins[].description and plugins[].source still resolve. Do not introduce a version field where none exists.
- **Acceptance criteria:**
  - [x] `plugins/sdd/.claude-plugin/plugin.json` has `"version": "2.0.0"` (was 1.0.0).
  - [x] `plugins/sdd/.claude-plugin/plugin.json` is otherwise unchanged (same name, description, author, license).
  - [x] `.claude-plugin/marketplace.json` was inspected; if it has a version field it is consistent with 2.0.0; if not, no version field is added.
  - [x] `claude plugin validate .` (or the IDE-side equivalent `/plugin validate .`) succeeds against the project at the end of the sprint.
- **Verification:**
  - Read `plugins/sdd/.claude-plugin/plugin.json` and confirm `version: 2.0.0`.
  - Read `.claude-plugin/marketplace.json` and confirm consistency.
  - Run plugin validation if available in this environment; otherwise note the validation step for the maintainer.
- **Status:** [x] Complete

---

### Item 4: Create `discovery/SKILL.md` stub

- **PRD ref:** none — stub creation, not behavior implementation `[unmapped]`
- **Spec ref:** `spec.md > /sdd:discovery (NEW)` (read for context — full implementation is a future sprint)
- **What to build:** Create `plugins/sdd/skills/discovery/SKILL.md` with v2-compliant YAML frontmatter (`name: discovery`, `description: <one-line>`, `disable-model-invocation: true`) and a brief body that:
  - Loads `skills/sdd-guide/SKILL.md` (per the plugin's loading convention).
  - States the command's intended job in one sentence ("open exploration before structural decisions — 'what is this?'").
  - States explicitly that the command's full v2 behavior — the interview, `docs/refs/` loading, `docs/discovery.md` write, process-notes append, handoff — is implemented in a later sprint.
  - Exits with a message naming the user-facing status: "discovery is registered for the v2 chain but not yet fully implemented. Run `/sdd:scope` to proceed for now."
- **Acceptance criteria:**
  - [x] `plugins/sdd/skills/discovery/SKILL.md` exists.
  - [x] YAML frontmatter contains `name: discovery`, `description: <non-empty>`, and `disable-model-invocation: true`.
  - [x] Body loads `skills/sdd-guide/SKILL.md` per convention.
  - [x] Body states the command's intended purpose in one sentence.
  - [x] Body explicitly states full behavior is implemented in a later sprint.
  - [x] Body emits a routing message pointing the user to `/sdd:scope` as the interim next-command (or an equivalent honest exit).
  - [x] File contains no v1 command name references.
- **Verification:**
  - Frontmatter parses as valid YAML.
  - Plugin validation includes this file without error.
- **Status:** [x] Complete

---

### Item 5: Create `pause/SKILL.md` stub

- **PRD ref:** none `[unmapped]`
- **Spec ref:** `spec.md > /sdd:pause (NEW)` (read for context — full implementation is a future sprint)
- **What to build:** Create `plugins/sdd/skills/pause/SKILL.md` with v2-compliant YAML frontmatter (`name: pause`, `description: <one-line>`, `disable-model-invocation: true`) and a brief body that:
  - Loads `skills/sdd-guide/SKILL.md`.
  - States the command's intended job in one sentence ("distill the in-flight command's conversation into a resume file at `docs/<command>-resume.md`").
  - States explicitly that the command's full v2 behavior — the pause-resume schema-driven write per `references/pause-resume.md`, the prerequisite check on `lastCommand`, the overwrite semantics — is implemented in a later sprint.
  - Exits with a message: "pause is registered for the v2 chain but not yet fully implemented."
- **Acceptance criteria:**
  - [x] `plugins/sdd/skills/pause/SKILL.md` exists.
  - [x] YAML frontmatter contains `name: pause`, `description: <non-empty>`, and `disable-model-invocation: true`.
  - [x] Body loads `skills/sdd-guide/SKILL.md`.
  - [x] Body states the command's intended purpose in one sentence.
  - [x] Body explicitly states full behavior is implemented in a later sprint.
  - [x] Body emits an honest not-yet-implemented exit message.
  - [x] File contains no v1 command name references.
- **Verification:**
  - Frontmatter parses as valid YAML.
- **Status:** [x] Complete

---

### Item 6: Create `unpause/SKILL.md` stub

- **PRD ref:** none `[unmapped]`
- **Spec ref:** `spec.md > /sdd:unpause (NEW)` (read for context — full implementation is a future sprint)
- **What to build:** Create `plugins/sdd/skills/unpause/SKILL.md` with v2-compliant YAML frontmatter (`name: unpause`, `description: <one-line>`, `disable-model-invocation: true`) and a brief body that:
  - Loads `skills/sdd-guide/SKILL.md`.
  - States the command's intended job in one sentence ("zero-argument resume of whatever was paused, sourced from `lastCommand`").
  - States explicitly that the command's full v2 behavior — reading `lastCommand`, loading the resume file, reading the source command's SKILL.md, resuming at the Pending decision section, post-completion cleanup of the resume file — is implemented in a later sprint.
  - Exits with a message: "unpause is registered for the v2 chain but not yet fully implemented."
- **Acceptance criteria:**
  - [x] `plugins/sdd/skills/unpause/SKILL.md` exists.
  - [x] YAML frontmatter contains `name: unpause`, `description: <non-empty>`, and `disable-model-invocation: true`.
  - [x] Body loads `skills/sdd-guide/SKILL.md`.
  - [x] Body states the command's intended purpose in one sentence.
  - [x] Body explicitly states full behavior is implemented in a later sprint.
  - [x] Body emits an honest not-yet-implemented exit message.
  - [x] File contains no v1 command name references.
- **Verification:**
  - Frontmatter parses as valid YAML.
- **Status:** [x] Complete

---

### Item 7: Bug Fix 1 — `/sdd:feedback` no `lastCommand` write

- **PRD ref:** `prd.md > Bug Fixes` (feedback lastCommand) `[PRD: aaaa, aaab, aaac]`
- **Spec ref:** `spec.md > /sdd:feedback` (v2 changes, no `lastCommand` write)
- **What to build:** Remove the State-Updates step in `plugins/sdd/skills/feedback/SKILL.md` that writes `lastCommand` to `docs/project-state.json`. Update Step 2 (Capture Context) to read `lastCommand` directly from `docs/project-state.json` — the file-read-order workaround ("use the value that was stored when the file was first read in the Loading step, before the state update") is no longer needed because the state update no longer happens. Update the Important Reminders entry that referred to the workaround. After this fix, consecutive `/sdd:feedback` runs all attribute the same preceding command (whatever `lastCommand` was before the first `/feedback` ran), satisfying ACs `aaaa`, `aaab`, `aaac`.
- **Acceptance criteria:**
  - [x] The "State Updates (immediate)" section in `feedback/SKILL.md` no longer contains an instruction to write `lastCommand` to `docs/project-state.json`. (The section may be removed entirely or revised to clarify what state, if any, is updated; the spec is clear that no `lastCommand` write happens.)
  - [x] Step 2 (Capture Context) reads `lastCommand` directly from `docs/project-state.json` without invoking any "use value before update" workaround.
  - [x] The Important Reminders entry that referenced capturing-the-previous-`lastCommand`-before-overwriting has been updated to reflect the new behavior (no overwrite happens) or removed if it no longer applies.
  - [x] The behavior aligns with PRD ACs `aaaa`, `aaab`, `aaac`: startup writes no `lastCommand`; "After command" sources from current `lastCommand`; N consecutive `/feedback` runs all produce notes whose "After command" equals the pre-first-`/feedback` `lastCommand`.
  - [x] No other behavior changes (the feedback note format, the project-state.json read for sprint number, the open-concerns skip, the first-run explanation gate — all preserved).
- **Verification:**
  - Read `feedback/SKILL.md` and confirm no `lastCommand = "/sdd:feedback"` write occurs.
  - Walk through the AC scenarios mentally: a single `/feedback` after `/sdd:scope` → "After command" is `/sdd:scope`; two consecutive `/feedback` calls after `/sdd:scope` → both notes' "After command" is `/sdd:scope`. Confirm the updated SKILL.md produces these outcomes.
- **Status:** [x] Complete

---

## Notes

- Items 1 and 2 together carry the rename load. Item 1 is the atomic file-system + renamed-content operation; Item 2 is the cross-cutting prose update. Sequencing them avoids parallel-edit conflicts; bundling them into one item would create a single oversized item with cross-domain failure modes.
- Stubs (Items 4–6) are tagged `[unmapped]` because they don't directly satisfy any user-visible AC. The user-visible ACs for `/sdd:discovery`, `/sdd:pause`, and `/sdd:unpause` (e.g., `aaah`–`aabg`) are satisfied when later sprints implement the commands' full behavior.
- Item 3 (manifest bump) is also `[unmapped]` — version bump is a release artifact decision, not a PRD AC.
- Bug Fix 2 (PRD AC checkoff during `/sdd:build` wrap-up — ACs `aaad`–`aaag`) is deferred to a later sprint because it depends on `/sdd:build`'s wrap-up phase being implemented, which is a substantial feature in itself.
- `docs/v2-verification.md` seeding (PRD AC `aahj`) is unresolved: per spec it was supposed to happen at `/sdd:spec` finalization, but the v2 spec phase completed without seeding. Surfacing here as a thing the maintainer should track; a one-off seed item belongs in a future sprint.
- Sprint 4 candidate (provisional): `MIGRATION-v1-to-v2.md` (the migration guide itself — 5 stories, ~17 ACs in the v1→v2 Migration epic) plus the `docs/v2-verification.md` seeding one-off.
