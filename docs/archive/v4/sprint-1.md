# Sprint 1

The whole v4 cycle in one sprint: build the new `/sdd:archive` command end-to-end. Items are ordered by the spec's dependency chain — schema/guide groundwork first, then the new `skills/archive/SKILL.md` authored section-by-section (pre-flight → plan gate → execute → reset → git), then manifest registration, then verification. `/sdd:archive` is a single new SKILL.md, so items 2–6 author different sections of that one file, each mapped to its own PRD story. End state: a working `/sdd:archive` that passes `claude plugin validate .` and is exercised AC-by-AC on a disposable branch.

Out of scope: adopting Caveman's executable machinery and the deferred share-back/auto-update items — see `docs/backlog.md`.

## Build Preferences
- **Build mode:** autonomous

## Checklist

### Item 1 — Schema + guide groundwork
- [x] Document `cycleNumber` field + reset map in `references/living-documents.md`; add `/sdd:archive` to the Out-of-pattern commands list in `sdd-guide/SKILL.md` [unmapped]
- **PRD ref:** `prd.md > Directory Reset`
- **Spec ref:** `spec.md > Version Resolution`, `spec.md > project-state.json Reset`, `spec.md > Handoff`
- **What to build:** In `plugins/sdd/skills/sdd-guide/references/living-documents.md`, document the new top-level integer `cycleNumber` field in the `project-state.json` schema and the full reset map (which fields carry, which reset, and to what). In `plugins/sdd/skills/sdd-guide/SKILL.md`, add `/sdd:archive` to the "Out-of-pattern commands" list so the guide stays the single source of truth for handoff behavior. Enabling documentation for the mapped items below; no directly testable product AC of its own.
- **Acceptance criteria:**
  - [x] `references/living-documents.md` documents the `cycleNumber` field (top-level integer, sole owner `/sdd:archive`, absent → assume 1) and the reset map exactly as specced.
  - [x] `sdd-guide/SKILL.md` Out-of-pattern commands list includes `/sdd:archive` with its one-line outcome + two-line handoff to `/sdd:scope`.
- **Verification:** Read both files; confirm the `cycleNumber` field and reset map are documented in `living-documents.md` matching `spec.md > project-state.json Reset`, and that `/sdd:archive` appears in the Out-of-pattern list with the `/sdd:scope` next-step.

---

### Item 2 — Skill scaffold + pre-flight + version resolution
- [x] Create `skills/archive/SKILL.md` (frontmatter, loading, first-run blurb); pre-flight state capture, target-`N` resolution with collision-advance, nothing-to-archive exit [PRD: rcsc, aswc, aswd]
- **PRD ref:** `prd.md > Artifact Sweep`, `prd.md > Run Control & Safety`
- **Spec ref:** `spec.md > Archive Command Skill`, `spec.md > Version Resolution`, `spec.md > Artifact Sweep`
- **What to build:** Create `plugins/sdd/skills/archive/SKILL.md` with `name: archive`, a description, and `disable-model-invocation: true`. Load `sdd-guide/SKILL.md` only. Implement the first-run blurb gated on `commandExplanationsShown.archive` (once-per-project, not once-per-cycle). Pre-flight: validate `project-state.json` parses; capture `cycleNumber` (absent → 1), `lastCommand`, `currentSprint`; resolve target `N` starting at `docs/archive/v{cycleNumber}/` and advancing past any existing `v{N}`; compute the allow-list sweep set; if empty, report nothing-to-archive and exit with zero changes.
- **Acceptance criteria:**
  - [x] `rcsc` When invoked with no cycle artifacts present (only the live carry-forward trio in `docs/` and no root-level `process-notes-*.md`), the command reports that there is nothing to archive and makes no changes — no `v{N}/` directory is created, no branch, no PR.
  - [x] `aswc` The target version `N` is read from the cycle-number field in `project-state.json`.
  - [x] `aswd` When `docs/archive/v{N}/` already exists at the version read from `project-state.json`, the command advances to the next unused `v{N}` and uses that directory for the archive.
- **Verification:** Run `claude plugin validate .` (skill registers, frontmatter well-formed). On a disposable copy with only the trio present, confirm nothing-to-archive exits with zero changes. With an existing `docs/archive/v{N}/`, confirm target advances to the next unused version.

---

### Item 3 — Plan gate + confirmation
- [x] Plan gate: will-archive (grouped + counts) / left-in-place lists, target `v{N}`, branch, PR intent; decline = zero changes [PRD: rcsa, rcsb]
- **PRD ref:** `prd.md > Run Control & Safety`
- **Spec ref:** `spec.md > Plan Gate & Confirmation`
- **What to build:** Before any file move or git operation, display a single plan: will-archive list (grouped with counts, not exhaustive per-filename), left-in-place list (anything in `docs/` outside the allow-list, the trio, and `docs/archive/`), target `docs/archive/v{N}/` (noting any advance), living-doc snapshots + `INDEX.md` to be written, the `project-state.json` reset summary, and (git repo only) branch `archive-v{N}` + PR intent. Wait for one explicit confirmation; no separate PR-open confirmation. On decline: no file changes, no git changes.
- **Acceptance criteria:**
  - [x] `rcsa` Before moving any file or running any git operation, the command displays a plan that lists the files to be moved, the target archive directory `v{N}`, the branch name it will create, and the PR it will open, then waits for explicit user confirmation.
  - [x] `rcsb` When the user declines at the confirmation gate, the command makes no file changes and no git changes — no moves, no branch, no commit, no PR.
- **Verification:** On a disposable copy, invoke and confirm the plan lists will-archive (grouped + counts), left-in-place, target, branch, and PR intent. Decline at the gate; confirm zero file and git changes.

---

### Item 4 — Execute: sweep + living-doc snapshots + INDEX.md
- [x] Create `v{N}/`; byte-identical trio snapshots (pre-reset); allow-list moves; generate `INDEX.md` [PRD: aswa, aswb, aswe, ldpa, ldpb, ldpc, aixa, aixb, aixc]
- **PRD ref:** `prd.md > Artifact Sweep`, `prd.md > Living-Doc Preservation`, `prd.md > Archive Index`
- **Spec ref:** `spec.md > Artifact Sweep`, `spec.md > Living-Doc Snapshots`, `spec.md > INDEX.md Generation`
- **What to build:** After confirmation: create `docs/archive/v{N}/`; snapshot the three living docs (`backlog.md`, `sdd-feedback.md`, `project-state.json`) byte-identical into `v{N}/` **before** the reset; move the allow-list sweep set into `v{N}/` (exact names `scope.md`, `prd.md`, `spec.md`, `discovery.md`, `retro.md`, `open-concerns.md`; globs `sprint-*.md`, `*-resume.md` in `docs/`; root `process-notes-*.md`); generate `v{N}/INDEX.md` from the files actually swept (grouped Contents, `lastCommand` in the `project-state.json` bullet and summary, factual `## Cycle summary` with `Archived: <date>`, sprints, artifact count). The trio stays live in `docs/`.
- **Acceptance criteria:**
  - [x] `aswa` Running `/sdd:archive` moves every item in `docs/` — except the live carry-forward trio (`backlog.md`, `sdd-feedback.md`, `project-state.json`) — into `docs/archive/v{N}/`. *(See open concern: spec settled on an allow-list, not a deny-list; reconcile in `/sdd:refine`.)*
  - [x] `aswb` All root-level `process-notes-*.md` files are moved into `docs/archive/v{N}/`.
  - [x] `aswe` After the sweep completes, the only files remaining in `docs/` (outside `docs/archive/`) are the three live carry-forward files.
  - [x] `ldpa` After the command completes, `backlog.md`, `sdd-feedback.md`, and `project-state.json` are all still present in `docs/`.
  - [x] `ldpb` A snapshot copy of each of the three living docs is written into `docs/archive/v{N}/`.
  - [x] `ldpc` Each snapshot copy in `docs/archive/v{N}/` is byte-identical to its live `docs/` counterpart as it stood at archive time (before the reset is applied to the live `project-state.json`).
  - [x] `aixa` `docs/archive/v{N}/` contains an `INDEX.md` file after the command completes.
  - [x] `aixb` `INDEX.md` is generated automatically; the command does not prompt the user for a hand-authored cycle description.
  - [x] `aixc` `INDEX.md` names the cycle number `N`, lists the artifacts that were swept into the archive, and records the `lastCommand` value read from `project-state.json` at archive time.
- **Verification:** On a disposable copy, run through confirmation. Confirm: allow-list artifacts moved into `v{N}/`; root `process-notes-*.md` moved; only the trio left in `docs/`; trio also present as snapshots in `v{N}/`; `diff` each snapshot against its live counterpart shows byte-identical (project-state.json snapshot taken pre-reset); `INDEX.md` present, auto-generated, names `N`, lists swept artifacts, records `lastCommand`.

---

### Item 5 — project-state.json reset
- [x] Apply the reset map to live `project-state.json` (post-snapshot); leave valid parseable JSON [PRD: drsa, drsb, drsc]
- **PRD ref:** `prd.md > Directory Reset`
- **Spec ref:** `spec.md > project-state.json Reset`
- **What to build:** After the snapshot, rewrite the live `docs/project-state.json` per the reset map: `version` carry; `cycleNumber` → `N+1`; `currentSprint` → reset (see note); `buildMode` carry; `smallProject` → `null`; `lastCommand` → `"archive"`; `commandExplanationsShown` normalized (drop stale `sprint`/`iterate`/`reflect`, add `archive`, flip all to `false` except `archive` which is preserved); `notes` reseeded to a fresh line naming the new cycle and where the prior cycle archived. **Note (open concern):** the spec reset map says `currentSprint → 1`, but with `/sdd:plan`'s `currentSprint + 1` rule that makes a new cycle's first sprint number `2`. Reset `currentSprint → 0` (or omit) so the next cycle's first sprint is `1`. Reconcile the spec/reference wording as part of this item.
- **Acceptance criteria:**
  - [x] `drsa` After the command completes, running `/sdd:scope` begins a new cycle with no carryover of the prior cycle's sprint state or per-command explanation flags (the new cycle re-judges `smallProject` from scratch and re-shows first-run command explanations).
  - [x] `drsb` After the command completes, the cycle-number field in `project-state.json` is set so that the next `/sdd:scope`/`/sdd:archive` cycle targets a fresh version, not the version just archived.
  - [x] `drsc` The live `project-state.json` remains a valid, parseable JSON file after the reset.
- **Verification:** On a disposable copy, after a run: parse `project-state.json` (valid JSON); confirm `cycleNumber == N+1`, `currentSprint` reset so the next first sprint is `1`, `smallProject == null`, `commandExplanationsShown` normalized (no `sprint`/`iterate`/`reflect`; `archive` present; all others `false`), `notes` reseeded. Simulate a fresh `/sdd:scope` startup and confirm no prior-cycle carryover.

---

### Item 6 — Git integration
- [x] Repo detect; branch → commit (tight staging) → push → PR; branch-collision suffix; pre-flight dirty-tree warning; per-step failure handling [PRD: gita, gitb, gitc]
- **PRD ref:** `prd.md > Git Integration`
- **Spec ref:** `spec.md > Git Integration`
- **What to build:** Detect repo via `git rev-parse --is-inside-work-tree`. In a repo, run branch → commit → push → PR (`gh pr create`); branch name `archive-v{N}`, auto-suffix (`-2`, …) + notify on collision. Tight staging — stage only the archive's own paths (new `v{N}/` tree, moved-file deletions, reset `project-state.json`), never `git add -A`. Pre-flight: if the tree has unrelated uncommitted changes, warn (informational, non-blocking). Not a repo → file ops only. On any git step failing after the file archive completed, report the specific failing step and resulting state, leave the completed file archive (and any successful git work) in place, and prompt the user how to proceed — no rollback.
- **Acceptance criteria:**
  - [x] `gita` When the working directory is inside a git repository, the command creates a branch, commits the archive changes, pushes the branch, and opens a PR for the archive commit.
  - [x] `gitb` When the working directory is not a git repository, the command performs the file operations only and creates no branch, commit, or PR.
  - [x] `gitc` When the file archive has completed but a git step fails (for example `gh` is unavailable, no remote is configured, the push is rejected, or the branch already exists), the command reports the specific failure and prompts the user to confirm how to proceed; the completed file archive is left in place rather than rolled back or silently abandoned.
- **Verification:** On a disposable git branch, run the full path: confirm branch `archive-v{N}` created, commit stages only archive paths, push + PR opened. In a non-repo temp dir, confirm file ops only (no branch/commit/PR). Force a git failure (e.g. unset remote) and confirm the specific step is reported, the file archive is left in place, and the user is prompted.

---

### Item 7 — Register command + version bump
- [x] Register `/sdd:archive` in `plugin.json`; bump `plugin.json` + `marketplace.json` versions together [unmapped]
- **PRD ref:** `prd.md > What We're Building`
- **Spec ref:** `spec.md > Runtime & Deployment`, `spec.md > File Structure`
- **What to build:** Register the new `archive` command in `plugins/sdd/.claude-plugin/plugin.json` and bump its version; move `.claude-plugin/marketplace.json` to the same version (they move together). Enabling/release plumbing — no product AC.
- **Acceptance criteria:**
  - [x] `plugin.json` registers the `archive` command (auto-discovered from `skills/archive/SKILL.md`; plugin.json carries no commands array, per v1–v3 convention) and carries the bumped version `3.1.0`.
  - [x] `marketplace.json` version matches `plugin.json` — version is single-sourced in `plugin.json` (marketplace references it via `source`); no separate version field, as in all prior releases. *(AC/CLAUDE.md "move together" wording assumes a marketplace version field that has never existed; flag for `/sdd:refine`.)*
- **Verification:** `claude plugin validate .` passes with the command registered; confirm both manifest versions match.

---

### Item 8 — Verification (structural + AC-by-AC on disposable branch)
- [x] `claude plugin validate .`; AC-by-AC manual pass; destructive/git ACs exercised on a disposable copy/branch then discarded [unmapped]
- **PRD ref:** all stories
- **Spec ref:** `spec.md > Testing Strategy`
- **What to build:** Run `claude plugin validate .` (exit 0). Walk every PRD AC observably: confirmation gate, decline (zero changes), nothing-to-archive, allow-list sweep, left-in-place reporting, living-doc preservation + byte-identical snapshots, `INDEX.md` contents, the reset map, and the git path. Exercise the destructive/git-integrated ACs in a throwaway copy or disposable git branch so moves/branch/PR can be driven and discarded without disturbing live state. Do **not** run the real `/sdd:archive` on this project until the disposable walkthrough passes.
- **Acceptance criteria:**
  - [x] `claude plugin validate .` exits successfully with the new skill registered.
  - [x] Every PRD AC (`rcsa`–`gitc`) has a recorded observable verification result from the disposable-branch walkthrough.
  - [x] The safety guarantees (confirmation gate, decline/nothing-to-archive zero-change) are confirmed before any real-data run.
- **Verification:** A recorded pass exists naming each AC and its observed result; `claude plugin validate .` exit 0 captured; destructive checks confirmed run on a disposable copy/branch.

---

## Out of Scope

Deferred items: see `docs/backlog.md`.
