# Migrating from SDD v1 to SDD v2

This guide is for users with an existing v1 project state (`docs/project-state.json`, planning artifacts, sprint files, resume files, or legacy `process-notes.md`) who want to continue their work on v2.

## What changed in v2

v2 adds `/sdd:discovery` at the start of the planning chain. `/sdd:scope` is narrower (boundary only — exploration moved to `/sdd:discovery`). `/sdd:prd` and `/sdd:spec` are unchanged. `/sdd:sprint` is renamed to `/sdd:plan`. `/sdd:iterate` is renamed to `/sdd:polish`. `/sdd:reflect` is removed (its work was absorbed into `/sdd:build` wrap-up and `/sdd:retro`). New utilities: `/sdd:pause` and `/sdd:unpause`.

Everything else — PRD acceptance criteria IDs, spec structure, sprint-tag conventions, the `docs/backlog.md` append-only log — carries over without change.

## State-aware migration table

Find the value of `lastCommand` in your `docs/project-state.json`. Use the row below to decide which v2 command to invoke next. The **Stay on (mid-flight)** column applies when the v1 command had not finished its phase; the **Next v2 command (after completion)** column applies when it had. The **One-time action** column lists any file rename or migration step required at the transition.

| v1 `lastCommand` | Stay on (mid-flight) | Next v2 command (after completion) | One-time action | Notes |
|---|---|---|---|---|
| `/sdd:onboard` | `/sdd:onboard` | `/sdd:discovery` | none | v2 adds `/sdd:discovery` to the chain after onboard. Onboarding is single-session in practice. |
| `/sdd:scope` | `/sdd:scope` | `/sdd:prd` | none | `/sdd:scope` is narrower in v2 — boundary-drawing only. If exploration content is still pending, run `/sdd:discovery` before re-running `/sdd:scope`. |
| `/sdd:prd` | `/sdd:prd` | `/sdd:spec` | none | Unchanged in v2. |
| `/sdd:spec` | `/sdd:spec` | `/sdd:plan` | none | Unchanged in v2. Successor in the chain renamed from `/sdd:sprint` to `/sdd:plan`. |
| `/sdd:sprint` | `/sdd:plan` (renamed) | `/sdd:build` | Rename `docs/sprint-resume.md` → `docs/plan-resume.md` if it exists. | v1's `/sdd:sprint` is now `/sdd:plan`. v1 names are not aliased. |
| `/sdd:build` | `/sdd:build` | `/sdd:polish` (sprint polish), `/sdd:plan` (next sprint), or `/sdd:retro` (project close) — depends on wrap-up branch. | none | `/sdd:build` grew a wrap-up phase in v2 that absorbed `/sdd:reflect`'s responsibilities. |
| `/sdd:iterate` | `/sdd:polish` (renamed) | `/sdd:build` | Rename `docs/iterate-resume.md` → `docs/polish-resume.md` if it exists. | v1's `/sdd:iterate` is now `/sdd:polish`. v1 names are not aliased. |
| `/sdd:reflect` (mid-flight) | `/sdd:build` if the sprint isn't closed (build's wrap-up absorbed reflect's PRD-checkoff + tech-debt-capture responsibilities); `/sdd:plan` if the sprint was already closed before reflect started. | Depends on the absorbing path — follow the absorbing command's Next column. | none | `/sdd:reflect` does not exist in v2. Any in-flight reflect work is abandoned; the absorbing command picks up the responsibilities. |
| `/sdd:reflect` (post-completion) | n/a (already complete) | `/sdd:plan` for the next sprint. | none | Anything reflect wrote to disk in v1 persists as historical record. v2 does not re-run or alter it. |
| `/sdd:refine` | `/sdd:refine` | `/sdd:plan` | none | Unchanged in v2. |
| `/sdd:retro` | `/sdd:retro` | Project terminal (no next command). | none | `/sdd:retro` only fires at project close in v2 and adds a cross-project-pattern-capture step. The per-sprint retro posture from v1 is gone. |
| `/sdd:feedback` | `/sdd:feedback` | Returns to the prior `lastCommand`'s next step (feedback is instant-return). | none | In v2, `/sdd:feedback` does not clobber `lastCommand` (v1 bug fixed). `feedbackLocalPath` profile field enables cross-project forwarding. |
| unset / empty `lastCommand` | n/a | `/sdd:onboard` (first-time user) or `/sdd:discovery` (returning user who already onboarded). | none | Covers brand-new projects and users who never recorded a command. |

## Renamed commands and resume files

Two commands were renamed in v2. The behavior is the same; only the command name changed.

- `/sdd:sprint` → `/sdd:plan`
- `/sdd:iterate` → `/sdd:polish`

If your project has v1 resume files in `docs/`, rename them to their v2 equivalents before invoking the corresponding v2 command:

| v1 resume file | v2 resume file |
|---|---|
| `sprint-resume.md` | `plan-resume.md` |
| `iterate-resume.md` | `polish-resume.md` |
| `reflect-resume.md` | (no v2 equivalent — review the notes, then enter `/sdd:build` to use its wrap-up) |

Rename the file on disk before continuing. v2 will not look under the v1 filename.

Additionally, **`/sdd:scope` is narrower in v2**. In v1, `/scope` covered both boundary-setting and open exploration. In v2, exploration moved to `/sdd:discovery`, and `/sdd:scope` focuses on boundary only. If you want exploratory notes captured for a v1 project carrying into v2, run `/sdd:discovery` before re-entering `/sdd:scope`.

## Legacy `process-notes.md`

If your project has a v1-style single `docs/process-notes.md` file, treat it as a **read-only historical reference**. v2 does not append to it, and v2 commands do not read from it.

**Optional one-time recommendation:** if you want process notes to remain a living artifact in v2, split the legacy file into per-phase files (`process-notes-discovery.md`, `process-notes-scope.md`, `process-notes-prd.md`, `process-notes-spec.md`) by copying the relevant sections into each. This is a one-time manual operation and is not required — leaving the legacy file alone is fully supported.

## `docs/archive/v1/`

If your project has `docs/archive/v1/` (the v1 planning-artifact archive), v2 leaves it untouched. The v2 active-prose migration sweep does not modify anything under `docs/archive/v1/`. It is preserved as historical record.

## New v2 capabilities

### `/sdd:pause` and `/sdd:unpause`

v2 introduces two new utility commands for suspending and resuming long-running command sessions.

- **`/sdd:pause`** — invoke during any in-flight `/sdd:*` command. It distills the current conversation into `docs/<command>-resume.md` (for example, `docs/plan-resume.md` when paused mid-`/sdd:plan`) and then instructs you to run `/sdd:unpause` later to continue.
- **`/sdd:unpause`** — reads `lastCommand` from `docs/project-state.json`, loads the corresponding resume file, and picks up where the paused command left off.

These replace the v1 pattern of relying on each command to detect its own resume file on re-entry. In v2 the pause/resume mechanism is explicit and uniform across all command phases.

### `feedbackLocalPath` profile field

`~/.claude/sdd-user-profile.json` gains a new optional field, `feedbackLocalPath`. When set, `/sdd:feedback` forwards notes to `<feedbackLocalPath>/docs/sdd-feedback.md` in addition to writing them locally in the current project.

This is opt-in. `/sdd:onboard` offers to set the field during onboarding and is skip-friendly — if you decline, `/sdd:feedback` continues to work exactly as before, writing only to the current project's feedback file.

### `/sdd:discovery`

`/sdd:discovery` is a **genuinely new command in v2** — it is not a rename of any v1 command. It sits between `/sdd:onboard` and `/sdd:scope` in the planning chain.

`/sdd:discovery` is open-ended exploration. Users can upload reference files (PDFs, images, supporting docs) to `docs/refs/` and discuss the problem space freely. The command writes `docs/discovery.md`, which is **frozen at the start of `/sdd:scope`** — discovery becomes read-only context once scope begins.

For v1 projects carrying into v2, running `/sdd:discovery` is optional. The chain still works if you skip directly from `/sdd:onboard` (or from your v1 state) to `/sdd:scope`.

## No aliases

v1 command names — `/sdd:sprint`, `/sdd:iterate`, `/sdd:reflect` — are **NOT aliased in v2**. There is no fallback that maps v1 names to v2 commands. Invoking `/sdd:sprint` in a v2 environment will not resolve; the user must invoke `/sdd:plan` instead. Likewise for `/sdd:iterate` → `/sdd:polish` and for the removed `/sdd:reflect`.

v1 command names appear in this migration guide solely for documented migration-context purposes: the rename pairs above, the rows of the state-aware migration table, and this no-alias statement. They are not directives — nothing in v2 expects a user to type a v1 command name at the prompt.
