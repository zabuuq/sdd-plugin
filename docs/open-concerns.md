# Open Concerns

## Open

<!--
Entries here are unresolved questions or feedback items that need attention in a future phase.

Entry format:
### [Concern title]
- **Source:** /sdd:[command] — [phase/context description]
- **Target:** /sdd:[command] (which command should resolve this, if known)
- **Concern:** [description of the concern, question, or issue]
-->

### Soft acceptance criteria for MIT distribution
- **Source:** /sdd:retro — surfaced in requirements-thinking section.
- **Target:** /sdd:refine (when items with judgment-based acceptance criteria are next refined)
- **Concern:** Several PRD acceptance criteria rely on personal judgment (e.g., "enough context to pick this up cold months later" for backlog entries). Fine for self-use, weaker for outside contributors. When refining or adding similar criteria, anchor them with example entries or anti-examples.

## Resolved

<!--
Entries here have been addressed — including concerns resolved implicitly through other decisions.

Entry format:
### [Concern title]
- **Source:** /sdd:[command]
- **Resolved by:** /sdd:[command]
- **Resolution:** [how it was resolved — include explanation for implicit resolutions]
-->

### One-time PRD AC ID backfill for this project
- **Source:** /sdd:spec — Beat 2 (PRD-AC reference tag syntax).
- **Resolved by:** /sdd:sprint (sprint-1)
- **Resolution:** Sprint-1 Item 4 backfilled unique 4-char lowercase alphabetic IDs (`aaaa` through `aahx`) onto every AC line in `docs/prd.md` — 206 ACs, all conforming to the `[a-z]{4}` pattern, all unique.

### PRD edit for smallProject detection relocation
- **Source:** /sdd:spec — Beat 4 (right-size detection).
- **Resolved by:** /sdd:sprint (sprint-1)
- **Resolution:** Sprint-1 Item 1 removed the small-project detection story from the `/sdd:discovery Command` epic and added an equivalent `/sdd:scope`-scoped story under `Interview Command Behaviors` covering Phase 1 detection, authoritative write of `smallProject` to `docs/project-state.json` at end of Phase 1, and signals defined in `references/right-sizing.md`.

### New profile fields to add to /sdd:onboard schema
- **Source:** /sdd:spec — Beats 6 and 9 (handoff warning + sprint-mode memory).
- **Resolved by:** /sdd:sprint (sprint-1)
- **Resolution:** Sprint-1 Item 2 added a new story under `/sdd:onboard Polish` enumerating both new profile fields: `handoffWarningShown: false` (seeded by /sdd:onboard) and `defaultSprintMode: null` (or omitted with semantic equivalence).

### Cross-project pattern file name in PRD
- **Source:** /sdd:spec — Round 2 Q1.
- **Resolved by:** /sdd:sprint (sprint-1)
- **Resolution:** Sprint-1 Item 3 rewrote the relevant AC under `/sdd:retro at Project Close` to name `~/.claude/sdd-cross-project-patterns.md` explicitly as the destination file, removing the "deferred to /sdd:spec" phrasing.

### AC-ID notation form
- **Source:** /sdd:spec — Beat 2 / spec write.
- **Resolved by:** /sdd:sprint (sprint-1)
- **Resolution:** Sprint-1 Item 4 adopted the strict `[a-z]{4}` form across the project: the PRD backfill used sequential alphabetic IDs, and the alphanumeric examples (`a7kp`, `b3mq`, `c1xw`) in `docs/spec.md` were replaced with valid alphabetic examples. The interview-era correction note flagging the slip was also removed since it no longer has surrounding text to correct.

### Post-sprint command ceremony
- **Source:** /sdd:retro — surfaced in the project reflection, friction-at-boundaries pattern.
- **Resolved by:** /sdd:scope (v2 Round 1 Topic 2)
- **Resolution:** `/sdd:reflect` dissolves entirely in v2. Its responsibilities split: `/sdd:build` grows a wrap-up phase (PRD checkoff, story splitting, "anything notable?" beat, tech-debt capture, write `process-notes-sprint-N.md`). `/sdd:retro` stays but only fires at project close and adds a cross-project-patterns step writing to `~/.claude/sdd-user-profile.json`. `/sdd:refine` stays as-is, recommended by `/sdd:plan` (not by reflect). Net: one cohesive close-out (in `/build`), one project-close retrospective (`/retro`) — no three stacked ceremonies.

### Global user profile location
- **Source:** /sdd:prd — Open Question carried into /spec.
- **Resolved by:** /sdd:spec
- **Resolution:** `~/.claude/sdd-user-profile.json`. Accessible from any project directory via standard file read operations.

### Per-project state file format
- **Source:** /sdd:prd — Open Question carried into /spec.
- **Resolved by:** /sdd:spec
- **Resolution:** JSON (`docs/project-state.json`). Structured, easy to parse, no human-editing use case.

### PRD size threshold
- **Source:** /sdd:prd — Open Question carried into /spec.
- **Resolved by:** /sdd:spec
- **Resolution:** 10+ epics triggers phase-splitting recommendation. 5+ unvetted items flags scope creep. Line count was rejected as too arbitrary.

### Seed file format
- **Source:** /sdd:prd — Open Question carried into /spec.
- **Resolved by:** /sdd:spec
- **Resolution:** Structured markdown with origin, idea, relevant architecture, conventions, and additional context sections. Consumed by `/sdd:scope` as starting context.

### /sdd:scope seed file detection
- **Source:** /sdd:spec — New Issues From Spec.
- **Resolved by:** /sdd:sprint (sprint-1 planning)
- **Resolution:** Convention — look for `docs/seed.md` in the project directory. If found, read it as starting context. Embedded as acceptance criterion in sprint-1 Item 5.

### Plugin-level CLAUDE.md content
- **Source:** /sdd:spec — New Issues From Spec.
- **Resolved by:** /sdd:sprint (sprint-1 planning)
- **Resolution:** Minimal pointer — instructs Claude to load sdd-guide when a command is invoked. Behavioral detail lives in sdd-guide, not the plugin CLAUDE.md. Embedded as acceptance criterion in sprint-1 Item 1.

### Communication style enforcement
- **Source:** /sdd:spec — New Issues From Spec.
- **Resolved by:** /sdd:sprint (sprint-1 planning)
- **Resolution:** sdd-guide core reads communication style from user profile and treats it as authoritative — the user's preference overrides the default tone. Embedded as acceptance criterion in sprint-1 Item 2.

### Spec/runtime duplication
- **Source:** /sdd:retro — flagged as a maintenance liability in the project reflection. Reaffirmed during v2 scoping (Round 3 Topic 1.5 δ) when discussing /spec output count.
- **Resolved by:** /sdd:spec (v2 — Beat 1)
- **Resolution:** v2 spec adopts a lean delta-and-resolution posture. `docs/spec.md` covers "what + why" (cross-references PRD epics, resolves all open concerns, lists Key Technical Decisions); "how" detail (parser rules, schemas, loading orders, behavioral text) lives in `references/*.md` files inside the plugin (`right-sizing.md`, `pause-resume.md`, `sprint-tags.md`, `context-management.md`, plus updates to v1's existing references). The runtime artifact (plugin code) is the single source of truth.

### Per-round summary file schema, location, and naming
- **Source:** /sdd:scope — Round 3 Topic 1.5 + 1.6 collapse.
- **Resolved by:** /sdd:spec (v2 — Beat 7, consolidated with pause distillation)
- **Resolution:** Consolidated with /sdd:pause resume-file schema. Per-round summary writes use the same schema, same location convention (`docs/<command>-resume.md`), and trigger when the three-tier context-management model recommends `/clear`. Single mechanism — no separate per-round summary format. Schema and quality bar owned by `references/pause-resume.md`.

### Right-size detection signals and thresholds
- **Source:** /sdd:scope — Round 3 Topic 6 closure.
- **Resolved by:** /sdd:spec (v2 — Beat 4)
- **Resolution:** Heuristic with signals listed in `references/right-sizing.md`: one primary feature, no external users, no multi-component architecture, no third-party integrations, brief or absent reference uploads, concise opening framing. No numeric thresholds. Detection placement relocated from /sdd:discovery to /sdd:scope per user pushback in the interview (PRD edit required, captured separately). Subsequent commands re-evaluate on startup. Per-command skippable beats enumerated for /scope, /prd, /spec, /plan. Thinness signal for sub-5-question rounds defined (short answers + deflections + agent framing toward closure; 2+ of these triggers a 2-3-question round when smallProject is true).

### Three-tier between-rounds recognition heuristic
- **Source:** /sdd:scope — Round 3 Topic 7 closure.
- **Resolved by:** /sdd:spec (v2 — Beat 5)
- **Resolution:** Recognition signals locked: round size, artifact-conversation gap, superseded noise, token-usage approximation (rough proxies, no exact count). Tier mapping rules in `references/context-management.md`. Failure-mode mitigation: AI self-assessment is fallible; user override upward always allowed; downward override carries a brief context-weight warning. `/clear` branch consolidates with pause-resume — agent writes a resume file before emitting the recommendation.

### End-of-command handoff message wording
- **Source:** /sdd:scope — Round 3 Topic 7 closure.
- **Resolved by:** /sdd:spec (v2 — Beat 6)
- **Resolution:** Standard two-line form locked: one-line outcome summary + the literal Run-/clear-then-next-command sentence. First-handoff explanatory paragraph prepended once per user (tracked via `handoffWarningShown` on user profile). Next-command map enumerated for every interview command. Out-of-pattern commands (/build, /retro, /pause, /unpause, /feedback) explicitly handled. Template lives inline in sdd-guide/SKILL.md.

### docs/v2-verification.md granularity and columns
- **Source:** /sdd:scope — Round 3 Topic 2 closure.
- **Resolved by:** /sdd:spec (v2 — Beat 9)
- **Resolution:** 6-column markdown table — ID / Source / Summary / Shipped (sprint/item) / Verified / Notes. FB-NNN three-digit-padded IDs assigned at /sdd:spec finalization seeding. Write triggers: /spec seeds; /build wrap-up writes Shipped + Notes on close-sprint for items carrying `[FB: ...]` tags; Verified is manual-only; /retro doesn't touch. Sdd-plugin-specific gate keyed on file presence. New `[FB: FB-NNN]` sprint-item tag mirrors `[PRD: ...]` format.

### File uploads at non-discovery workflow points
- **Source:** /sdd:scope — Phase 1 triage carried as "investigate and decide."
- **Resolved by:** /sdd:spec (v2 — Beat 8)
- **Resolution:** Decision: shared `docs/refs/` directory; readers limited to /sdd:discovery (PRD-required) and /sdd:spec (architectural reference context). No interactive upload UI anywhere; user manages the directory between commands. Late-arriving refs after /discovery are not retroactively incorporated into earlier-command outputs — if they substantively change shape, that's a re-scope event.

### Pause distillation quality bar
- **Source:** /sdd:scope — Round 2 Topic 6 closure.
- **Resolved by:** /sdd:spec (v2 — Beat 7)
- **Resolution:** Per-section quality bar defined in `references/pause-resume.md`. Required sections: Status / Setup-already-done / Confirmed decisions / Round status / Pending decision / Files for next-session / Instructions for next-session. Distill-not-dump rules: no verbatim conversation, no agent reasoning trail, no re-explanation of plugin behavior, cross-reference don't restate. Length target 1–3 pages of markdown.

### PRD-AC reference tag syntax
- **Source:** /sdd:prd — Phase 2 Round 1 Topic A closure.
- **Resolved by:** /sdd:spec (v2 — Beat 2)
- **Resolution:** ID-based, 4-char lowercase alphabetic (`[a-z]{4}`). Assigned at PRD-write time by /sdd:prd and /sdd:refine; collision-checked against existing PRD before settling. Inline in backticks at start of AC text. Sprint tag format `[PRD: id1, id2]` for mapped (comma-separated for multi-AC items) or `[unmapped]` for not. Stable across reorders. No drift detection, no embedded snippets. Specific parser regex documented in `references/sprint-tags.md`. One-time backfill for this project's existing `docs/prd.md` tracked as a separate open concern.

### Iteration-candidate marker syntax
- **Source:** /sdd:prd — Phase 2 Round 1 Topic B closure.
- **Resolved by:** /sdd:spec (v2 — Beat 3)
- **Resolution:** Single class `[iteration-candidate]` at start-of-line + free-prose description until next blank line or next marker. Optional inline `(item: a7kp)` sprint-item reference for readers, not parser-enforced. No severity field. Tech-debt, quality concerns, and surprises surface at wrap-up's "anything notable?" prompt — not as in-flight markers. Parser regex documented in `references/sprint-tags.md`. Fabrication prohibited per PRD; agent writes the marker only on observed real candidates.

### Definition of "clean state" for /sdd:build revert
- **Source:** /sdd:retro — carried over from migration of the hackathon-plugin reflection.
- **Resolved by:** /sdd:spec (v2 — Beat 12)
- **Resolution:** Clean state = the most recent user commit on the current branch. /sdd:build does NOT auto-commit. On stop-revert-revise: with `gitPreference: true` the agent proposes `git reset --hard HEAD` after warning about uncommitted changes and waiting for explicit go-ahead; with `gitPreference: false` the agent provides a manual rollback summary (files changed + what to revert to where). Auto-checkpoint per item explicitly rejected as feature expansion outside v2's streamlining scope.

### /sdd:polish re-open mechanism
- **Source:** /sdd:prd — Sprint Loop epic, AC deferred to /sdd:spec.
- **Resolved by:** /sdd:spec (v2 — Beat 10)
- **Resolution:** Manifest-based, user-driven, surgical un-check. Close-sprint writes a `[close-sprint-manifest]` block to `process-notes-sprint-N.md` recording PRD ACs checked, story-split mappings, iteration-candidate dispositions, tech-debt entries, and v2-verification rows updated. Re-open reads the most recent manifest, prompts the user to select which ACs to un-check, reverses corresponding splits, appends a `[sprint-reopened]` marker. Edge cases (partial un-split, /refine-added ACs to split-off stories, /refine-deleted ACs, multi-cycle close-reopen) documented in spec.

### feedbackLocalPath path normalization
- **Source:** /sdd:prd — Cross-Project Feedback Transfer epic, AC deferred to /sdd:spec.
- **Resolved by:** /sdd:spec (v2 — Beat 11)
- **Resolution:** Six-step normalization: resolve to absolute, resolve symlinks, normalize separators to forward slashes, strip trailing slash, case-fold on Windows and macOS (preserve case on Linux), descendant check via equals-or-startsWith-path-plus-slash. Edge cases: unset/null path, missing target, file-not-directory, symlink loops, macOS case-sensitive APFS. Symlink-loop or resolve errors fall back to literal string comparison.

## Deferred

<!--
Entries here have been intentionally postponed with rationale.

Entry format:
### [Concern title]
- **Source:** /sdd:[command]
- **Deferred by:** /sdd:[command]
- **Rationale:** [why it was deferred and when it should be revisited]
-->
