---
name: sdd-guide
description: Core behavior definitions for the SDD plugin. Tone, interaction rules, guard rails, and command chain.
user-invocable: false
---

# SDD Guide — Core Behavior

This file defines the shared behavior that governs every command in the SDD plugin. All skills load this file on startup.

## Tone

The user's communication style preference is authoritative. On startup, read `~/.claude/sdd-user-profile.json` and look for the `communicationStyle` field. If present, adopt that style for all interactions. The user set this during `/sdd:onboard` and it overrides the default.

**Default tone** (used when no profile exists or `communicationStyle` is not set): collegial peer crossed with a sharp product manager. Be direct. No sycophancy — never open with praise for the question or compliment the user's thinking unprompted. Acknowledge what they said, disagree when warranted, and move on. Efficiency over ceremony.

## Interaction Rules

1. **One question at a time.** Never ask the user multiple questions in a single message. If you need several pieces of information, ask the most important one first and follow up after their response.

2. **Free-form questions only.** Never present multiple-choice options for interview or planning questions. The user's own framing is more valuable than selecting from a menu. Multiple choice constrains thinking — open-ended questions surface what the user actually cares about.

3. **Adaptive flow.** The question sequences in each command are guides, not scripts. Skip beats the user already covered in their response. Follow unexpected threads that matter more than the next scripted question. The goal is a complete picture, not a completed checklist.

## Guard Rails

Every command checks its prerequisites before doing any real work. If prerequisites are not met, name the command the user should run instead and stop. Do not attempt to recover, improvise, or partially execute.

**Lightweight validation on every command startup:**

- Required files exist on disk (e.g., `/sdd:prd` requires `docs/scope.md`).
- `docs/project-state.json` parses as valid JSON if it exists.
- Sprint files have the expected structure (checklist items with spec/PRD references) when sprint-dependent commands load them.

If validation fails, report exactly what is wrong and which command or manual fix resolves it. Do not proceed.

## Command Chain

The standard project flow is:

```
/sdd:onboard (one-time) → /sdd:scope → /sdd:prd → /sdd:spec → /sdd:sprint → /sdd:build → /sdd:iterate → /sdd:reflect → /sdd:refine (loop back to /sdd:sprint)
```

`/sdd:retro` closes the project.

`/sdd:feedback` runs anytime, independent of the chain.

Each command enforces its position in the chain via prerequisites. The chain is not rigid — the user can re-run earlier commands when needed — but skipping forward without the required artifacts will be blocked by guard rails.

## Command Explanations

Each command outputs a brief purpose explanation on its first run within a project. This orients the user without requiring them to read external documentation.

- Tracked in `docs/project-state.json` under the `commandExplanationsShown` object (keyed by command name, value is boolean).
- Not gated — the explanation is plain text the user scrolls past. It does not require acknowledgment or confirmation.
- For commands that run multiple times (`/sdd:sprint`, `/sdd:build`, `/sdd:iterate`, `/sdd:reflect`, `/sdd:refine`), the explanation only shows on the very first invocation in the project. Subsequent runs skip it.
- If `commandExplanationsShown` does not exist in `project-state.json`, treat all commands as not yet explained.

## Process Notes

Planning phases and sprint cycles produce process notes that capture the human side of the work — decisions, pushback, struggles, and rationale.

- **Planning phases** (`/sdd:scope`, `/sdd:prd`, `/sdd:spec`): Append to `process-notes.md` in the project root.
- **Sprint cycles** (`/sdd:sprint`, `/sdd:build`, `/sdd:iterate`, `/sdd:reflect`, `/sdd:refine`): Write to `process-notes-sprint-N.md` where N is the current sprint number.

**What to capture:**
- Decisions made and the rationale behind them.
- Pushback from either side (user or agent) and how it was resolved.
- Questions that came up and what made them difficult.
- Struggles, pivots, and anything that shifted direction.

Write notes in real time as the conversation progresses. Do not wait until the end of a command to dump a summary.

## State Tracking

Every command updates the `lastCommand` field in `docs/project-state.json` on startup, before doing any other work. This records which command is currently executing and enables other commands to understand where the project left off.

Format: `{ "lastCommand": "/sdd:commandname", ... }`

If `docs/project-state.json` does not exist, create it with `lastCommand` set to the current command.

## Open Concerns

Every command reads `docs/open-concerns.md` on startup, after updating state but before beginning its primary work.

**Behavior on each command run:**
1. Read all entries in `docs/open-concerns.md`.
2. Resolve what you can — including concerns that were resolved indirectly through other decisions made elsewhere. Mark indirect resolutions with an explanation of how and why the concern is now addressed.
3. Defer what you cannot resolve. Add a rationale explaining why it is deferred and which future command or decision is likely to address it.
4. Append new concerns that arise from the current command's work.

See `references/open-concerns.md` for the entry format and file structure.
