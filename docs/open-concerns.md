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

### Definition of "clean state" for /sdd:build revert
- **Source:** /sdd:retro — carried over from migration of the hackathon-plugin reflection.
- **Target:** /sdd:refine or /sdd:spec (when the build skill is next revised)
- **Concern:** The spec covers stop-revert-revise on build failures but doesn't define the revert anchor precisely. What counts as a "clean state"? Last commit? Last green test? Last `/sdd:build` checkpoint? Worth pinning down before the next sprint loop.

### Spec/runtime duplication
- **Source:** /sdd:retro — flagged as a maintenance liability in the project reflection. Reaffirmed during v2 scoping (Round 3 Topic 1.5 δ) when discussing /spec output count.
- **Target:** /sdd:spec (v2 spec phase)
- **Concern:** v1's `docs/spec.md` (935 lines) partially restated content that lives inside the plugin (`references/context-loading.md`, `references/living-documents.md`). When the spec *is* the runtime artifact, duplication compounds. v2 spec passes should compress to "what + why" and let in-plugin reference files own the "how." User confirmed willingness to dig deeper at spec-design time.

### Soft acceptance criteria for MIT distribution
- **Source:** /sdd:retro — surfaced in requirements-thinking section.
- **Target:** /sdd:refine (when items with judgment-based acceptance criteria are next refined)
- **Concern:** Several PRD acceptance criteria rely on personal judgment (e.g., "enough context to pick this up cold months later" for backlog entries). Fine for self-use, weaker for outside contributors. When refining or adding similar criteria, anchor them with example entries or anti-examples.

### Per-round summary file schema, location, and naming
- **Source:** /sdd:scope — Round 3 Topic 1.5 + 1.6 collapse.
- **Target:** /sdd:spec
- **Concern:** Per-round summary writes were referenced during context-management design but specifics were deferred. Need to pin: filename convention, location (`docs/`?), schema (does it reuse the `/sdd:pause` resume schema, or is it lighter?), who reads it after a `/clear`+re-run. Potential consolidation with `/sdd:pause` resume schema.

### Right-size detection signals and thresholds
- **Source:** /sdd:scope — Round 3 Topic 6 closure.
- **Target:** /sdd:spec
- **Concern:** Locked the *behavior* (interview commands recognize small projects and right-size rounds/questions/ceremony). The exact detection signals and thresholds were deferred — the right shape is a small heuristic, not a numeric rule. Specify at spec time: what signals (Phase 1 length, feedback-item count, in-scope deliverable count, project domain hints), how the "small" hint propagates downstream, how it interacts with the 5/10 question cap.

### Three-tier between-rounds recognition heuristic
- **Source:** /sdd:scope — Round 3 Topic 7 closure.
- **Target:** /sdd:spec
- **Concern:** Locked the three-tier model (continue / /compact / /clear) and definite-recommendation framing. The recognition heuristic needs concrete signals at spec time: round length, artifact-vs-conversation coverage, noise ratio (superseded back-and-forth), token-usage approximation. Also: AI self-assessment of context weight is imperfect — historical overload happened unnoticed. Mitigation language (user can always override upward) is locked; explicit failure modes worth documenting.

### End-of-command handoff message wording
- **Source:** /sdd:scope — Round 3 Topic 7 closure.
- **Target:** /sdd:spec
- **Concern:** Between commands, default `/clear` with explicit instruction is locked. The exact wording the agent emits (what does the handoff message say, does it include the next command's name, does it warn first-time users about losing context) is spec-time.

### `docs/v2-verification.md` granularity and columns
- **Source:** /sdd:scope — Round 3 Topic 2 closure.
- **Target:** /sdd:spec
- **Concern:** New artifact locked as the v2-release audit trail. Schema (1:1 row per feedback item vs grouped, columns: feedback ref / status / verification command run / notes / date), write trigger (implementing command writes the row at build time? at PR time?), and exact format are spec-time.

### File uploads at non-discovery workflow points
- **Source:** /sdd:scope — Phase 1 triage carried as "investigate and decide."
- **Target:** /sdd:spec
- **Concern:** Round 3 Topic 1 confirmed file uploads land in `/discovery` (Shape C). Whether they should also appear at `/scope`, `/prd`, `/spec`, or `/plan` was flagged for investigation rather than decided. Spec phase: investigate user need (do uploads at other points serve a real flow?) and decide.

### Pause distillation quality bar
- **Source:** /sdd:scope — Round 2 Topic 6 closure.
- **Target:** /sdd:spec
- **Concern:** `/sdd:pause` use-case 2 (context-rot mitigation via pause → /clear → unpause) requires the resume file to seed a zero-context model — not just a status snapshot. Pause must distill, not dump. Spec phase: define the quality bar concretely (what must be in the file, what shouldn't be, length guidance, what counts as "good enough to seed cold").

### PRD-AC reference tag syntax
- **Source:** /sdd:prd — Phase 2 Round 1 Topic A closure.
- **Target:** /sdd:spec
- **Concern:** Locked the *mechanism* — every sprint-item line carries either a PRD-AC reference tag or an explicit `[unmapped]` tag, validated by `/sdd:plan` before sprint finalization, consumed by `/sdd:build` wrap-up for deterministic checkoff. Exact tag syntax deferred to spec. Open questions: positional refs (e.g., `[PRD: Epic 3 / Story 2 / AC 4]`) vs ID-based (assign stable IDs to ACs at PRD-write time and ref by ID), how to handle PRD AC renumbering (e.g., when /sdd:refine reorders), whether the tag should embed a short text snippet for human readability + drift detection, whether one sprint item can carry multiple `[PRD: ...]` tags (for items that satisfy multiple AC at once) or whether multi-AC items must be split. Decision drives parser behavior in both `/sdd:plan` validation and `/sdd:build` wrap-up checkoff.

### Iteration-candidate marker syntax
- **Source:** /sdd:prd — Phase 2 Round 1 Topic B closure.
- **Target:** /sdd:spec
- **Concern:** Locked the *mechanism* — agent writes a structured marker into `process-notes-sprint-N.md` at the moment of observation during `/sdd:build` execution; wrap-up reads only entries carrying the marker; marker existence before wrap-up began is the testable bar for "came up during execution." Exact marker syntax deferred to spec. Open questions: marker prefix format (e.g., `[iteration-candidate: ...]` vs a dedicated section header vs a YAML-style metadata block), required vs optional fields (description, severity, related-checklist-item ref), whether the marker should also support a "tech-debt-candidate" or "surprise" variant or whether disposition is purely a wrap-up concern, how the parser distinguishes marker entries from ordinary prose in process notes. Decision drives parser behavior in `/sdd:build` wrap-up's candidate surfacing.

## Resolved

<!--
Entries here have been addressed — including concerns resolved implicitly through other decisions.

Entry format:
### [Concern title]
- **Source:** /sdd:[command]
- **Resolved by:** /sdd:[command]
- **Resolution:** [how it was resolved — include explanation for implicit resolutions]
-->

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

## Deferred

<!--
Entries here have been intentionally postponed with rationale.

Entry format:
### [Concern title]
- **Source:** /sdd:[command]
- **Deferred by:** /sdd:[command]
- **Rationale:** [why it was deferred and when it should be revisited]
-->
