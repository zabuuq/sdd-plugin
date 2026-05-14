# Sprint 18

**Theme:** v1→v2 Migration Guide — closes the 21 remaining ACs in the v1→v2 Migration epic (`aago`–`aahi`). Final sprint of the v2 development cycle. Authors `MIGRATION-v1-to-v2.md` at the plugin root (file existence + narrative sections + state-aware migration table), verifies the archive index note already on disk, and sweeps the live codebase for stray v1 command names.

**Tangible outcome:** After this sprint:
- `MIGRATION-v1-to-v2.md` exists at the plugin root containing: opening framing (what v2 changes vs. v1), rename pairs (`/sdd:sprint` → `/sdd:plan`, `/sdd:iterate` → `/sdd:polish`), `/sdd:scope` narrowing statement, resume-file rename instructions, legacy `process-notes.md` handling guidance + optional one-time split recommendation, archive-untouched statement, dedicated sections introducing `/sdd:pause`/`/sdd:unpause` and `feedbackLocalPath`, statement that `/sdd:discovery` is a genuinely new command, statement that v1 command names are not aliased in v2, and a state-aware migration table keyed on v1 `lastCommand` values.
- The migration table uses the locked 5-column schema: `v1 lastCommand | Stay on (mid-flight) | Next v2 command (after completion) | One-time action | Notes`. Includes a row for every v1 command name (per `aags`), with `/sdd:reflect` getting two rows for the mid-reflect and post-reflect cases (per `aagt`).
- `docs/archive/v1/INDEX.md` is confirmed to satisfy `aahb` (already on disk: "This directory contains a pre-rename snapshot of v1 planning artifacts. Files here are historical and not modified by v2."). Verify-and-check; no edits expected.
- Codebase sweep confirms no v1 command names (`/sdd:sprint`, `/sdd:iterate`, `/sdd:reflect`) appear in the live plugin surface or live `docs/` prose outside explicit migration-context references. Any drift fixed in place. `docs/archive/v1/` is untouched by the sweep.
- The v1→v2 Migration epic closes in full (21 ACs). After this sprint, all 5 v2 epics close and v2 is feature-complete. Project moves to GA / release-prep posture.

**Out of scope:**
- Soft acceptance criteria for MIT distribution (carried open concern, target `/sdd:refine`). Not addressable in a migration-guide sprint — needs a refinement pass.
- Any v3+ migration concerns. This sprint is v1→v2 only.
- Edits to `docs/archive/v1/` content beyond the already-present index note (the archive is hard-excluded from the sweep per `aahh`).
- CHANGELOG or release-notes authoring. The migration guide is user-facing migration instructions; a release CHANGELOG (if added later) is separate.

**Activation caveat:** Same as every prior v2 sprint. Edits to disk under `plugins/sdd/` and to repo-level files do not change live agent behavior until the cached marketplace plugin is reinstalled. The migration guide is authored for users who will land on v2 *post*-reinstall; the guide itself describes the transition they're about to undergo. The sweep audits the on-disk source of truth.

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** Four items; later items build on earlier work.

1. Item 1 (author MIGRATION-v1-to-v2.md narrative sections) — lands the file and the bulk of the prose first. Item 2 layers the table onto an existing file rather than creating it.
2. Item 2 (state-aware migration table) — largest single sub-deliverable; gets focused attention after the narrative shell is in place.
3. Item 3 (verify archive index note) — trivial verify-and-check against existing `docs/archive/v1/INDEX.md` content.
4. Item 4 (v1 command name sweep) — runs last so the sweep can validate against the newly-authored migration file as the legitimate exception locus.

## Checklist

### Item 1: Author MIGRATION-v1-to-v2.md narrative sections `[PRD: aago, aagp, aagq, aagv, aagw, aagx, aagy, aagz, aaha, aahc, aahd, aahe, aahi]`

- **PRD ref:** `prd.md > v1 → v2 Migration` (stories 1, 3, 4, 5, 6 — file existence + opening framing + rename mechanics + legacy process-notes handling + new-capability introductions + no-alias statement) `[PRD: aago, aagp, aagq, aagv, aagw, aagx, aagy, aagz, aaha, aahc, aahd, aahe, aahi]`
- **Spec ref:** `spec.md > Beat 6 (end-of-command handoff)` (next-command map), `spec.md > Beat 7 (pause distillation)` (resume file schema for the rename-pair guidance), `references/right-sizing.md` and `references/sprint-tags.md` (v2 names referenced throughout), plus the v2 scope's "what changed" framing in `docs/scope.md`.
- **What to build:** Create `MIGRATION-v1-to-v2.md` at the plugin root (i.e., `plugins/sdd/MIGRATION-v1-to-v2.md` — the file lives alongside the plugin, not under `docs/`). Author every narrative section the AC set requires; Item 2 adds the migration table on top of this shell.

  1. **File existence (`aago`).** Create `plugins/sdd/MIGRATION-v1-to-v2.md`. Project convention is plugin-root-adjacent migration docs; the `plugin root` reference in the AC means `plugins/sdd/`.
  2. **Opening framing (`aagp`).** Open with a clear, honest statement of what changed v1 → v2:
     - `/sdd:discovery` is added at the start of the planning chain (new command).
     - `/sdd:scope` is narrower — boundary-drawing only; exploration moved to `/sdd:discovery`.
     - `/sdd:prd` and `/sdd:spec` are unchanged.
     - `/sdd:sprint` is renamed to `/sdd:plan`.
     - `/sdd:iterate` is renamed to `/sdd:polish`.
     - `/sdd:reflect` is removed (responsibilities absorbed into `/sdd:build` wrap-up + `/sdd:retro`).
     - New utilities `/sdd:pause` and `/sdd:unpause` for multi-session resume.
  3. **No intermediate-renaming framing (`aagq`).** The opening framing must not present an intermediate proposal in which `/sdd:scope` and `/sdd:prd` were renamed in tandem. v2 went straight from the v1 names to the current v2 names; do not document a path that did not ship.
  4. **Rename pairs section (`aagv`).** A dedicated section documenting the two rename pairs:
     - `/sdd:sprint` → `/sdd:plan`
     - `/sdd:iterate` → `/sdd:polish`
  5. **Resume-file rename instructions (`aagw`).** Enumerate the resume-file renames explicitly:
     - `docs/sprint-resume.md` → `docs/plan-resume.md`
     - `docs/iterate-resume.md` → `docs/polish-resume.md`
     - If neither file exists at v2 migration time, no action is needed (the `aagw` AC text says "when such files exist").
     - Resume file content carries over unchanged — the schema is identical across v1 → v2.
  6. **`/sdd:scope` narrowing statement (`aagx`).** State explicitly that `/sdd:scope` is narrower in v2: it draws boundaries only. Exploration content that v1's `/sdd:scope` mixed in has moved to `/sdd:discovery`.
  7. **Legacy `process-notes.md` handling (`aagy`).** State that any legacy single-file `process-notes.md` from a v1 project is treated as a read-only historical reference. v2 commands do not write to it. v2's per-phase process-notes files (`process-notes-discovery.md`, `process-notes-scope.md`, etc.) live alongside.
  8. **Optional one-time split recommendation (`aagz`).** Include an optional one-time recommendation for converting a legacy `process-notes.md` into per-phase files. Frame it as optional, not required — v2 commands work fine without the split (legacy file stays read-only; new entries go to per-phase files).
  9. **Archive-untouched statement (`aaha`).** State that `docs/archive/v1/` is left untouched by v2. Anything a v1 project archived stays as-is. v2 commands do not modify the archive.
  10. **`/sdd:pause` and `/sdd:unpause` introduction (`aahc`).** A dedicated section introducing the pause/unpause utility pair: what they do (capture/resume in-flight interview state across sessions), when to use them (mid-interview when context is fading or switching machines), how they relate to the resume-file convention (`docs/<command>-resume.md`). The section is introductory, not a full spec — point to `references/pause-resume.md` for the schema and quality bar.
  11. **`feedbackLocalPath` introduction (`aahd`).** A dedicated section introducing the `feedbackLocalPath` profile field: what it's for (cross-project feedback transfer — notes written in user projects also forward to the plugin clone for upstream review), how it's set (`/sdd:onboard`), what happens when unset (`/sdd:feedback` writes only locally; no warning).
  12. **`/sdd:discovery` is genuinely new (`aahe`).** Explicit statement that `/sdd:discovery` is a brand-new command, not a rename of any v1 command. v1 implicitly mixed discovery into `/sdd:scope`; v2 separates them.
  13. **No-alias statement (`aahi`).** Explicit statement that v1 command names are not aliased in v2. Running `/sdd:sprint` in v2 fails (no command); the user must invoke `/sdd:plan`. Same for `/sdd:iterate` (→ `/sdd:polish`) and `/sdd:reflect` (no replacement; absorbed into `/sdd:build` wrap-up + `/sdd:retro`).

  **Tone:** Match the project's existing voice — direct, no ceremony, no sycophancy. Reader is a v1 user landing on v2; assume they read the v1 docs at some point and want to know what changed and what to do. Keep sections short and concrete.

  **Section ordering:** Build's call within the spec. A reasonable default: opening framing → rename pairs → `/sdd:scope` narrowing → resume-file renames → legacy `process-notes.md` handling + optional split → archive-untouched statement → migration table (Item 2 fills this section) → new-capability sections (`/sdd:pause`/`/sdd:unpause`, `feedbackLocalPath`, `/sdd:discovery`-is-new) → no-alias statement. Build picks whichever ordering reads cleaner.

- **Acceptance criteria:**
  - [x] `aago` The plugin ships a file named `MIGRATION-v1-to-v2.md` at the plugin root.
  - [x] `aagp` The opening framing of `MIGRATION-v1-to-v2.md` states: v2 adds `/sdd:discovery` at the start of the planning chain; `/sdd:scope` is narrower (boundary only — exploration moved to `/sdd:discovery`); `/sdd:prd` and `/sdd:spec` are unchanged; `/sdd:sprint` is renamed to `/sdd:plan`; `/sdd:iterate` is renamed to `/sdd:polish`; `/sdd:reflect` is removed; new utilities `/sdd:pause` and `/sdd:unpause`.
  - [x] `aagq` The migration guide does not present an intermediate renaming framing in which `/sdd:scope` and `/sdd:prd` were renamed in tandem.
  - [x] `aagv` The migration guide documents the rename pairs `/sdd:sprint` → `/sdd:plan` and `/sdd:iterate` → `/sdd:polish`.
  - [x] `aagw` The migration guide instructs users to rename v1 resume files to their v2 names when such files exist.
  - [x] `aagx` The migration guide includes a statement that `/sdd:scope` is narrower in v2 (exploration moved to `/sdd:discovery`).
  - [x] `aagy` The migration guide states that legacy `process-notes.md` is treated as a read-only historical reference.
  - [x] `aagz` The migration guide includes an optional one-time split recommendation for converting legacy `process-notes.md` into per-phase files.
  - [x] `aaha` The migration guide states that `docs/archive/v1/` is left untouched by v2.
  - [x] `aahc` `MIGRATION-v1-to-v2.md` contains a section introducing `/sdd:pause` and `/sdd:unpause`.
  - [x] `aahd` `MIGRATION-v1-to-v2.md` contains a section introducing the `feedbackLocalPath` profile field and its purpose.
  - [x] `aahe` `MIGRATION-v1-to-v2.md` contains a section stating that `/sdd:discovery` is a genuinely new command (not a rename of any v1 command).
  - [x] `aahi` `MIGRATION-v1-to-v2.md` explicitly states that v1 command names are not aliased in v2.
- **Verification:**
  - Confirm `plugins/sdd/MIGRATION-v1-to-v2.md` exists on disk.
  - Read the opening framing paragraph(s) and confirm all six v1→v2 transition statements from `aagp` are present.
  - Grep the file for `/sdd:prd` and `/sdd:scope` in tandem with "renamed" — expected: zero hits (per `aagq`).
  - Confirm a section names the two rename pairs (`/sdd:sprint` → `/sdd:plan`, `/sdd:iterate` → `/sdd:polish`).
  - Confirm a section names the two resume-file renames with the "when such files exist" qualifier.
  - Confirm the `/sdd:scope` narrowing statement.
  - Confirm the legacy `process-notes.md` read-only-reference statement and the optional one-time split recommendation.
  - Confirm the archive-untouched statement.
  - Confirm dedicated sections introducing `/sdd:pause`/`/sdd:unpause`, `feedbackLocalPath`, and `/sdd:discovery`-is-new.
  - Confirm the no-alias statement.
  - Confirm the 13 PRD ACs are now checked in `docs/prd.md`.
- **Status:** [x] Complete

---

### Item 2: Add state-aware migration table to MIGRATION-v1-to-v2.md `[PRD: aagr, aags, aagt, aagu]`

- **PRD ref:** `prd.md > v1 → v2 Migration` (story 2 — state-aware migration table) `[PRD: aagr, aags, aagt, aagu]`
- **Spec ref:** `spec.md > Beat 6 (end-of-command handoff)` (next-command map by v1 lastCommand value).
- **What to build:** Add a state-aware migration table to `MIGRATION-v1-to-v2.md`. Locked schema and content per Sprint 18 planning Round 1 (see `process-notes-sprint-18.md > Round 1 decisions > Q1, Q2`).

  **Column schema (5 columns):**

  | v1 `lastCommand` | Stay on (mid-flight) | Next v2 command (after completion) | One-time action | Notes |

  Column semantics:
  - **v1 `lastCommand`:** the literal value the user has in `docs/project-state.json` at v2 migration time.
  - **Stay on (mid-flight):** the v2 command to invoke if the user was partway through the v1 command when v2 landed. For most commands, this equals the v2 equivalent (rename or unchanged). For `/sdd:reflect`, the mid-flight value diverges from the post-completion value — see the two reflect rows below.
  - **Next v2 command (after completion):** the v2 command to invoke after the v1 command completes cleanly. Follows the v2 chain (`/sdd:onboard` → `/sdd:discovery` → `/sdd:scope` → … → `/sdd:retro`).
  - **One-time action:** any file rename or migration step required at the transition. Resume-file renames go here.
  - **Notes:** explanatory context — what changed, what to expect, anything subtle.

  **Rows required (per `aags`, every v1 command name appears; per `aagt`, reflect gets two rows):**

  V1 chain command names: `/sdd:onboard`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:sprint`, `/sdd:build`, `/sdd:iterate`, `/sdd:reflect`, `/sdd:refine`, `/sdd:retro`, plus utilities `/sdd:feedback`. Optional: `/sdd:pause`/`/sdd:unpause` (v2-only, but worth a "not applicable" row for completeness — Build's call). Plus an unset/empty `lastCommand` row.

  Row content guidance:
  - **`/sdd:onboard`:** unchanged in v2. Stay on `/sdd:onboard`. Next: `/sdd:discovery` (new). Action: none. Notes: v2 adds `/sdd:discovery` to the chain after onboard.
  - **`/sdd:scope`:** stay on `/sdd:scope`. Next: `/sdd:prd`. Action: none. Notes: `/sdd:scope` is narrower in v2 — boundary-drawing only. If exploration content is still pending, run `/sdd:discovery` before re-running `/sdd:scope`.
  - **`/sdd:prd`:** stay on `/sdd:prd`. Next: `/sdd:spec`. Action: none. Notes: unchanged.
  - **`/sdd:spec`:** stay on `/sdd:spec`. Next: `/sdd:plan`. Action: none. Notes: unchanged. Successor in the chain renamed from `/sdd:sprint` to `/sdd:plan`.
  - **`/sdd:sprint` (renamed):** stay on `/sdd:plan`. Next: `/sdd:build`. Action: rename `docs/sprint-resume.md` to `docs/plan-resume.md` if it exists. Notes: v1's `/sdd:sprint` is now `/sdd:plan`. v1 names are not aliased.
  - **`/sdd:build`:** stay on `/sdd:build`. Next: `/sdd:polish` (renamed from iterate) or `/sdd:plan` (next sprint) or `/sdd:retro` (project close), depending on wrap-up branch. Action: none. Notes: `/sdd:build` grew a wrap-up phase in v2 absorbing `/sdd:reflect`'s responsibilities.
  - **`/sdd:iterate` (renamed):** stay on `/sdd:polish`. Next: `/sdd:build`. Action: rename `docs/iterate-resume.md` to `docs/polish-resume.md` if it exists. Notes: v1's `/sdd:iterate` is now `/sdd:polish`. v1 names are not aliased.
  - **`/sdd:reflect` (mid-flight, removed) — row 1 per `aagt`:** stay on `/sdd:build` if the sprint isn't closed (build's wrap-up absorbed reflect's PRD-checkoff + tech-debt-capture responsibilities) OR `/sdd:plan` if the sprint was already closed before reflect started. Next: depends on the absorbing path. Action: none. Notes: `/sdd:reflect` does not exist in v2. Any in-flight reflect work is abandoned; the absorbing command picks up the responsibilities.
  - **`/sdd:reflect` (post-completion, removed) — row 2 per `aagt`:** stay on (not applicable — already complete). Next: `/sdd:plan` for the next sprint. Action: none. Notes: anything reflect wrote to disk in v1 persists as historical record. v2 does not re-run or alter it.
  - **`/sdd:refine`:** stay on `/sdd:refine`. Next: `/sdd:plan`. Action: none. Notes: unchanged.
  - **`/sdd:retro`:** stay on `/sdd:retro`. Next: project terminal (no next command). Action: none. Notes: `/sdd:retro` only fires at project close in v2 and adds a cross-project-pattern-capture step. The retro-per-sprint posture from v1 is gone.
  - **`/sdd:feedback`:** stay on `/sdd:feedback`. Next: returns to the prior `lastCommand`'s next step (feedback is instant-return). Action: none. Notes: in v2, `/sdd:feedback` does not clobber `lastCommand` (v1 bug fixed). `feedbackLocalPath` profile field enables cross-project forwarding.
  - **`/sdd:pause` / `/sdd:unpause`:** v2-only commands. If a v1 user has these as their `lastCommand` value, they're already in v2 — no migration needed. Build can omit these rows or include them with a "v2-only — no migration applicable" note.
  - **Unset / empty `lastCommand`:** starting fresh. Run `/sdd:onboard` (first-time user) or `/sdd:discovery` (returning user who has already onboarded). Action: none. Notes: covers brand-new projects and users who never recorded a command.

  Build is empowered to refine row text for clarity; the locked elements are the column schema, the row coverage (every v1 command name including reflect mid/post), and the mid-reflect/post-reflect content captured in Round 1 Q2.

- **Acceptance criteria:**
  - [x] `aagr` `MIGRATION-v1-to-v2.md` contains a state-aware migration table keyed on v1 `lastCommand` values.
  - [x] `aags` The table contains a row for every v1 command name that could appear as `lastCommand`, including the now-removed `/sdd:reflect`.
  - [x] `aagt` The table contains separate rows for "mid-reflect" and "post-reflect" cases, each naming a v2 command to invoke next.
  - [x] `aagu` Each row names the v2 command to invoke next and any one-time action required (e.g., resume-file rename).
- **Verification:**
  - Read the table header row and confirm it matches the 5-column schema.
  - Walk the row list and confirm every v1 command name appears at least once: `/sdd:onboard`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:sprint`, `/sdd:build`, `/sdd:iterate`, `/sdd:reflect` (×2 for mid + post), `/sdd:refine`, `/sdd:retro`, `/sdd:feedback`. Unset/empty also covered.
  - Confirm the two reflect rows are distinct in their Stay-on column (mid points to `/sdd:build`-or-`/sdd:plan`; post is not applicable / already complete).
  - Confirm every row has a non-empty Next v2 command (or an explicit "not applicable / terminal" value where appropriate) and a One-time action (or "none").
  - Confirm the 4 PRD ACs are now checked in `docs/prd.md`.
- **Status:** [x] Complete

---

### Item 3: Verify archive index note `[PRD: aahb]`

- **PRD ref:** `prd.md > v1 → v2 Migration` (story 4 — legacy process notes + archive policy) `[PRD: aahb]`
- **Spec ref:** None directly; `docs/archive/v1/` is a project convention documented in `AGENTS.md` and reinforced by `aahh` (archive untouched by sweep).
- **What to build:** Verify-and-check pass against `docs/archive/v1/INDEX.md`. The file already contains: "This directory contains a pre-rename snapshot of v1 planning artifacts. Files here are historical and not modified by v2." This satisfies `aahb` directly.

  No edits are expected. If the verify pass finds the file content has drifted from the satisfying text, Build patches it back to a one-line clarifying note. Otherwise, the only action is checking off `aahb` in `docs/prd.md`.

  This item exists as a separate sprint entry rather than folded into Item 1 because:
  - It maps to a single AC and a single file outside the migration guide itself.
  - The action is verification-only on the expected path; bundling it with Item 1's authoring would muddy the verification step.
  - Keeping it separate makes the AC-to-item mapping audit-clean.

- **Acceptance criteria:**
  - [x] `aahb` The index file inside `docs/archive/v1/` contains a one-line note clarifying that the archive contains a pre-rename snapshot of v1 planning artifacts.
- **Verification:**
  - Read `docs/archive/v1/INDEX.md` and confirm the content satisfies `aahb` (a clarifying note about the pre-rename snapshot of v1 planning artifacts).
  - If satisfied as-is, no file edit. If drifted, restore to a one-line clarifying note.
  - Confirm the 1 PRD AC is now checked in `docs/prd.md`.
- **Status:** [x] Complete

---

### Item 4: v1 command name sweep across codebase `[PRD: aahf, aahg, aahh]`

- **PRD ref:** `prd.md > v1 → v2 Migration` (story 6 — no aliases + consistent v2 naming + archive untouched) `[PRD: aahf, aahg, aahh]`
- **Spec ref:** None directly; sweep scope and exception heuristic locked in Sprint 18 planning Round 1 (see `process-notes-sprint-18.md > Round 1 decisions > Q4, Q5`).
- **What to build:** Grep-based audit across the live plugin surface and live `docs/` prose for v1 command names. Three v1 names to sweep: `/sdd:sprint`, `/sdd:iterate`, `/sdd:reflect`. For each hit, classify as legitimate (migration-context reference) or stale (must be fixed).

  **Sweep scope (locked in Round 1 Q4):**

  *Included:*
  - `plugins/sdd/skills/**/*.md` — all SKILL.md, reference files, templates.
  - `plugins/sdd/README.md` (if present).
  - `plugins/sdd/.claude-plugin/*.json` — manifest and marketplace JSON.
  - `docs/discovery.md`, `docs/scope.md`, `docs/prd.md`, `docs/spec.md`, `docs/v2-verification.md`, `docs/backlog.md`, `docs/open-concerns.md`.
  - `AGENTS.md` at repo root.
  - `CLAUDE.md` at repo root.
  - `docs/sdd-feedback.md` if present.

  *Hard-excluded:*
  - `docs/archive/v1/**` — per `aahh`, the archive is untouched by the sweep. Legitimate v1 references inside the archive are not violations.
  - `plugins/sdd/MIGRATION-v1-to-v2.md` — migration-context by definition; v1 names are required there.
  - `docs/sprint-1.md` through `docs/sprint-N.md` — historical sprint planning records. Treated as decision logs.
  - `process-notes-*.md` files at repo root — append-only decision history.

  **Aliases to confirm absent (per `aahf`):**
  - No file under `plugins/sdd/` defines a `/sdd:sprint`, `/sdd:iterate`, or `/sdd:reflect` command alias (e.g., no SKILL.md frontmatter naming these as command names; no commands directory entries; no marketplace registration).

  **Migration-context exception heuristic (locked in Round 1 Q5):**

  A v1 name is legitimate when it appears in:
  1. Historical/comparative prose — "v1 used `/sdd:sprint`; v2 uses `/sdd:plan`" or similar v1/v2-contrast framing.
  2. Quoted v1 feedback notes in `docs/sdd-feedback.md`.
  3. Backlog entries referencing v1 origins.

  A v1 name is **not** legitimate if it appears as:
  - A live behavioral instruction ("when `/sdd:sprint` runs, do X").
  - A current command-chain reference.
  - An unqualified mention that implies v1 names are still active.

  **Build steps:**

  1. Run three greps across the included file set:
     - `\/sdd:sprint` (escape the slash for grep regex; match the literal command name including the leading slash).
     - `\/sdd:iterate`.
     - `\/sdd:reflect`.
  2. For each grep, capture every hit (file path + line + matched line text) in `process-notes-sprint-18.md` under an audit section.
  3. For each hit, apply the heuristic. Classify as legitimate or stale.
  4. For stale hits: rewrite to the v2 equivalent. `/sdd:sprint` → `/sdd:plan`; `/sdd:iterate` → `/sdd:polish`; `/sdd:reflect` → absorbed-into-build-wrap-up wording (the right replacement depends on what the sentence was claiming; not always a 1-to-1 substitution).
  5. Re-grep after fixes. Stale hits should drop to zero. Legitimate hits stay logged as audit trail.
  6. Confirm `docs/archive/v1/**` was untouched (no edits inside the archive during the sweep).
  7. Confirm no SKILL.md or commands/ entry registers a v1 command name as an active alias.
  8. Check off `aahf`, `aahg`, `aahh` in `docs/prd.md`.

- **Acceptance criteria:**
  - [x] `aahf` v2 ships with no command alias for `/sdd:sprint`, `/sdd:iterate`, or `/sdd:reflect`.
  - [x] `aahg` Every file under `skills/`, `commands/`, the plugin root README, and live prose under `docs/` (excluding `docs/archive/v1/`) uses v2 command names consistently, with no v1 command name appearing except in explicit migration-context references.
  - [x] `aahh` `docs/archive/v1/` is left untouched by the v2 migration sweep.
- **Verification:**
  - Three grep commands recorded in `process-notes-sprint-18.md` with their hit counts and classifications.
  - Re-grep after any fixes; stale hit count is zero.
  - Inspect `plugins/sdd/` for any SKILL.md, command-registration, or marketplace entry that names a v1 command — expected: none.
  - Inspect `docs/archive/v1/` directory state (file count and modification timestamps of contained files) — expected: no edits during sprint execution.
  - Confirm the 3 PRD ACs are now checked in `docs/prd.md`.
- **Status:** [x] Complete

---

## Notes

- **Closes the v1 → v2 Migration epic in full.** 21 ACs (`aago`, `aagp`, `aagq`, `aagr`, `aags`, `aagt`, `aagu`, `aagv`, `aagw`, `aagx`, `aagy`, `aagz`, `aaha`, `aahb`, `aahc`, `aahd`, `aahe`, `aahf`, `aahg`, `aahh`, `aahi`). Pre-Sprint-18 unchecked count: 21 ACs in this epic only. Post-Sprint-18 projection: **zero unchecked ACs across the entire v2 PRD.** All 5 v2 epics close with this sprint.
- **PRD checkoff watch.** Per Sprints 11-17 discipline: sprint close must propagate AC checkoffs to `docs/prd.md` immediately. Sprint 18 follows the same pattern.
- **No FB mapping.** None of `aago`–`aahi` correspond to a row in `docs/v2-verification.md`. The v1→v2 Migration epic is GA-eve documentation, not v1-feedback-derived. No `[FB: ...]` tags on items; `/sdd:build` wrap-up writes no v2-verification row updates for this sprint. Same posture as Sprints 16 and 17.
- **Doc-authoring sprint.** Items 1, 2, 3 are pure markdown authoring/verification on file paths the spec already names. Item 4 is grep audit with conditional surgical fixes. Risk profile is low; the locked Round 1 decisions remove the major ambiguities.
- **Topics deferred to `/sdd:build` context** (in case they surface during implementation):
  - Item 1: section ordering within the migration guide. Default ordering listed in the item; Build picks whatever reads cleanly.
  - Item 1: tone calibration — match existing project voice (direct, no ceremony). Worth a brief re-read of `docs/spec.md` or the sdd-guide SKILL.md to pick up the cadence.
  - Item 2: optional `/sdd:pause`/`/sdd:unpause` rows in the migration table. Build's call whether to include or omit.
  - Item 2: row formatting for cells that need multiple lines (e.g., `/sdd:build`'s Next column has three conditional branches). Use multi-line cells or split into sub-rows — Build picks whichever reads cleaner without breaking markdown table rendering.
  - Item 4: if a stale hit lands in a file that's also being edited by Item 1 or 2 (e.g., a leftover `/sdd:sprint` reference in `docs/spec.md`), the fix is in-place during Item 4; no need to retroactively edit Item 1's output.
  - Item 4: the three grep terms must match the literal command names including the leading `/sdd:` prefix to avoid false positives (e.g., the word "sprint" alone hits everywhere; `/sdd:sprint` is the safe pattern).
- **Cached marketplace plugin is still v1.** All Sprint 18 edits ship to disk. The migration guide is read by users *after* they install v2; it's written against final-state v2 behavior. The sweep audits on-disk v2 source of truth. First real exercise of the guide requires the v2 marketplace flip — which is the GA event Sprint 18 enables.
- **Post-Sprint-18 posture:** v2 is feature-complete. Remaining work to ship v2 (out of scope for this sprint): bump plugin version in `plugins/sdd/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`; tag a release; cut over the marketplace. None of these are PRD ACs — they're release mechanics, handled outside the sprint loop.
