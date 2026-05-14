# Sprint 16

**Theme:** Automatic Backlog Generation — closes the entire Automatic Backlog Generation epic (12 ACs, `aafs`–`aagd`). When a user defers an item during any of the five planning commands (`/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`), they get an explicit "defer to backlog" vs "drop" confirmation prompt; on affirmative defer, a structured self-contained entry appends to `docs/backlog.md`; on drop, the decision lands only in the current phase's process-notes file. Generated planning artifacts swap inline "out of scope" / "deferred items" sections for a one-line pointer to `docs/backlog.md` once the backlog has at least one entry.

**Tangible outcome:** After this sprint:
- A new `plugins/sdd/skills/sdd-guide/references/backlog.md` reference file owns the entry format (Title / What / Why deferred / Trigger to revisit / Dependencies / Source — fixed order, `---` separator between entries, append-only), the Source-field format with examples, and the defer-to-backlog write-trigger protocol (explicit prompt distinguishing "defer to backlog" from "drop"; affirmative-only writes; drops to process-notes only).
- `discovery/SKILL.md`, `scope/SKILL.md`, `prd/SKILL.md`, `spec/SKILL.md`, `plan/SKILL.md` each load `references/backlog.md` and route any deferral surfaced during planning through its confirmation prompt and entry-append path. (Existing scattered backlog mentions in `scope/SKILL.md` get reconciled with the new reference — the project-split write in `/sdd:scope` continues to land in `docs/backlog.md` and now goes through the same prompt.)
- Generated artifacts (`docs/scope.md`, `docs/prd.md`, `docs/spec.md`, `docs/sprint-N.md`, `docs/discovery.md`) emit a single-line pointer to `docs/backlog.md` in lieu of an inline deferred-items section when `docs/backlog.md` exists with at least one entry. Both the relevant template (`scope-template.md`, `prd-template.md`, `spec-template.md`, `sprint-template.md`, `discovery-template.md`) and the writing SKILL.md text reflect this.

**Out of scope:**
- v1→v2 Migration guide (`aago`–`aahi`, 21 ACs). Written against final-state v2; deferred until v2 is near GA.
- v2 Release Verification remaining (`aahj`, `aahl`–`aaht`, `aahu`, `aahw`, `aahx`, 13 ACs). Seeding hook + dogfood-approach docs; bundle near GA.
- Soft acceptance criteria for MIT distribution (carried open concern, target `/sdd:refine`). Not a sprint-16 item — it lands when judgment-based ACs are next refined, and backlog entries are exactly the kind of judgment-based ACs that concern targets, so revisit after this sprint ships.

**Activation caveat:** None of these edits change agent behavior until the maintainer reinstalls the cached marketplace plugin. Same caveat as every prior v2 sprint.

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** Two items; Item 2 builds on Item 1's reference file and the touched SKILL.md surfaces. Sequential dispatch matches Sprints 5-15.

1. Item 1 (reference file + write triggers) — lands the heaviest work: one new reference file + 5 SKILL.md edits to wire the defer-prompt and entry-append path.
2. Item 2 (artifact pointer mechanism) — adjusts the same 5 SKILL.md files' artifact-generation step plus the 5 templates so generated artifacts emit a backlog pointer when entries exist.

## Checklist

### Item 1: Backlog reference file + defer-to-backlog write triggers `[PRD: aafs, aaft, aafu, aafv, aafw, aafx, aafy, aafz, aaga]`

- **PRD ref:** `prd.md > Automatic Backlog Generation` (stories 1 and 2 — write-trigger and entry format) `[PRD: aafs, aaft, aafu, aafv, aafw, aafx, aafy, aafz, aaga]`
- **Spec ref:** `spec.md > Backlog Entry Format` (entry schema, Source field examples, separator, append-order, write-trigger including the defer-vs-drop prompt) + `spec.md > /sdd:refine` ("`docs/backlog.md` is NOT touched" exclusion) + `references/living-documents.md > Exclusions from the Living-Documents Chain` (backlog excluded from `/sdd:refine`'s update chain).
- **What to build:** Two coupled changes:

  1. **Create `plugins/sdd/skills/sdd-guide/references/backlog.md`.** Owns every "how" detail for the backlog mechanism. Sections:
     - **Purpose** — one-paragraph statement of what `docs/backlog.md` is for and which commands write to it.
     - **Entry format** — the 6-field schema in fixed order: Title, What, Why deferred, Trigger to revisit, Dependencies, Source. Each entry is preceded by a `### [Item title]` heading whose title text matches the Title field. Entries are separated by `---` on its own line. Field bullets follow the literal format in `spec.md > Backlog Entry Format` (`- **Title:**`, `- **What:**`, etc.). Append-only across the file's lifetime — existing entries are not modified by subsequent commands.
     - **Source field format** — `Source: /sdd:[command] — [phase/round identifier] — [topic]`. Include the three spec-provided examples verbatim (`/sdd:scope` Phase 1 audience beat; `/sdd:prd` Round 2 deepening; `/sdd:spec` Phase 1 v2-verification.md schema beat).
     - **Write trigger** — defer-to-backlog vs drop confirmation prompt. The writing command emits an explicit prompt distinguishing the two options before any write to `docs/backlog.md`. Only an affirmative "defer to backlog" answer produces an entry; "drop" or any non-affirmative answer writes no entry and records the drop decision in the current phase's process-notes file only. The prompt fires every time a planning command surfaces an item the user wants to defer or drop — there is no implicit/silent path that writes to `docs/backlog.md`.
     - **Writers** — enumerated commands: `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`. (Per spec.) Non-writers: every other command. `/sdd:refine` explicitly does not edit `docs/backlog.md`; cross-reference `references/living-documents.md > Exclusions from the Living-Documents Chain`.
     - **Append order and modification rule** — new entries appended in write order to the end of the file; existing entries are not modified, reordered, or removed by subsequent commands.
     - **Bootstrap** — if `docs/backlog.md` does not exist when the first deferral fires, the writing command creates it. The created file begins with a one-line marker comment (HTML-style `<!-- -->`) naming the file's purpose; the first entry follows.
     - Brief parser note: entries are detected by `^### ` heading scan; "has at least one entry" means at least one `^### ` heading exists in the file (the pointer-mechanism reference in Item 2 uses this check).

  2. **Wire the defer-to-backlog confirmation logic into 5 SKILL.md files.** For each of `plugins/sdd/skills/discovery/SKILL.md`, `plugins/sdd/skills/scope/SKILL.md`, `plugins/sdd/skills/prd/SKILL.md`, `plugins/sdd/skills/spec/SKILL.md`, and `plugins/sdd/skills/plan/SKILL.md`:
     - Add `references/backlog.md` to the command's startup load list, alongside the existing references each command loads.
     - At every point in the command flow where the user signals they want to defer or drop an item that won't make it into the current artifact, route the decision through the explicit defer-to-backlog vs drop prompt described in `references/backlog.md > Write trigger`. The SKILL.md describes the routing in one or two sentences and cross-references the reference file for the prompt mechanics, entry format, and write semantics — it does NOT restate the prompt text, entry fields, or the 6-section schema inline.
     - For `/sdd:scope`: the existing project-split write path that lands deferred pieces in `docs/backlog.md` (current `scope/SKILL.md` Steps 195-197 and `### Backlog (if applicable)` section, ~240-242) routes through the same defer-to-backlog prompt now. Reconcile the existing prose: project-split deferred pieces are written via the prompt — not silently. The mechanism (file location, entry format) is unchanged; the user gating is the new addition.
     - For `/sdd:plan`: the new defer-to-backlog prompt fires when items surface during sprint planning that the user wants to push off rather than fit into the current sprint. (Distinct from items that already live in PRD Unvetted or already-deferred backlog entries — those don't re-defer.)
     - For `/sdd:discovery`, `/sdd:prd`, `/sdd:spec`: same pattern, at the natural deferral points in each command's flow (a deferred idea raised mid-interview; an out-of-scope item surfaced during PRD interview; a spec deferral that doesn't belong in `Open Issues`).
     - Each touched SKILL.md states that drops record only to the phase's process-notes file (the appropriate `process-notes-<phase>.md` or `process-notes-sprint-N.md`). No silent file writes.

- **Acceptance criteria:**
  - [x] `aafs` When the user defers an item during `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, or `/sdd:plan`, the command emits an explicit confirmation prompt before writing to `docs/backlog.md`.
  - [x] `aaft` The confirmation prompt distinguishes "defer to backlog" from "drop."
  - [x] `aafu` On user response "defer to backlog" (or equivalent affirmative), the command appends a backlog entry to `docs/backlog.md`.
  - [x] `aafv` On any user response other than the affirmative defer-to-backlog answer, the command writes no entry to `docs/backlog.md`.
  - [x] `aafw` When an item is dropped rather than deferred, the dropping decision is recorded only in the planning command's process-notes file for the current phase.
  - [x] `aafx` Each backlog entry contains the fields Title, What, Why deferred, Trigger to revisit, Dependencies, and Source, in that order.
  - [x] `aafy` The Source field records the command, the session or round, and the thread or topic where the deferral occurred (exact field format defined in `references/backlog.md > Source field format`).
  - [x] `aafz` Each backlog entry is separated from the next by a horizontal rule (`---`) on its own line.
  - [x] `aaga` New entries are appended to `docs/backlog.md` in the order they are written; existing entries are not modified by subsequent commands.
- **Verification:**
  - Confirm `plugins/sdd/skills/sdd-guide/references/backlog.md` exists and contains the sections enumerated under **What to build** Part 1 (Purpose / Entry format / Source field format / Write trigger / Writers / Append order and modification rule / Bootstrap).
  - Confirm the entry-format example in `references/backlog.md` matches `spec.md > Backlog Entry Format` field-for-field (Title, What, Why deferred, Trigger to revisit, Dependencies, Source), uses `### [Item title]` headings, and shows the `---` separator.
  - Confirm `references/backlog.md` defines the defer-vs-drop confirmation prompt as the explicit gate before any write — affirmative-only writes, drops to process-notes only.
  - Confirm each of `discovery/SKILL.md`, `scope/SKILL.md`, `prd/SKILL.md`, `spec/SKILL.md`, `plan/SKILL.md` loads `references/backlog.md` on startup (added to its load list) and routes deferral decisions through the prompt with a cross-reference to the reference file rather than restating it.
  - Confirm `scope/SKILL.md`'s existing project-split backlog write path is reconciled: writes still land in `docs/backlog.md`, but now go through the defer-to-backlog prompt.
  - Confirm none of the five SKILL.md files restate the 6-field entry schema or the prompt text inline.
- **Status:** [x] Complete

---

### Item 2: Planning artifacts emit backlog pointer in lieu of inline deferred-items `[PRD: aagb, aagc, aagd]`

- **PRD ref:** `prd.md > Automatic Backlog Generation` (story 3 — artifacts point at backlog rather than restate) `[PRD: aagb, aagc, aagd]`
- **Spec ref:** `spec.md > Backlog Entry Format` (the "When `docs/backlog.md` has at least one entry, planning artifacts include a pointer to `docs/backlog.md` in lieu of an inline deferred-items section" rule) + the artifact list in `aagd` (`/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`).
- **What to build:** Adjust artifact-generation behavior across five commands and their templates so that when `docs/backlog.md` exists with at least one entry, the generated artifact emits a single-line pointer to `docs/backlog.md` instead of an inline "out of scope" or "deferred items" section. When `docs/backlog.md` does not exist or has no entries, the existing artifact behavior is preserved (no pointer; the existing inline section, if any, behaves as before — but for the v2 cycle most artifact templates don't carry inline deferred-items sections, so the dominant case is: pointer when entries exist, nothing when they don't).

  1. **Update 5 templates** to swap inline deferred-items sections (where present) for a single-line pointer block conditional on backlog state:
     - `plugins/sdd/skills/sdd-guide/templates/scope-template.md` — replace any "What We'd Add With More Time" / inline deferred-items / out-of-scope-deferrals section content with the pointer block. (Section heading retention is template-author's call; the constraint is no inline list when the backlog has entries.)
     - `plugins/sdd/skills/sdd-guide/templates/prd-template.md` — replace any "What We'd Add With More Time" inline list with the pointer block. The existing project-state pattern (current `docs/prd.md` line 374-375 — "Deferred items: see `docs/backlog.md`.") confirms this is the right phrasing.
     - `plugins/sdd/skills/sdd-guide/templates/spec-template.md` — replace any inline deferred-items / out-of-scope-list section with the pointer block.
     - `plugins/sdd/skills/sdd-guide/templates/sprint-template.md` — sprint files do not currently carry an inline deferred-items section; add the pointer block to the **Out of scope** section when `docs/backlog.md` has entries. (Sprint files' own per-sprint "Out of scope" list of next-sprint candidates is sprint-planning content, distinct from project-wide deferred items; the pointer addresses the project-wide list.)
     - `plugins/sdd/skills/sdd-guide/templates/discovery-template.md` — same pattern.
  2. **Update 5 SKILL.md files** to perform the conditional check at artifact-generation time:
     - `discovery/SKILL.md`, `scope/SKILL.md`, `prd/SKILL.md`, `spec/SKILL.md`, `plan/SKILL.md` each add a check at artifact-write time: if `docs/backlog.md` exists and contains at least one `^### ` entry heading (per the parser note in `references/backlog.md`), emit the pointer block. Otherwise, follow the existing artifact-generation path with no pointer.
  3. **Pointer block phrasing.** Use the established convention from the current `docs/prd.md` and `docs/scope.md`:

     ```
     ## [Section heading appropriate to the artifact]

     Deferred items: see `docs/backlog.md`.
     ```

     The section heading varies by artifact (e.g., "What We'd Add With More Time" in PRD; "Out of scope (project-wide)" in scope, prd, spec; sprint files use their existing "Out of scope" section with the pointer appended at the bottom). Exact heading text is template-author's call as long as the pointer line `Deferred items: see` followed by ``docs/backlog.md`` is present verbatim and the pointer occupies one line.

- **Acceptance criteria:**
  - [x] `aagb` When `docs/backlog.md` exists and contains at least one entry at the moment a planning command generates its artifact, the generated artifact includes a pointer to `docs/backlog.md` in lieu of an inline deferred-items section.
  - [x] `aagc` Generated planning artifacts contain no inline "out of scope" or "deferred items" section that restates entries from `docs/backlog.md`.
  - [x] `aagd` This rule applies to artifacts generated by `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, and `/sdd:plan`.
- **Verification:**
  - Confirm each of `scope-template.md`, `prd-template.md`, `spec-template.md`, `sprint-template.md`, `discovery-template.md` contains the pointer-block placeholder (e.g., the literal pointer line) and does not restate inline deferred-items content.
  - Confirm each of `discovery/SKILL.md`, `scope/SKILL.md`, `prd/SKILL.md`, `spec/SKILL.md`, `plan/SKILL.md` performs the backlog-state check at artifact generation: file existence + `^### ` heading count.
  - Confirm the conditional behavior — when `docs/backlog.md` is absent or empty, no pointer is emitted and the artifact's existing structure is preserved.
  - Confirm the pointer line uses the established phrasing (`Deferred items: see \`docs/backlog.md\`.` or close equivalent) and occupies a single line.
- **Status:** [x] Complete

---

## Notes

- **Closes the Automatic Backlog Generation epic in full.** 12 ACs (`aafs`–`aagd`) across 3 stories. Post-Sprint-16 projection: 34 ACs remaining across 2 epics (v1→v2 Migration: 21, v2 Release Verification remaining: 13). Two of the original five v2 epics close with this sprint — the running count of closed epics goes to 4 of 5 (Process Notes Growth closed Sprint 14, `/sdd:polish` re-open + `/sdd:retro at Project Close` closed Sprint 15, Automatic Backlog Generation closes here).
- **PRD checkoff watch.** Per Sprints 11-15 discipline: sprint close must propagate AC checkoffs to `docs/prd.md` immediately. Sprint 16 follows the same pattern.
- **No FB mapping.** None of `aafs`–`aagd` correspond to a row in `docs/v2-verification.md`. The Automatic Backlog Generation epic is PRD-walkthrough-derived (Round 2 v2 spec interview), not v1-feedback-derived. No `[FB: ...]` tags on items; `/sdd:build` wrap-up writes no v2-verification row updates for this sprint.
- **One new reference file, 5 SKILL.md edits in Item 1; 5 template edits and the same 5 SKILL.md edits in Item 2.** Item 1's SKILL.md changes add a startup-load + defer-routing block per file. Item 2's SKILL.md changes add the artifact-generation check per file. The two items touch overlapping SKILL.md surfaces but the edits are disjoint — Item 1 adds startup/runtime behavior, Item 2 adds artifact-write conditional behavior. Sequential dispatch keeps the diffs reviewable per item.
- **Spec already pins every substantive design call.** Entry format, Source field format, separator, append-only invariant, write trigger, pointer mechanism, and the v2-cycle observation that the existing `docs/prd.md` and `docs/scope.md` already use the pointer phrasing — all resolved in `spec.md > Backlog Entry Format` and `spec.md > /sdd:refine` and `references/living-documents.md > Exclusions from the Living-Documents Chain`. No deepening rounds at planning time.
- **Topics deferred to `/sdd:build` context** (in case they surface during implementation):
  - Item 1: exact placement of the defer-prompt routing inside each of the five SKILL.md flows — natural anchor is wherever the command currently surfaces deferrals (e.g., `/sdd:scope`'s project-split path; `/sdd:plan`'s sprint-grouping discussion). Discover at build.
  - Item 1: whether `references/backlog.md` should also document a "first-write bootstrap" template for the `docs/backlog.md` file (one-line HTML comment marker) — included in the spec for this item; verify at build that the comment text doesn't conflict with any existing convention.
  - Item 1: `/sdd:scope`'s existing project-split write path. Discover at build whether the reconciliation reads cleanly with the new prompt routing, or whether a section-level rewrite is cleaner than localized edits.
  - Item 2: exact section heading for the pointer block per template. Spec is silent on the heading text; build picks something coherent with the artifact (PRD uses "What We'd Add With More Time"; scope uses "Out of scope" or similar). Pointer line text is fixed; heading is template-author's call.
  - Item 2: backlog-state check helper. If the 5 SKILL.md files each do the file-exists + heading-count check, consider lifting it to a one-line spec inside `references/backlog.md` for cross-reference rather than five duplicate inline checks. Bias toward referencing.
- **Cached marketplace plugin is still v1.** All sprint-16 edits ship to disk but don't change agent behavior until reinstall. First real exercise of the defer-to-backlog prompt requires running a v2 planning command post-reinstall.
- **Natural next-sprint candidates after Sprint 16:**
  - v2 Release Verification remaining (`aahj`, `aahl`–`aaht`, `aahu`, `aahw`, `aahx`, 13 ACs) — smaller and closer to GA; seeding hook in `/sdd:spec` + dogfood-approach docs.
  - v1→v2 Migration guide (`aago`–`aahi`, 21 ACs) — largest single-file write; best to do near GA when v2 state is final.
