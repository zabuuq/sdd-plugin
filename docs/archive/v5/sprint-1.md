# Sprint 1

The whole v5 cycle in one sprint: extend `/sdd:archive` to give the maintainer per-file control over `docs/refs/` at archive time. All behavior change lands in one file — `plugins/sdd/skills/archive/SKILL.md` — so the items author different parts of that one edit, ordered by the spec's pipeline seams: the read-only detection/listing/parse half first, then the mutation half behind the single confirmation, then the inert-path guarantee, then the manifest bump. The one load-bearing ordering rule (move selected refs *before* INDEX generation) lives in item 2. End state: `/sdd:archive` with full refs selection, passing `claude plugin validate .` and exercised on a disposable-branch fixture, ready for the real end-of-v5 dogfood run.

Out of scope: the deferred Caveman-machinery, share-back, and auto-update items — see `docs/backlog.md`.

## Build Preferences
- **Build mode:** autonomous

## Checklist

### Item 1 — Refs detection, numbered listing & selection parsing
- [x] Pre-flight recursive enumeration of `docs/refs/` (flattened relative paths; dotfiles/hidden excluded, symlinks count, empty subdirs contribute nothing) + plan-gate Step A: show numbered list, wait for reply, parse selection [PRD: rfsa, rfsb, rfsc, rfsd]
- **PRD ref:** `prd.md > Refs Selection`
- **Spec ref:** `spec.md > Refs Detection & Listing`, `spec.md > Selection & Reply Parsing`
- **What to build:** In `plugins/sdd/skills/archive/SKILL.md`, add a pre-flight (read-only) computation that walks all of `docs/refs/` to any depth and produces a flattened candidate list, each entry the path relative to `docs/refs/`. Exclude any entry whose name begins with `.` (a `docs/refs/` holding only `.gitkeep` counts as empty); count symlinks as ordinary files; empty subdirectories contribute no entry. At plan-gate Step A (only when the sweep set is non-empty **and** the candidate list is non-empty): display a simple numbered list (one line per candidate, relative path), wait for the user's reply, and parse the selection. Accepted reply forms: comma-separated numbers (`1,3,5`), inclusive ranges (`2-4`), keywords `all` / `none`, and combinations (`1,3-5,8`); `all` selects every candidate (other tokens ignored); `none` and an empty reply select nothing; invalid/out-of-range tokens are silently dropped and valid selections honored (safe — the resolved set still passes the final confirmation). No file under `docs/refs/` is moved during this step.
- **Acceptance criteria:** (from `prd.md > Refs Selection`)
  - [x] `rfsa` During a real archive run (the normal sweep set is non-empty) in which `docs/refs/` contains at least one file, the command outputs a numbered list before performing any refs move.
  - [x] `rfsb` The numbered list contains exactly one entry per file found anywhere under `docs/refs/`, including files inside nested subdirectories — the listing is fully flattened, recursing to any depth.
  - [x] `rfsc` Each numbered entry displays the file's path relative to `docs/refs/`, so two files with the same name in different subdirectories appear as distinct entries.
  - [x] `rfsd` The command waits for the user's selection reply and moves no file under `docs/refs/` until that reply is received.
- **Verification:** `claude plugin validate .` (frontmatter still well-formed). On a disposable copy with a populated `docs/refs/` fixture (nested subdirs, a same-named file in two subdirs, a `.gitkeep`, a symlink): confirm the numbered list appears before any move, is fully flattened/recursive, shows relative paths (same-named files distinct), excludes the `.gitkeep`, and that no file moves until a reply is given. Drive each reply form (`1,3`, `2-4`, `all`, `none`, empty, an out-of-range token) and confirm each resolves to the expected selected set.

---

### Item 2 — Plan-gate fold-in, move execution, INDEX enumeration & git staging
- [x] Fold resolved selection into the plan ("Will archive (refs)" group; `docs/refs/` excluded from "Left in place"); single confirmation; move (not copy) selected refs to `docs/archive/v{N}/refs/<relpath>` after the sweep and before INDEX generation; emit INDEX `reference files:` group + count; stage `docs/refs/` deletions explicitly by path [PRD: rfse, rfsf, rfsg, rfsh, rfsi]
- **PRD ref:** `prd.md > Refs Selection`
- **Spec ref:** `spec.md > Plan Gate Integration`, `spec.md > Refs Move Execution`, `spec.md > INDEX.md Refs Enumeration`, `spec.md > Git Staging of Refs Moves`
- **What to build:** At plan-gate Step B, fold the resolved selection into the displayed plan as a `Will archive (refs):` group, and remove `docs/refs/` from the generic "Left in place" computation in all cases (prevents double-surfacing, satisfies `rfsi`). The single existing confirmation approves the whole run; the Step A reply is plan-building input, not a go/no-go. On decline: no file changes, no git changes; the Step A selection is discarded. In Execute, after the allow-list sweep and **before** `INDEX.md` generation, move (not copy) each selected ref from `docs/refs/<relpath>` to `docs/archive/v{N}/refs/<relpath>`, creating intermediate subdirs as needed, so the selected file no longer exists at its original location. Unselected files are left untouched — not copied, deleted, renamed, or reported; a leftover empty `docs/refs/` directory is left in place (the command does not remove the directory itself). When at least one ref was swept, add a `reference files:` group to `INDEX.md` Contents — one bullet per swept ref by flattened relative path under `refs/`, placed after the process-notes group and before the `project-state.json` snapshot bullet — and count the swept refs toward the cycle-summary `Artifacts archived: <count>` total (no separate count). Extend the existing tight staging to stage the new `docs/archive/v{N}/refs/...` adds (covered by staging the `v{N}/` tree) and the `docs/refs/...` deletions **explicitly by path**; never `git add -A`; unselected refs are never staged.
- **Acceptance criteria:** (from `prd.md > Refs Selection`)
  - [x] `rfse` Every file the user selects is moved — not copied — out of `docs/refs/` into `docs/archive/v{N}/refs/`.
  - [x] `rfsf` A selected file at `docs/refs/<relpath>` is written to `docs/archive/v{N}/refs/<relpath>`, preserving its relative subdirectory path under `refs/`.
  - [x] `rfsg` After the move completes, each selected file no longer exists at its original `docs/refs/` location.
  - [x] `rfsh` Every file the user does not select remains at its original path under `docs/refs/` after the command completes.
  - [x] `rfsi` Unselected files receive no other action — they are not copied to the archive, deleted, renamed, or reported beyond their absence from the swept set.
- **Verification:** On a disposable git branch with the populated fixture: confirm the plan shows a `Will archive (refs)` group matching the selection and that `docs/refs/` never appears under "Left in place"; decline once and confirm zero file/git changes; confirm on the real run that selected refs land at `docs/archive/v{N}/refs/<relpath>` (relpath preserved, intermediate dirs created), are gone from `docs/refs/`, and that a same-named file in a second subdir is placed correctly. Confirm unselected files are byte-for-byte untouched and unreported. Confirm `INDEX.md` gains a `reference files:` group (flattened relative paths, placed after process notes, before the snapshot bullet) and that swept refs are included in `Artifacts archived`. Inspect the commit: only archive paths + the explicit `docs/refs/` deletions are staged; unselected refs are not in the commit.

---

### Item 3 — Behavior preservation / inert paths
- [x] Guarantee refs handling is fully inert when it does not engage: absent refs, empty/dotfile-only refs, and empty-sweep-set-wins-over-populated-refs each produce no list, no prompt, no refs process note, no INDEX reference-files group; every other archive surface stays byte-for-byte unchanged [PRD: bpra, bprb, bprc, bprd, bpre]
- **PRD ref:** `prd.md > Behavior Preservation`
- **Spec ref:** `spec.md > Behavior Preservation & Inert Path`, `spec.md > Plan Gate Integration` (ordering vs. nothing-to-archive)
- **What to build:** Ensure the refs path engages **only** when the sweep set is non-empty and at least one countable (non-hidden) ref exists. When `docs/refs/` is absent, or present but holds zero countable files (empty, only empty subdirs, or only dotfiles), produce no numbered list, no selection prompt, and no refs-related process note — and emit no `reference files:` group in `INDEX.md` and no refs in the count. The existing nothing-to-archive exit (empty sweep set) short-circuits **before** plan-gate Step A, so a populated `docs/refs/` on its own never triggers a prompt or a move. In every inert case, the sweep allow-list, living-doc trio snapshot, `INDEX.md` generation, `project-state.json` reset, and git steps each produce exactly their pre-feature result. A refs-related process note is written **only** when the feature actually engages (refs listed and a selection taken).
- **Acceptance criteria:** (from `prd.md > Behavior Preservation`)
  - [x] `bpra` When `docs/refs/` is absent, the command produces no numbered list, no selection prompt, and no refs-related process note.
  - [x] `bprb` When `docs/refs/` exists but contains zero files (empty, or only empty subdirectories), the command produces no numbered list, no selection prompt, and no refs-related process note.
  - [x] `bprc` When `docs/refs/` is empty or absent, the existing sweep allow-list, living-doc trio snapshot, `INDEX.md` generation, `project-state.json` reset, and git steps each produce the same result they produced before this feature.
  - [x] `bprd` When the normal sweep set is empty, the command takes its existing "nothing to archive" exit even if `docs/refs/` contains one or more files.
  - [x] `bpre` In the empty-sweep-set case, no numbered list is shown and no file under `docs/refs/` is moved.
- **Verification:** On a disposable copy, run four inert scenarios and confirm zero refs-related output and that every non-refs surface is byte-for-byte what it was pre-feature: (1) `docs/refs/` absent; (2) `docs/refs/` empty / only empty subdirs; (3) `docs/refs/` holding only a `.gitkeep`; (4) empty sweep set but `docs/refs/` populated — confirm the nothing-to-archive exit wins, no list shown, no ref moved. Confirm no refs process note is written in any inert case.

---

### Item 4 — Version bump
- [x] Bump `plugins/sdd/.claude-plugin/plugin.json` version; move `.claude-plugin/marketplace.json` to match (they move together) [unmapped]
- **PRD ref:** `prd.md > What We're Building`
- **Spec ref:** `spec.md > Runtime & Deployment`, `spec.md > File Structure`
- **What to build:** Bump the version in `plugins/sdd/.claude-plugin/plugin.json` for the v5 release and move `.claude-plugin/marketplace.json` to the same version (per the v4 release, version is single-sourced in `plugin.json`; the marketplace references it via `source` with no separate version field). Release plumbing only — no product AC. Exact target version chosen at release time.
- **Acceptance criteria:**
  - [x] `plugin.json` carries the bumped v5 version (3.2.0).
  - [x] `marketplace.json` is consistent with `plugin.json` (single-sourced via `source`, as in all prior releases).
- **Verification:** `claude plugin validate .` passes; confirm the manifest version reflects the v5 bump.

---

## Out of Scope

Deferred items: see `docs/backlog.md`.
