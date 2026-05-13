# v2 Verification

Sdd-plugin-specific audit-trail artifact for the v2 release. One row per in-scope feedback item from `docs/scope.md`'s "In-scope deliverables" section. Persists after v2 ships as a historical record.

## Verification approach

**Primary dogfood.** Jason runs v2 end-to-end on a small website project covering `/sdd:discovery` through `/sdd:retro`. This is the principal verification path and exercises every in-the-chain command (`/sdd:onboard`, `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:build`, `/sdd:polish`, `/sdd:refine`, `/sdd:retro`). Most in-scope feedback items get their end-to-end exercise here.

**Supplementary projects.** Other in-progress projects in varying completion states cover items the primary dogfood won't reach because a small build doesn't naturally produce the relevant signal. The supplementary projects must cover, at minimum:

- **Cross-machine pause** — `/sdd:pause` on one machine, `/sdd:unpause` on another, end-to-end resume verified.
- **The 10-question deepening-round cap** — a project where a deepening round legitimately hits 10 questions and the agent surfaces the cap with an explicit permission ask.
- **Per-round `/clear`-and-continue** — a round long enough that the three-tier recommendation legitimately resolves to `/clear`, the user runs it, and the next round picks up cleanly.
- **Cross-project pattern capture written by `/sdd:retro`** — `/sdd:retro` reaches its cross-project-patterns step at project close and writes preferences to `~/.claude/sdd-user-profile.json`, then a subsequent `/sdd:onboard` reads them back.
- **Longer deepening rounds** — interview rounds whose length and density exercise the right-size signals in the opposite direction from the small-project bias.

**Explicit pause/unpause exercise.** `/sdd:pause` and `/sdd:unpause` are deliberately exercised across these projects even when not strictly required for the work, so the resume-file schema (Status / Setup-already-done / Confirmed decisions / Round status / Pending decision / Files for next-session / Instructions for next-session) and round-trip behavior get verified end-to-end. Distill-not-dump quality is checked: the resume file must successfully seed a zero-context model.

**Pillar 1 mapping.** "Verified end-to-end at least once" satisfies Pillar 1 of `docs/scope.md`'s "What 'done' looks like" definition — specifically: "Every triaged-in-scope feedback item has shipped and been exercised end-to-end at least once." See `docs/scope.md > What "done" looks like`. The `Verified` column in the table below is the per-item record of Pillar 1 satisfaction; Pillar 1 closes when every row is checked.

The `Shipped (sprint/item)` column is auto-written by `/sdd:build` wrap-up when a sprint item carrying a `[FB: FB-NNN]` tag closes. The `Verified` column is manually checked by Jason after end-to-end exercise — no SDD command writes a checked verified box.

## Feedback items

| ID | Source | Summary | Shipped (sprint/item) | Verified | Notes |
|----|--------|---------|-----------------------|----------|-------|
| FB-001 | Event Planning App | `/sdd:feedback` never updates `lastCommand` (back-to-back feedback bug) | sprint 8, item 1 | `[ ]` | |
| FB-002 | fiver-gigs | PRD state-tracking checks off non-split sprint items at `/sdd:build` wrap-up | | `[ ]` | |
| FB-003 | sdd-plugin self-use | New `/sdd:prd` command owns `docs/prd.md` (non-tech + tech requirements) | | `[ ]` | |
| FB-004 | sdd-plugin self-use | New `/sdd:pause` distills in-flight command into `<command>-resume.md` (dogfooded schema, distill-not-dump) | | `[ ]` | |
| FB-005 | sdd-plugin self-use | New `/sdd:unpause` reads `lastCommand`, loads resume file + source SKILL, picks up (no flag/path/name) | | `[ ]` | |
| FB-006 | sdd-plugin self-use | Pause file cleanup: resumed command deletes its own resume file when underlying work completes | | `[ ]` | |
| FB-007 | Event Planning App, fiver-gigs | Clarifying questions never advance the interview (hold position, no agent-substituted answers) | | `[ ]` | |
| FB-008 | fiver-gigs | 5 suggested / 10 hard cap questions per round; past 10 requires explicit user permission | | `[ ]` | |
| FB-009 | Event Planning App | Definite end-of-round recommendation with reasoning (never a blank "continue?" prompt) | | `[ ]` | |
| FB-010 | Event Planning App | Deepening-round previews: agent previews topics the next round would cover | | `[ ]` | |
| FB-011 | sdd-plugin self-use | Right-size detection across interview commands with small-project hint propagated downstream | | `[ ]` | |
| FB-012 | fiver-gigs | Interview commands actively prompt for additional user input during scope work | | `[ ]` | |
| FB-013 | sdd-plugin self-use | File uploads supported during `/sdd:discovery` (designated location, agent reads as context) | | `[ ]` | |
| FB-014 | Event Planning App, fiver-gigs | Between-rounds three-tier context model (continue / `/compact` / `/clear`) with definite recommendation + reasoning | | `[ ]` | |
| FB-015 | sdd-plugin self-use | Between-commands default-`/clear` handoff message (plugin emits, user runs) | | `[ ]` | |
| FB-016 | sdd-plugin self-use | Recognition heuristic signals for round-length / artifact-vs-conversation coverage / noise ratio | | `[ ]` | |
| FB-017 | sdd-plugin self-use | `/sdd:onboard` communication-style question is fully open-ended (no bulleted example menu) | | `[ ]` | |
| FB-018 | sdd-plugin self-use | `/sdd:onboard` surfaces preferences location (`~/.claude/sdd-user-profile.json`) and re-run updates them | | `[ ]` | |
| FB-019 | sdd-plugin self-use | `/sdd:onboard` ends with heads-up that `/sdd:discovery` is an interview, not a one-shot | | `[ ]` | |
| FB-020 | sdd-plugin self-use | `/sdd:onboard` new beat for `/sdd:feedback` (what / when / where notes go) | | `[ ]` | |
| FB-021 | sdd-plugin self-use | `/sdd:onboard` new optional `feedbackLocalPath` question (skip-friendly) | | `[ ]` | |
| FB-022 | sdd-plugin self-use | `/sdd:onboard` informational paragraph on third-party plugin auto-update mechanics (information, not advocacy) | | `[ ]` | |
| FB-023 | sdd-plugin self-use | `/sdd:onboard` update flow surfaces retro-written cross-project pattern preferences alongside originals | | `[ ]` | |
| FB-024 | sdd-plugin self-use | `/sdd:plan` purely planning: reads previous sprint notes, surfaces carry-forward, PRD state check, plans sprint, validates every line has `[PRD: ...]` or `[unmapped]`, next-command recommendation | | `[ ]` | |
| FB-025 | sdd-plugin self-use | Sprint-mode memory: `/sdd:plan` asks "use last sprint's mode?" with "always use this mode" toggle (`defaultSprintMode` in profile) | | `[ ]` | |
| FB-026 | sdd-plugin self-use | `/sdd:build` wrap-up phase: satisfaction check, close (PRD checkoff, story-split, tech-debt capture, sprint notes), re-open escape hatch | | `[ ]` | |
| FB-027 | sdd-plugin self-use | `/sdd:retro` fires only at project close (when all PRD criteria checked); no longer recommended per-sprint | | `[ ]` | |
| FB-028 | sdd-plugin self-use | `/sdd:retro` cross-project-patterns step writes preferences to `~/.claude/sdd-user-profile.json` for future `/sdd:onboard` to read | | `[ ]` | |
| FB-029 | sdd-plugin self-use | New `feedbackLocalPath` field in `~/.claude/sdd-user-profile.json`, collected during `/sdd:onboard` (skip-friendly) | | `[ ]` | |
| FB-030 | sdd-plugin self-use | `/sdd:feedback` writes locally AND forwards to `<feedbackLocalPath>/docs/sdd-feedback.md` when set | sprint 8, item 2 | `[ ]` | |
| FB-031 | sdd-plugin self-use | `/sdd:feedback` inside-clone detection: cwd == `feedbackLocalPath` (or descendant) → local write IS the forward, no double-write | sprint 8, item 3 | `[ ]` | |
| FB-032 | sdd-plugin self-use | `/sdd:feedback` failure handling: invalid path → save locally, warn once per session, never lose note, no retry | sprint 8, item 4 | `[ ]` | |
| FB-033 | sdd-plugin self-use | All four planning commands (`/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`) and `/sdd:plan` write to `docs/backlog.md` on user-confirmed deferral | | `[ ]` | |
| FB-034 | sdd-plugin self-use | Rejected-idea drops live in process notes only (no second tracker) | | `[ ]` | |
| FB-035 | sdd-plugin self-use | Backlog entry schema: Title / What / Why deferred / Trigger to revisit / Dependencies / Source | | `[ ]` | |
| FB-036 | sdd-plugin self-use | In-flight planning docs use pure-reference model for deferred items (no inline restatement) | | `[ ]` | |
| FB-037 | sdd-plugin self-use | Planning notes split per phase: `process-notes-discovery.md`, `process-notes-scope.md`, `process-notes-prd.md`, `process-notes-spec.md`; sprint notes stay `process-notes-sprint-N.md` | | `[ ]` | |
| FB-038 | sdd-plugin self-use | `/sdd:retro` reads process notes selectively | | `[ ]` | |
| FB-039 | sdd-plugin self-use | No within-phase compaction of process notes (`/sdd:pause` covers in-flight summarization needs) | | `[ ]` | |
| FB-040 | sdd-plugin self-use | `MIGRATION-v1-to-v2.md` ships in the plugin with the agreed user-facing framing (no intermediate-shuffle narrative) | | `[ ]` | |
| FB-041 | sdd-plugin self-use | Migration guide includes state-aware migration table keyed on v1 `lastCommand` with mid-flight vs completed columns (handles dead `/sdd:reflect` two-case) | | `[ ]` | |
| FB-042 | sdd-plugin self-use | Migration guide covers resume-file rename rules (`sprint-resume.md` → `plan-resume.md`, `iterate-resume.md` → `polish-resume.md`, `reflect-resume.md` → no v2 equivalent) | | `[ ]` | |
| FB-043 | sdd-plugin self-use | Atomic rename with no command-name aliases; migration guide states this explicitly | | `[ ]` | |
| FB-044 | sdd-plugin self-use | Full v2-rename sweep on active material (skills, commands, sdd-guide, README, live `docs/` prose); `docs/archive/v1/` untouched with one-line pre-rename snapshot note on the archive index | | `[ ]` | |
