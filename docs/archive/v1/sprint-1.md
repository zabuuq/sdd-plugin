# Sprint 1

> Migrated from the hackathon plugin's `checklist.md`. All 16 items shipped in autonomous mode and were verified via three downstream test projects plus a marketplace publish to `zabuuq/sdd-plugin`. PRD references are inferred mappings; some items (scaffolding, README) are infrastructure that doesn't map to a single PRD epic.

## Build Preferences
- **Build mode:** autonomous
- **Comprehension checks:** N/A (autonomous)
- **Git:** Commit after each item with message: "Complete step N: [title]"
- **Verification:** Checkpoints every 3-4 items for user review
- **Check-in cadence:** N/A (autonomous)

## Resolved Open Issues (carried in from /spec)

The following open issues from `spec.md > New Issues From Spec` were resolved during sprint planning and embedded as acceptance criteria below:

- **Seed file detection:** `/sdd:scope` checks for `docs/seed.md` by convention. → Item 5
- **Plugin-level CLAUDE.md content:** Minimal thin pointer — loads sdd-guide. → Item 1
- **Communication style enforcement:** sdd-guide core reads style from user profile and treats it as authoritative. → Item 2

## Checklist

### 1. Plugin scaffolding — directory structure, manifests, plugin CLAUDE.md
- **PRD ref:** `prd.md > Cross-Cutting: Living Documents` (infrastructure to support all commands)
- **Spec ref:** `spec.md > Plugin Repository Structure`, `spec.md > Plugin Manifest`
- **What to build:** Create the full plugin directory tree (`sdd-plugin/plugins/sdd/skills/` with subdirs for sdd-guide, each command, references, and templates). Write `marketplace.json` at the repo root under `.claude-plugin/`. Write `plugin.json` at `plugins/sdd/.claude-plugin/`. Write the plugin-level `CLAUDE.md` as a minimal pointer that instructs Claude to load sdd-guide when a command is invoked.
- **Acceptance criteria:**
  - [x] Directory structure matches `spec.md > Plugin Repository Structure`
  - [x] `marketplace.json` and `plugin.json` match the schemas in `spec.md > Plugin Manifest`
  - [x] Plugin CLAUDE.md is a thin pointer, not a second source of behavioral truth
  - [x] Frontmatter in all files uses correct field names and values
- **Verification:** Run `claude --plugin-dir ./plugins/sdd` and confirm the plugin loads without errors. Visually inspect tree against spec.
- **Status:** [x] Complete

---

### 2. sdd-guide shared core + reference files
- **PRD ref:** `prd.md > Cross-Cutting: Living Documents`, `prd.md > Cross-Cutting: Process Notes`, `prd.md > Cross-Cutting: Command Explanations`
- **Spec ref:** `spec.md > Shared Behavior: sdd-guide`
- **What to build:** Write `sdd-guide/SKILL.md` covering tone, interaction rules, guard rails, command chain, command explanations, process notes, state tracking, open-concerns behavior. Write all four reference files: `references/deepening-rounds.md`, `references/context-loading.md`, `references/living-documents.md`, `references/open-concerns.md`.
- **Acceptance criteria:**
  - [x] sdd-guide core covers all behavioral points from `spec.md > sdd-guide Core (SKILL.md)`
  - [x] Each reference file covers its full section from the spec
  - [x] sdd-guide core instructs reading communication style from user profile and treating it as authoritative
  - [x] Frontmatter has `user-invocable: false`
- **Verification:** Read each file and confirm spec-defined behaviors are present. Cross-check the context-loading table against the per-command loading rules.
- **Status:** [x] Complete

---

### 3. open-concerns template
- **PRD ref:** `prd.md > Cross-Cutting: Living Documents` (template support)
- **Spec ref:** `spec.md > Reference: open-concerns.md`, `spec.md > Data Model > open-concerns.md`
- **What to build:** Write `sdd-guide/templates/open-concerns-template.md` defining the three-section structure (Open, Resolved, Deferred) with the entry format for each section.
- **Acceptance criteria:**
  - [x] Template matches the `open-concerns.md` schema
  - [x] All three sections (Open, Resolved, Deferred) are present with correct entry fields
  - [x] Format is clear enough for any command skill to read and update consistently
- **Verification:** Compare the template side-by-side with the spec's open-concerns schema.
- **Status:** [x] Complete

---

### 4. /sdd:onboard command
- **PRD ref:** `prd.md > Onboarding`
- **Spec ref:** `spec.md > Command Skills > /sdd:onboard`
- **What to build:** Write `skills/onboard/SKILL.md`. Behavior: profile check, full SDD workflow explanation, workflow-explanation preference, git preferences, communication style with examples and "other" option, write user profile JSON.
- **Acceptance criteria:**
  - [x] SKILL.md loads sdd-guide core
  - [x] All six behavior steps from spec are covered
  - [x] User profile JSON schema matches spec
  - [x] Loading behavior matches the context-loading reference table
  - [x] Frontmatter permissions match actual behavior (disable-model-invocation: true)
- **Verification:** Run `/sdd:onboard` in a test project; confirm `~/.claude/sdd-user-profile.json` is created with valid JSON containing all expected fields.
- **Status:** [x] Complete

---

**Checkpoint: paused for user review after items 1-4.**

---

### 5. /sdd:scope command + scope template
- **PRD ref:** `prd.md > Scoping a Project`
- **Spec ref:** `spec.md > Command Skills > /sdd:scope`
- **What to build:** Write `skills/scope/SKILL.md` and `sdd-guide/templates/scope-template.md`. Behavior: first-run blurb, git repo check, workflow overview, create docs/ + project-state.json, interview, project size assessment, deepening rounds, generate scope.md, init open-concerns.md, append to process-notes.md. Implement seed file detection (`docs/seed.md`).
- **Acceptance criteria:**
  - [x] SKILL.md loads sdd-guide core + deepening-rounds + user profile
  - [x] All 10 behavior steps are present
  - [x] Scope template matches reference implementation structure
  - [x] Seed file detection is explicit
  - [x] Loading behavior matches context-loading reference
- **Verification:** Confirm SKILL.md path references, template sections, and seed file logic.
- **Status:** [x] Complete

---

### 6. /sdd:prd command + prd template
- **PRD ref:** `prd.md > Defining Product Requirements`
- **Spec ref:** `spec.md > Command Skills > /sdd:prd`
- **What to build:** Write `skills/prd/SKILL.md` and `sdd-guide/templates/prd-template.md`. Behavior: first-run blurb, address /prd-targeted concerns, walk scope section by section, organize as user stories in epics with stable headings, testable checkboxes, surface edge cases, monitor epic count, deepening rounds, generate prd.md.
- **Acceptance criteria:**
  - [x] SKILL.md loads sdd-guide core + deepening-rounds + scope.md
  - [x] Prerequisites: docs/scope.md must exist
  - [x] PRD structure matches `spec.md > /sdd:prd > Structure` (epics as ##, stories as list items, checkboxes, Unvetted section)
  - [x] Loading behavior matches context-loading reference
- **Verification:** Confirm prerequisites gate on scope.md, template includes Unvetted section, loading declarations match.
- **Status:** [x] Complete

---

### 7. /sdd:spec command + spec template
- **PRD ref:** `prd.md > Technical Specification`
- **Spec ref:** `spec.md > Command Skills > /sdd:spec`
- **What to build:** Write `skills/spec/SKILL.md` and `sdd-guide/templates/spec-template.md`. Behavior: tech preferences, deployment, web search, architecture mapped to PRD epics, file structure, data flow, testing strategy, multi-service boundaries, deepening rounds, generate spec.md, generate/merge CLAUDE.md and AGENTS.md.
- **Acceptance criteria:**
  - [x] SKILL.md loads sdd-guide core + deepening-rounds + scope.md + prd.md
  - [x] Prerequisites: scope.md and prd.md must exist
  - [x] All 16 behavior steps present
  - [x] CLAUDE.md and AGENTS.md generation includes merge logic
  - [x] Spec structure requirements encoded (every component gets its own heading, cross-reference PRD epics, full file tree, data flow, granular subsections)
- **Verification:** Confirm prerequisites gate on both files, merge logic present, granular subsection structure enforced.
- **Status:** [x] Complete

---

### 8. /sdd:sprint command + sprint template
- **PRD ref:** `prd.md > Sprint Planning`
- **Spec ref:** `spec.md > Command Skills > /sdd:sprint`
- **What to build:** Write `skills/sprint/SKILL.md` and `sdd-guide/templates/sprint-template.md`. Behavior: first-run blurb (only on very first /sprint), read PRD for incomplete + unvetted, recommend /refine, flag scope creep, propose grouping by dependencies/relatedness/tangible milestones, build mode choice (locked for sprint), deepening rounds, generate sprint-N.md, update project-state.json.
- **Acceptance criteria:**
  - [x] SKILL.md loads sdd-guide core + deepening-rounds + project-state.json + prd.md (incomplete + unvetted only)
  - [x] Prerequisites: spec.md must exist
  - [x] Sprint item structure matches `spec.md > Sprint checklist item structure`
  - [x] Build mode is recorded in project-state.json
  - [x] First-run blurb only fires on the very first /sprint
- **Verification:** Confirm prerequisites, item structure fields, project-state.json update logic.
- **Status:** [x] Complete

---

**Checkpoint: paused for user review after items 5-8.**

---

### 9. /sdd:build — step-by-step mode
- **PRD ref:** `prd.md > Building`
- **Spec ref:** `spec.md > Command Skills > /sdd:build`
- **What to build:** Write `skills/build/SKILL.md` (frontmatter allowed-tools: Bash(git *) Read Write Edit Glob Grep Agent). Step-by-step behavior: read open-concerns, pick first unchecked item, surgical context loading (spec subsection + PRD story only), build, write/run tests or explain manual verification, show what was built, check off item, update CLAUDE.md/AGENTS.md if patterns emerge, update open-concerns, tell user to /clear and continue. Re-entry logic. Stop-revert-revise error handling. Lightweight startup validation.
- **Acceptance criteria:**
  - [x] SKILL.md loads sdd-guide core + context-loading + living-documents + CLAUDE.md + current sprint + project-state.json, then per-item spec/PRD subsection
  - [x] Prerequisites: current sprint file exists with unchecked items
  - [x] Re-entry load order matches spec
  - [x] Error handling follows stop-revert-revise pattern
  - [x] Surgical context loading is explicit (per-item, not full documents)
  - [x] allowed-tools matches spec
- **Verification:** Confirm surgical loading, re-entry order, error handling, and frontmatter tool list.
- **Status:** [x] Complete

---

### 10. /sdd:build — autonomous mode (layered on)
- **PRD ref:** `prd.md > Building`
- **Spec ref:** `spec.md > Command Skills > /sdd:build` (autonomous mode)
- **What to build:** Add autonomous behavior to `skills/build/SKILL.md`. Works through multiple items, pauses every 3-4 for review, uses subagents (Agent tool) for item execution, summary at end. Branches on build mode in project-state.json. Mode locked for sprint.
- **Acceptance criteria:**
  - [x] Autonomous mode behavior clearly separated from step-by-step
  - [x] Checkpoint cadence is every 3-4 items
  - [x] Subagent usage via Agent tool specified
  - [x] Mode branching reads from project-state.json
  - [x] No mid-sprint mode switching
  - [x] frontmatter allowed-tools includes Agent
- **Verification:** Confirm branching logic, checkpoint interval, subagent usage, Agent tool in frontmatter.
- **Status:** [x] Complete

---

### 11. /sdd:iterate
- **PRD ref:** `prd.md > Iterating Within a Sprint`
- **Spec ref:** `spec.md > Command Skills > /sdd:iterate`
- **What to build:** Write `skills/iterate/SKILL.md`. Behavior: first-run blurb, read open-concerns, surface backlog items, quick review with 2-3 observations, ask what user wants to work on, scope to mini-checklist (no artificial cap), append under `## Iteration N` header, multiple cycles supported.
- **Acceptance criteria:**
  - [x] SKILL.md loads sdd-guide core + current sprint + CLAUDE.md, selectively loads spec sections
  - [x] Prerequisites: all current sprint items complete
  - [x] Backlog surfacing present
  - [x] Iteration items append under `## Iteration N` header
  - [x] Loading behavior matches context-loading reference
- **Verification:** Confirm prerequisites, backlog surfacing, iteration header format.
- **Status:** [x] Complete

---

### 12. /sdd:reflect + reflect template
- **PRD ref:** `prd.md > Sprint Reflection`
- **Spec ref:** `spec.md > Command Skills > /sdd:reflect`
- **What to build:** Write `skills/reflect/SKILL.md` and `sdd-guide/templates/reflect-template.md`. Phase 1 sprint retro (smoothness, sizing, blockers, wins). Phase 2 product check (built vs planned, quality, partial completion). Partial story splitting in PRD (each split self-contained). Recommend /refine if unvetted, /retro if all complete. Write process-notes-sprint-N.md.
- **Acceptance criteria:**
  - [x] SKILL.md loads sdd-guide core + current sprint + prd.md + project-state.json
  - [x] Prerequisites: current sprint should be complete (warn but don't block)
  - [x] Story splitting produces self-contained stories
  - [x] Loading behavior matches context-loading reference
- **Verification:** Confirm two-phase structure, story splitting logic detail, conditional next-step recommendations.
- **Status:** [x] Complete

---

**Checkpoint: paused for user review after items 9-12.**

---

### 13. /sdd:refine
- **PRD ref:** `prd.md > Refining Unvetted Items`
- **Spec ref:** `spec.md > Command Skills > /sdd:refine`
- **What to build:** Write `skills/refine/SKILL.md`. Behavior: first-run blurb, compressed interview per unvetted item, check spec impact, discuss implications with user. Update incrementally in dependency order: prd.md → spec.md → AGENTS.md → CLAUDE.md, each with show + confirm. PRD health monitoring (10+ epics, 5+ unvetted thresholds).
- **Acceptance criteria:**
  - [x] SKILL.md loads sdd-guide core + living-documents + prd.md (unvetted) + spec.md (affected sections)
  - [x] Prerequisites: prd.md has Unvetted/Proposed items
  - [x] Incremental update ordering explicit (PRD → spec → AGENTS → CLAUDE)
  - [x] User confirmation required at each step
  - [x] PRD health thresholds match spec
- **Verification:** Confirm four-step incremental order, per-step confirmation, health monitoring thresholds.
- **Status:** [x] Complete

---

### 14. /sdd:retro + retro template
- **PRD ref:** `prd.md > Project Retrospective`
- **Spec ref:** `spec.md > Command Skills > /sdd:retro`
- **What to build:** Write `skills/retro/SKILL.md` and `sdd-guide/templates/retro-template.md`. Read all process-notes files, retro across entire project, plugin feedback handling (explain how to take feedback to plugin repo and run SDD on the plugin itself), final open-concerns resolution.
- **Acceptance criteria:**
  - [x] SKILL.md loads sdd-guide core + all process-notes + prd.md + sdd-feedback.md (if exists), selectively spec.md (summary only)
  - [x] Prerequisites: all PRD items should be complete (warn but don't block)
  - [x] Plugin feedback handling present
  - [x] Open-concerns final resolution explicit
- **Verification:** Confirm all process-notes files read, sdd-feedback.md handling explanation, final open-concerns pass.
- **Status:** [x] Complete

---

### 15. /sdd:feedback
- **PRD ref:** `prd.md > Plugin Improvement Feedback`
- **Spec ref:** `spec.md > Command Skills > /sdd:feedback`
- **What to build:** Write `skills/feedback/SKILL.md` (argument-hint: [your feedback note]). Accept note from $ARGUMENTS. Auto-capture context from project-state.json (timestamp, sprint, last command, project dir). Append entry to docs/sdd-feedback.md. Confirm save. Return immediately.
- **Acceptance criteria:**
  - [x] SKILL.md loads sdd-guide core + project-state.json
  - [x] Prerequisites: project-state.json must exist
  - [x] Entry format matches `spec.md > /sdd:feedback > Entry format`
  - [x] argument-hint set in frontmatter
  - [x] Auto-context capture includes all four fields
- **Verification:** Confirm entry format, argument-hint, immediate-return design.
- **Status:** [x] Complete

---

### 16. README.md for the plugin repository
- **PRD ref:** Out-of-band — supports `scope.md > What "Done" Looks Like` and distribution
- **Spec ref:** `spec.md > Plugin Repository Structure`
- **What to build:** Generate a comprehensive README.md at repo root: one-paragraph plugin summary, full command chain, install instructions (marketplace add + plugin install + local dev), workflow overview, quick start, artifact list, MIT license, attribution to hackathon-in-a-plugin curriculum.
- **Acceptance criteria:**
  - [x] README covers installation, all commands, workflow, quick-start, license
  - [x] Installation paths match spec
  - [x] All 11 commands listed with accurate descriptions
  - [x] MIT license stated
  - [x] Attribution to hackathon-in-a-plugin included
- **Verification:** A new user can install and understand the workflow without any other file. All 11 commands present.
- **Status:** [x] Complete

---

## Iteration 1 — Distribution setup

- **What:** Get the plugin published to its own GitHub repo so others can install via marketplace.
- **Steps taken:**
  - Restructured: dev repo (`zabuuq/devpost-hackathon-sdd-plugin`, formerly `sdd-plugin`) holds planning docs + plugin under `sdd-plugin/`. Distribution needs `.claude-plugin/marketplace.json` at root, so created separate `zabuuq/sdd-plugin` repo.
  - Pushed plugin contents via git CLI (the GitHub API push had frozen at 27 files).
  - Updated README install instructions to reference `zabuuq/sdd-plugin`.
  - Validated install via `/plugin marketplace add zabuuq/sdd-plugin` + `/plugin install sdd@sdd-marketplace`.
- **Status:** [x] Complete — published, installed, validated on three downstream test projects.
