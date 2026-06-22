# SDD v5 — `/sdd:archive` Refs Selection — Technical Specification

## Posture

This spec is a **command-extension design doc**. It defines *what* the refs-selection feature adds to the existing `/sdd:archive` command, where the new behavior slots into that command's existing execution pipeline, and the behavioral contract for each edge — but leaves the exact `SKILL.md` prose to `/sdd:plan` (sequencing) and `/sdd:build` (execution). It points at the existing `plugins/sdd/skills/archive/SKILL.md` sections it modifies rather than restating their unchanged content.

The feature is **strictly additive and self-contained to one command**. No `project-state.json` schema change, no new reference file, no new skill. The "how" detail (refs detection, reply parsing) is specific to `/sdd:archive` — not a cross-cutting mechanism — so it lives in the archive `SKILL.md` itself, not in `references/`. The markdown-only, zero-runtime posture is preserved: no hook, no script, no MCP.

## Stack

Unchanged from v4.

| Layer | Choice | Reference Docs |
|-------|--------|----------------|
| Platform | Claude Code plugin (skills-based) | https://code.claude.com/docs/en/plugins |
| Language | Markdown (SKILL.md + YAML frontmatter) | https://code.claude.com/docs/en/skills |
| Runtime code | None — behavior is markdown instructions | — |
| Distribution | GitHub marketplace repo | https://code.claude.com/docs/en/plugin-marketplaces |
| Validation | `claude plugin validate .` | https://code.claude.com/docs/en/plugins-reference |
| Git/PR mechanism | `git` + `gh pr create` (invoked by the agent, not bundled) | https://cli.github.com/manual/gh_pr_create |

No build step, no runtime dependencies, no compiled code.

## Runtime & Deployment

Unchanged from v4. Claude Code CLI/desktop with plugin support; GitHub marketplace distribution. Shipping the feature is a version bump: `plugins/sdd/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` move together (exact bump TBD at release, not in this spec).

## Architecture Overview

The feature adds refs handling at four points in `/sdd:archive`'s existing ordered pipeline. There is no new component and no new runtime — refs detection is a pre-flight computation, selection is a plan-gate sub-step, the move is an execute step, and enumeration extends `INDEX.md` and the git staging set.

The single hard control-flow rule: the **two-step plan gate**. The refs list is shown and the selection collected *before* the plan is displayed; the resolved selection then folds into the plan as a "Will archive (refs)" group, and the existing **single** confirmation approves the complete plan. The selection reply is a plan-*building* input, not a go/no-go confirmation — the one-confirmation-covers-the-whole-run contract is preserved literally.

```
  /sdd:archive invoked
        │
        ▼
  ┌──────────────────────────────────────────────────────────────────┐
  │ PRE-FLIGHT (no changes)                                           │
  │  validate / capture state / resolve N / detect git               │
  │  compute sweep set (ALLOW-LIST)                                   │
  │  sweep set empty? ──► NOTHING-TO-ARCHIVE exit, zero changes ◄─────┼─ wins even
  │                                                                   │  if refs present
  │  + NEW: enumerate refs candidates under docs/refs/ (recursive,    │
  │         flattened relative paths, dotfiles/hidden excluded)       │
  └──────────────────────────────────────────────────────────────────┘
        │ sweep set non-empty
        ▼
  ┌──────────────────────────────────────────────────────────────────┐
  │ PLAN GATE (two-step, ONE confirmation)                           │
  │  + NEW step A: refs candidates non-empty?                         │
  │       ──► show numbered list (relative paths) → collect reply     │
  │           → parse selection (resolve selected set)               │
  │  step B: display full plan                                        │
  │       Will archive: <sweep set, grouped+counts>                  │
  │       Will archive (refs): <selected refs>   ◄── NEW group       │
  │       Left in place: <set, EXCLUDING docs/refs/>  ◄── NEW excl.  │
  │       target / snapshots / reset / branch+PR                     │
  │  ONE confirmation → decline = zero changes, exit                 │
  └──────────────────────────────────────────────────────────────────┘
        │ confirm
        ▼
  ┌──────────────────────────────────────────────────────────────────┐
  │ EXECUTE                                                          │
  │  create docs/archive/v{N}/ → snapshot trio → move sweep set      │
  │  + NEW: move SELECTED refs → docs/archive/v{N}/refs/<relpath>    │
  │         (before INDEX generation; unselected refs untouched)     │
  │  generate v{N}/INDEX.md  (+ NEW reference-files group; refs       │
  │         counted in "Artifacts archived")                         │
  │  reset live project-state.json                                   │
  │  git (if repo): branch → commit (tight staging + NEW refs paths) │
  │         → push → PR                                              │
  └──────────────────────────────────────────────────────────────────┘
        │
        ▼
  one-line outcome + handoff (Run /clear, then /sdd:scope)  ── unchanged
```

### Data Flow — the refs operation

The new data entity is the **selected ref subset** branching from `docs/refs/` into the frozen archive, while the unselected subset stays live and untouched.

```
 docs/refs/ (working)                    docs/archive/v{N}/refs/ (frozen)
 ────────────────────                    ───────────────────────────────
 foo.md            ──┐ SELECTED ──► move ──► refs/foo.md
 sub/bar.md        ──┘ (1, 3…)     ──► move ──► refs/sub/bar.md   (relpath preserved)

 baz.md            ──┐ UNSELECTED ─► (no action) ─► stays at docs/refs/baz.md
 sub/.gitkeep      ──┘ / hidden    ─► (never listed, never moved)
                                              │
                                              └─► enumerated in INDEX.md
                                                   (refs/foo.md, refs/sub/bar.md)
                                                   + counted in "Artifacts archived"
```

**Edge path — sweep set empty but `docs/refs/` populated:** the nothing-to-archive exit wins. No numbered list is shown, no ref is moved, zero changes (`bprd`/`bpre`). A populated `docs/refs/` on its own is never "something to archive."

**Edge path — `docs/refs/` empty or absent:** the feature is fully inert. No list, no prompt, no process note, no `INDEX.md` reference-files group — every other archive surface is byte-for-byte what it was pre-feature (`bpra`/`bprb`/`bprc`).

## Refs Detection & Listing

Implements `prd.md > Refs Selection` (detect-and-list story; ACs `rfsa`–`rfsd`).

### Placement: pre-flight enumeration, plan-gate display

Detection is a **pre-flight computation** — it reads and computes only, moving nothing, consistent with the existing pre-flight contract. It produces the flattened candidate list. The list is *displayed* and the selection *collected* later, at the plan gate (see Plan Gate Integration). Splitting it this way keeps all mutation behind the single confirmation.

### Enumeration rules

- **Recurse to any depth.** Walk all of `docs/refs/`, flattening every nested file into one list (`rfsb`).
- **One entry per file**, displayed as the path **relative to `docs/refs/`** (`rfsc`) — so `docs/refs/sub/bar.md` lists as `sub/bar.md`, and two same-named files in different subdirectories are distinct entries.
- **Dotfiles / hidden files excluded.** Any entry whose name begins with `.` (e.g. `.gitkeep`, `.DS_Store`) is not counted, not listed, not moved. Consequence: a `docs/refs/` holding only a `.gitkeep` counts as **empty** → no prompt, feature inert (consistent with `bprb`).
- **Symlinks count as files** — listed and moved like any ordinary entry.
- **Empty subdirectories** contribute nothing (no file = no entry).

### Numbered list format

A simple numbered list, one line per candidate, e.g.:

```
docs/refs/ has references. Select which to archive:
  1. foo.md
  2. sub/bar.md
  3. notes/2026-design.md
Reply with numbers to archive (e.g. 1,3 or 2-3 or all / none).
```

This extends the existing "show the user what's happening to each file" model of the plan gate, adding a per-item decision rather than a passive report. No elaborate UI — a numbered list and a numeric reply (PRD non-goal).

### Wait-for-reply

The command **moves no file under `docs/refs/` until the selection reply is received** (`rfsd`). Enumeration and display happen; mutation does not.

## Selection & Reply Parsing

Implements `prd.md > Refs Selection` (deferred reply-parsing decision, PRD non-goal).

### Accepted forms

- **Comma-separated numbers:** `1,3,5`
- **Ranges:** `2-4` (inclusive)
- **Keywords:** `all`, `none`
- **Combinations:** `1,3-5,8`

### Keyword and empty semantics

- `all` → select **every** candidate. Any other tokens in the same reply are ignored.
- `none` → select **nothing**; every ref left live.
- **Empty reply** → same as `none`: archive no refs.

### Forgiveness: ignore and proceed

Invalid or out-of-range tokens are **silently dropped**; valid selections are honored. No re-prompt. This is safe because the resolved selection is surfaced in the plan's "Will archive (refs)" group and still passes the single final confirmation before any file moves — a fat-fingered number cannot silently archive the wrong file; it appears (or is absent) in the plan for the user to catch.

## Plan Gate Integration

Implements `prd.md > Refs Selection` and `prd.md > Behavior Preservation` (ACs `rfsa`, `rfsd`, `bprd`, `bpre`).

### Two-step gate, single confirmation

The gate runs in two steps but takes exactly **one** go/no-go confirmation:

1. **Step A — refs selection (conditional).** Only when the sweep set is non-empty **and** the refs candidate list is non-empty: show the numbered list, collect the reply, resolve the selected set. This step asks for *input*, not confirmation.
2. **Step B — plan display + confirmation.** Display the complete plan, including the resolved refs as a `Will archive (refs):` group, then take the single confirmation that approves the whole run.

The selection reply is a plan-building input; the confirmation contract ("one confirmation covers the entire run; no separate prompt later") is preserved literally.

### Ordering against nothing-to-archive

The existing nothing-to-archive exit (sweep set empty) **short-circuits before Step A**. When the sweep set is empty, the command exits with zero changes and never reaches the refs list — even if `docs/refs/` is populated (`bprd`, `bpre`).

### Left-in-place exclusion

`docs/refs/` is **never** shown in the plan's "Left in place" set — in *all* cases: feature engaged, refs empty/absent, or partial selection. The refs directory is owned wholly by the refs-handling path and is excluded from the generic left-in-place computation outright. This prevents double-surfacing and satisfies `rfsi` (unselected refs get no reporting beyond their absence from the swept set). (Pre-feature, `docs/refs/` fell into "Left in place"; this removes it from that computation.)

### Decline

On decline at Step B, the command makes **no file changes and no git changes** — unchanged from existing behavior. No ref is moved; the selection collected in Step A is discarded.

## Refs Move Execution

Implements `prd.md > Refs Selection` (move story; ACs `rfse`–`rfsi`).

### Move, not copy; relative path preserved

After confirmation, each **selected** ref at `docs/refs/<relpath>` is **moved** (not copied) to `docs/archive/v{N}/refs/<relpath>`, preserving its relative subdirectory path under `refs/` (`rfse`, `rfsf`). Intermediate subdirectories under `docs/archive/v{N}/refs/` are created as needed. After the move, the selected file no longer exists at its original `docs/refs/` location (`rfsg`).

### Ordering within Execute

The refs move runs **after** the allow-list sweep and **before** `INDEX.md` generation, so the generated index can enumerate and count the swept refs. Execute order: create `v{N}/` → snapshot trio → move sweep set → **move selected refs** → generate `INDEX.md` → reset live `project-state.json`.

### Unselected refs untouched

Every unselected file remains at its original path under `docs/refs/` after the command completes (`rfsh`) and receives **no other action** — not copied, deleted, renamed, or reported beyond its absence from the swept set (`rfsi`). A leftover empty `docs/refs/` directory (when every ref was selected) is left as-is; the command does not remove the directory itself.

## INDEX.md Refs Enumeration

Implements `prd.md > Refs Selection` (resolves the open concern "INDEX.md enumeration of swept refs").

### Full per-file enumeration

When refs were swept, `docs/archive/v{N}/INDEX.md` gains a **reference files** group in its Contents list, one bullet per swept ref by its **flattened relative path** under `refs/`:

```markdown
## Contents

- <existing swept-file groups: planning artifacts / sprint files / process notes>
- reference files: `refs/foo.md`, `refs/sub/bar.md`
- `project-state.json` — snapshot of final v{N} state (lastCommand `<value>`).
- `backlog.md`, `sdd-feedback.md` — snapshots of the cross-cycle living docs …
```

Granularity is flattened relative paths, **not** a single `refs/` summary line. The reference-files group is emitted **only when at least one ref was swept** — when none were selected (or refs was empty/absent), the group is omitted entirely (preserving `bprc` for the no-refs case).

### Count

Swept refs **count toward** the cycle-summary `Artifacts archived: <count>` total (all-files-archived semantics) — not a separate count.

## Git Staging of Refs Moves

Implements `prd.md > Behavior Preservation` (git steps; AC `bprc`).

The existing tight-staging discipline (stage only the archive's own paths; **never** `git add -A`) extends to cover the refs moves:

- The new `docs/archive/v{N}/refs/...` adds — already covered by staging the `docs/archive/v{N}/` tree.
- The `docs/refs/...` **deletions** of the selected files — these are *outside* the archive tree and must be staged **explicitly by path**.
- **Unselected refs** are untouched working-tree files and are **never staged** (they stay out of the archive commit, like any other unrelated working-tree content).

No other change to Git Integration: branch → commit → push → PR, repo detection, collision suffix, dirty-tree warning, and failure handling are all unchanged.

## Behavior Preservation & Inert Path

Implements `prd.md > Behavior Preservation` (ACs `bpra`–`bpre`).

The feature must be **completely inert** when refs handling does not engage:

- **`docs/refs/` absent** (`bpra`): no numbered list, no selection prompt, no refs-related process note.
- **`docs/refs/` present but zero countable files** — empty, only empty subdirectories, or only hidden/dotfiles (`bprb`): same inertness as absent.
- **In either inert case** (`bprc`): the sweep allow-list, living-doc trio snapshot, `INDEX.md` generation, `project-state.json` reset, and git steps each produce exactly the result they produced before this feature — no reference-files group in `INDEX.md`, no refs in the count, `docs/refs/` simply absent from every surface.
- **Sweep set empty** (`bprd`, `bpre`): nothing-to-archive exit wins; no list, no ref moved, even if `docs/refs/` is populated.

A refs-related process note is written **only** when the feature actually engages (refs listed and a selection taken). The inert paths write nothing.

## File Structure

```
plugins/sdd/skills/archive/
  SKILL.md                       # EDIT — add: pre-flight refs enumeration;
                                 #   plan-gate two-step (list + selection parse,
                                 #   "Will archive (refs)" group, docs/refs/
                                 #   excluded from "Left in place"); refs move
                                 #   step; INDEX.md reference-files group + count;
                                 #   tight-staging of refs deletions

plugins/sdd/.claude-plugin/plugin.json      # EDIT — version bump
.claude-plugin/marketplace.json             # EDIT — version bump (moves with plugin.json)

docs/
  spec.md                        # this file
  refs/                          # the directory this feature handles
                                 #   (may or may not exist at runtime)
  archive/v1|v2|v3|v4/           # existing convention archive mirrors (unchanged)
```

No files are deleted and no new skill or reference file is added. All behavior change is contained in the one `SKILL.md` edit; the manifest bump is mechanical. `project-state.json` schema is unchanged from v4.

## Testing Strategy

No automated tests — consistent with the plugin's zero-runtime posture.

### Structural

`claude plugin validate .` exits successfully with the edited skill's frontmatter still well-formed.

### AC-by-AC manual verification

Each PRD acceptance criterion is exercised observably against a populated `docs/refs/` fixture (including nested subdirs, a same-named file in two subdirs, a `.gitkeep`, and a symlink):

- **Refs Selection** (`rfsa`–`rfsi`): list appears before any move; flattened recursive listing; relative-path display; wait-for-reply; selected files moved (not copied) with relative path preserved and gone from source; unselected files untouched and unreported.
- **Reply parsing:** `1,3` / `2-4` / `all` / `none` / empty / out-of-range-ignored each resolve to the right selected set, visible in the plan group.
- **Behavior Preservation** (`bpra`–`bpre`): absent refs, empty refs, dotfile-only refs, and the empty-sweep-set-but-populated-refs case each leave every other archive surface byte-for-byte unchanged and show no prompt.

The destructive/git-integrated ACs are exercised in a **throwaway copy or disposable git branch**, so the moves, branch, and PR can be driven and then discarded without disturbing live state.

### Dogfood (real acceptance test)

The genuine end-of-cycle run of `/sdd:archive` on **this** project when v5 closes — with a populated `docs/refs/` — archiving the selected refs into `docs/archive/v5/refs/`. One-shot and non-rollback-able, so it runs only **after** the disposable-branch walkthrough passes.

### Coverage expectation

Every PRD story has a named verification above. The hard line is the inert path: the empty/absent/dotfile-only and empty-sweep-set cases must produce zero refs-related output before any populated-refs run is trusted.

## Key Technical Decisions

| Decision | Why | Tradeoff |
|----------|-----|----------|
| Two-step plan gate (list+select, then one confirmation) | Preserves the "one confirmation covers the whole run" contract while adding an interactive selection | Two prompts instead of one; the selection step must clearly not read as the final go-ahead |
| Refs detection in pre-flight; display/select at the gate | Keeps all mutation behind the single confirmation; matches pre-flight's read-only contract | Candidate list is computed even on runs the user ultimately declines |
| Nothing-to-archive exit wins over populated refs | A populated `docs/refs/` alone is not "a cycle to close"; preserves the existing early exit | A user wanting *only* refs archived must wait until a real archive run |
| Dotfiles/hidden excluded from candidates | A lone `.gitkeep` keeping the dir in git must not count as "populated" | A user who genuinely wants to archive a dotfile cannot select it (acceptable — refs are content, not scaffolding) |
| `docs/refs/` excluded from "Left in place" in all cases | Refs are owned by the refs path; avoids double-surfacing; satisfies `rfsi` | "Left in place" no longer mentions `docs/refs/` even when refs stays live (intended) |
| Full per-file INDEX enumeration + counted in total | The archive's frozen record names every kept ref; count stays all-files-archived | INDEX Contents grows with ref count (fine — it is a manifest) |
| Move selected refs before INDEX generation | INDEX can enumerate and count the swept refs from on-disk truth | Execute ordering is load-bearing; a future edit must not reorder it |
| Refs move rides existing tight staging | One staging discipline; unrelated work still never enters the commit | The `docs/refs/` deletions must be staged explicitly by path (outside the archive tree) |
| Feature self-contained in archive `SKILL.md` (no new reference file) | Refs handling is command-specific, not cross-cutting | Archive `SKILL.md` grows; acceptable for a single-command behavior |

## Open Issues

- **INDEX reference-files group ordering.** Where the `reference files:` bullet sits within Contents (placed here after process notes, before the `project-state.json` snapshot bullet). Confirm the placement reads cleanly. → resolve at `/sdd:plan` / `/sdd:build`.
- **Numbered-list wording.** The exact prompt/list phrasing is illustrative in this spec; final wording is a `/sdd:build` detail, constrained only to a numbered list + numeric/keyword reply.
- **Leftover empty `docs/refs/` directory** after a full selection — spec leaves the empty directory in place (does not remove it). Confirm this is the desired end state at `/sdd:build`.

## Out of Scope

Deferred items: see `docs/backlog.md`.
