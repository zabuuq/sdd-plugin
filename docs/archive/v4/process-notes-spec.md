# Process Notes — /sdd:spec (v4, /sdd:archive)

Real-time notes for the spec phase. Append-only. Four categories: decisions, pushback, difficult questions, pivots.

## Decisions

- **Cycle-number ownership (resolves open concern: "Who increments the cycle number").** `/sdd:archive` is the sole owner of `cycleNumber`. After sweeping cycle N into `docs/archive/v{N}/`, archive sets `cycleNumber` to N+1 as part of the reset. `/sdd:scope` never touches the field. Single writer → no double-increment, no stale-number race. Matches the PRD Directory Reset AC ("cycle-number field is set so the next cycle targets a fresh version").
  - Semantics: `cycleNumber` = the current working cycle.
  - Bootstrap (field absent): assume `1`; the existing "advance to next unused `v{N}`" collision check carries us to the right directory (v1–v3 occupied → lands on v4). No separate seeding step needed.
  - Bump basis: archive sets `cycleNumber` = (actual N used, after any advance) + 1, so subsequent runs read it directly without re-scanning.

- **`project-state.json` reset map (resolves open concern: "Which project-state.json fields reset vs. carry forward").** Snapshot live file into `v{N}/` byte-identical FIRST, then reset live copy:
  - `version` — carry (schema version, `1`).
  - `cycleNumber` — bump to N+1 (see above).
  - `currentSprint` — reset to `1`.
  - `buildMode` — carry (stable user preference; mirrors profile `defaultSprintMode`). *User agreed.*
  - `smallProject` — reset to `null` (new cycle re-judges, per PRD).
  - `lastCommand` — reset to `"archive"` (truthful; `/sdd:scope` overwrites on next startup).
  - `commandExplanationsShown` — rewrite to clean current command set (see below), flip all keys to `false` EXCEPT `archive`.
  - `notes` — rewrite to a fresh seed line naming the new cycle and where the prior cycle archived.

- **`commandExplanationsShown` cleanup (scope addition this cycle).** Archive's reset normalizes the object:
  - Removes the stale legacy keys `sprint`, `iterate`, `reflect` (not real commands). *User put this cleanup in scope.*
  - Adds an `archive` key. `/sdd:archive` shows a first-run blurb like every other command — but it fires **once per project/directory lifetime, not once per cycle**. So the reset leaves `archive`'s value untouched (once `true`, stays `true` across cycles); only the other keys flip back to `false`.

- **Git step shape (resolves open concern: "Git step shape — PR confirmation, branch naming, no-`gh` fallback").**
  - Branch naming: `archive-v{N}`, where N is the resolved version actually used (mirrors the manual `archive-v3` / PR #6 precedent).
  - PR-open: assume `gh pr create` (consistent with PR #6). *User: assume.* Absence/auth-failure of `gh` is a recoverable "report + prompt" failure.
  - No separate PR-open confirmation. Covered by the single upfront plan gate, which already lists the branch and the PR to be opened.
  - Repo detection: `git rev-parse --is-inside-work-tree`. Not a repo → file ops only, no git.
  - Ordering: branch → commit → push → PR-open. Any step can fail independently (gh missing → only PR fails; no remote / push rejected → push fails; etc.).
  - On any git failure after the file archive: stop at the failing step, report which step failed and the resulting state (e.g., commit left on local branch), leave the file archive and any successful git work in place, prompt how to proceed. (PRD-fixed observable; spec nails the per-step reporting.)
  - Branch collision: if `archive-v{N}` already exists, auto-suffix (`archive-v{N}-2`, …) like the directory advance, AND notify the user that the base branch already existed. *User: auto-suffix + notify.*

- **`INDEX.md` template (resolves open concern: "INDEX.md exact contents and format").** Auto-generated, structural-but-factual. Skeleton: `# docs/archive/v{N}/` title → static intro line → `## Contents` (auto-enumerated from files actually swept, grouped: planning artifacts / sprint files / process notes / living-doc snapshots; `project-state.json` and the two living-doc snapshots called out with `lastCommand` noted) → `## Cycle summary` (factual metadata bullets, not prose).
  - Contents list derived from the real swept file set, not a fixed assumption.
  - `lastCommand` recorded in both the `project-state.json` bullet and the summary (satisfies PRD AC).
  - `Archived: <date>` line kept (agent knows the date). *User: keep.*
  - `## Cycle summary` = metadata bullets (cycle N, archived date, lastCommand, sprints, artifact count). No interactive hand-authored narrative. *User: fine with metadata bullets.*

- **Post-archive handoff (resolves open concern: "Post-archive handoff / next-step").** `/sdd:archive` is out-of-pattern (with build/retro/pause), not an interview command — not bound by the interview-handoff template or the `handoffWarningShown` first-handoff explanation. Emits a one-line outcome + the two-line handoff:
  - `Cycle v{N} archived to docs/archive/v{N}/. PR opened: <url>.` (PR line conditional on the git step running; omitted/replaced on no-repo or git failure.)
  - `Run \`/clear\`, then \`/sdd:scope\` to start the next cycle.`
  - No summary/receipt block — *user: one line is fine.* The plan gate already shows what will happen pre-confirmation.
  - Spec adds `/sdd:archive` to the "Out-of-pattern commands" list in `sdd-guide/SKILL.md`.

- **Command placement, loading, protocol fit.**
  - Location `plugins/sdd/skills/archive/SKILL.md`; frontmatter `name: archive`, `description`, `disable-model-invocation: true`.
  - Registration: `plugins/sdd/.claude-plugin/plugin.json` + `.claude-plugin/marketplace.json` (versions move together); new command → version bump.
  - Loads `sdd-guide/SKILL.md` only. Does NOT load deepening-rounds / context-management / right-sizing / backlog references.
  - Follows: Output Constraints/tone, first-run command-explanation blurb (`archive` key), Guard Rails validation.
  - Exempt from: Open Concerns resolution protocol (it *moves* `open-concerns.md`), Deepening Rounds, interview-handoff template.
  - Anytime utility, no precondition guard (like `/sdd:feedback`/`/sdd:pause`); not inserted into the linear chain.
  - **Snapshot-first / startup-overwrite exemption (difficult question — correctness of the snapshot).** sdd-guide State Tracking says every command overwrites `lastCommand` on startup. Archive is **exempt**: it captures the existing `lastCommand` for the snapshot + `INDEX.md`, snapshots `project-state.json` byte-identical (untouched), and sets `lastCommand: "archive"` only later as part of the live reset. Preserves the cycle's true final command in the snapshot (matches v3 precedent showing `"retro"`). *User agreed.*

- **Execution sequence + plan gate + nothing-to-archive.** Order: validate/blurb → read+capture state → resolve N (cycleNumber, advance past existing dirs) → compute sweep set (all of `docs/` minus the live trio and `docs/archive/`, plus root `process-notes-*.md`) → nothing-to-archive check (empty sweep set → zero changes, exit) → show plan + one confirmation → on confirm: create `v{N}/` → snapshot trio byte-identical (project-state.json pre-reset) → move sweep set → generate INDEX.md → reset live project-state.json → git (branch→commit→push→PR if repo) → outcome + handoff. Decline → zero changes.
  - Plan file list: **grouped with counts** (e.g., "18 sprint files, 18 process-notes, scope/prd/spec/retro"), not exhaustive per-filename. *User: grouped.*

- **Testing strategy.** No automated tests (repo posture). Structural: `claude plugin validate .`. AC-by-AC manual verification, with destructive/git ACs exercised in a throwaway copy or disposable branch (drive then discard). Real acceptance test = the genuine end-of-v4 dogfood archive run (archives v4 → resets for v5), run only after the disposable-branch walkthrough passes. *User: matches.*

## Deepening Round 1 (quick / right-sized, 3 questions)

- **D1 — Git commit scope.** Tight staging: archive stages only its own changes (new `v{N}/` tree, the moved-file deletions from old paths, the reset `project-state.json`) — NOT `git add -A`, so unrelated uncommitted work stays out of the archive commit. Plus a **pre-flight warning** if the working tree has unrelated uncommitted changes (informational, non-blocking — user decides whether to stash first). *User: tight staging + pre-flight warning.*

- **D2 — PIVOT: sweep model flipped deny-list → allow-list.** Original PRD framing was "everything in `docs/` except the trio." User reframed: sweep only what **SDD itself creates**, to avoid archiving foreign/manual files. New model:
  - **Allow-list (swept):** exact names `docs/scope.md`, `docs/prd.md`, `docs/spec.md`, `docs/discovery.md`, `docs/retro.md`, `docs/open-concerns.md`; globs `docs/sprint-*.md`, `docs/*-resume.md`; root `process-notes-*.md`.
  - **Carry-forward (live, snapshotted, never swept):** `docs/backlog.md`, `docs/sdd-feedback.md`, `docs/project-state.json`.
  - **Consequence:** ad-hoc/project-specific cycle docs (e.g. v3's `efficiency-techniques.md`, `load-map.md`, `dogfood-comparison.md`; this repo's `v2-verification.md`) are NOT on the allow-list and would be left behind.
  - **Resolution (b):** plan gate shows TWO lists — *will archive* (allow-list matches) and *left in place* (everything else in `docs/` outside the trio and `docs/archive/`). User confirms what stays before anything moves; can abort and move stragglers manually. *User: allow-list + confirm what is left.*
  - **Spec impact:** contradicts the PRD AC wording "moves every item in `docs/` except the live carry-forward trio." Flag in spec Open Issues as a PRD edit for `/sdd:refine` — observable changes from deny-list to allow-list.

- **D3 — Stale-key normalization timing.** Define the `commandExplanationsShown` normalization (drop `sprint`/`iterate`/`reflect`, add `archive`) **once**, in archive's reset logic. Do NOT separately clean the live file during v4 build. One code path; dead keys are harmless until archive runs. *User: define the normalization logic once.*

## Spec generation + doc merge

- **Spec posture.** `docs/spec.md` written as a new-command design doc (matches the repo's lean spec posture): behavioral contract + where files land + cross-cutting edits, leaving exact SKILL.md prose to plan/build. The `project-state.json` schema change (`cycleNumber` + normalized `commandExplanationsShown`) is named for `references/living-documents.md`, not restated.
- **Component sections** map to PRD stories: Archive Command Skill, Version Resolution, Artifact Sweep, Living-Doc Snapshots, INDEX.md Generation, project-state.json Reset, Plan Gate & Confirmation, Git Integration, Handoff. Each opens with an `Implements prd.md > [Story]` cross-ref.
- **CLAUDE.md / AGENTS.md merge — surgical, not overwrite.** Both files exist and are authoritative + v3-centric. Updated the active-cycle context (v3→v4), registered `/sdd:archive`, added three archive architecture-decision blocks to AGENTS.md (allow-list sweep, cycleNumber sole-ownership, snapshot-first exemption), and refreshed stale v3-doc pointers (those docs now live in `docs/archive/v3/`). Preserved all cycle-agnostic conventions and v2/v3 history.
- **Difficult question / spec-vs-PRD tension.** The D2 allow-list pivot leaves `docs/spec.md` diverging from an approved PRD AC (Artifact Sweep "moves every item in `docs/` except the trio"). Recorded in spec Open Issues and carried as a NEW open concern targeted at `/sdd:refine` rather than silently overriding the PRD.
