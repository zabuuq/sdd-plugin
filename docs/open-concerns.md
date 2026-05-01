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

### Post-sprint command ceremony
- **Source:** /sdd:retro — surfaced in the project reflection, friction-at-boundaries pattern.
- **Target:** /sdd:scope or /sdd:refine (next-phase redesign of the sprint-loop commands)
- **Concern:** `/sdd:reflect` (two-phase) + recommend `/sdd:refine` + recommend `/sdd:retro`, plus `/sdd:refine` as a separate compressed-interview step, is heavy when nothing notable happened in a sprint. Consider a fast-path skip or collapsing into a single more flexible command. Already being acted on via the SDD-redesigning-SDD loop.

### Spec/runtime duplication
- **Source:** /sdd:retro — flagged as a maintenance liability in the project reflection.
- **Target:** /sdd:spec (next major revision)
- **Concern:** `docs/spec.md` (935 lines) partially restates content that lives inside the plugin (`references/context-loading.md`, `references/living-documents.md`). When the spec *is* the runtime artifact, duplication compounds. Future spec passes should compress to "what + why" and let in-plugin reference files own the "how."

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
