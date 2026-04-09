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

SDD provides 11 commands organized into four workflow phases.

### Planning Phase

| Command | Description |
|---------|-------------|
| `/sdd:onboard` | One-time setup. Creates a global user profile with your communication style, git preferences, and workflow explanation preference. |
| `/sdd:scope` | Collaborative brainstorm that interviews you about your project idea, pushes back on vague thinking, and distills everything into a clear scope document. |
| `/sdd:prd` | Produces a product requirements document from the scope. User stories grouped into epics with testable acceptance criteria. |
| `/sdd:spec` | Generates a technical specification covering architecture, data models, and file structure. Also produces CLAUDE.md and AGENTS.md for your project. |

### Sprint Loop

| Command | Description |
|---------|-------------|
| `/sdd:sprint` | Pulls a logical batch of work from the PRD into a buildable sprint checklist with spec references and verification steps. |
| `/sdd:build` | Executes the sprint checklist. Supports step-by-step mode (one item per session) and autonomous mode (multiple items with checkpoints). |
| `/sdd:iterate` | Polish and improve what was built. Scopes bugs, features, or UX improvements through a brief interview and appends them to the sprint. |
| `/sdd:reflect` | Sprint-level retrospective covering process and product. Splits partial stories and recommends next steps. |
| `/sdd:refine` | Runs unvetted PRD items through a compressed planning interview. Updates spec and related documents if needed. |

### Project Close

| Command | Description |
|---------|-------------|
| `/sdd:retro` | Project-level retrospective across all sprints. Synthesizes patterns and explains how to act on plugin feedback. |

### Anytime

| Command | Description |
|---------|-------------|
| `/sdd:feedback` | Quick flag for plugin improvement ideas. Captures your note with context and returns immediately. Usage: `/sdd:feedback [your note]` |

## Workflow

```
Planning Phase       /sdd:onboard → /sdd:scope → /sdd:prd → /sdd:spec

Sprint Loop          /sdd:sprint → /sdd:build → /sdd:iterate → /sdd:reflect → /sdd:refine
                         ↑                                                          │
                         └──────────────────── loop back ───────────────────────────┘

Project Close        /sdd:retro

Anytime              /sdd:feedback
```

The **planning phase** runs once per project. You move through each command in order, and each one builds on the artifacts produced by the previous command.

The **sprint loop** repeats as many times as needed. Each cycle plans a batch of work, builds it, polishes it, reflects on the process, and refines any new items that emerged. After `/sdd:reflect`, you loop back to `/sdd:sprint` for the next batch.

**/sdd:retro** runs once when the project is complete. It reads all sprint retrospectives and process notes to produce a project-level summary.

**/sdd:feedback** can be used at any point to flag ideas for improving the plugin itself.

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
   /sdd:sprint
   /sdd:build
   ```

6. **Iterate and reflect** after each sprint, then loop back for the next one.

## Artifacts

SDD creates the following files in your project:

### Project documents (in `docs/`)

| File | Created by |
|------|-----------|
| `scope.md` | `/sdd:scope` |
| `prd.md` | `/sdd:prd` |
| `spec.md` | `/sdd:spec` |
| `sprint-N.md` | `/sdd:sprint` |
| `backlog.md` | `/sdd:sprint` (overflow items) |
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
| `process-notes-sprint-N.md` | `/sdd:reflect` |

### Global files

| File | Created by |
|------|-----------|
| `~/.claude/sdd-user-profile.json` | `/sdd:onboard` |

## License

MIT

## Attribution

Adapted from the [hackathon-in-a-plugin](https://github.com/devpost-curriculum) curriculum by Devpost.
