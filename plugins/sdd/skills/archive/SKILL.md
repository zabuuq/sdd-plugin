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

**This exit short-circuits BEFORE the plan gate's Step A** (`bprd`, `bpre`). When the sweep set is empty the command exits here with zero changes and never reaches the refs list — **even if `docs/refs/` is populated**. A populated `docs/refs/` alone never triggers a numbered list, a selection prompt, or a ref move; refs handling is reachable only on a real archive run.

### Enumerate refs candidates

Compute the flattened candidate list of references under `docs/refs/`. This is a **read-only** computation — like every other pre-flight step it reads and computes only; it **moves nothing**. The list is merely *built* here; it is *displayed* and the user's selection *collected* later, at the plan gate (Step A), so that all mutation stays behind the single confirmation.

Walk `docs/refs/` and produce a flat list of candidate entries:

- **Recurse to any depth.** Flatten every file found anywhere under `docs/refs/` — including in nested subdirectories — into one single list.
- **One entry per file**, displayed as the file's path **relative to `docs/refs/`**. So `docs/refs/foo.md` lists as `foo.md`, and `docs/refs/sub/bar.md` lists as `sub/bar.md`. Two same-named files in different subdirectories (e.g. `sub/notes.md` and `archive-notes/notes.md`) are **distinct entries**.
- **Exclude hidden/dotfiles.** Skip any entry whose filename begins with `.` (e.g. `.gitkeep`, `.DS_Store`) — it is not counted, not listed, and not a candidate. A `docs/refs/` that holds **only** dotfiles (such as a lone `.gitkeep`) therefore yields an **empty** candidate list and counts as empty.
- **Symlinks count as ordinary files** — list them like any other entry.
- **Empty subdirectories contribute nothing** — only files produce entries; bare directories do not.

If `docs/refs/` is absent or yields no candidates after the exclusions above, the candidate list is empty. An empty candidate list means Step A at the plan gate is skipped entirely (see below); refs handling stays dormant.

Computing this list here is harmless even on a nothing-to-archive run: the nothing-to-archive exit above short-circuits before the plan gate, so an empty sweep set never reaches Step A regardless of what `docs/refs/` holds.

## Plan Gate & Confirmation

Pre-flight has now computed the sweep set, the target version `N`, and the left-in-place set, and has written and moved nothing. Before moving any file, writing any snapshot, resetting `project-state.json`, or running any git operation, display **one** plan and wait for **one** explicit user confirmation. This single gate covers the entire run — there is **no separate confirmation** later for opening the PR or for any other step.

The plan gate has two parts: **Step A** (refs selection — a plan-*building* input collected before the plan is shown) and **Step B** (the plan display below plus the single confirmation). Step A gathers input only; it is **not** a confirmation. The single explicit confirmation remains the one in **Wait for confirmation** below.

### Step A — refs selection (plan-building input)

Run this step **only when both** of the following hold:

- the **sweep set is non-empty** (a real archive is happening — the nothing-to-archive exit did not fire), **and**
- the **refs candidate list** computed in pre-flight (`### Enumerate refs candidates`) is **non-empty**.

If either is empty, **skip Step A entirely** — do not display the list, do not prompt, and proceed straight to **Display the plan**. Refs handling stays dormant when there is nothing to sweep or no live references.

When Step A runs, display the candidates as a simple numbered list — one line per candidate, showing its path relative to `docs/refs/` — then **wait for the user's reply**. This step **moves no file** under `docs/refs/`; it only displays and collects a selection. Use this format (the exact wording is a build-time authoring choice; keep it to a numbered list plus a numeric/keyword reply, with no elaborate UI):

```
docs/refs/ has references. Select which to archive:
  1. foo.md
  2. sub/bar.md
  3. notes/2026-design.md
Reply with numbers to archive (e.g. 1,3 or 2-3 or all / none).
```

**Parse the reply** into a **selected refs** set (a subset of the candidate list). Accepted reply forms:

- **Comma-separated numbers** — `1,3,5` selects candidates 1, 3, and 5.
- **Inclusive ranges** — `2-4` selects candidates 2, 3, and 4.
- **Keywords** — `all` selects every candidate; `none` selects nothing.
- **Combinations** — `1,3-5,8` mixes numbers and ranges.

Keyword and empty semantics:

- **`all`** selects every candidate. If `all` appears anywhere in the reply, any other tokens in the same reply are **ignored** — the result is the full candidate list.
- **`none`** selects nothing — every reference is left live for the next cycle.
- An **empty reply** is treated the same as `none` — nothing selected.

Forgiveness (no re-prompt): silently **drop** any invalid or out-of-range token (a non-numeric token that is not a keyword, a number larger than the candidate count, a malformed range), and **honor the valid selections** from the same reply. Do **not** re-prompt. This is safe because the resolved selected-refs set is surfaced back to the user in the plan and still passes the single final confirmation below — a fat-fingered number cannot silently archive the wrong file.

The resolved **selected refs** set is the output of Step A. It is held and **fed into the plan gate**: a later build item folds it into the plan display (a "Will archive (refs)" group) and the move execution. Step A itself resolves the set and performs **no move** — collecting input only, it leaves the single confirmation below fully intact.

### Display the plan

Present the plan as plain text, in this order. Keep it scannable — group and count, do not dump every filename.

1. **Will archive** — the sweep set, **grouped with counts**, not an exhaustive per-filename list. Group by kind, for example: "the planning trio (scope, prd, spec)", "discovery / retro / open-concerns (whichever are present)", "N sprint files", "N resume files", "N root process-notes files". Show only the groups that actually have members. A one-line total file count is fine; a full filename roster is not.

   **`Will archive (refs):` group** — *only when Step A ran and resolved a non-empty selected-refs set.* List the resolved selected refs by their **flattened relative path** under `docs/refs/` (e.g. `foo.md`, `sub/bar.md`) — one per line. This folds the Step A selection into the plan so the user sees exactly which references the single confirmation below will sweep. **Omit this group entirely** when Step A was skipped or resolved to an empty selection (no refs to archive) — keeping the plan inert for the no-refs case.

2. **Left in place** — the left-in-place set computed in pre-flight: everything in `docs/` that is neither on the allow-list, nor part of the carry-forward trio, nor under `docs/archive/`. List these by name (this set is normally small, and the point is to let the user see exactly what stays behind). If it is empty, say so. Make clear that these files will **not** be moved — so the user can abort here, relocate any straggler manually, and re-run.

   **`docs/refs/` is NEVER shown here.** Exclude `docs/refs/` (and everything under it) from this "Left in place" set **outright, in all cases** — engaged, empty/absent, or partial selection. Refs are owned wholly by the refs path: selected refs surface in the `Will archive (refs):` group above; unselected refs simply stay live and are not re-surfaced here. This holds even when Step A was skipped (empty candidate list or no sweep) — `docs/refs/` still does not appear under "Left in place." Pre-feature, `docs/refs/` would have fallen into this set; it is now removed from the computation entirely, so it is never double-surfaced.

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
- **The refs selection is discarded** — the selected-refs set resolved in Step A is dropped; no reference under `docs/refs/` is moved, and nothing is retained from the selection.

The repository and `docs/` are left exactly as pre-flight found them. Report that the archive was cancelled and that nothing was changed.

## Execute the Archive (sweep, snapshots, INDEX.md)

This section runs **only after** the user has given the single explicit confirmation at the Plan Gate. Use the authoritative target version `N` and the sweep set computed in pre-flight, and the `lastCommand`, `cycleNumber`, and `currentSprint` values captured there.

Two later steps are **out of scope here and must not be performed in this section:**

- **The live `docs/project-state.json` reset** is a later section. Do **not** reset, re-key, or otherwise edit the live `docs/project-state.json` here.
- **The git steps** (branch, commit, push, PR) are a later section. Do **not** run any git operation here.

Run the steps below in **exactly this order**: create `v{N}/` → snapshot trio → move sweep set → move selected refs → generate INDEX. The order matters: the trio snapshot (step 2) **must be taken before** the later reset section touches the live `docs/project-state.json`, so the snapshot captures the cycle's true pre-reset state — including the real final `lastCommand`. Do not reorder to snapshot after the reset. The refs move (step 4) **must run after** the allow-list sweep (step 3) and **before** `INDEX.md` generation (step 5), so the swept-refs set is settled before the INDEX enumerates it.

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

Move only the files actually present in the pre-flight sweep set — do not invent or assume artifacts the cycle did not produce. After this step (and the refs move in step 4), the **only** files left in `docs/` outside `docs/archive/` are the live carry-forward trio (`backlog.md`, `sdd-feedback.md`, `project-state.json`) and any **unselected** references still live under `docs/refs/`.

### 4. Move the selected references

Move the **selected refs** set resolved at the Plan Gate (Step A) out of `docs/refs/` and into the archive. Run this step **after** the allow-list sweep (step 3) and **before** INDEX generation (step 5).

- **Engage only when the selected-refs set is non-empty.** If Step A was skipped (no sweep, or empty candidate list) or resolved to an empty selection, this step does **nothing at all** — it moves, creates, and reports nothing. Refs handling stays inert.
- **Move, not copy.** For each selected ref at `docs/refs/<relpath>`, **MOVE** it to `docs/archive/v{N}/refs/<relpath>`, preserving its relative subdirectory path under `refs/`. So `docs/refs/foo.md` → `docs/archive/v{N}/refs/foo.md`, and `docs/refs/sub/bar.md` → `docs/archive/v{N}/refs/sub/bar.md`. **Create intermediate subdirectories** under `docs/archive/v{N}/refs/` as needed.
- **After the move, the selected file no longer exists** at its original `docs/refs/` location — it is moved, not copied.
- **Unselected refs are left untouched.** Every reference the user did not select remains at its original path under `docs/refs/`. Do **not** copy, delete, rename, or otherwise act on an unselected ref — it is simply absent from the swept set.
- **Leftover empty `docs/refs/`.** If every candidate was selected, `docs/refs/` may be left empty after the moves. **Leave the empty directory in place** — this command does not remove the `docs/refs/` directory itself.

Record the set of refs **actually swept** (by flattened relative path under `refs/`) — step 5 enumerates it in `INDEX.md` and counts it toward the archived-artifacts total.

### 5. Generate `docs/archive/v{N}/INDEX.md`

Auto-generate the `INDEX.md` from the files **actually swept** (allow-list sweep in step 3 plus any references swept in step 4) plus the snapshotted trio. This is fully automatic — **do not prompt the user** for a hand-authored description or summary. Mirror the established skeleton used by prior cycles (`docs/archive/v1|v2|v3/INDEX.md`).

Write the file using this template, substituting the runtime values:

```
# docs/archive/v{N}/

This directory contains a snapshot of v{N} planning, sprint, and process
artifacts, archived at the start of the next cycle. Files here are historical
and are not modified by later cycles.

## Contents

- <auto-enumerated swept files, grouped: planning artifacts / sprint files / process notes>
- reference files: <flattened relative paths of swept refs — e.g. refs/foo.md, refs/sub/bar.md> (only when >=1 ref swept)
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
- **Contents list** — derived from the files *actually swept* (step 3 allow-list sweep and step 4 refs move), grouped so the list stays accurate regardless of which artifacts the cycle produced:
  - **planning artifacts** — the swept exact-name docs (`scope.md`, `prd.md`, `spec.md`, `discovery.md`, `retro.md`, `open-concerns.md`) that are present;
  - **sprint files** — swept `sprint-*.md` (and any `*-resume.md`);
  - **process notes** — swept `process-notes-*.md`;
  - **reference files** — the refs **actually swept** in step 4, listed one per bullet line as `reference files:` followed by their **flattened relative paths under `refs/`** (e.g. `reference files: refs/foo.md, refs/sub/bar.md`). Emit this group with **flattened per-file granularity**, not a single `refs/` summary line. **Emit it ONLY when at least one ref was swept**; omit it entirely when no ref was swept.
  List only groups that have members. Place the `reference files` group **after** the swept-file groups (planning / sprint / process notes) and **before** the fixed `project-state.json` snapshot bullet. After all swept-file groups (including reference files), include the fixed `project-state.json` and `backlog.md`/`sdd-feedback.md` snapshot bullets shown in the template.
- **`lastCommand`** — the value captured in pre-flight (pre-reset). It appears in **both** the `project-state.json` Contents bullet and the `Last command at archive:` summary bullet.
- **`Archived:`** — the current date as supplied by the running agent at runtime.
- **`Sprints:`** — the captured `currentSprint`.
- **`Artifacts archived:`** — the count of **all files actually swept**: the allow-list sweep set (step 3) **plus** every reference swept in step 4. Refs count toward this single total (all-files-archived semantics) — they are **not** tallied separately. The trio snapshots are not counted as swept artifacts.

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

Setting `lastCommand: "archive"` **here** is the deferred write promised in `## State Updates (startup exemption)` at the top of this file. Startup deliberately did **not** write `lastCommand`; it captured the existing value and held it so the snapshot (Execute step 2) and `INDEX.md` (Execute step 5) could record the cycle's true final command. Now that those have been written, the live `lastCommand` is finally set to `"archive"`. This is the **only** place `/sdd:archive` writes `lastCommand`.

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
   - the new `docs/archive/v{N}/` tree (the created directory, the snapshotted trio, the swept files, the generated `INDEX.md`, **and the new `docs/archive/v{N}/refs/...` files moved in from `docs/refs/`** — these additions are already covered by staging the `docs/archive/v{N}/` tree);
   - the moved-file **deletions** from their old paths (the swept files removed from `docs/` and from the repo root);
   - **the `docs/refs/...` deletions of the selected (moved) references** — these old paths are **outside** the `docs/archive/v{N}/` tree, so they are **not** covered by staging the archive tree and **must be staged EXPLICITLY BY PATH**. Stage only the moved (selected) refs' old paths; **unselected references are untouched working-tree files and are NEVER staged**;
   - the reset `docs/project-state.json`.

   **NEVER run `git add -A`** (or `git add .`). Stage the specific archive paths explicitly so any unrelated uncommitted work in the tree — including any unselected references still live under `docs/refs/` — stays **out** of the archive commit. Then commit with a clear message naming the archived cycle (e.g. `Archive v{N} cycle artifacts to docs/archive/v{N}/`).

3. **Push.** Push the branch to the remote (setting upstream as needed).

4. **PR-open.** Open a pull request via `gh pr create`. Capture the resulting PR url for the handoff.

### Failure handling (no rollback)

If the file archive completed but a git step fails — for example `gh` is missing or unauthenticated, no remote is configured, the push is rejected, or the branch step fails — **do not roll back.** The completed file archive (the swept `docs/archive/v{N}/`, the snapshots, the `INDEX.md`, and the reset `project-state.json`) and any git work that already succeeded (e.g. a local commit that was made before the push failed) are **left in place.**

Report the **specific failing step** and the **resulting state** — say plainly what succeeded (file archive done; branch/commit made if they were) and what failed and why — then **prompt the user how to proceed** (e.g. authenticate `gh` and re-run the PR step manually, add a remote and push, or open the PR by hand). Do not silently abandon the run and do not undo any completed work.

## Behavior Preservation & Inert Path

Refs handling is **completely inert** unless it actually engages. It engages **only** when the sweep set is non-empty **and** at least one countable (non-hidden) ref exists in the pre-flight candidate list. In **every** other case the run produces **none** of the refs surfaces: no numbered list, no selection prompt, no refs-related process note, no `reference files:` group in `INDEX.md`, and no refs counted in the archived-artifacts total. The local notes that enforce this — the Step A skip conditions (`### Step A — refs selection`), the move-step engage-gate (`### 4. Move the selected references`), and the INDEX omit (`### 5. Generate ... INDEX.md`) — are the per-site enforcement of the consolidated guarantee below.

The inert cases, mapped to their acceptance criteria:

- **`docs/refs/` absent (`bpra`).** The candidate list is empty, Step A is skipped, and the command produces no numbered list, no selection prompt, and no refs-related output or record.
- **`docs/refs/` present but zero countable files (`bprb`).** Empty, only empty subdirectories, or only hidden/dotfiles (such as a lone `.gitkeep`) all yield an empty candidate list — the exclusions in `### Enumerate refs candidates` make these count as empty. Same inertness as absent: no list, no prompt, no refs-related output or record.
- **Both inert cases preserve every non-refs surface byte-for-byte (`bprc`).** In either inert case the sweep allow-list, the living-doc trio snapshot, `INDEX.md` generation, the `project-state.json` reset, and the git steps each produce **exactly** the result they produced before this feature: no `reference files:` group in `INDEX.md`, no refs in the archived-artifacts count, and `docs/refs/` simply **absent from every surface** (it appears neither under "Will archive (refs)", nor under "Left in place", nor in the commit's staged paths). The refs feature adds nothing to a run on which it does not engage.
- **Empty sweep set (`bprd`, `bpre`).** The existing nothing-to-archive exit (`## Pre-flight > ### Nothing-to-archive exit`) **wins and short-circuits BEFORE Step A** — **even if `docs/refs/` is populated**. No numbered list is shown and no file under `docs/refs/` is moved; the command exits with zero changes.

**Refs-related output is confined to the engaged path.** A refs-related note or record (the candidate list display, the `Will archive (refs):` plan group, the `reference files:` INDEX group, the refs contribution to the count) is emitted **only when the feature actually engages** — refs listed at Step A **and** a selection resolved into the swept set. On every inert path the command emits and records **nothing** refs-related. (`/sdd:archive` is a mechanical command, exempt from the interview-style open-concerns and process-notes protocols per `## Loading > Protocol exemptions` — so this guarantee is simply the **absence** of any refs-specific output or record on an inert run. Do **not** introduce a new process-notes mechanism for this command.)

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
