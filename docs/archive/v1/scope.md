# Spec-Driven Development Plugin

## Idea
A Claude Code plugin that provides a structured, interview-driven workflow for planning and building software projects — from initial scoping through retrospective — producing real artifacts at every stage.

## Who It's For
Jason — a 30-year veteran application developer who builds websites, web applications, and larger multi-service platforms using Claude Code. Built for personal use, public repo with MIT license. Not targeting other users initially, but designed cleanly enough that others could adopt it.

## Inspiration & References
- **The hackathon-in-a-plugin curriculum** — the direct ancestor. The workflow structure (scope → PRD → spec → checklist → build → iterate → reflect) is proven and effective. The hackathon framing, learner profiles, submission artifacts, and time-boxing are what's being stripped away.
- **Jason's existing ChatGPT prompt builder workflow** — a manual version of the same idea: tell the AI to ask questions, plan collaboratively, then build. This plugin replaces that with a repeatable, refined process.
- **Agile development methodology** — sprint planning, iterative refinement, retrospectives, backlogs. The plugin's workflow maps naturally to agile concepts.
- **GitHub project management** — issues, milestones, labels, and project boards as the tracking layer instead of markdown checklists.

## Goals
- Replace the ad-hoc "prompt builder" workflow with a structured, repeatable process.
- Produce artifacts that directly configure the AI's behavior during builds (CLAUDE.md, agents.md) — planning becomes operating instructions.
- Make project progress tangible through GitHub issues, milestones, and boards rather than text files the AI manages in isolation.
- Build a plugin that improves itself over time based on usage feedback.
- Support projects of any size — from a weekend website to a multi-phase microservices platform — by evaluating scope and recommending phase breakdowns when needed.

## What "Done" Looks Like
A working Claude Code plugin with the following slash commands, each performing a structured interview and producing artifacts:

- `/scope` — Collaborative brainstorm that produces a scope document. The plugin interviews the user, pushes back on vague thinking, and distills the conversation into a clear project definition.
- `/prd` — Produces a product requirements document from the scope. Covers users, features, requirements, success criteria.
- `/spec` — Technical specification. Architecture, data models, tech stack, service boundaries. After this phase, an initial CLAUDE.md and agents.md are generated.
- `/checklist` — Converts the spec into actionable work items. These become GitHub issues organized under milestones with labels. The plugin evaluates project size and recommends phase breakdowns for large efforts. CLAUDE.md is finalized before build begins.
- `/build` — Executes the work, consuming GitHub issues. Supports step-by-step and autonomous modes.
- `/iterate` — Optional refinement cycles for polishing after the initial build.
- `/retro` — Retrospective. Reviews what worked, what didn't, surfaces patterns from feedback, and suggests plugin improvements.

Additionally:
- A lightweight project onboard step or integrated context-gathering at the start of `/scope`.
- CLAUDE.md generated progressively through planning phases and finalized before build. Includes tech stack, coding conventions, commit rules, environment strategy.
- agents.md for cross-agent compatibility.
- README.md generated during the build phase.
- A backlog system for capturing deferred ideas that can seed future projects or phases.
- Process notes (markdown) tracking decision history and reasoning across all phases.
- A plugin self-improvement mechanism: feedback collected during project use, stored in the plugin's own repo, with a command (e.g., `/improve`) to review accumulated feedback and propose plugin changes.

## What's Explicitly Cut
- **Hackathon framing** — no learner profiles, no submission artifacts, no hackathon energy or coaching tone. This is a professional tool, not a learning experience.
- **Sycophantic tone** — no "great question," no "love that idea," no unnecessary encouragement. The plugin communicates as a peer — a sharp product manager collaborating at the user's level. It acknowledges, it disagrees when warranted, it moves on.
- **Time-boxing to 3-4 hours** — projects take as long as they take. The plugin adapts to project size rather than constraining to a fixed window.
- **Cross-agent portability as a primary concern** — this is a Claude Code plugin. agents.md is generated for flexibility, but the workflow isn't designed around multi-agent compatibility.
- **Issue template design** — the exact layout and formatting of GitHub issues will be discovered and refined through use, not locked down upfront.
- **Self-improvement automation** — the `/improve` command is in scope, but the plugin won't automatically modify itself. Changes are always reviewed and approved.

## Loose Implementation Notes
- Built as a Claude Code plugin using the slash command / skills architecture from the hackathon plugin as a starting point.
- The hackathon plugin repo is already downloaded as a subdirectory — serves as the reference implementation to adapt from.
- GitHub integration via `gh` CLI for issue/milestone/project board creation.
- CLAUDE.md generation is a progressive artifact — started after spec, updated after PRD, finalized before build.
- Backlog items captured during planning phases, stored in a way that's portable to new project instances.
- Feedback mechanism: notes stored in the plugin's own repo directory, consumed by a dedicated command.
- The plugin should evaluate project scope during planning and actively recommend breaking large projects into phases — functioning as a peer who exercises judgment, not just a tool that takes orders.
- Process notes remain as markdown — they track thinking and decisions, while GitHub issues track work.
