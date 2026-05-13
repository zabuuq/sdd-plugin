# SDD Plugin — Spec-Driven Development for Claude Code

SDD is a Claude Code plugin that guides you through a structured, interview-driven workflow for planning and building software projects. It replaces ad-hoc prompting with a repeatable process: scope the project, produce a product requirements document, write a technical spec, then execute in sprint cycles with built-in retrospectives. Every phase produces real Markdown artifacts in your repository, so the project's knowledge base grows alongside the code.

## Installation

### From a plugin marketplace

```bash
# Add the marketplace (one-time)
/plugin marketplace add zabuuq/sdd-plugin

# Install the plugin
/plugin install sdd@sdd-marketplace
```

### Local development

```bash
# Clone the repository, then run Claude Code with the plugin directory
claude --plugin-dir ./plugins/sdd
```

## Commands

SDD provides 13 commands organized into four workflow phases.

### Planning Phase

| Command | Description |
|---------|-------------|
| `/sdd:onboard` | One-time setup. Creates a global user profile with your communication style, git preferences, and workflow explanation preference. |
| `/sdd:discovery` | Open exploration before structural decisions. Reads files placed in `docs/refs/` and runs an interview to capture "what is this?" |
| `/sdd:scope` | Defines the project boundary — what's in, what's out. Narrower in v2; exploration moved to `/sdd:discovery`. |
| `/sdd:prd` | Produces a product requirements document from the scope. User stories grouped into epics with testable acceptance criteria. |
| `/sdd:spec` | Generates a technical specification covering architecture, data models, and file structure. Also produces CLAUDE.md and AGENTS.md for your project. |

### Sprint Loop

| Command | Description |
|---------|-------------|
| `/sdd:plan` | Pulls a logical batch of work from the PRD into a buildable sprint checklist with spec references and verification steps. |
| `/sdd:build` | Executes the sprint checklist. Supports step-by-step mode (one item per session) and autonomous mode (multiple items with checkpoints). Wraps up with PRD checkoff, story splitting, and a "anything notable?" beat that closes the sprint. |
| `/sdd:polish` | Optional post-sprint cleanup. Scopes bugs, features, or UX improvements through a brief interview and appends them to the sprint. |
| `/sdd:refine` | Runs unvetted PRD items through a compressed planning interview. Updates spec and related documents if needed. |

### Project Close

| Command | Description |
|---------|-------------|
| `/sdd:retro` | Project-level retrospective across all sprints. Synthesizes patterns and explains how to act on plugin feedback. |

### Anytime

| Command | Description |
|---------|-------------|
| `/sdd:pause` | Distill the in-flight command's conversation into a resume file at `docs/<command>-resume.md` for multi-session resume. |
| `/sdd:unpause` | Zero-argument resume of whatever was paused — reads `lastCommand`, loads the matching resume file, and picks up. |
| `/sdd:feedback` | Quick flag for plugin improvement ideas. Captures your note with context and returns immediately. Usage: `/sdd:feedback [your note]` |

## Workflow

```
Planning Phase       /sdd:onboard → /sdd:discovery → /sdd:scope → /sdd:prd → /sdd:spec

Sprint Loop          /sdd:plan → /sdd:build → /sdd:polish (optional) → /sdd:refine (if unvetted items)
                         ↑                                                                   │
                         └──────────────────── loop back ────────────────────────────────────┘

Project Close        /sdd:retro

Anytime              /sdd:pause, /sdd:unpause, /sdd:feedback
```

The **planning phase** runs once per project. You move through each command in order, and each one builds on the artifacts produced by the previous command.

The **sprint loop** repeats as many times as needed. Each cycle plans a batch of work, builds it (with `/sdd:build` closing the sprint in its wrap-up phase), optionally polishes via `/sdd:polish`, and runs `/sdd:refine` against any unvetted items that emerged. After the loop, you return to `/sdd:plan` for the next batch.

**/sdd:retro** runs once when the project is complete. It reads all sprint process notes to produce a project-level summary and captures cross-project patterns to your user profile.

**/sdd:pause** and **/sdd:unpause** are anytime utilities for suspending and resuming the current command across sessions. **/sdd:feedback** can be used at any point to flag ideas for improving the plugin itself.

## Quick Start

1. **Install the plugin** using one of the methods above.

2. **Run onboard** to set up your user profile:
   ```
   /sdd:onboard
   ```

3. **Start a project** by navigating to your project directory and running scope:
   ```
   /sdd:scope
   ```
   If you have initial ideas written up, place them in `docs/seed.md` before running `/sdd:scope` and they will be used as starting context.

4. **Continue through the planning phase** in order:
   ```
   /sdd:prd
   /sdd:spec
   ```

5. **Enter the sprint loop:**
   ```
   /sdd:plan
   /sdd:build
   ```

6. **Optionally polish and refine** after each sprint, then loop back to `/sdd:plan` for the next one.

## Artifacts

SDD creates the following files in your project:

### Project documents (in `docs/`)

| File | Created by |
|------|-----------|
| `scope.md` | `/sdd:scope` |
| `prd.md` | `/sdd:prd` |
| `spec.md` | `/sdd:spec` |
| `sprint-N.md` | `/sdd:plan` |
| `backlog.md` | All planning commands (overflow items) |
| `open-concerns.md` | `/sdd:scope` (updated by every command) |
| `project-state.json` | `/sdd:scope` (updated by every command) |
| `sdd-feedback.md` | `/sdd:feedback` |
| `retro.md` | `/sdd:retro` |

### Project root files

| File | Created by |
|------|-----------|
| `CLAUDE.md` | `/sdd:spec` |
| `AGENTS.md` | `/sdd:spec` |
| `README.md` | `/sdd:spec` |
| `process-notes.md` | Planning phase commands |
| `process-notes-sprint-N.md` | Sprint loop commands (`/sdd:plan`, `/sdd:build`, `/sdd:polish`, `/sdd:refine`) |

### Global files

| File | Created by |
|------|-----------|
| `~/.claude/sdd-user-profile.json` | `/sdd:onboard` |

## License

MIT

## Attribution

Adapted from the [hackathon-in-a-plugin](https://github.com/devpost-curriculum) curriculum by Devpost.
