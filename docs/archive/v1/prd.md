# Spec-Driven Development Plugin — Product Requirements

## Problem Statement
Building software with AI coding agents today is ad-hoc — developers jump straight to prompting without structured planning, leading to vague instructions, scope creep, and builds that drift from intent. A veteran developer who has done this for 30 years needs a repeatable, structured workflow that produces real artifacts at every stage, exercises judgment about project size and priorities, and improves itself over time through use.

## User Stories

### Onboarding

- As a first-time user, I want the plugin to explain the full SDD workflow so that I understand what each step does and why before I start using it.
  - [x] When `/onboard` runs, it outputs a plain-text explanation of the entire workflow: planning phase (scope, PRD, spec), sprint loop (sprint, build, iterate, reflect, refine), and project close (retro)
  - [x] The explanation covers the sprint loop concept specifically — that after the first build cycle, you loop back for more sprints
  - [x] The explanation is not gated — it's output text the user can scroll past, not a prompt requiring acknowledgment
  - [x] The user is asked whether they want to see this explanation at the start of future projects or suppress it; preference is stored in the user profile

- As a first-time user, I want to set my git preferences so that projects can optionally include repo setup and git workflow instructions.
  - [x] The plugin asks whether the user regularly uses git repos for projects
  - [x] If yes, downstream commands (starting with `/scope`) check for an existing repo and offer to initialize one if missing
  - [x] The preference is stored in the global user profile

- As a first-time user, I want to set my preferred communication style so that the plugin's tone matches how I like to work.
  - [x] The plugin presents a few common styles as examples (terse and direct, more conversational, pushes back hard on ideas, etc.) with enough context to understand the choice
  - [x] The user can pick one of the presented styles or describe their own ("other")
  - [x] The chosen style is stored in the global user profile and applied across all commands in all projects

- As a returning user, I want to update my preferences without re-doing the full onboarding so that my profile stays current as my needs change.
  - [x] If `/onboard` detects an existing user profile, it says so and offers to update specific preferences rather than starting from scratch
  - [x] The user can update any individual preference without re-answering everything

- As a user, I want my profile stored globally so that it works across all projects on this machine.
  - [x] The user profile is written to a global location (plugin directory or `~/.claude/`)
  - [x] All commands in any project directory can read the profile
  - [x] The profile persists across projects without requiring re-creation

### Scoping a Project

- As a user starting a new project, I want to collaboratively define what I'm building so that the project has a clear, well-reasoned scope before any planning begins.
  - [x] `/scope` checks for a global user profile; if missing, instructs the user to run `/onboard` first
  - [x] The interview covers: the idea, who it's for, inspiration/references, goals, what "done" looks like
  - [x] The plugin asks whether this is a solo or team project
  - [x] If git preference is enabled in the user profile, the plugin checks for an existing git repo and asks if the user wants one initialized if missing
  - [x] The interview is adaptive — follows the user's lead, skips beats that have been naturally covered, pursues threads that matter
  - [x] A scope document is produced in `docs/scope.md`

- As a user with a large project idea, I want the plugin to evaluate project size and recommend breaking it into smaller pieces so that each piece is manageable and goes through the full SDD workflow independently.
  - [x] The plugin assesses project size based on the conversation and flags when a project is too large for a single pass
  - [x] When flagged, the plugin proposes specific boundaries for how to split the project into smaller pieces
  - [x] The user makes the final call on where to cut
  - [x] The scope document captures the full vision while clearly defining what's being built now vs. later
  - [x] Deferred pieces are captured with enough context to seed a future `/scope` conversation

- As a user, I want to see a brief explanation of what `/scope` does the first time I run it in a project so that I have context without needing to remember the onboarding walkthrough.
  - [x] The first time `/scope` runs in a project, it outputs a brief blurb explaining its purpose
  - [x] The blurb is not gated — just text that can be scrolled past
  - [x] On subsequent runs (if the command is re-run to re-scope), the blurb does not appear again
  - [x] Display state is tracked in a per-project state file in `docs/`

### Defining Product Requirements

- As a user with a scope document, I want to translate my scope into precise, testable requirements so that every behavior is defined before building starts.
  - [x] `/prd` checks for `docs/scope.md`; if missing, instructs the user to run `/scope` first
  - [x] The interview walks through the scope section by section, turning casual brainstorm language into precise behavior descriptions
  - [x] Requirements are organized as user stories grouped into epics with clear, stable heading names
  - [x] Each story has testable acceptance criteria written as checkboxes
  - [x] Edge cases and "what if" questions are surfaced and resolved
  - [x] The PRD is produced in `docs/prd.md`

- As a user, I want the PRD to serve as both the requirements document and the progress tracker so that sprint planning can read one file to know the full project state.
  - [x] Each user story's acceptance criteria are checkboxes that get checked off when completed in a sprint
  - [x] `/sprint` reads the PRD and can immediately distinguish done stories from remaining stories from unvetted stories
  - [x] Unvetted items (added during sprints but not yet refined) are placed in a clearly marked "Unvetted" or "Proposed" section

- As a user, I want the plugin to monitor PRD size as a project health indicator so that the document doesn't bloat beyond what's useful.
  - [x] When the PRD grows too large (through refinement adding items), the plugin flags it
  - [x] The plugin recommends migrating items to the backlog file to keep the PRD manageable
  - [x] The user decides which items to move

- As a user, I want the first-run explanation blurb for `/prd`.
  - [x] Same behavior as described in the Scoping epic

### Technical Specification

- As a user with a PRD, I want to design the technical architecture collaboratively so that the spec is detailed enough for any agent to build from without asking questions.
  - [x] `/spec` checks for `docs/scope.md` and `docs/prd.md`; if either is missing, instructs the user to run the missing command first
  - [x] The interview covers: tech preferences, deployment, stack research (web search for current docs), architecture mapped to PRD epics, file structure, and data flow
  - [x] Testing strategy is defined: unit tests, integration tests, e2e tests, framework choice, coverage expectations
  - [x] For multi-service or microservice architectures, service boundaries and communication patterns are defined
  - [x] The spec is produced in `docs/spec.md`

- As a user, I want CLAUDE.md and AGENTS.md generated after the spec so that AI agents have operating instructions for the project.
  - [x] If CLAUDE.md exists, the plugin reads it, preserves relevant content, and merges in spec-derived instructions
  - [x] If CLAUDE.md doesn't exist, it's generated fresh
  - [x] Same logic for AGENTS.md
  - [x] AGENTS.md contains agent-agnostic content: project context, architecture decisions, coding conventions
  - [x] CLAUDE.md contains Claude-specific instructions and includes an explicit instruction to read AGENTS.md
  - [x] Both files are living documents — any command that changes architecture, conventions, or patterns updates them as needed

- As a user, I want the first-run explanation blurb for `/spec`.
  - [x] Same behavior as described in the Scoping epic

### Sprint Planning

- As a user ready to build, I want to pull a logical batch of work from the PRD into a buildable sprint so that I'm working on a coherent, meaningful chunk.
  - [x] `/sprint` reads the PRD and identifies all incomplete and unvetted items
  - [x] If unvetted items exist, the plugin recommends running `/refine` before sprint planning (but doesn't require it)
  - [x] The plugin recommends a grouping of items based on dependencies, relatedness, and logical sequence
  - [x] Grouping prioritizes tangible milestones — each sprint should end with something the user can see, use, or test
  - [x] The user makes the final call on what goes into the sprint
  - [x] A sprint checklist is produced in `docs/sprint-N.md` (incrementing with each sprint)
  - [x] Each checklist item has: title, PRD/spec reference, what to build, acceptance criteria, verification steps (automated or manual per the testing strategy)

- As a user, I want to choose between step-by-step and autonomous build modes so that I control how much I'm involved during execution.
  - [x] Step-by-step mode: `/build` stops between every checklist item with a `/clear` to keep context fresh
  - [x] Autonomous mode: `/build` works through multiple items, pausing at checkpoints every 3-4 items for review
  - [x] The mode choice is locked in for the sprint

- As a user, I want the first-run explanation blurb for `/sprint`.
  - [x] Same behavior as described in the Scoping epic; only shows on the very first `/sprint` run in the project, not on subsequent sprints

### Building

- As a user, I want the plugin to execute my sprint checklist so that the planned work gets built according to the spec.
  - [x] `/build` reads the current sprint file and picks up the first unchecked item
  - [x] For each item, the plugin builds according to the spec reference and acceptance criteria
  - [x] At check-in points (per item in step-by-step, every 3-4 items in autonomous), the plugin shows what was built and explains how to verify it
  - [x] If the testing strategy calls for automated tests, the plugin writes and runs them alongside the feature
  - [x] If manual verification, the plugin explains what to look for and how to test
  - [x] CLAUDE.md and AGENTS.md are updated if build work reveals new patterns or conventions

- As a user, I want the plugin to handle problems during build gracefully so that a broken step doesn't derail the entire sprint.
  - [x] If something breaks, the plugin stops and proposes reverting to the last clean state
  - [x] The plugin works with the user to revise the checklist before resuming
  - [x] The sprint checklist is a living document that adapts when plans meet reality

- As a user, I want the first-run explanation blurb for `/build`.
  - [x] Same behavior as described in the Scoping epic

### Iterating Within a Sprint

- As a user who has completed the sprint checklist, I want to polish and improve what was built so that the sprint's deliverables are solid before moving on.
  - [x] `/iterate` checks that all items in the current sprint file are complete; if not, instructs the user to run `/build` first
  - [x] The plugin asks what the user wants to work on — bugs, new features, UX polish, or anything else
  - [x] The plugin does a quick review of current state and surfaces 2-3 observations
  - [x] Work is scoped through a brief interview and converted into a mini-checklist
  - [x] The mini-checklist is sized based on what the work actually requires — no artificial cap
  - [x] Items are appended to the current sprint file under an `## Iteration N` header
  - [x] The user runs `/build` to execute the iteration items
  - [x] Multiple iteration cycles are supported within a single sprint

- As a user, I want the first-run explanation blurb for `/iterate`.
  - [x] Same behavior as described in the Scoping epic

### Sprint Reflection

- As a user who has completed a sprint, I want to review both process and product so that I can improve how I work and verify what I built.
  - [x] `/reflect` runs in two phases, in order:
    1. **Sprint retro (process):** Did the sprint go smoothly? Were items sized right? What blocked progress? What went well?
    2. **Product check:** Does what was built match what was planned? Quality concerns? Any items partially complete?
  - [x] For partially completed stories, the plugin splits them in the PRD: one story with completed acceptance criteria (checked off), one with what's remaining (still open)
  - [x] Story splitting produces self-contained stories, each with their own complete acceptance criteria
  - [x] After the product check, the plugin checks for unvetted items in the PRD and recommends `/refine` if any exist
  - [x] When all PRD items are complete, the plugin recommends `/retro` instead of another sprint
  - [x] Sprint-level process notes are written to `process-notes-sprint-N.md`

- As a user, I want the first-run explanation blurb for `/reflect`.
  - [x] Same behavior as described in the Scoping epic

### Refining Unvetted Items

- As a user with unvetted items in the PRD, I want to run them through a compressed planning interview so that they're properly defined before being pulled into a sprint.
  - [x] `/refine` reads the PRD and identifies all items in the Unvetted/Proposed section
  - [x] For each item (or group of related items), the plugin conducts a compressed interview — enough to produce proper user stories with acceptance criteria
  - [x] The plugin checks for spec impact: new data models, changed component interactions, new service boundaries
  - [x] Spec implications are discussed with the user during the conversation, not silently applied
  - [x] Once the user agrees, the plugin updates both `docs/prd.md` (moving items from unvetted to vetted) and `docs/spec.md` (if architecture changes)
  - [x] CLAUDE.md and AGENTS.md are updated if spec changes warrant it

- As a user, I want the first-run explanation blurb for `/refine`.
  - [x] Same behavior as described in the Scoping epic

### Project Retrospective

- As a user who has completed all PRD items, I want a project-level retrospective that looks across all sprints so that I can synthesize what I learned and improve the plugin.
  - [x] `/retro` reads all sprint process notes files, the PRD, the spec, and any other docs artifacts
  - [x] The retro covers both process and product across the entire project — patterns, what worked, what didn't, how the workflow performed
  - [x] The plugin checks for the plugin improvement notes file; if it exists, it explains how to take that file to the plugin's own repo and run the SDD process on the plugin itself
  - [x] A project-level retrospective document is produced

- As a user, I want the first-run explanation blurb for `/retro`.
  - [x] Same behavior as described in the Scoping epic

### Plugin Improvement Feedback

- As a user who notices something the plugin could do better, I want to flag it quickly without interrupting my current work.
  - [x] `/plugin` accepts a note (e.g., `/plugin this refinement step felt too heavy for a one-line change`)
  - [x] The note is appended to a plugin improvement markdown file in the project directory
  - [x] The plugin confirms the note was saved and immediately returns to whatever the user was doing
  - [x] No interruption to the current workflow

### Backlog Management

- As a user, I want ideas that don't belong in the current project to be captured so that I don't forget them and can act on them later.
  - [x] During any planning phase, items identified as out-of-scope for the current project are written to `docs/backlog.md`
  - [x] Each backlog entry captures: what the item is, why it was deferred, what it connects to in the current project, any constraints or decisions discussed, and enough context to pick it up cold months later
  - [x] During `/iterate`, backlog items are surfaced for consideration
  - [x] Small items that fit the current project can be pulled into an iteration
  - [x] Large items that deserve their own project trigger the seed file flow

- As a user who wants to spin off a backlog item into its own project, I want a rich seed file generated so that the new project starts with real context.
  - [x] The seed file includes: the backlog entry content, relevant architectural decisions from the current spec, conventions from AGENTS.md, and any other context that would apply to the new project
  - [x] The plugin explains the path: take the seed file to a new project directory, run `/scope` to start fresh with the seeded context
  - [x] The plugin advises finishing the current project (including `/retro`) before starting the new one

### Cross-Cutting: Command Explanations

- As a user, I want each command to briefly explain itself the first time it runs in a project so that I have context without needing to remember the onboarding walkthrough.
  - [x] Every command outputs a brief explanation of its purpose the first time it runs in a project
  - [x] The explanation is plain text output, not gated — the user can scroll past it
  - [x] For commands that run multiple times (`/sprint`, `/build`, `/iterate`, `/reflect`, `/refine`), the explanation only shows on the very first invocation in the project
  - [x] Display state is tracked in a per-project state file in `docs/`

### Cross-Cutting: Living Documents

- As a user, I want the planning documents to be updatable when the project evolves so that they stay accurate and useful.
  - [x] `docs/prd.md`, `docs/spec.md`, `CLAUDE.md`, and `AGENTS.md` are all living documents
  - [x] The plugin's default stance is to resist changes to scope/PRD/spec — new items go to backlog or unvetted section
  - [x] When enough has accumulated that the project has fundamentally shifted, the plugin recognizes this and suggests re-scoping
  - [x] Re-scoping is framed as a deliberate, weighty decision — not something done casually

### Cross-Cutting: Process Notes

- As a user, I want decision history tracked across all phases so that I can look back at why things were decided.
  - [x] Planning phases (scope, PRD, spec) write to a shared `process-notes.md`
  - [x] Each sprint cycle writes to its own `process-notes-sprint-N.md`
  - [x] Process notes capture: decisions made and why, pushback and responses, questions and struggles, what resonated

## Unvetted

<!-- Items added during sprints that have not yet been refined. /sdd:refine processes these before future sprints. Do not remove this section. -->

## What We're Building

Everything above constitutes the complete product. The acceptance criteria for each story define "done." The plugin is built as a Claude Code plugin using the slash command / skills architecture, adapted from the hackathon-in-a-plugin reference implementation.

Key architectural note: tracking is markdown-first. No GitHub issues, milestones, or project boards in this version. All artifacts live in the project directory, version controlled with the code.

## What We'd Add With More Time

- **GitHub integration** — issues, milestones, labels, and project boards as an optional tracking layer alongside or replacing markdown files
- **Jira / Linear integration** — same concept, different platforms. Would require an abstraction layer over the tracking backend
- **Multi-agent support** — AGENTS.md is generated now, but dedicated configuration files for other AI agents (Gemini, Codex, Copilot) could be added
- **Automated plugin improvement** — currently user-flagged and manually applied. Could evolve toward the plugin analyzing its own process notes and suggesting improvements automatically
- **Team collaboration features** — PR workflows, code review integration, assignment and notification when multiple people are involved
- **Project templates** — pre-seeded scope/PRD/spec for common project types (web app, API, CLI tool) to accelerate the planning phases

## Non-Goals

- **GitHub/Jira issue tracking in v1** — tracking is markdown-only. Integration with external project management tools is a future enhancement, not a first-version concern.
- **Hardcoded tone or personality** — the plugin's communication style is driven by user preference from `/onboard`, not baked into the commands.
- **Time-boxed sprints** — sprints are grouped by logical coherence and dependency, not by time estimates. The AI builds fast; the value is in meaningful chunks, not two-week cycles.
- **Automatic plugin self-modification** — the `/plugin` command captures feedback and `/retro` explains how to act on it, but the plugin never modifies itself without explicit user action.
- **Cross-project backlog management** — the backlog lives in `docs/backlog.md` per project. Cross-project visibility is handled by generating seed files, not by a global backlog system.
