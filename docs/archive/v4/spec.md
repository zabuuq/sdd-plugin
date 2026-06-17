# SDD v4 — `/sdd:archive` Command — Technical Specification

## Posture

This spec is a **new-command design doc**. It defines *what* `/sdd:archive` does, its behavioral contract, where its files land, and the cross-cutting edits it requires — but leaves the exact `SKILL.md` prose to `/sdd:plan` (sequencing) and `/sdd:build` (execution). "How" detail that outlives the cycle (the `project-state.json` schema, the out-of-pattern command roster) lands in `plugins/sdd/skills/sdd-guide/` and its references; this spec points at those targets rather than restating them.

`/sdd:archive` is a **new, separate command** — not an extension of `/sdd:retro`. It changes one cross-cutting invariant: the `project-state.json` schema gains a `cycleNumber` field and its `commandExplanationsShown` object is normalized. Everything else is additive. The markdown-only, zero-runtime posture is preserved: no hook, no script, no MCP.

## Stack

Unchanged from v3.

| Layer | Choice | Reference Docs |
|-------|--------|----------------|
| Platform | Claude Code plugin (skills-based) | https://code.claude.com/docs/en/plugins |
| Language | Markdown (SKILL.md + YAML frontmatter) | https://code.claude.com/docs/en/skills |
| Runtime code | None — behavior is markdown instructions | — |
| Distribution | GitHub marketplace repo | https://code.claude.com/docs/en/plugin-marketplaces |
| Validation | `claude plugin validate .` | https://code.claude.com/docs/en/plugins-reference |
| Git/PR mechanism | `git` + `gh pr create` (invoked by the agent, not bundled) | https://cli.github.com/manual/gh_pr_create |

No build step, no runtime dependencies, no compiled code. `/sdd:archive` drives `git` and `gh` as external CLIs the agent calls; it ships none of them.

## Runtime & Deployment

Unchanged from v3. Claude Code CLI/desktop with plugin support; GitHub marketplace distribution. A new command is a version bump on ship: `plugins/sdd/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` move together (exact bump TBD at release, not in this spec).

## Architecture Overview

`/sdd:archive` is one new `SKILL.md` skill. Its "architecture" is an **ordered execution pipeline** plus one **schema change** to `project-state.json`. There are no services and no new runtime. The command reads state, computes a versioned archive target, gets one confirmation, then performs file moves, writes archive artifacts, resets live state, and (in a git repo) branches/commits/pushes/PRs.

```
  /sdd:archive invoked
        │
        ▼
  ┌─────────────────────────────────────────────────────────────┐
  │ PRE-FLIGHT (no changes)                                      │
  │  validate project-state.json parses                          │
  │  first-run blurb (archive key) if not shown                  │
  │  read + capture state: cycleNumber, lastCommand, currentSprint│
  │  resolve target N (advance past existing docs/archive/v{N}/) │
  │  compute sweep set (ALLOW-LIST)                              │
  │  nothing-to-archive? ──► report, exit, zero changes          │
  └─────────────────────────────────────────────────────────────┘
        │
        ▼
  ┌─────────────────────────────────────────────────────────────┐
  │ PLAN GATE (one confirmation)                                 │
  │  show: will-archive list (grouped+counts), left-in-place     │
  │        list, target v{N}, branch archive-v{N}, PR intent     │
  │  decline ──► zero changes, exit                              │
  └─────────────────────────────────────────────────────────────┘
        │ confirm
        ▼
  ┌─────────────────────────────────────────────────────────────┐
  │ EXECUTE                                                      │
  │  create docs/archive/v{N}/                                   │
  │  snapshot trio byte-identical (project-state.json PRE-reset) │
  │  move sweep set into v{N}/                                   │
  │  generate v{N}/INDEX.md                                      │
  │  reset live project-state.json (cycleNumber → N+1, etc.)     │
  │  git (if repo): branch → commit (tight staging) → push → PR  │
  └─────────────────────────────────────────────────────────────┘
        │
        ▼
  one-line outcome + handoff (Run /clear, then /sdd:scope)
```

### Data Flow — the archive operation

The primary data entity is the **cycle artifact set** moving from the working tree into a frozen versioned directory, with three living docs branching off as snapshots while their live copies persist.

```
 docs/ (working)                         docs/archive/v{N}/ (frozen)
 ─────────────────                       ──────────────────────────
 scope/prd/spec/discovery/retro.md ─────► (moved) ─────► same files
 open-concerns.md                  ─────► (moved)
 sprint-*.md                       ─────► (moved)
 *-resume.md                       ─────► (moved)
 (root) process-notes-*.md         ─────► (moved)
                                              │
 backlog.md ───────┐                          │
 sdd-feedback.md ──┼── stay LIVE ──► copy ────┤ (byte-identical snapshots)
 project-state.json┘   (pre-reset)            │
                                              ├─► INDEX.md (generated)
 project-state.json (live) ◄── reset ─────────┘   (lists swept files,
   cycleNumber → N+1                               lastCommand, summary)
   lastCommand → "archive"
   smallProject → null  ... (see Reset map)
```

**Error/edge path — git step fails after the file archive completes:** the file archive (moves, snapshots, `INDEX.md`, reset) is already on disk and is **left in place**. The command reports which git step failed (branch / push / PR) and the resulting state (e.g., "commit is on local branch `archive-v{N}`"), then prompts the user how to proceed. No rollback of completed file work.

## Archive Command Skill

Implements `prd.md > Run Control & Safety` (and is the container for every other story below).

The single new skill that defines the command.

### Location & frontmatter

- Lives at `plugins/sdd/skills/archive/SKILL.md` — one skill per command, matching every existing command.
- Frontmatter: `name: archive`, a `description`, and `disable-model-invocation: true`. The model-invocation guard is mandatory — archive is destructive-ish and must only ever run on explicit user invocation.

### Loading & protocol fit

- **Loads on startup:** `skills/sdd-guide/SKILL.md` only — for tone and Output Constraints. It does **not** load `deepening-rounds.md`, `context-management.md`, `right-sizing.md`, or `backlog.md`; none apply to a mechanical command.
- **Follows** from sdd-guide: Output Constraints / tone; the first-run command-explanation blurb (the `archive` key, see below); Guard Rails lightweight validation (valid JSON, expected files).
- **Exempt** from: the Open Concerns resolution protocol (archive *moves* `open-concerns.md` into the archive rather than resolving it), Deepening Rounds, and the interview-handoff template.
- **Chain position:** an anytime utility with no precondition guard, like `/sdd:feedback` and `/sdd:pause`. It is not inserted into the linear chain; the command-chain docs note it as anytime.

### First-run blurb

`/sdd:archive` prints a one-time purpose blurb like every other command, gated on `commandExplanationsShown.archive`. Unlike the interview commands, this blurb fires **once per project/directory lifetime, not once per cycle** — archive's own reset does not flip the `archive` key back to `false` (see Reset map).

## Version Resolution

Implements `prd.md > Artifact Sweep` and `prd.md > Directory Reset`.

### `cycleNumber` field

`project-state.json` gains a top-level integer `cycleNumber` = the current working cycle. Documented in `references/living-documents.md` alongside the rest of the schema. `/sdd:archive` is the **sole owner** of this field — `/sdd:scope` never reads or writes it. Single writer ⇒ no double-increment, no stale-number race.

### Resolution & bootstrap

- Read `cycleNumber`. **If absent, assume `1`** (the field does not exist on disk today).
- Resolve the target directory: start at `docs/archive/v{cycleNumber}/`; if it already exists, **advance to the next unused `v{N}`**. This collision-advance both satisfies the PRD AC and serves as the bootstrap mechanism — an absent field defaulting to `1` advances past `v1`/`v2`/`v3` to land on the correct next version.
- The **resolved** `N` (after advance) is authoritative for the directory name, the branch name, and the `cycleNumber` bump.

### Increment

After archiving into `v{N}/`, archive sets live `cycleNumber = N + 1` as part of the reset. Subsequent runs read it directly without rescanning.

## Artifact Sweep

Implements `prd.md > Artifact Sweep`.

### Allow-list (what moves)

The sweep targets only artifacts **SDD itself produces**, by an explicit allow-list — *not* a "everything except the trio" deny-list. This prevents archiving foreign or hand-placed files in `docs/`.

- **Exact names in `docs/`:** `scope.md`, `prd.md`, `spec.md`, `discovery.md`, `retro.md`, `open-concerns.md`.
- **Globs in `docs/`:** `sprint-*.md`, `*-resume.md`.
- **Root-level:** `process-notes-*.md`.

All of the above move into `docs/archive/v{N}/`.

### Carry-forward trio (never swept)

`docs/backlog.md`, `docs/sdd-feedback.md`, `docs/project-state.json` stay live in `docs/`. They are snapshotted (see Living-Doc Snapshots), not moved.

### Left-in-place visibility

Anything in `docs/` that is neither on the allow-list, nor the trio, nor `docs/archive/` itself — e.g. ad-hoc/project-specific cycle docs — is **not swept**. The plan gate surfaces these as an explicit *left-in-place* list so the user sees exactly what stays behind and can abort to move stragglers manually before re-running. (See Plan Gate.)

### Nothing-to-archive

If the computed sweep set is empty (no allow-list matches in `docs/`, no root `process-notes-*.md`), the command reports there is nothing to archive and makes **zero changes** — no `v{N}/` directory, no branch, no PR.

### PRD divergence note

This allow-list model **diverges from the PRD's Artifact Sweep wording** ("moves every item in `docs/` except the live carry-forward trio"), which describes a deny-list. The observable changed during spec deepening. Recorded in Open Issues as a PRD edit for `/sdd:refine` to reconcile.

## Living-Doc Snapshots

Implements `prd.md > Living-Doc Preservation`.

### Byte-identical snapshot

A copy of each of the three living docs (`backlog.md`, `sdd-feedback.md`, `project-state.json`) is written into `docs/archive/v{N}/`, **byte-identical** to its live counterpart as it stood at archive time.

### Ordering vs. reset

The `project-state.json` snapshot is taken **before** the live reset is applied (the snapshot must capture the pre-reset state, including the cycle's true final `lastCommand`). Execution order: create `v{N}/` → snapshot the trio → … → reset live `project-state.json`. The two non-state living docs (`backlog.md`, `sdd-feedback.md`) are not mutated, so their snapshot timing is unconstrained beyond "before exit."

## INDEX.md Generation

Implements `prd.md > Archive Index`.

### Template

Auto-generated, no interactive prompt for a hand-authored description. Structural-but-factual, mirroring the established `docs/archive/vN/INDEX.md` skeleton:

```markdown
# docs/archive/v{N}/

This directory contains a snapshot of v{N} planning, sprint, and process
artifacts, archived at the start of the next cycle. Files here are historical
and are not modified by later cycles.

## Contents

- <auto-enumerated swept files, grouped: planning artifacts / sprint files /
   process notes>
- `project-state.json` — snapshot of final v{N} state (lastCommand `<value>`).
- `backlog.md`, `sdd-feedback.md` — snapshots of the cross-cycle living docs
   as they stood at v{N} close; live copies remain in `docs/`.

## Cycle summary

- Cycle: v{N}
- Archived: <date>
- Last command at archive: `<lastCommand>`
- Sprints: <currentSprint>
- Artifacts archived: <count>
```

### Contents derivation

- The Contents list is built from the **files actually swept**, not a fixed assumption — it stays accurate regardless of which artifacts the cycle produced.
- `lastCommand` (captured pre-reset) appears in both the `project-state.json` bullet and the summary, satisfying the PRD AC.
- `## Cycle summary` is factual metadata bullets only — no narrative prose, since there is no interactive authoring step. An `Archived: <date>` line is included (the running agent supplies the date).

## project-state.json Reset

Implements `prd.md > Directory Reset`.

### Reset map

Applied to the **live** file after the snapshot is taken:

| Field | Reset behavior | Rationale |
|---|---|---|
| `version` | Carry (`1`) | Schema version, not cycle state. |
| `cycleNumber` | Bump to `N+1` | Resolved N + 1; sole owner. |
| `currentSprint` | Reset to `0` (or omit) | No carryover of prior sprint state. `/sdd:plan` computes the next sprint as `currentSprint + 1`, so `0` (or absent) makes the next cycle's first sprint `1`. |
| `buildMode` | Carry | Stable user preference (mirrors profile `defaultSprintMode`). |
| `smallProject` | Reset to `null` | New cycle re-judges from scratch. |
| `lastCommand` | Reset to `"archive"` | Truthful; `/sdd:scope` overwrites on next startup. |
| `commandExplanationsShown` | Normalize + flip to `false` (except `archive`) | See below. |
| `notes` | Rewrite to a fresh seed line | Names the new cycle and where the prior cycle archived. |

The reset must leave `project-state.json` a valid, parseable JSON file (PRD AC).

### commandExplanationsShown normalization

The reset **normalizes** the object to the current command set in one place (this is the single definition of the normalization logic — the live file is not separately cleaned during the v4 build):

- **Drop the stale legacy keys** `sprint`, `iterate`, `reflect` (not real commands).
- **Add an `archive` key.**
- Flip every key to `false` **except `archive`**, whose value is preserved (the archive blurb is once-per-project, not once-per-cycle).

### Snapshot-first / startup-overwrite exemption

`/sdd:archive` is **exempt from the sdd-guide State Tracking rule** that every command overwrites `lastCommand` on startup. Instead it captures the existing `lastCommand` first (for the snapshot and `INDEX.md`), snapshots `project-state.json` untouched, and sets `lastCommand: "archive"` only later, in this reset. This preserves the cycle's true final command in the snapshot — matching the v3 precedent, whose snapshot correctly reads `"retro"`.

## Plan Gate & Confirmation

Implements `prd.md > Run Control & Safety`.

### Plan contents

Before any file move or git operation, the command displays a single plan and waits for explicit confirmation. The plan lists:

- **Will archive** — the sweep set, **grouped with counts** (e.g., "scope/prd/spec, 18 sprint files, 18 process-notes"), not exhaustive per-filename.
- **Left in place** — everything in `docs/` outside the allow-list, the trio, and `docs/archive/`.
- **Target** `docs/archive/v{N}/`, noting if `N` was advanced past an existing directory.
- **Living-doc snapshots** to be written, and that `INDEX.md` will be generated.
- **`project-state.json` reset** summary.
- **Branch** `archive-v{N}` (noting any collision suffix) and that a **PR** will be opened — git repo only.

### Confirmation & decline

One confirmation gate covers the whole run; there is **no separate PR-open confirmation**. On decline, the command makes **no file changes and no git changes** — no moves, branch, commit, or PR.

## Git Integration

Implements `prd.md > Git Integration`.

### Repo detection

Detect via `git rev-parse --is-inside-work-tree`. **Not a repo** → perform the file operations only; no branch, commit, or PR.

### Branch / commit / push / PR

In a repo, run in order: **branch → commit → push → PR-open**. PR-open uses `gh pr create`. Branch name is `archive-v{N}` (resolved N).

### Tight staging + pre-flight warning

- **Tight staging:** the commit stages only the archive's own changes — the new `docs/archive/v{N}/` tree, the moved-file deletions from their old paths, and the reset `project-state.json`. **Not** `git add -A`. Unrelated uncommitted work stays out of the archive commit.
- **Pre-flight warning:** if the working tree has unrelated uncommitted changes at archive time, the command warns the user (informational, non-blocking — the user decides whether to stash first).

### Branch collision

If `archive-v{N}` already exists, **auto-suffix** (`archive-v{N}-2`, …) like the directory advance, **and notify** the user that the base branch already existed.

### Failure handling

If the file archive completed but a git step fails (`gh` missing/unauthenticated, no remote, push rejected, branch issue), the command **reports the specific failing step and the resulting state**, leaves the completed file archive (and any successful git work, e.g. a local commit) in place, and **prompts the user how to proceed**. No rollback. (PRD-fixed observable; spec pins the per-step reporting.)

## Handoff

Implements `prd.md > Run Control & Safety`.

`/sdd:archive` is **out-of-pattern** (with `/sdd:build`, `/sdd:retro`, `/sdd:pause`) — not an interview command, so it is not bound by the interview-handoff template or the `handoffWarningShown` first-handoff explanation. It emits a one-line outcome plus the two-line handoff:

```
Cycle v{N} archived to docs/archive/v{N}/. PR opened: <url>.

Run `/clear`, then `/sdd:scope` to start the next cycle.
```

The PR line is conditional — present only when the git step ran and opened a PR; omitted or replaced with the no-repo / failure state otherwise. Next step is always `/sdd:scope`. Spec adds `/sdd:archive` to the **Out-of-pattern commands** list in `sdd-guide/SKILL.md` so the guide stays the single source of truth.

## File Structure

```
plugins/sdd/skills/archive/
  SKILL.md                       # NEW — the /sdd:archive command

plugins/sdd/skills/sdd-guide/
  SKILL.md                       # EDIT — add /sdd:archive to Out-of-pattern commands list
  references/living-documents.md # EDIT — document cycleNumber field + the reset map

plugins/sdd/.claude-plugin/plugin.json      # EDIT — register command; version bump
.claude-plugin/marketplace.json             # EDIT — version bump (moves with plugin.json)

docs/
  spec.md                        # this file
  archive/v1|v2|v3/              # existing convention archive mirrors (unchanged)
```

No files are deleted. The only behavioral change outside the new skill is the `project-state.json` schema (`cycleNumber` + normalized `commandExplanationsShown`), documented in `references/living-documents.md`.

## Testing Strategy

No automated tests — consistent with the plugin's zero-runtime posture.

### Structural

`claude plugin validate .` exits successfully with the new skill registered and frontmatter well-formed.

### AC-by-AC manual verification

Each PRD acceptance criterion is exercised observably: the confirmation gate, the decline path (zero changes), nothing-to-archive, the allow-list sweep, left-in-place reporting, living-doc preservation + byte-identical snapshots, `INDEX.md` contents, the reset map, and the git path. The **destructive/git-integrated ACs are exercised in a throwaway copy or a disposable git branch**, so the moves, branch, and PR can be driven and then discarded without disturbing live state.

### Dogfood (real acceptance test)

The genuine end-of-cycle run of `/sdd:archive` on **this** project when v4 closes — archiving v4 into `docs/archive/v4/` and resetting for v5. This is the ultimate proof, but it is one-shot and non-rollback-able, so it runs only **after** the disposable-branch walkthrough passes.

### Coverage expectation

Every PRD story has a named verification above. The hard line is safety: the confirmation gate and the decline/nothing-to-archive zero-change guarantees must hold before any dogfood run.

## Key Technical Decisions

| Decision | Why | Tradeoff |
|----------|-----|----------|
| Allow-list sweep, not deny-list | Never archive foreign/manual files in `docs/` | Ad-hoc cycle docs aren't auto-swept; user moves stragglers (mitigated by left-in-place plan list) |
| `/sdd:archive` sole owner of `cycleNumber` | Single writer; no double-increment or stale-number race | `/sdd:scope` stays unaware of versioning |
| Absent `cycleNumber` → assume `1` + collision-advance | Reuses the existing advance check as the bootstrap; no separate seeding step | First run scans `docs/archive/` to land on the right N |
| Snapshot-first; archive exempt from startup `lastCommand` overwrite | Snapshot preserves the cycle's true final command (matches v3 `"retro"`) | One documented exception to a sdd-guide universal rule |
| Single confirmation gate; no separate PR confirm | One reviewable plan covers the whole run | User must read the full plan before confirming |
| Tight staging + pre-flight warning | Unrelated uncommitted work never enters the archive commit | Archive must track exactly which paths it touched |
| Branch collision auto-suffix + notify | Mirrors the directory-advance behavior; never clobbers an existing branch | Branch name can drift from the clean `archive-v{N}` form |
| Auto-generated factual `INDEX.md` | No interactive authoring step (PRD); always present | Loses the v2/v3 hand-written narrative summary |
| `commandExplanationsShown` normalized once, in archive's reset | One code path; live file stays untouched until archive runs | Dead keys linger in the live file until the next archive (harmless) |

## Open Issues

- **Allow-list diverges from the PRD Artifact Sweep AC.** The PRD says "moves every item in `docs/` except the live carry-forward trio" (deny-list); spec settled on an allow-list of SDD-produced artifacts plus a left-in-place plan list. → reconcile via `/sdd:refine` (PRD edit) before/around `/sdd:build`.
- **Branch-collision suffix vs. directory-advance N.** Directory collisions advance `N`; branch collisions suffix `-2`. These are intentionally different mechanisms — confirm the combination reads clearly in the plan output. → resolve at `/sdd:plan` / `/sdd:build`.
- **`notes` seed-line content and date sourcing.** The reset rewrites `notes` and `INDEX.md` carries an `Archived:` date; the agent supplies the date at run time. Exact seed-line wording is a `/sdd:build` detail. → resolve at `/sdd:build`.

## Out of Scope

Deferred items: see `docs/backlog.md`.
