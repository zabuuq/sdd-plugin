---
name: archive
description: Close a finished project cycle — sweep its SDD artifacts into docs/archive/v{N}/, snapshot the living docs, reset project-state.json for a fresh /sdd:scope, and (in a git repo) branch, commit, push, and open a PR.
disable-model-invocation: true
---

# /sdd:archive

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, output constraints, and guard rails throughout.

## Loading

On startup, load the following:

1. `skills/sdd-guide/SKILL.md` (shared behavior) — and nothing else.

**Skips:** Do NOT load `references/deepening-rounds.md`, `references/context-management.md`, `references/right-sizing.md`, or `docs/backlog.md`. None of them apply to a mechanical close-and-reset command. `/sdd:archive` asks no interview questions, runs no deepening rounds, and makes no right-sizing judgment.

**Protocol exemptions** — `/sdd:archive` is deliberately exempt from several cross-cutting protocols defined in `sdd-guide`:

- **Open Concerns.** Do NOT run the open-concerns resolution protocol on startup. `/sdd:archive` *moves* `docs/open-concerns.md` into the archive (it is on the sweep allow-list below) rather than resolving or deferring its entries. Touching it as "open concerns" would fight the sweep.
- **Deepening Rounds.** Not an interview command; no rounds.
- **Interview-handoff template.** Does not emit the interview handoff.

**Chain position:** an anytime utility with no precondition guard, like `/sdd:feedback`, `/sdd:pause`, and `/sdd:unpause`. It is NOT part of the linear command chain and enforces no chain-position prerequisite. Its only startup requirement is that `docs/project-state.json` exists and parses (see Pre-flight).

## State Updates (startup exemption)

**Unlike every other command, `/sdd:archive` does NOT overwrite `lastCommand` on startup.** This is a deliberate exception to the state-tracking rule in `skills/sdd-guide/SKILL.md > ## State Tracking`, which otherwise requires every command to write its own name to `lastCommand` before doing any other work.

Instead, on startup `/sdd:archive` *captures* the existing `lastCommand` value and holds it. The captured value is needed later for the living-doc snapshot and the generated `INDEX.md` (a future item) — it records which command the cycle was sitting on when it was archived. If `/sdd:archive` clobbered `lastCommand` at startup, that information would be lost.

`/sdd:archive` only sets `lastCommand: "archive"` later, during the reset step (a future item), after the captured value has already been used. **A future reader or item must not add a startup `lastCommand` write to this command.**

## First-Run Explanation

Check `docs/project-state.json` for `commandExplanationsShown.archive`.

**If `archive` has NOT been shown (or `commandExplanationsShown` does not exist):**

Output the following as plain text. This is not a question — do not wait for acknowledgment. Output it and continue.

---

**What /sdd:archive does**

Archive closes out a finished project cycle. It sweeps the cycle's SDD-produced artifacts (scope, prd, spec, discovery, retro, open concerns, sprint files, resume files, and root process-notes) into a versioned `docs/archive/v{N}/` with an auto-generated `INDEX.md`, snapshots the cross-cycle living docs, resets `docs/project-state.json` so the next `/sdd:scope` starts clean, and — when run inside a git repo — branches, commits, pushes, and opens a PR. The carry-forward living docs (`docs/backlog.md`, `docs/sdd-feedback.md`, `docs/project-state.json`) are never swept; they stay live and are only snapshotted.

---

After outputting, set `commandExplanationsShown.archive` to `true` in `docs/project-state.json`.

**Once per project, not once per cycle.** The `archive` explanation fires a single time over the lifetime of a project directory. `/sdd:archive`'s own reset (a future item) **preserves** the `archive` key at its current value rather than flipping it back to `false` — so a maintainer who has already seen the blurb once never sees it again, even across many archived cycles in the same directory.

**If `archive` HAS been shown:** Skip the explanation entirely. Do not mention it.

## Pre-flight

Run these checks and computations before proposing any plan or making any change. Pre-flight reads and computes only — it writes nothing and moves nothing.

### Validate and capture state

1. Confirm `docs/project-state.json` exists and parses as valid JSON. If it is missing or does not parse, stop and report exactly what is wrong and that the user should restore or recreate it (per `skills/sdd-guide/SKILL.md > ## Guard Rails`). Do not attempt partial execution.
2. Capture from `docs/project-state.json`:
   - **`cycleNumber`** — the current working cycle (top-level integer). **If absent, assume `1`.**
   - **`lastCommand`** — the captured value held per the State Updates exemption above.
   - **`currentSprint`** — the current sprint number.

### Resolve target version N

Resolve the authoritative target version `N` for this archive:

1. Start at `docs/archive/v{cycleNumber}/` (using the captured `cycleNumber`, defaulting to `1` when absent).
2. If that directory already exists, advance to the next unused `v{N}` — increment until you reach a `v{N}` whose `docs/archive/v{N}/` directory does not yet exist.
3. The resolved `N` is **authoritative** for the archive directory name, the later branch name, and the later `cycleNumber` bump (all future items). When `cycleNumber` was absent and defaulted to `1`, this same advance-past-existing logic is the bootstrap path — it walks past any already-present `v1`/`v2`/`v3` to the first free slot.

### Detect git repository

Detect whether the working directory is inside a git repository by running `git rev-parse --is-inside-work-tree`. A clean exit reporting `true` means this is a git repo; a non-zero exit (or any "not a git repository" error) means it is not. **Record this repo/no-repo result** and hold it: the **Plan Gate** uses it to decide whether to show the conditional branch/PR lines, and the later **Git Integration** section uses it to decide whether to run any git operation at all.

**Dirty-tree warning (informational, non-blocking).** If this is a git repo, also check for unrelated uncommitted changes in the working tree at archive time (e.g. via `git status --porcelain`). If the tree carries uncommitted work that is not part of this archive, **warn the user** that those changes are present and that the archive's commit will stage only its own files (so the unrelated work stays out of the archive commit). This warning is purely **informational and non-blocking** — do not stop, and do not stash anything; the user decides whether to stash or commit their own work first before proceeding through the plan gate.

### Compute the sweep set (allow-list)

Compute the set of files this archive would sweep. **Do not move anything yet** — this step only builds the list.

Include a file only if it matches the allow-list:

- **Exact names in `docs/`:** `scope.md`, `prd.md`, `spec.md`, `discovery.md`, `retro.md`, `open-concerns.md`.
- **Globs in `docs/`:** `sprint-*.md`, `*-resume.md`.
- **Root-level glob:** `process-notes-*.md`.

Never include any of the following in the sweep set:

- The carry-forward trio — `docs/backlog.md`, `docs/sdd-feedback.md`, `docs/project-state.json`. These stay live; they are only snapshotted (a future item), never swept.
- The `docs/archive/` directory itself or anything inside it.

Anything else in `docs/` that is neither on the allow-list nor part of the trio nor under `docs/archive/` is left in place by this step (it will be surfaced later in the plan gate — a future item).

### Nothing-to-archive exit

If the computed sweep set is empty, report that there is nothing to archive and make **zero** changes — do not create a `v{N}/` directory, do not branch, do not commit, do not push, do not open a PR, and do not modify `docs/project-state.json`. Stop cleanly.

## Plan Gate & Confirmation

Pre-flight has now computed the sweep set, the target version `N`, and the left-in-place set, and has written and moved nothing. Before moving any file, writing any snapshot, resetting `project-state.json`, or running any git operation, display **one** plan and wait for **one** explicit user confirmation. This single gate covers the entire run — there is **no separate confirmation** later for opening the PR or for any other step.

### Display the plan

Present the plan as plain text, in this order. Keep it scannable — group and count, do not dump every filename.

1. **Will archive** — the sweep set, **grouped with counts**, not an exhaustive per-filename list. Group by kind, for example: "the planning trio (scope, prd, spec)", "discovery / retro / open-concerns (whichever are present)", "N sprint files", "N resume files", "N root process-notes files". Show only the groups that actually have members. A one-line total file count is fine; a full filename roster is not.

2. **Left in place** — the left-in-place set computed in pre-flight: everything in `docs/` that is neither on the allow-list, nor part of the carry-forward trio, nor under `docs/archive/`. List these by name (this set is normally small, and the point is to let the user see exactly what stays behind). If it is empty, say so. Make clear that these files will **not** be moved — so the user can abort here, relocate any straggler manually, and re-run.

3. **Target** — `docs/archive/v{N}/`, using the authoritative `N` resolved in pre-flight. If `N` was advanced past one or more existing `v{N}` directories (because the starting `cycleNumber` slot was already taken), say so explicitly, e.g. "v3 already exists, so this cycle will be archived as v4."

4. **Living-doc snapshots** — note that snapshots of the carry-forward trio (`docs/backlog.md`, `docs/sdd-feedback.md`, `docs/project-state.json`) will be written into the archive, and that an `INDEX.md` will be auto-generated. The live trio itself stays in `docs/` and is not moved.

5. **`project-state.json` reset** — note that `docs/project-state.json` will be reset for a fresh cycle (so the next `/sdd:scope` starts clean). A one-line summary is enough here; the exact reset map is applied later.

6. **Branch & PR** — *git repo only.* Note that a branch `archive-v{N}` will be created and a PR opened. If `archive-v{N}` already exists and a collision suffix will be applied, name the actual branch that will be used. **Omit both this branch line and the PR mention entirely when the project is not a git repo** — in that case the run will sweep, snapshot, and reset locally with no git step, and the plan must not promise a branch or PR.

### Wait for confirmation

After displaying the plan, ask for one explicit confirmation to proceed and stop. Do not move, write, reset, branch, or commit anything until the user explicitly confirms.

### On decline

If the user declines (or gives anything other than an explicit confirmation), make **no changes of any kind** and stop cleanly:

- **No file changes** — no files moved into the archive, no `v{N}/` directory created, no snapshots written, no `INDEX.md` generated, and no edit to `docs/project-state.json` (it is not reset).
- **No git changes** — no branch created, no commit, no push, and no PR opened.

The repository and `docs/` are left exactly as pre-flight found them. Report that the archive was cancelled and that nothing was changed.

## Execute the Archive (sweep, snapshots, INDEX.md)

This section runs **only after** the user has given the single explicit confirmation at the Plan Gate. Use the authoritative target version `N` and the sweep set computed in pre-flight, and the `lastCommand`, `cycleNumber`, and `currentSprint` values captured there.

Two later steps are **out of scope here and must not be performed in this section:**

- **The live `docs/project-state.json` reset** is a later section. Do **not** reset, re-key, or otherwise edit the live `docs/project-state.json` here.
- **The git steps** (branch, commit, push, PR) are a later section. Do **not** run any git operation here.

Run the steps below in **exactly this order**. The order matters: the trio snapshot (step 2) **must be taken before** the later reset section touches the live `docs/project-state.json`, so the snapshot captures the cycle's true pre-reset state — including the real final `lastCommand`. Do not reorder to snapshot after the reset.

### 1. Create the archive directory

Create `docs/archive/v{N}/` using the resolved `N` from pre-flight. (Its parent `docs/archive/` already holds prior cycles; only the new `v{N}/` is created.)

### 2. Snapshot the carry-forward trio (before any reset)

Copy each of the three carry-forward living docs into `docs/archive/v{N}/`, **byte-identical** to its live counterpart as it stands at archive time:

- `docs/backlog.md` → `docs/archive/v{N}/backlog.md`
- `docs/sdd-feedback.md` → `docs/archive/v{N}/sdd-feedback.md`
- `docs/project-state.json` → `docs/archive/v{N}/project-state.json`

These are **copied, not moved** — the live trio stays in `docs/`. 

**Critical ordering:** the `project-state.json` snapshot is taken **now, before** the later reset section edits the live file. Snapshotting at this point captures the cycle's genuine pre-reset state (including the real final `lastCommand`). If the live file were reset first, the snapshot would record the post-reset `lastCommand: "archive"` instead of the true final command — so this copy must precede the reset.

### 3. Move the allow-list sweep set

**Move** (not copy) every file in the sweep set computed in pre-flight into `docs/archive/v{N}/`, preserving filenames. The sweep set is the allow-list:

- Exact `docs/` names: `scope.md`, `prd.md`, `spec.md`, `discovery.md`, `retro.md`, `open-concerns.md`.
- `docs/` globs: `sprint-*.md`, `*-resume.md`.
- Root-level glob: `process-notes-*.md`.

Move only the files actually present in the pre-flight sweep set — do not invent or assume artifacts the cycle did not produce. After this step, the **only** files left in `docs/` outside `docs/archive/` are the live carry-forward trio (`backlog.md`, `sdd-feedback.md`, `project-state.json`).

### 4. Generate `docs/archive/v{N}/INDEX.md`

Auto-generate the `INDEX.md` from the files **actually swept** plus the snapshotted trio. This is fully automatic — **do not prompt the user** for a hand-authored description or summary. Mirror the established skeleton used by prior cycles (`docs/archive/v1|v2|v3/INDEX.md`).

Write the file using this template, substituting the runtime values:

```
# docs/archive/v{N}/

This directory contains a snapshot of v{N} planning, sprint, and process
artifacts, archived at the start of the next cycle. Files here are historical
and are not modified by later cycles.

## Contents

- <auto-enumerated swept files, grouped: planning artifacts / sprint files / process notes>
- `project-state.json` — snapshot of final v{N} state (lastCommand `<value>`).
- `backlog.md`, `sdd-feedback.md` — snapshots of the cross-cycle living docs as they stood at v{N} close; live copies remain in `docs/`.

## Cycle summary

- Cycle: v{N}
- Archived: <date>
- Last command at archive: `<lastCommand>`
- Sprints: <currentSprint>
- Artifacts archived: <count>
```

Fill the template as follows:

- **`{N}`** — the resolved target version.
- **Contents list** — derived from the files *actually swept* (step 3), grouped so the list stays accurate regardless of which artifacts the cycle produced:
  - **planning artifacts** — the swept exact-name docs (`scope.md`, `prd.md`, `spec.md`, `discovery.md`, `retro.md`, `open-concerns.md`) that are present;
  - **sprint files** — swept `sprint-*.md` (and any `*-resume.md`);
  - **process notes** — swept `process-notes-*.md`.
  List only groups that have members. After the swept-file groups, include the fixed `project-state.json` and `backlog.md`/`sdd-feedback.md` snapshot bullets shown in the template.
- **`lastCommand`** — the value captured in pre-flight (pre-reset). It appears in **both** the `project-state.json` Contents bullet and the `Last command at archive:` summary bullet.
- **`Archived:`** — the current date as supplied by the running agent at runtime.
- **`Sprints:`** — the captured `currentSprint`.
- **`Artifacts archived:`** — the count of files actually swept in step 3 (the trio snapshots are not counted as swept artifacts).

`## Cycle summary` is **factual metadata bullets only** — no narrative prose. Do not write a paragraph summarizing the cycle; emit only the bulleted metadata above.

After this section completes, the live `docs/project-state.json` reset and the git steps run in the later sections.

## Reset the Live `project-state.json`

This section runs **only after** the Execute section above has completed — and, critically, **only after step 2 of that section has already written the byte-identical pre-reset snapshot** of `docs/project-state.json` into `docs/archive/v{N}/`. **This reset edits the LIVE `docs/project-state.json` only; it never touches the snapshot.** Do not reorder: the snapshot is taken first (Execute step 2), the live reset second (here). A future reader must not move this reset before the snapshot, or the archived copy would record the post-reset `lastCommand: "archive"` instead of the cycle's true final command.

The reset map below is restated from its source of truth, `skills/sdd-guide/references/living-documents.md > ## project-state.json Schema and Reset` (see its **Reset map** and **commandExplanationsShown normalization** subsections). That reference is authoritative; apply the per-field behavior here without needing to open it.

Rewrite the live `docs/project-state.json` field-by-field, using the authoritative `N` resolved in pre-flight:

| Field | Reset behavior |
|---|---|
| `version` | **Carry** — leave at `1`. Schema version, not cycle state. |
| `cycleNumber` | **Bump to `N + 1`**, where `N` is the authoritative target version resolved in pre-flight. |
| `currentSprint` | **Reset to `0` (or omit the field entirely) — NOT `1`.** `/sdd:plan` computes the next sprint as `currentSprint + 1`, so `0` or an absent field makes the next cycle's first sprint `1`. |
| `buildMode` | **Carry** — preserve the existing value. Stable user preference. |
| `smallProject` | **Reset to `null`.** The new cycle re-judges right-sizing from scratch. |
| `lastCommand` | **Set to `"archive"`.** |
| `commandExplanationsShown` | **Normalize and flip to `false` except `archive`** — see the rule below. |
| `notes` | **Rewrite to a fresh seed line** naming the new cycle and where the prior cycle was archived. |

### `lastCommand` — the deferred write

Setting `lastCommand: "archive"` **here** is the deferred write promised in `## State Updates (startup exemption)` at the top of this file. Startup deliberately did **not** write `lastCommand`; it captured the existing value and held it so the snapshot (Execute step 2) and `INDEX.md` (Execute step 4) could record the cycle's true final command. Now that those have been written, the live `lastCommand` is finally set to `"archive"`. This is the **only** place `/sdd:archive` writes `lastCommand`.

### `commandExplanationsShown` normalization

This is the single place the `commandExplanationsShown` object is normalized — the live file is not separately cleaned during the build. Apply all three rules:

- **Drop the stale legacy keys** `sprint`, `iterate`, and `reflect` (not real commands).
- **Add an `archive` key** if one is not already present.
- **Flip every key to `false` EXCEPT `archive`**, whose existing value is **preserved**. The archive blurb is once-per-project, not once-per-cycle — a maintainer who has already seen it must not see it again after a reset. Every other command's explanation re-fires for the fresh cycle.

### `notes` reseed

Replace `notes` with a short, factual seed line for the new cycle — naming the new cycle number (`N + 1`) and stating that the prior cycle was archived to `docs/archive/v{N}/`. Keep it to one plain line; no narrative.

### Validity

After the rewrite, `docs/project-state.json` **must remain valid, parseable JSON.** Write the complete object, not a patch: emit every carried, bumped, reset, and normalized field together so the file parses cleanly. (If you reset `currentSprint` by omitting it rather than writing `0`, simply do not emit that key — the object is still valid.)

After this reset completes, the git steps (branch, commit, push, PR) run in the next section.

## Git Integration

This section runs **only after** the file archive (Execute section) and the live `docs/project-state.json` reset have both completed. Use the repo/no-repo result and the authoritative target version `N` recorded in pre-flight.

### Not a git repository

If pre-flight determined this is **not** a git repo, perform **file operations only** — no branch, no commit, no push, and no PR. The sweep, the trio snapshot, the `INDEX.md`, and the reset have already happened; git is simply skipped. Do not error and do not attempt any git command. Note in the handoff that no PR was opened because the project is not a git repo.

### In a git repository

If pre-flight determined this **is** a git repo, run these four steps in order: **branch → commit → push → PR-open.**

1. **Branch.** Create and switch to a branch named `archive-v{N}`, using the authoritative `N` from pre-flight.
   - **Branch collision:** if `archive-v{N}` already exists, **auto-suffix** to the first free name (`archive-v{N}-2`, then `archive-v{N}-3`, …) — the same advance-past-existing logic the archive directory uses — **and notify the user** that the base branch `archive-v{N}` already existed and which suffixed branch was used instead.

2. **Commit (tight staging).** Stage **only the archive's own changes**, never the whole tree:
   - the new `docs/archive/v{N}/` tree (the created directory, the snapshotted trio, the swept files, and the generated `INDEX.md`);
   - the moved-file **deletions** from their old paths (the swept files removed from `docs/` and from the repo root);
   - the reset `docs/project-state.json`.

   **NEVER run `git add -A`** (or `git add .`). Stage the specific archive paths explicitly so any unrelated uncommitted work in the tree stays **out** of the archive commit. Then commit with a clear message naming the archived cycle (e.g. `Archive v{N} cycle artifacts to docs/archive/v{N}/`).

3. **Push.** Push the branch to the remote (setting upstream as needed).

4. **PR-open.** Open a pull request via `gh pr create`. Capture the resulting PR url for the handoff.

### Failure handling (no rollback)

If the file archive completed but a git step fails — for example `gh` is missing or unauthenticated, no remote is configured, the push is rejected, or the branch step fails — **do not roll back.** The completed file archive (the swept `docs/archive/v{N}/`, the snapshots, the `INDEX.md`, and the reset `project-state.json`) and any git work that already succeeded (e.g. a local commit that was made before the push failed) are **left in place.**

Report the **specific failing step** and the **resulting state** — say plainly what succeeded (file archive done; branch/commit made if they were) and what failed and why — then **prompt the user how to proceed** (e.g. authenticate `gh` and re-run the PR step manually, add a remote and push, or open the PR by hand). Do not silently abandon the run and do not undo any completed work.

## Handoff

After Git Integration finishes (whether it opened a PR, skipped git for a non-repo, or stopped at a git failure), emit the completion handoff. `/sdd:archive` is out-of-pattern — it is not an interview command — so it does **not** use the interview-handoff template and does **not** touch `handoffWarningShown`. Emit a one-line outcome plus a two-line handoff pointing at `/sdd:scope`:

```
Cycle v{N} archived to docs/archive/v{N}/. PR opened: <url>.

Run `/clear`, then `/sdd:scope` to start the next cycle.
```

Substitute the resolved `{N}` and the actual PR url at runtime.

**The PR line is conditional.** Include `PR opened: <url>.` only when the git step actually ran and opened a PR. Otherwise replace it with the true state:

- **Not a git repo:** omit the PR sentence (e.g. `Cycle v{N} archived to docs/archive/v{N}/.` with no PR clause, optionally noting the project is not a git repo).
- **Git step failed:** state the failure rather than a PR url (e.g. note the file archive is complete but the PR was not opened, consistent with the failure report above).

**The next step is ALWAYS `/sdd:scope`**, regardless of the git outcome — the second handoff line never changes.
