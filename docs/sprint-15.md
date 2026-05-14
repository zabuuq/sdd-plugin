# Sprint 15

**Theme:** Lifecycle close-out — closes two adjacent epics that cover end-of-sprint and end-of-project ceremonies. `/sdd:polish` gains a surgical re-open escape hatch for prematurely-closed sprints; `/sdd:plan` learns to gate the `/sdd:retro` recommendation on genuine project completion; `/sdd:retro` gains its cross-project pattern capture step at the close of its interview. Nine ACs (`aaev`–`aaex`, `aaey`, `aaez`, `aafb`–`aafe`) close.

**Tangible outcome:** After this sprint:
- A user who runs `/sdd:polish` on a sprint that's already been closed (i.e., its `process-notes-sprint-N.md` carries a `[close-sprint-manifest]` block) gets a `Re-open the sprint?` prompt before any iteration work proceeds. On confirmation, they're shown the manifest's checked-off ACs, choose which to un-check, story splits reverse against the manifest's mapping for the selected ACs, and a `[sprint-reopened]` marker is appended to the process notes before the iteration interview begins. On rejection, the command exits with an explanation that re-opening is required to proceed.
- `/sdd:plan` recommends `/sdd:retro` at end-of-command **only** when its PRD state check finds every AC checked **and** the Unvetted section empty. No other SDD command recommends `/sdd:retro` at every sprint boundary.
- `/sdd:retro` adds a final cross-project pattern capture step that runs **after** its retrospective interview is complete, asks the user one pattern at a time, and appends each user-confirmed pattern to `~/.claude/sdd-cross-project-patterns.md` in the spec's documented entry format.

The three sub-stories touch three disjoint SKILL.md files — `polish/SKILL.md`, `plan/SKILL.md` (Item 2's edit), and `retro/SKILL.md` (Item 3's edit) — with one cross-file verification grep for `aaey`. Parallel execution would be structurally safe; sequential is consistent with the Sprint 5-14 pattern.

**Out of scope:**
- Automatic Backlog Generation (`aafs`–`aagd`, 12 ACs). Cross-cutting across 5 planning commands; bigger lift than this sprint.
- v1→v2 Migration guide (`aago`–`aahi`, 21 ACs). Lower priority until v2 nears GA; migration narrative should be written against final-state v2.
- v2 Release Verification remaining (`aahj`, `aahl`–`aaht`, `aahu`, `aahw`, `aahx`, 13 ACs). Four separate user stories that don't pair as cleanly; better to bundle near GA.
- `aafa` is already shipped (`/sdd:build` wrap-up gates the retro recommendation correctly); Sprint 15 covers the `/sdd:plan` side (`aaez`) and the cross-command no-blanket-recommendation invariant (`aaey`).

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** Three single-file items with a cross-file verification on Item 2. Sequential dispatch matches Sprints 5-14. Items are independent of each other, so parallel would be structurally safe — choose sequential for consistency and easier debugging if anything goes sideways.

1. Item 1 (`/sdd:polish` re-open mechanism) — lands the largest single-file change (new startup branch + interview-driven AC un-check loop).
2. Item 2 (`/sdd:plan` retro-recommendation gate + cross-command invariant) — gates the `/sdd:plan` recommendation; greps every SKILL.md for blanket sprint-boundary retro recommendations and removes them if found.
3. Item 3 (`/sdd:retro` cross-project pattern capture) — appends a new final step to the retro interview; minimal additions, well-spec'd output format.

## Checklist

### Item 1: `/sdd:polish` re-open mechanism `[PRD: aaev, aaew, aaex]`

- **PRD ref:** `prd.md > Sprint Loop` (the `/sdd:polish` re-open escape hatch user story) `[PRD: aaev, aaew, aaex]`
- **Spec ref:** `spec.md > /sdd:polish (RENAMED from /iterate)` (re-open escape hatch behavior, lines 439-450) + `spec.md > Close-Sprint Manifest` (lines 603-639, exact mechanism and edge cases) + `plugins/sdd/skills/sdd-guide/references/sprint-tags.md > [close-sprint-manifest]` and `> [sprint-reopened]` (parsers).
- **What to build:** Update `plugins/sdd/skills/polish/SKILL.md` to add a re-open escape hatch that fires at the moment the command currently enforces its "all items complete" prerequisite. The re-open behavior must match the spec's `Close-Sprint Manifest > Re-open behavior` steps 1-9 exactly. Concretely:

  1. **Detect closed-sprint state.** After loading `docs/sprint-N.md` (current Step 5 in `polish/SKILL.md`), scan `process-notes-sprint-N.md` for the most recent `[close-sprint-manifest] ... [/close-sprint-manifest]` block using the parser documented in `references/sprint-tags.md > [close-sprint-manifest] structured block`. The sprint is "closed" iff such a block exists. If no manifest exists, the existing prerequisite check fires unchanged.
  2. **Replace or branch the existing Prerequisites check.** When the sprint is closed (manifest present), the existing "All checklist items in the current sprint file must be complete" prerequisite no longer fires — instead, the re-open prompt fires. When the sprint is open (no manifest), the existing prerequisite check fires unchanged (incomplete items still block).
  3. **Emit the literal prompt** `Re-open the sprint?` (one question at a time per sdd-guide). Wait for the user's response. (Satisfies `aaev`.)
  4. **On user confirmation:** surface the manifest's `PRD ACs checked: ...` list; ask "Which (if any) should be re-opened?" (one question, free-form). Parse the user's answer against the manifest's AC IDs. For each selected AC:
     - Un-check the AC in `docs/prd.md` (find the `[x]` for that AC ID and flip it to `[ ]`).
     - If the AC was part of a story-split mapping per the manifest's `Story splits:` line, reverse the split for that AC — restore the AC to its original story anchor; if the split-off story is left with no remaining ACs (and no `/sdd:refine` additions since close), delete the empty split-off story.
     - ACs added by `/sdd:refine` to the split-off story since close: not touched by reverse, they stay (per spec's `What re-open does NOT reverse` clause and step 5 of `Re-open behavior`).
     - ACs deleted by `/sdd:refine` since close: emit a one-line informational warning naming the missing AC ID, skip that AC's un-check, do not block (per spec's step 6).
     - Append a `[sprint-reopened]` marker entry to `process-notes-sprint-N.md` in the format documented in `references/sprint-tags.md > [sprint-reopened] process-notes marker`. The marker carries the timestamp, the un-checked AC IDs, and the splits-undone mapping for the selected ACs.
     - Proceed with the existing iteration interview flow (Step 5 in `polish/SKILL.md` and downstream). (Satisfies `aaew`. Spec's "deferred to /sdd:spec" phrase in the PRD refers to the manifest mechanism; the spec resolves it with the Close-Sprint Manifest section.)
  5. **On user rejection** (the user answers no, exits, or otherwise declines the re-open prompt): exit `/sdd:polish` immediately. Emit a single-line message explaining that re-opening is required to proceed when the sprint is closed. Do not run any iteration interview. Do not modify any files. (Satisfies `aaex`.)
  6. **Multiple cycles:** if the sprint has been opened and re-closed multiple times (multiple manifests in the process-notes file), the **most recent** manifest is authoritative for re-open. Earlier manifests stay as history but don't drive the re-open logic. Per spec's `Multiple cycles` clause.
  7. **What re-open does NOT reverse:** iteration-candidate dispositions, tech-debt entries, and `docs/v2-verification.md` "Shipped" rows are not touched by the re-open path. These are append-only history and / or manual-disposition records; re-litigation is the user's manual responsibility.

  Place the new re-open branch logically before the existing Prerequisites check so that closed-sprint state is detected and handled before the "items must be complete" check fires. The existing prerequisite text stays in place for the open-sprint path.

  Cross-reference (do not restate) `spec.md > Close-Sprint Manifest` and `references/sprint-tags.md > [close-sprint-manifest]` / `> [sprint-reopened]` for the structural detail. The SKILL.md should name those references rather than duplicate their parser regexes or schema.

- **Acceptance criteria:**
  - [x] `aaev` When `/sdd:polish` is invoked and the targeted sprint's `process-notes-sprint-N.md` file exists indicating the sprint was closed, the system asks "Re-open the sprint?" before proceeding.
  - [x] `aaew` On user confirmation, the sprint is reopened; the specific mechanism for unwinding PRD checkoff and process-notes effects is deferred to `/sdd:spec` (and resolved in `spec.md > Close-Sprint Manifest`; this item wires the SKILL.md to that resolved mechanism).
  - [x] `aaex` On user rejection, `/sdd:polish` exits with a message explaining that re-opening is required to proceed.
- **Verification:**
  - Re-read `polish/SKILL.md` and confirm the closed-sprint detection (manifest lookup) fires before the existing "all items complete" prerequisite check.
  - Confirm the literal `Re-open the sprint?` prompt appears in the SKILL.md text (so the runtime question is verbatim per spec).
  - Confirm the SKILL.md cross-references `spec.md > Close-Sprint Manifest` and `references/sprint-tags.md > [close-sprint-manifest]` / `> [sprint-reopened]`, rather than duplicating the parsers or schema inline.
  - Confirm the SKILL.md names what re-open does NOT reverse (iteration dispositions, tech-debt, v2-verification rows), per spec's `What re-open does NOT reverse` clause.
  - Confirm the SKILL.md handles user rejection by exiting with a re-open-required message — no iteration interview runs, no file writes.
  - Confirm the SKILL.md handles multiple cycles by reading the most recent manifest only.
- **Status:** [x] Complete

---

### Item 2: `/sdd:retro` recommendation timing `[PRD: aaey, aaez]`

- **PRD ref:** `prd.md > /sdd:retro at Project Close` (the recommendation-timing user story) `[PRD: aaey, aaez]`
- **Spec ref:** `spec.md > /sdd:retro` recommendation-timing bullet (lines 471-472): "fires only at project close. `/sdd:plan` and `/sdd:build` recommend `/sdd:retro` only when all PRD ACs are checked AND the Unvetted section is empty. Running `/sdd:retro` on an incomplete project is not blocked..."
- **What to build:** Two changes:
  1. **Add the gated retro recommendation to `plugins/sdd/skills/plan/SKILL.md`** (`aaez`). At end-of-command, after sprint generation and process-notes write, perform a PRD state check: scan `docs/prd.md` for unchecked ACs (`- [ ]`) and check whether the Unvetted section is empty (no bullet entries between `## Unvetted` and the next `## ` heading). If both conditions are true (every AC checked **and** Unvetted is empty), emit a recommendation to run `/sdd:retro`. If either condition is false, do not recommend `/sdd:retro`. The recommendation lands as part of the standard end-of-command handoff; it does not run `/sdd:retro` itself.
     - Mirror the existing pattern in `build/SKILL.md`'s wrap-up which gates the same recommendation per `aafa` (already shipped). Use the same PRD-state-check logic — duplicate the check by reference (point at the same logic; if it lives in a reference file, point at that), or write a parallel check in `plan/SKILL.md` if `build/SKILL.md` has it inlined.
  2. **Verify no other SDD command recommends `/sdd:retro` at every sprint boundary** (`aaey`). Grep across `plugins/sdd/skills/*/SKILL.md` for any text that recommends `/sdd:retro` unconditionally at sprint boundaries (e.g., a phrase like "after each sprint, run `/sdd:retro`" or similar). Acceptable recommendations are gated (build wrap-up's `aafa`-satisfying gate, plan end-of-command's new `aaez` gate). Any blanket sprint-boundary recommendation found must be removed or replaced with the gated form. If no blanket recommendations are found, the AC is satisfied by the current state — no edits beyond Item 2's `plan/SKILL.md` change.

- **Acceptance criteria:**
  - [x] `aaey` No SDD command recommends running `/sdd:retro` at every sprint boundary.
  - [x] `aaez` `/sdd:plan` recommends `/sdd:retro` only when its PRD state check finds every acceptance criterion checked and the Unvetted section empty.
- **Verification:**
  - Re-read `plan/SKILL.md` and confirm a PRD state check fires at end-of-command, with the recommendation emitted only when (every AC checked) **and** (Unvetted section empty) are both true.
  - Grep across `plugins/sdd/skills/*/SKILL.md` for case-insensitive matches of `/sdd:retro` in proximity to `recommend` / `next` / `run` / `after sprint`. For each match, confirm the recommendation is gated on the project-complete state (or is in a non-recommendation context like spec-reference prose). No blanket sprint-boundary recommendations remain.
  - Confirm `build/SKILL.md`'s `aafa`-satisfying gate is unchanged by this item (it already exists; the verification is just to make sure Item 2's edits don't break it).
- **Status:** [x] Complete

---

### Item 3: `/sdd:retro` cross-project pattern capture `[PRD: aafb, aafc, aafd, aafe]`

- **PRD ref:** `prd.md > /sdd:retro at Project Close` (the cross-project pattern capture user story) `[PRD: aafb, aafc, aafd, aafe]`
- **Spec ref:** `spec.md > /sdd:retro` cross-project pattern capture step (lines 473-480): "Fires only after the rest of the retro interview is complete. Captures patterns the user wants to carry forward. Writes confirmed patterns to `~/.claude/sdd-cross-project-patterns.md` (markdown, append-only). Entry format: `- YYYY-MM-DD (project-name): User's chosen phrasing of the pattern.` Patterns are written only after explicit per-pattern user confirmation."
- **What to build:** Add a new final step to `plugins/sdd/skills/retro/SKILL.md` that fires **after** the rest of the retro interview is complete (i.e., after Steps 3, 4, 5, 6 — which cover process retrospective, product assessment, plugin feedback handling, and retro document generation respectively). Insert the new step between Step 6 (Generate Retrospective Document) and Step 7 (Final Open Concerns Resolution), or — if a cleaner spot exists in the current file — at the equivalent post-interview, pre-cleanup point. (`aafe` — fires only after the rest of the interview.)

  **Step behavior:**
  - Ask the user: "Are there any patterns from this project you want to carry forward to future projects?" Free-form, open-ended. (One question at a time per sdd-guide.)
  - If the user says no or equivalent, skip the step entirely. No file write. Proceed to the next retro step.
  - If the user names one or more patterns, ask about each pattern one at a time. For each pattern: surface the user's chosen phrasing back to them and ask "Capture this pattern as `[user's phrasing]`?" (per-pattern confirmation, `aafd`).
  - For each user-confirmed pattern, append a line to `~/.claude/sdd-cross-project-patterns.md` in the format `- YYYY-MM-DD (project-name): User's chosen phrasing of the pattern.` (`aafb`, `aafc`). Use the current date (today, in `YYYY-MM-DD` form) and the project name (sourced from `docs/project-state.json` if it has a name field, or the project directory's basename as a fallback).
  - If `~/.claude/sdd-cross-project-patterns.md` does not exist, create it on the first append. The file is append-only across projects.
  - If the user declines a pattern at the per-pattern confirmation prompt, do not write that pattern. Move to the next pattern (or exit the step if no more patterns).

  Cross-reference (do not restate) `spec.md > /sdd:retro` cross-project pattern capture step for the entry format and ordering invariants.

- **Acceptance criteria:**
  - [x] `aafb` `/sdd:retro` includes a final step after its retrospective interview that captures cross-project patterns the user wants to carry forward.
  - [x] `aafc` `/sdd:retro` writes captured patterns to `~/.claude/sdd-cross-project-patterns.md` (append-only markdown).
  - [x] `aafd` `/sdd:retro` writes a captured pattern only after the user has explicitly confirmed it.
  - [x] `aafe` The pattern-capture step fires only after the rest of `/sdd:retro`'s interview has been completed.
- **Verification:**
  - Re-read `retro/SKILL.md` and confirm the new pattern-capture step appears **after** the retrospective interview steps (Process Retrospective, Product Assessment, Plugin Feedback Handling, Generate Retrospective Document) and **before** the final cleanup / open-concerns / state-update steps. The ordering must make it impossible for the step to fire while the retro interview is still mid-flight.
  - Confirm the step asks one question at a time, with per-pattern confirmation before any write to `~/.claude/sdd-cross-project-patterns.md`.
  - Confirm the entry format exactly matches `- YYYY-MM-DD (project-name): User's chosen phrasing of the pattern.` per spec.
  - Confirm the file path is `~/.claude/sdd-cross-project-patterns.md` (not project-local).
  - Confirm the step is skippable when the user has no patterns to capture (no empty/spurious writes).
- **Status:** [x] Complete

---

## Notes

- All 9 in-sprint ACs close two epic threads: `/sdd:polish` re-open (3) and `/sdd:retro at Project Close` (6, with `aafa` already shipped). The `/sdd:retro at Project Close` epic closes entirely with this sprint.
- **PRD checkoff watch.** Per Sprints 11-14 discipline, sprint close must propagate AC checkoffs to `docs/prd.md` immediately. Sprint 15 follows the same pattern.
- **No FB mapping.** None of `aaev`–`aafe` correspond to a row in `docs/v2-verification.md`. Both epics are PRD-walkthrough-derived (Sprint Loop polish re-open story, /sdd:retro at Project Close stories), not v1-feedback-derived. No `[FB: ...]` tags on items; `/sdd:build` wrap-up writes no v2-verification row updates for this sprint.
- **Three single-file SKILL.md edits, one cross-file grep.** Item 1 → `polish/SKILL.md`. Item 2 → `plan/SKILL.md` (edit) + grep across all SKILL.md (verification). Item 3 → `retro/SKILL.md`. The grep in Item 2 may surface zero-edit verifications (if no blanket recommendations exist) or surface incidental edits (if they do).
- **No new design calls.** Every behavior is resolved in `spec.md > /sdd:polish`, `spec.md > /sdd:retro`, and `spec.md > Close-Sprint Manifest`. The cross-project pattern entry format is specified verbatim in the spec.
- **Cached marketplace plugin is still v1.** Same caveat as every prior sprint. Sprint 15's edits ship to disk in 3 SKILL.md files but aren't live as agent behavior until the maintainer reinstalls. First real test of `/sdd:polish` re-open requires a closed sprint and a follow-up `/sdd:polish` invocation against it. First real test of `/sdd:plan`'s gated retro recommendation comes on the next post-reinstall plan run. First real test of `/sdd:retro` cross-project capture requires a project-close retro.
- **No deepening rounds requested at planning time.** Pre-vetted ACs, fully-specified spec material, three single-file items with a single cross-file verification. Topics a round would have covered (deferred to `/sdd:build` implementation context):
  - Item 1: exact placement of the manifest-detection branch relative to the existing Prerequisites section in `polish/SKILL.md` — discover at build time; the natural spot is before the "all items complete" check, but the file's existing structure may suggest a cleaner integration point.
  - Item 1: whether the manifest parser's regex is copied inline or referenced from `references/sprint-tags.md` — reference, do not duplicate (sdd-guide reference posture).
  - Item 2: whether the PRD-state-check logic in `plan/SKILL.md` should be lifted into a shared reference file or duplicate `build/SKILL.md`'s check — discover at build time. Bias toward duplicating a few lines rather than introducing a new reference file for one check; a new reference is overkill for two callsites.
  - Item 2: how aggressive to be if a stale blanket-recommendation reference surfaces in an unexpected SKILL.md — keep mechanical, replace with gated form, leave any substantive prose cleanup for a later pass.
  - Item 3: project-name source for the pattern entry — `docs/project-state.json` field if present, project-directory basename as fallback. Confirm at build time.
  - Cached-marketplace caveat applies as usual; first real exercise of these behaviors requires a reinstall.
- **Natural next-sprint candidates after Sprint 15:**
  - Auto Backlog Generation (`aafs`–`aagd`, 12 ACs) — cross-cutting across 5 planning commands; next-biggest visible gap.
  - v1→v2 Migration guide (`aago`–`aahi`, 21 ACs) — biggest single-file write; closer to GA.
  - v2 Release Verification remaining (`aahj`, `aahl`–`aaht`, `aahu`, `aahw`, `aahx`, 13 ACs) — seeding / persistence / dogfood docs.
- **Post-Sprint-15 projection:** 46 ACs remaining across 3 epics (55 − 9). Three of the original five epics close with this sprint: `/sdd:polish` re-open and `/sdd:retro at Project Close` close in Sprint 15; Process Notes Growth closed in Sprint 14.
