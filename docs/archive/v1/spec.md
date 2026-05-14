# SDD Plugin — Technical Specification

## Stack

- **Platform:** Claude Code plugin (skills-based architecture)
- **Language:** Markdown (SKILL.md files with YAML frontmatter)
- **Runtime code:** None — all behavior is defined through skill instructions and templates
- **Distribution:** GitHub marketplace repo
- **Reference implementation:** [hackathon-in-a-plugin](https://github.com/devpost-curriculum) v0.2.0 — adapted, not forked

This plugin has no build step, no runtime dependencies, and no compiled code. The "stack" is the Claude Code plugin system itself.

**Relevant docs:**
- [Claude Code plugins](https://code.claude.com/docs/en/plugins)
- [Skills reference](https://code.claude.com/docs/en/skills)
- [Plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces)
- [Plugins reference](https://code.claude.com/docs/en/plugins-reference)

## Runtime & Deployment

- **Runtime:** Claude Code CLI or desktop app with plugin support
- **Deployment:** GitHub repository structured as a plugin marketplace
- **Installation:** `/plugin marketplace add <github-user>/sdd-plugin` → `/plugin install sdd@<marketplace-name>`
- **Testing during development:** `claude --plugin-dir ./plugins/sdd`
- **Environment requirements:** Claude Code with plugin support, `git` CLI available
- **No API keys, no external services, no hosting infrastructure**

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    sdd-guide (shared core)                   │
│        Tone, interaction rules, guard rails, command chain   │
│                                                             │
│  references/                                                │
│  ├── deepening-rounds.md    (planning commands)             │
│  ├── context-loading.md     (all commands, esp. /build)     │
│  ├── living-documents.md    (/refine, /build, /iterate)     │
│  └── open-concerns.md       (cross-phase concern tracking)  │
└──────────────┬──────────────────────────────────────────────┘
               │ loaded by each command skill (core only)
               │ + selective reference loading per command
               │
    ┌──────────┴──────────────────────────────────────┐
    │          PLANNING PHASE                          │
    │  /onboard → /scope → /prd → /spec               │
    ├─────────────────────────────────────────────────┤
    │          SPRINT LOOP                             │
    │  /sprint → /build → /iterate → /reflect → /refine│
    │     ↑                                     │      │
    │     └─────────── loop back ───────────────┘      │
    ├─────────────────────────────────────────────────┤
    │          PROJECT CLOSE                           │
    │  /retro                                          │
    ├─────────────────────────────────────────────────┤
    │          ANYTIME                                 │
    │  /feedback                                       │
    └─────────────────────────────────────────────────┘
```

### Data Flow

```
/onboard ──→ ~/.claude/sdd-user-profile.json (global, one-time)
                        │
                        ▼
/scope ──────→ docs/scope.md
                        │
                        ▼
/prd ────────→ docs/prd.md ←──────────────── /refine (updates)
                        │
                        ▼
/spec ───────→ docs/spec.md ←──────────────── /refine (updates)
             → CLAUDE.md ←─────────────────── /refine, /build (updates)
             → AGENTS.md ←─────────────────── /refine, /build (updates)
                        │
                        ▼
/sprint ─────→ docs/sprint-N.md
                        │
                        ▼
/build ──────→ code artifacts + checklist updates
             → CLAUDE.md, AGENTS.md (updates if needed)
                        │
                        ▼
/iterate ────→ iteration items appended to sprint-N.md
                        │
                        ▼
/reflect ────→ process-notes-sprint-N.md
             → PRD story splitting (if partial completion)
                        │
                        ▼
/retro ──────→ project retrospective document

Cross-cutting:
  /feedback ──→ docs/sdd-feedback.md (anytime)
  All commands → docs/open-concerns.md (read, resolve, append)
  All commands → docs/project-state.json (read/update)
  Planning commands → process-notes.md (append)
```

## Plugin Repository Structure

Implements `prd.md > Cross-Cutting: Living Documents` and marketplace distribution.

```
sdd-plugin/                              # marketplace repo root
├── .claude-plugin/
│   └── marketplace.json                 # marketplace catalog
├── README.md                            # installation instructions
└── plugins/
    └── sdd/                             # the plugin
        ├── .claude-plugin/
        │   └── plugin.json              # manifest (name: "sdd")
        ├── CLAUDE.md                    # plugin-level agent instructions
        └── skills/
            ├── sdd-guide/               # shared behavior (user-invocable: false)
            │   ├── SKILL.md             # core: tone, interaction rules, guard rails, command chain
            │   ├── references/
            │   │   ├── deepening-rounds.md
            │   │   ├── context-loading.md
            │   │   ├── living-documents.md
            │   │   └── open-concerns.md
            │   └── templates/
            │       ├── scope-template.md
            │       ├── prd-template.md
            │       ├── spec-template.md
            │       ├── sprint-template.md
            │       ├── reflect-template.md
            │       ├── retro-template.md
            │       └── open-concerns-template.md
            ├── onboard/
            │   └── SKILL.md
            ├── scope/
            │   └── SKILL.md
            ├── prd/
            │   └── SKILL.md
            ├── spec/
            │   └── SKILL.md
            ├── sprint/
            │   └── SKILL.md
            ├── build/
            │   └── SKILL.md
            ├── iterate/
            │   └── SKILL.md
            ├── reflect/
            │   └── SKILL.md
            ├── refine/
            │   └── SKILL.md
            ├── retro/
            │   └── SKILL.md
            └── feedback/
                └── SKILL.md
```

## Plugin Manifest

### marketplace.json

```json
{
  "name": "sdd-marketplace",
  "owner": {
    "name": "Jason McCoy"
  },
  "metadata": {
    "description": "Spec-driven development workflow plugin for Claude Code"
  },
  "plugins": [
    {
      "name": "sdd",
      "source": "./plugins/sdd",
      "description": "Structured, interview-driven workflow for planning and building software projects"
    }
  ]
}
```

### plugin.json

```json
{
  "name": "sdd",
  "description": "Spec-driven development workflow — from scoping through retrospective — producing real artifacts at every stage.",
  "version": "1.0.0",
  "author": {
    "name": "Jason McCoy"
  },
  "license": "MIT"
}
```

## Shared Behavior: sdd-guide

Implements `prd.md > Cross-Cutting: Command Explanations`, `prd.md > Cross-Cutting: Process Notes`, and `prd.md > Cross-Cutting: Living Documents`.

The sdd-guide is a non-user-invocable skill that defines shared behavior across all commands. It is split into a core file and reference files to minimize context overhead.

### sdd-guide Core (SKILL.md)

**Frontmatter:**
```yaml
---
name: sdd-guide
description: Core behavior definitions for the SDD plugin. Tone, interaction rules, guard rails, and command chain.
user-invocable: false
---
```

**Content covers:**
- **Tone:** Driven by user preference from `/onboard`. Default: collegial peer / sharp product manager. No sycophancy. The plugin acknowledges, disagrees when warranted, moves on.
- **Interaction rules:** One question at a time. Free-form questions only. Adaptive — skip beats naturally covered, follow threads that matter.
- **Guard rails:** Every command checks prerequisites before running. Missing prerequisites → name the command to run and stop. Lightweight validation: required files exist, `project-state.json` parses as valid JSON, sprint files have expected structure.
- **Command chain:** `/onboard` (one-time) → `/scope` → `/prd` → `/spec` → `/sprint` → `/build` → `/iterate` → `/reflect` → `/refine` (loop back to `/sprint`). `/retro` closes the project. `/feedback` runs anytime.
- **Command explanations:** Each command outputs a brief purpose explanation on first run in a project. Tracked in `project-state.json`. Not gated — plain text the user scrolls past.
- **Process notes:** Planning phases append to `process-notes.md`. Sprint cycles write to `process-notes-sprint-N.md`. Notes capture: decisions and rationale, pushback and responses, questions and struggles.
- **State tracking:** Every command updates `lastCommand` in `project-state.json` on startup.
- **Open concerns:** Every command reads `docs/open-concerns.md` on startup, resolves what it can, defers what it can't (with rationale), and appends new concerns from its own work.

### Reference: deepening-rounds.md

Used by: `/scope`, `/prd`, `/spec`, `/sprint`

Defines the two-phase interview pattern:
- **Phase 1 — Mandatory questions:** The minimum needed for a meaningful document. Asked one at a time, open-ended. Stay adaptive — skip beats naturally covered, follow unexpected threads.
- **Phase 2 — Deepening rounds:** After mandatory questions, offer the choice: "I've got enough to generate your [document]. Want another round to sharpen things, or ready to proceed?" Each round generates 4-5 targeted questions. Repeatable. Log how many rounds and what surfaced.

### Reference: context-loading.md

Used by: all commands, critical for `/build`

Defines per-command loading rules to prevent context window exhaustion:

| Command | Loads in full | Loads selectively | Skips |
|---------|--------------|-------------------|-------|
| `/onboard` | sdd-guide core | — | everything else |
| `/scope` | sdd-guide core, deepening-rounds, user profile | — | — |
| `/prd` | sdd-guide core, deepening-rounds, `docs/scope.md` | — | — |
| `/spec` | sdd-guide core, deepening-rounds, `docs/scope.md`, `docs/prd.md` | — | — |
| `/sprint` | sdd-guide core, deepening-rounds, `docs/project-state.json` | `docs/prd.md` (incomplete + unvetted items only) | completed PRD stories, full spec |
| `/build` | sdd-guide core, context-loading, living-documents, CLAUDE.md, current sprint file, `docs/project-state.json` | spec subsection + PRD story for current item only | full spec, full PRD, other sprint files, process notes |
| `/iterate` | sdd-guide core, current sprint file, CLAUDE.md | `docs/spec.md` (relevant sections) | full PRD, other sprint files |
| `/reflect` | sdd-guide core, current sprint file, `docs/prd.md`, `docs/project-state.json` | — | full spec (unless needed for story splitting) |
| `/refine` | sdd-guide core, living-documents, `docs/prd.md` (unvetted section) | `docs/spec.md` (affected sections only) | completed PRD stories |
| `/retro` | sdd-guide core, all process notes files, `docs/prd.md`, `docs/sdd-feedback.md` | `docs/spec.md` (summary/overview only) | sprint files (use process notes instead) |
| `/feedback` | sdd-guide core, `docs/project-state.json` | — | everything else |

**Key principle:** `/build` in step-by-step mode reconstructs context after `/clear`. The re-entry load order is:
1. CLAUDE.md (which instructs reading AGENTS.md)
2. Current sprint file → find next unchecked item
3. The specific spec subsection and PRD story referenced by that item
4. `docs/open-concerns.md`
5. `docs/project-state.json` (build mode, sprint number, last command)

**Threshold rule:** If any single file exceeds 200 lines, load only the relevant section(s) rather than the full file. CLAUDE.md is exempt (it should be kept concise by design).

### Reference: living-documents.md

Used by: `/refine`, `/build`, `/iterate`

Defines the protocol for updating living documents:

- **Updatable documents:** `docs/prd.md`, `docs/spec.md`, `CLAUDE.md`, `AGENTS.md`
- **Default stance:** Resist changes to scope/PRD/spec. New items go to backlog or unvetted section.
- **Update ordering for `/refine`:** PRD first (move item from unvetted to vetted) → spec (architectural implications) → AGENTS.md (conventions) → CLAUDE.md (Claude-specific instructions). Each update shown to user and confirmed before proceeding to the next.
- **Re-scoping trigger:** When accumulated changes fundamentally shift the project, the plugin recognizes this and suggests re-scoping. Framed as a deliberate, weighty decision.
- **PRD health monitoring:**
  - **10+ epics** → recommend breaking the project into phases, each going through the full SDD workflow independently
  - **5+ unvetted items** → flag scope creep, recommend triaging to backlog or separate project

### Reference: open-concerns.md

Used by: all commands

Defines the cross-phase concern tracking mechanism:

- **File:** `docs/open-concerns.md`
- **Purpose:** Carries unresolved questions and △ feedback items across phase boundaries so they don't evaporate with conversation context
- **Each entry contains:**
  - Source (which command/phase raised it)
  - Target (which command should resolve it, if known)
  - The concern itself
  - Status: `open`, `resolved`, or `deferred`
  - Resolution or deferral rationale (when resolved/deferred)
- **Command behavior:** Every command reads the file on startup. Resolves what it can (including concerns resolved indirectly through other decisions — mark with explanation). Defers what it can't with rationale. Appends new concerns from its own embedded feedback.
- **Implicit resolution:** When a decision in one phase makes a concern moot, the concern is still explicitly marked resolved with a note explaining how.

## Command Skills

### /sdd:onboard

Implements `prd.md > Onboarding`.

**Frontmatter:**
```yaml
---
name: onboard
description: One-time setup for the SDD plugin. Creates a global user profile with communication style, git preferences, and workflow explanation preference.
disable-model-invocation: true
---
```

**Loads:** sdd-guide core

**Prerequisites:** None (this is the entry point)

**Behavior:**
1. Check for existing profile at `~/.claude/sdd-user-profile.json`
   - If exists: announce it, offer to update individual preferences
   - If not: run full onboarding
2. Output the full SDD workflow explanation (not gated — scrollable text):
   - Planning phase: scope → PRD → spec
   - Sprint loop: sprint → build → iterate → reflect → refine (loop)
   - Project close: retro
   - Anytime: feedback
3. Ask whether to show this explanation at the start of future projects (stored in profile)
4. Ask about git preferences (do they use git repos?)
5. Ask about communication style — present examples with an "other" option
6. Write `~/.claude/sdd-user-profile.json`

**Artifact:** `~/.claude/sdd-user-profile.json`

**Schema:**
```json
{
  "version": 1,
  "createdAt": "ISO-8601",
  "updatedAt": "ISO-8601",
  "communicationStyle": "string — user's chosen style",
  "gitPreference": true,
  "showWorkflowExplanation": true
}
```

### /sdd:scope

Implements `prd.md > Scoping a Project`.

**Frontmatter:**
```yaml
---
name: scope
description: Collaborative brainstorm that produces a scope document. Interviews the user, pushes back on vague thinking, and distills the conversation into a clear project definition.
disable-model-invocation: true
---
```

**Loads:** sdd-guide core, deepening-rounds reference, user profile

**Prerequisites:** `~/.claude/sdd-user-profile.json` must exist. If missing → "Run `/sdd:onboard` first."

**Behavior:**
1. First-run explanation blurb (if not shown before per `project-state.json`)
2. If git preference is enabled, check for existing git repo, offer to initialize if missing
3. If `showWorkflowExplanation` is true in profile, output workflow overview
4. Create `docs/` directory and `docs/project-state.json` if they don't exist
5. Interview covering: the idea, who it's for, inspiration/references, goals, what "done" looks like, solo or team
6. Assess project size — if too large for a single pass:
   - Propose specific boundaries for splitting
   - User makes final call
   - Scope doc captures full vision + what's being built now vs. later
   - Deferred pieces written to `docs/backlog.md` with full context
7. Deepening rounds (per deepening-rounds reference)
8. Generate `docs/scope.md`
9. Initialize `docs/open-concerns.md` from template
10. Append to `process-notes.md`

**Artifact:** `docs/scope.md`

### /sdd:prd

Implements `prd.md > Defining Product Requirements`.

**Frontmatter:**
```yaml
---
name: prd
description: Produces a product requirements document from the scope. Covers users, features, requirements, success criteria with testable acceptance criteria.
disable-model-invocation: true
---
```

**Loads:** sdd-guide core, deepening-rounds reference, `docs/scope.md`

**Prerequisites:** `docs/scope.md` must exist. If missing → "Run `/sdd:scope` first."

**Behavior:**
1. First-run explanation blurb
2. Read `docs/open-concerns.md` — address any concerns targeted at `/prd`
3. Walk through scope section by section, turning brainstorm language into precise behavior descriptions
4. Requirements organized as user stories grouped into epics with stable heading names
5. Each story has testable acceptance criteria as checkboxes
6. Surface edge cases and "what if" questions
7. Monitor epic count — if 10+ epics emerge, recommend phase-splitting
8. Deepening rounds
9. Generate `docs/prd.md`
10. Append to `process-notes.md`
11. Update `docs/open-concerns.md`

**Artifact:** `docs/prd.md`

**Structure:**
- Epics as `##` headings with stable names
- User stories as list items under epics
- Acceptance criteria as checkboxes under each story
- `## Unvetted` section at the bottom (initially empty)

### /sdd:spec

Implements `prd.md > Technical Specification`.

**Frontmatter:**
```yaml
---
name: spec
description: Technical specification designed for the project. Architecture, data models, tech stack, file structure. Detailed enough that any agent could build from it without asking questions.
disable-model-invocation: true
---
```

**Loads:** sdd-guide core, deepening-rounds reference, `docs/scope.md`, `docs/prd.md`

**Prerequisites:** `docs/scope.md` and `docs/prd.md` must exist. If either missing → name the missing command.

**Behavior:**
1. First-run explanation blurb
2. Read `docs/open-concerns.md` — address concerns targeted at `/spec`
3. Tech preferences interview (adapted to experience level from user profile)
4. Deployment discussion
5. Web search for current docs on proposed tools, libraries, APIs
6. Propose architecture section by section, mapped to PRD epics
7. Build file structure tree together
8. Walk through data flow for the most important data in the app
9. Define testing strategy: unit, integration, e2e, framework, coverage
10. For multi-service architectures: service boundaries and communication patterns
11. Deepening rounds
12. Generate `docs/spec.md`
13. Generate or merge CLAUDE.md
14. Generate or merge AGENTS.md
15. Append to `process-notes.md`
16. Update `docs/open-concerns.md`

**Artifacts:** `docs/spec.md`, `CLAUDE.md`, `AGENTS.md`

**Spec structure requirements:**
- Every architectural component gets its own heading — these become addresses for `/sprint`
- Cross-reference PRD epic headings throughout: "Implements `prd.md > [Epic]`"
- Include full file structure tree, annotated
- Include data flow diagrams
- Include URLs to reference docs the agent will need during `/build`
- Granular subsections: `## Frontend > ### Search Component`, `## API > ### GET /endpoint`, `## Data Model > ### Users Table`

**CLAUDE.md generation:**
- If CLAUDE.md exists: read it, preserve relevant content, merge spec-derived instructions
- If not: generate fresh
- Contains Claude-specific instructions
- Includes explicit instruction to read AGENTS.md
- Does NOT reference sprint files — `/build` loads sprint context itself
- Kept concise — serves as architectural summary for `/build` re-entry

**AGENTS.md generation:**
- Same merge/generate logic as CLAUDE.md
- Contains agent-agnostic content: project context, architecture decisions, coding conventions
- Living document — updated by any command that changes architecture or conventions

### /sdd:sprint

Implements `prd.md > Sprint Planning`.

**Frontmatter:**
```yaml
---
name: sprint
description: Pull a logical batch of work from the PRD into a buildable sprint. Produces a sprint checklist with spec references and verification steps.
disable-model-invocation: true
---
```

**Loads:** sdd-guide core, deepening-rounds reference, `docs/project-state.json`, `docs/prd.md` (incomplete + unvetted items only)

**Prerequisites:** `docs/spec.md` must exist. If missing → "Run `/sdd:spec` first."

**Behavior:**
1. First-run explanation blurb (only on very first `/sprint` in the project, not subsequent sprints)
2. Read `docs/open-concerns.md`
3. Read PRD — identify all incomplete and unvetted items
4. If unvetted items exist → recommend `/sdd:refine` before sprint planning (don't require it)
5. If 5+ unvetted items → flag scope creep per living-documents reference
6. Propose grouping based on:
   - Dependencies and relatedness
   - Logical sequence
   - Tangible milestones — each sprint should end with something the user can see, use, or test
   - No time-boxing — grouped by coherence, not by time estimates
7. User makes final call on what goes in
8. Ask for build mode choice: step-by-step or autonomous (locked for the sprint)
9. Deepening rounds (optional — for refining the sprint plan)
10. Generate `docs/sprint-N.md` (N from `project-state.json`)
11. Update `docs/project-state.json`: increment sprint counter, store build mode
12. Update `docs/open-concerns.md`

**Artifact:** `docs/sprint-N.md`

**Sprint checklist item structure:**
```markdown
### [Item Title]
- **PRD ref:** `prd.md > [Epic] > [Story]`
- **Spec ref:** `spec.md > [Section] > [Subsection]`
- **What to build:** Brief description of the implementation
- **Acceptance criteria:** (copied/adapted from PRD story)
  - [ ] Criterion 1
  - [ ] Criterion 2
- **Verification:** (automated or manual per testing strategy)
  - How to verify this item works
- **Status:** [ ] Not started
```

### /sdd:build

Implements `prd.md > Building`.

**Frontmatter:**
```yaml
---
name: build
description: Execute the current sprint checklist. Builds each item according to spec references and acceptance criteria.
disable-model-invocation: true
allowed-tools: Bash(git *) Read Write Edit Glob Grep Agent
---
```

**Loads:** sdd-guide core, context-loading reference, living-documents reference, CLAUDE.md, current sprint file, `docs/project-state.json`. Then selectively: spec subsection + PRD story for current item only.

**Prerequisites:** Current sprint file (`docs/sprint-N.md` where N = current sprint from `project-state.json`) must exist with unchecked items. If missing → "Run `/sdd:sprint` first." If all items checked → "Sprint complete. Run `/sdd:iterate` to polish or `/sdd:reflect` to review."

**Lightweight validation on startup:**
- `docs/project-state.json` parses as valid JSON
- Current sprint file exists
- Sprint file has at least one unchecked item

**Step-by-step mode behavior:**
1. Read `docs/open-concerns.md`
2. Pick up the first unchecked item from sprint file
3. Load only the spec subsection and PRD story referenced by that item
4. Build according to spec reference and acceptance criteria
5. If testing strategy calls for automated tests: write and run them
6. If manual verification: explain what to look for and how to test
7. Show what was built, explain verification
8. Check off the item in the sprint file
9. Update CLAUDE.md / AGENTS.md if build reveals new patterns
10. Update `docs/open-concerns.md` if issues arise
11. Tell user to run `/clear`, then `/sdd:build` to continue

**Autonomous mode behavior:**
1. Same as step-by-step but works through multiple items
2. Pauses at checkpoints every 3-4 items for user review
3. Uses subagents (Agent tool) for item execution
4. Summary at the end rather than per-item process notes

**Error handling:**
- If something breaks: stop, propose reverting to last clean state
- Work with user to revise the checklist before resuming
- Sprint checklist is a living document — adapts when plans meet reality

**Re-entry after `/clear` (step-by-step mode):**
1. CLAUDE.md → AGENTS.md
2. Current sprint file → next unchecked item
3. Specific spec subsection + PRD story for that item
4. `docs/open-concerns.md`
5. `docs/project-state.json`

### /sdd:iterate

Implements `prd.md > Iterating Within a Sprint`.

**Frontmatter:**
```yaml
---
name: iterate
description: Polish and improve what was built in the current sprint. Bugs, new features, UX improvements — scoped through a brief interview and added to the sprint file.
disable-model-invocation: true
---
```

**Loads:** sdd-guide core, current sprint file, CLAUDE.md. Selectively: relevant spec sections.

**Prerequisites:** All items in current sprint file must be complete. If not → "Run `/sdd:build` first."

**Behavior:**
1. First-run explanation blurb
2. Read `docs/open-concerns.md`
3. Surface `docs/backlog.md` items for consideration — small items that fit could be pulled in
4. Quick review of current state — surface 2-3 observations
5. Ask what the user wants to work on (bugs, features, UX, anything)
6. Scope through brief interview → convert to mini-checklist
7. Mini-checklist sized by what the work requires — no artificial cap
8. Append items to current sprint file under `## Iteration N` header
9. Tell user to run `/sdd:build` to execute iteration items
10. Multiple iteration cycles supported within a single sprint
11. Update `docs/open-concerns.md`

### /sdd:reflect

Implements `prd.md > Sprint Reflection`.

**Frontmatter:**
```yaml
---
name: reflect
description: Sprint-level retrospective covering both process and product. Reviews what worked, splits partial stories, recommends next steps.
disable-model-invocation: true
---
```

**Loads:** sdd-guide core, current sprint file, `docs/prd.md`, `docs/project-state.json`

**Prerequisites:** Current sprint should be complete (all items checked). Warn but don't block if items remain.

**Behavior:**
1. First-run explanation blurb
2. Read `docs/open-concerns.md`
3. **Phase 1 — Sprint retro (process):**
   - Did the sprint go smoothly?
   - Were items sized right?
   - What blocked progress?
   - What went well?
4. **Phase 2 — Product check:**
   - Does what was built match what was planned?
   - Quality concerns?
   - Any items partially complete?
5. **Partial story splitting:**
   - Completed acceptance criteria → one story (checked off in PRD)
   - Remaining criteria → new story (still open in PRD)
   - Each split story is self-contained with its own complete acceptance criteria
6. Check for unvetted items in PRD → recommend `/sdd:refine` if any exist
7. When all PRD items are complete → recommend `/sdd:retro` instead of another sprint
8. Write `process-notes-sprint-N.md`
9. Update `docs/open-concerns.md`

**Artifact:** `process-notes-sprint-N.md`

### /sdd:refine

Implements `prd.md > Refining Unvetted Items`.

**Frontmatter:**
```yaml
---
name: refine
description: Run unvetted PRD items through a compressed planning interview. Produces proper user stories with acceptance criteria and updates the spec if needed.
disable-model-invocation: true
---
```

**Loads:** sdd-guide core, living-documents reference, `docs/prd.md` (unvetted section), `docs/spec.md` (affected sections only)

**Prerequisites:** `docs/prd.md` must have items in the Unvetted/Proposed section. If none → "No unvetted items to refine."

**Behavior:**
1. First-run explanation blurb
2. Read `docs/open-concerns.md`
3. For each item (or group of related items):
   - Compressed interview — enough for proper stories with acceptance criteria
   - Check for spec impact: new data models, changed components, new service boundaries
   - Discuss spec implications with user during conversation
4. **Update documents incrementally in dependency order:**
   - `docs/prd.md` — move items from unvetted to vetted. Show change, get confirmation.
   - `docs/spec.md` — add/update architectural components if needed. Show change, get confirmation.
   - `AGENTS.md` — update conventions if warranted. Show change, get confirmation.
   - `CLAUDE.md` — update Claude-specific instructions if warranted. Show change, get confirmation.
5. Monitor PRD health:
   - 10+ epics → recommend phase-splitting
   - 5+ unvetted items remaining → flag scope creep
6. Update `docs/open-concerns.md`

### /sdd:retro

Implements `prd.md > Project Retrospective`.

**Frontmatter:**
```yaml
---
name: retro
description: Project-level retrospective across all sprints. Synthesizes what worked, what didn't, and how the workflow performed. Explains how to act on plugin feedback.
disable-model-invocation: true
---
```

**Loads:** sdd-guide core, all `process-notes*.md` files, `docs/prd.md`, `docs/sdd-feedback.md` (if exists). Selectively: `docs/spec.md` (summary only)

**Prerequisites:** All PRD items should be complete. Warn but don't block.

**Behavior:**
1. First-run explanation blurb
2. Read `docs/open-concerns.md` — resolve any remaining items
3. Read all sprint process notes and planning process notes
4. Retro covers process and product across entire project:
   - Patterns across sprints
   - What worked, what didn't
   - How the SDD workflow performed
5. If `docs/sdd-feedback.md` exists:
   - Read accumulated feedback
   - Explain how to take it to the plugin repo: switch to the plugin directory, use the SDD process on the plugin itself
6. Produce project-level retrospective document
7. Final `docs/open-concerns.md` update — close all remaining items or note them as carried to the retro doc

**Artifact:** `docs/retro.md`

### /sdd:feedback

Implements `prd.md > Plugin Improvement Feedback`.

**Frontmatter:**
```yaml
---
name: feedback
description: Quickly flag something the SDD plugin could do better. Captures the note with context and returns immediately.
disable-model-invocation: true
argument-hint: [your feedback note]
---
```

**Loads:** sdd-guide core, `docs/project-state.json`

**Prerequisites:** `docs/project-state.json` must exist (meaning a project is in progress).

**Behavior:**
1. Accept the user's note from `$ARGUMENTS`
2. Auto-capture context from `project-state.json`:
   - Timestamp (ISO-8601)
   - Current sprint number
   - Last command run
   - Project directory name
3. Append entry to `docs/sdd-feedback.md`
4. Confirm the note was saved
5. Return immediately — no interruption to current workflow

**Entry format in sdd-feedback.md:**
```markdown
### [Timestamp]
- **Sprint:** N
- **After command:** /sdd:[command]
- **Project:** [directory name]
- **Note:** [user's feedback]
```

## User Project Artifacts

Files generated in the user's project directory as they work through the workflow:

```
user-project/
├── docs/
│   ├── scope.md                     # from /sdd:scope
│   ├── prd.md                       # from /sdd:prd
│   ├── spec.md                      # from /sdd:spec
│   ├── backlog.md                   # deferred items, written during planning
│   ├── sprint-1.md                  # from /sdd:sprint
│   ├── sprint-2.md                  # subsequent sprints
│   ├── open-concerns.md             # cross-phase concern tracking
│   ├── project-state.json           # per-project ephemeral state
│   ├── sdd-feedback.md              # plugin improvement notes
│   └── retro.md                     # from /sdd:retro
├── process-notes.md                 # planning phases (scope, prd, spec)
├── process-notes-sprint-1.md        # per-sprint process notes
├── process-notes-sprint-2.md
├── CLAUDE.md                        # generated after /sdd:spec, living document
├── AGENTS.md                        # generated after /sdd:spec, living document
└── README.md                        # generated during /sdd:build
```

## Data Model

### project-state.json

Per-project ephemeral state. Not meant for human editing.

```json
{
  "version": 1,
  "currentSprint": 1,
  "buildMode": "step-by-step",
  "lastCommand": "build",
  "commandExplanationsShown": {
    "scope": true,
    "prd": true,
    "spec": true,
    "sprint": true,
    "build": false,
    "iterate": false,
    "reflect": false,
    "refine": false,
    "retro": false,
    "feedback": false
  }
}
```

### sdd-user-profile.json

Global user profile. One per machine, cross-project.

```json
{
  "version": 1,
  "createdAt": "2026-04-08T12:00:00Z",
  "updatedAt": "2026-04-08T12:00:00Z",
  "communicationStyle": "Terse and direct. No sycophancy. Collegial peer.",
  "gitPreference": true,
  "showWorkflowExplanation": true
}
```

### open-concerns.md

Cross-phase concern tracking.

```markdown
# Open Concerns

## Open

### [Concern title]
- **Source:** /sdd:[command] — [phase/context]
- **Target:** /sdd:[command] (if known)
- **Concern:** [description]

## Resolved

### [Concern title]
- **Source:** /sdd:[command]
- **Resolved by:** /sdd:[command]
- **Resolution:** [how it was resolved — including implicit resolution through other decisions]

## Deferred

### [Concern title]
- **Source:** /sdd:[command]
- **Deferred by:** /sdd:[command]
- **Rationale:** [why it was deferred and when it should be revisited]
```

### Backlog Entry Format

Each entry in `docs/backlog.md` captures maximum context per the PRD requirement:

```markdown
### [Item title]
- **What:** Description of the item
- **Why deferred:** Reason it's not in the current project
- **Connects to:** What it relates to in the current project
- **Constraints discussed:** Any decisions or constraints that apply
- **Context:** Enough detail to pick this up cold months later
```

### Seed File Format

When a backlog item is spun off into its own project:

```markdown
# [Project Name] — Seed Context

## Origin
Spun off from [source project] backlog during [phase].

## The Idea
[Backlog entry content]

## Relevant Architecture Decisions
[From current project's spec that would apply]

## Conventions
[From current project's AGENTS.md that would carry over]

## Additional Context
[Anything else relevant from the current project]
```

The seed file is placed in the new project directory. `/sdd:scope` detects and consumes it as starting context.

## Key Technical Decisions

### 1. Pure markdown skills architecture
**Decision:** No runtime code. All behavior defined through SKILL.md instructions and templates.
**Why:** Matches the reference implementation pattern. No build step, no dependencies, no deployment infrastructure. The plugin is its own specification.
**Tradeoff:** Limited to what can be expressed through agent instructions. No programmatic validation or state management beyond what the agent does via tool calls.

### 2. Split sdd-guide into core + references
**Decision:** Core SKILL.md contains tone, interaction rules, guard rails. Reference files contain deepening rounds, context loading, living documents, and open concerns patterns.
**Why:** Reduces context overhead. Every command loads core (~100 lines). Only commands that need specific patterns load those references. `/feedback` doesn't burn context on deepening rounds it'll never use.
**Tradeoff:** More files to maintain. Each command skill must explicitly declare which references it loads.

### 3. Surgical context loading for /build
**Decision:** `/build` loads only the specific spec subsection and PRD story for the current checklist item, not the full documents.
**Why:** Multi-sprint projects accumulate large specs and PRDs. Step-by-step mode with `/clear` means frequent context reconstruction. Loading full documents would exhaust the context window for large projects.
**Tradeoff:** The agent has narrower context per item — it might miss cross-cutting concerns that live in other spec sections. Mitigated by CLAUDE.md serving as the architectural summary.

### 4. Incremental document updates in /refine
**Decision:** Update documents one at a time in dependency order (PRD → spec → AGENTS.md → CLAUDE.md), showing each change to the user before proceeding.
**Why:** User maintains visibility and control over every change. If they disagree with a PRD change, downstream documents don't get updated based on a bad assumption.
**Tradeoff:** Slower than batch updates. More back-and-forth. But correctness and user trust outweigh speed here.

### 5. open-concerns.md for cross-phase continuity
**Decision:** Dedicated file for carrying unresolved questions and △ feedback across phase boundaries.
**Why:** Embedded feedback from one phase (e.g., /prd noting a spec concern) evaporates with the conversation. Concerns that are resolved implicitly through other decisions still need explicit closure. Without this mechanism, important issues fall through the cracks between `/clear` boundaries.
**Tradeoff:** Another file every command must read and maintain. Requires discipline from the skill instructions to consistently update it.

## Testing Strategy

Since the plugin is pure markdown with no runtime code, traditional automated testing doesn't apply. Testing is manual and structural:

- **Structural validation:** `claude plugin validate .` or `/plugin validate .` checks plugin.json, SKILL.md frontmatter, and directory structure
- **Functional testing:** Run each command in a test project using `claude --plugin-dir ./plugins/sdd` and verify:
  - Prerequisites are enforced correctly
  - Artifacts are generated in the expected locations
  - Cross-references between documents are valid
  - Context loading is selective (monitor for unexpected auto-compaction as a signal of context overload)
  - `project-state.json` updates correctly
  - `open-concerns.md` is read and updated by each command
- **Integration testing:** Run through a full workflow (onboard → scope → prd → spec → sprint → build → reflect → retro) on a small test project to verify the command chain works end-to-end
- **Context budget testing:** Test with a deliberately large project to verify `/build` re-entry doesn't trigger auto-compaction

## Open Issues

### From PRD Open Questions (resolved)

- **Global user profile location:** Resolved → `~/.claude/sdd-user-profile.json`. Accessible from any project directory via standard file read operations.
- **Per-project state file format:** Resolved → JSON (`docs/project-state.json`). Structured, easy to parse, no human-editing use case.
- **PRD size threshold:** Resolved → 10+ epics triggers phase-splitting recommendation. 5+ unvetted items flags scope creep.
- **Seed file format:** Resolved → structured markdown with origin, idea, relevant architecture, conventions, and additional context sections. Consumed by `/sdd:scope` as starting context.

### New Issues From Spec

- **`/sdd:scope` seed file detection:** How does `/scope` know a seed file is present? Convention: look for a file named `docs/seed.md` in the project directory. If found, read it as starting context and reference it during the interview.
- **Plugin-level CLAUDE.md content:** What should the plugin's own CLAUDE.md contain vs. what goes in sdd-guide core? The plugin CLAUDE.md should be minimal — just enough for Claude to understand it's an SDD plugin and to load sdd-guide when a command is invoked. Behavioral detail lives in sdd-guide.
- **Communication style enforcement:** The user profile stores a communication style string, but each command skill needs to actually apply it. The sdd-guide core should include an instruction to read the style from the profile and adapt accordingly. How strongly should this override the default tone? Recommendation: the profile style is authoritative — it's the user's explicit preference.
