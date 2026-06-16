# Sprint 1

**Theme:** PRD foundation — make `docs/prd.md` internally consistent with v2 spec and AC-ID-tagged so every subsequent sprint can validly use `[PRD: id1, id2]` tags.

**Tangible outcome:** `docs/prd.md` is fully v2-consistent; every AC carries a stable 4-char alphabetic ID; the five foundation open-concerns are resolved; alphanumeric AC-ID slips scrubbed from `docs/spec.md`.

**Out of scope:** plugin filesystem changes (skill renames, new SKILL.md files), v2 command behavior implementation, bug fixes.

## Build Preferences
- **Build mode:** autonomous

## Execution order

Items 1–3 must complete before item 4. Item 4 must complete before item 5. Items 1–3 are independent of each other and may run in any order.

## Checklist

### Item 1: Relocate smallProject detection from /sdd:discovery to /sdd:scope

- **PRD ref:** `prd.md > /sdd:discovery Command` (story to remove from) + `prd.md > Interview Command Behaviors` (epic to add new story under)
- **Spec ref:** `spec.md > PRD Edits Required > Item 1` and `spec.md > Right-Sizing Heuristic`
- **What to build:** Edit `docs/prd.md`. Remove the existing small-project story under the `/sdd:discovery Command` epic (the story currently at PRD lines 42–46: "As a plugin user on a small or simple project, I want `/sdd:discovery` to recognize that signal..."). Add a new story under the `Interview Command Behaviors` epic, scoped to `/sdd:scope`, covering authoritative detection at end of Phase 1.
- **Acceptance criteria:**
  - [x] The `/sdd:discovery Command` epic no longer contains any acceptance criterion referencing small-project detection, `smallProject: true` writing, or detection-during-discovery-interview behavior.
  - [x] A new story exists under the `Interview Command Behaviors` epic, scoped to `/sdd:scope`, with at minimum these ACs: (a) `/sdd:scope` detects small-project signals during Phase 1; (b) `/sdd:scope` writes the authoritative `smallProject` value to `docs/project-state.json` at end of Phase 1; (c) signals are defined in `references/right-sizing.md`.
  - [x] The existing cross-cutting small-project story under `Interview Command Behaviors` (about reading `smallProject` across all interview commands) is left intact and untouched by this edit.
  - [x] New ACs added in this item carry no IDs at the moment of writing (per sprint-1 edits-then-backfill ordering — item 4 assigns IDs).
- **Verification:**
  - Grep `docs/prd.md` for `smallProject` — every match must be under `Interview Command Behaviors`, not under `/sdd:discovery Command`.
  - Visual check: the new /sdd:scope-scoped story appears under `Interview Command Behaviors` and reads coherently alongside the existing reading-focused story.
- **Status:** [x] Complete

---

### Item 2: Add new v2 profile-field seeding story to /sdd:onboard Polish

- **PRD ref:** `prd.md > /sdd:onboard Polish`
- **Spec ref:** `spec.md > PRD Edits Required > Item 2` and `spec.md > /sdd:onboard > User profile schema`
- **What to build:** Edit `docs/prd.md`. Add a new dedicated story under the `/sdd:onboard Polish` epic covering v2's two new profile fields: `defaultSprintMode` and `handoffWarningShown`. Do not modify existing /sdd:onboard Polish stories.
- **Acceptance criteria:**
  - [x] A new story exists under `/sdd:onboard Polish` with framing like: *"As a plugin user, I want /sdd:onboard to seed the new v2 profile fields, so downstream commands have the slots they expect."*
  - [x] The new story's ACs include: (a) `/sdd:onboard` writes `handoffWarningShown: false` to `~/.claude/sdd-user-profile.json`; (b) `/sdd:onboard` writes `defaultSprintMode: null` (or omits the field with semantic equivalence) to `~/.claude/sdd-user-profile.json`.
  - [x] No existing story under `/sdd:onboard Polish` is modified by this item.
  - [x] New ACs carry no IDs at the moment of writing (per item 4 backfill).
- **Verification:**
  - Grep `docs/prd.md` for `defaultSprintMode` and `handoffWarningShown` — each must appear at least once in the new story's ACs.
  - Visual check: the new story is internally coherent and structurally consistent with adjacent /sdd:onboard Polish stories.
- **Status:** [x] Complete

---

### Item 3: Name the cross-project pattern file in the /sdd:retro AC

- **PRD ref:** `prd.md > /sdd:retro at Project Close`
- **Spec ref:** `spec.md > PRD Edits Required > Item 3` and `spec.md > /sdd:retro`
- **What to build:** Edit `docs/prd.md`. In the `/sdd:retro at Project Close` epic, find the AC that currently reads: *"/sdd:retro writes captured patterns to ~/.claude/sdd-user-profile.json or a sibling file in ~/.claude/ (specific file deferred to /sdd:spec)."* Replace it with an AC naming `~/.claude/sdd-cross-project-patterns.md` explicitly as the destination.
- **Acceptance criteria:**
  - [x] The /sdd:retro AC referencing "deferred to /sdd:spec" no longer contains that deferral phrasing.
  - [x] The replaced AC names `~/.claude/sdd-cross-project-patterns.md` literally as the destination file.
  - [x] No other /sdd:retro at Project Close ACs are modified by this item.
  - [x] If the AC needed full rewording (rather than minor edit), the rewritten AC carries no ID at write time (per item 4 backfill).
- **Verification:**
  - Grep `docs/prd.md` for `cross-project-patterns.md` — exactly one match, in the /sdd:retro at Project Close epic.
  - Grep `docs/prd.md` for `deferred to /sdd:spec` — no matches remain in the /sdd:retro AC body (matches in other epics, if any, are unaffected).
- **Status:** [x] Complete

---

### Item 4: Assign 4-char alphabetic AC IDs to every AC in docs/prd.md, and scrub a7kp-style examples from docs/spec.md

- **PRD ref:** `prd.md > Bug Fixes > Story 2` (PRD state-tracking requires AC IDs) — note this is the AC referenced by ID generation; the generator itself is implemented in a later sprint.
- **Spec ref:** `spec.md > PRD Acceptance Criteria IDs` and `spec.md > Open Issues > AC-ID notation slip`
- **What to build:** After items 1–3 complete, run an LLM-driven single-pass rewrite of `docs/prd.md` that assigns a unique 4-char lowercase alphabetic ID (`[a-z]{4}`) to every AC line. Track assigned IDs in working memory during the pass; regenerate on collision. Write the entire file back via one atomic write. Then scrub `docs/spec.md` of alphanumeric AC-ID example slips (specifically `a7kp` at line 549 and `[PRD: a7kp, b3mq]` at line 568, plus any other alphanumeric AC-ID-like strings) by replacing them with valid alphabetic examples.
- **Acceptance criteria:**
  - [x] Every AC line in `docs/prd.md` matches the pattern `^- \[ \] \`[a-z]{4}\` ` (markdown bullet, space, unchecked checkbox, space, backticked 4-char lowercase alphabetic ID, space, AC text).
  - [x] No two ACs in `docs/prd.md` share the same ID.
  - [x] AC line ordering and AC text content (other than the prepended ID) is preserved from before the backfill — no ACs added, removed, or reworded by this item.
  - [x] `docs/spec.md` contains no occurrences of `a7kp` or `b3mq` outside of the inline correction note at the existing slip location (line 549). The correction note itself may be edited or removed if the alphanumeric examples no longer exist in the surrounding text.
  - [x] Any alphanumeric strings in `docs/spec.md` that look like AC-ID examples (4-char strings mixing letters and digits in backticks, in AC-ID context) are replaced with valid `[a-z]{4}` examples or removed.
- **Verification:**
  - Regex scan `docs/prd.md`: every line matching `^- \[ \]` must also match `^- \[ \] \`[a-z]{4}\` `.
  - Uniqueness check: extract every `\`[a-z]{4}\`` token from `docs/prd.md` and verify the count of unique tokens equals the count of total tokens.
  - Regex scan `docs/spec.md` for `\`[a-z0-9]{4}\`` patterns where any character is a digit — only acceptable matches are inside explicit historical or correction-note context.
- **Status:** [x] Complete

---

### Item 5: Reconcile docs/open-concerns.md — move five resolved concerns to Resolved

- **PRD ref:** [unmapped] (this item is sprint-housekeeping; it resolves cross-cutting concerns tracked outside PRD ACs)
- **Spec ref:** `spec.md > PRD Edits Required` (resolution authority for items 1–3 + 5) + `spec.md > Open Issues > AC-ID notation slip` (resolution authority for item 4)
- **What to build:** Edit `docs/open-concerns.md`. Move the following five concerns from `## Open` to `## Resolved`, each with a `Resolved by: /sdd:sprint (sprint-1)` line and a one-line resolution note pointing at the specific sprint-1 item.
  - "One-time PRD AC ID backfill for this project" — resolved by item 4.
  - "PRD edit for smallProject detection relocation" — resolved by item 1.
  - "New profile fields to add to /sdd:onboard schema" — resolved by item 2.
  - "Cross-project pattern file name in PRD" — resolved by item 3.
  - "AC-ID notation form" — resolved by items 4 (alphabetic adoption) and 4's spec.md scrub.
  Leave "Soft acceptance criteria for MIT distribution" in `## Open` (targets /sdd:refine).
- **Acceptance criteria:**
  - [x] The five named concerns appear under `## Resolved` in `docs/open-concerns.md`, each with a `Resolved by: /sdd:sprint (sprint-1)` line and a one-line resolution note naming the responsible sprint-1 item.
  - [x] The five named concerns no longer appear under `## Open`.
  - [x] "Soft acceptance criteria for MIT distribution" remains under `## Open` unchanged.
  - [x] `## Deferred` section is not modified by this item.
- **Verification:**
  - Grep `docs/open-concerns.md` Open section: it should contain only "Soft acceptance criteria for MIT distribution."
  - Grep Resolved section: each of the five moved titles appears exactly once.
  - Total entry count is preserved (six entries total across Open + Resolved before; same six after, with five moved).
- **Status:** [x] Complete

---

## Notes

- This sprint is foundation-only. No plugin filesystem touched; no SKILL.md files modified; no new commands implemented.
- Items 1–3 may produce intermediate states where new ACs exist without IDs. This is expected per the edits-then-backfill ordering chosen in planning. Item 4 brings the PRD to its final v2-consistent state.
- Sprint 2 onward can rely on `docs/prd.md` having stable IDs and being internally consistent with `docs/spec.md > PRD Edits Required`.
