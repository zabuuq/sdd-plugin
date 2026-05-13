---
name: onboard
description: One-time setup for the SDD plugin. Creates a global user profile with communication style, git preferences, and workflow explanation preference.
disable-model-invocation: true
---

# /sdd:onboard

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

## Loading

- Load: sdd-guide core only
- Skip: everything else (no project files needed — this is the entry point)

## Prerequisites

None. This is the first command in the SDD workflow.

## Behavior

### Step 1: Check for Existing Profile

Check if `~/.claude/sdd-user-profile.json` exists.

**If it exists (update flow):**
- Read the file and announce: "I found an existing SDD profile."
- Show the current preferences: communication style, git preference, workflow explanation preference.
- Ask which preference they'd like to update. The user can update any individual preference without re-answering everything.
- After updates, write the file back with the updated `updatedAt` timestamp.
- Done — skip steps 2-6.

**If it does not exist (full onboarding):**
- Continue to step 2.

### Step 2: Workflow Explanation

Output the following as plain text. This is NOT a question — do not wait for acknowledgment. Just output it and continue to step 3.

---

**The SDD Workflow**

SDD (Spec-Driven Development) is a structured, interview-driven process for planning and building software. Here's how it works:

**Planning Phase:**
- `/sdd:discovery` — Open exploration before structural decisions. Reads files placed in `docs/refs/` and runs an interview to capture "what is this?"
- `/sdd:scope` — Defines the project boundary — what's in, what's out. We'll interview you, push back on vague thinking, and distill it into a clear project definition.
- `/sdd:prd` — Translates your scope into precise, testable product requirements organized as user stories with acceptance criteria.
- `/sdd:spec` — Designs the technical architecture: stack, file structure, data flow, testing strategy. Produces CLAUDE.md and AGENTS.md for AI agents to build from.

**Sprint Loop:**
- `/sdd:plan` — Pulls a logical batch of work from the PRD into a buildable sprint checklist with spec references and verification steps.
- `/sdd:build` — Executes the sprint checklist. Supports step-by-step (one item per session) or autonomous (works through multiple items with checkpoints) modes. Closes the sprint in its wrap-up phase (PRD checkoff, story splitting, "anything notable?" beat).
- `/sdd:polish` — Optional post-sprint cleanup. Bugs, features, UX improvements — scoped and appended to the sprint.
- `/sdd:refine` — Runs unvetted PRD items through a compressed planning interview before the next sprint.

Then loop back to `/sdd:plan` for the next cycle.

**Project Close:**
- `/sdd:retro` — Project-level retrospective across all sprints. Synthesizes what worked, what didn't, and how to improve. Captures cross-project patterns to your user profile.

**Anytime:**
- `/sdd:pause` and `/sdd:unpause` — Suspend and resume the current command across sessions.
- `/sdd:feedback` — Quickly flag something the plugin could do better. Captures your note with context and returns immediately.

---

### Step 3: Workflow Explanation Preference

Ask the user:

"Would you like to see that workflow explanation at the start of future projects, or would you prefer to skip it?"

Store the answer as `showWorkflowExplanation` (boolean).

### Step 4: Git Preference

Ask the user:

"Do you regularly use git repos for your projects? If yes, SDD commands will check for a repo and offer to initialize one when starting new projects."

Store the answer as `gitPreference` (boolean).

### Step 5: Communication Style

Ask the user (one question, free-form — not multiple choice):

"How do you prefer your AI collaborator to communicate? Here are some styles to consider — pick one or describe your own:

- **Terse and direct** — minimal words, no fluff, just the essential information
- **Conversational and collaborative** — thinks out loud together, more back-and-forth
- **Pushes back hard** — challenges assumptions aggressively, plays devil's advocate
- **Patient and thorough** — detailed explanations, takes time to walk through reasoning

Or describe something else entirely — this is your preference and it'll be applied across all SDD commands."

Store the answer as `communicationStyle` (string — their exact words or chosen style).

### Step 6: Write User Profile

Write `~/.claude/sdd-user-profile.json` with the following schema:

```json
{
  "version": 1,
  "createdAt": "<current ISO-8601 timestamp>",
  "updatedAt": "<current ISO-8601 timestamp>",
  "communicationStyle": "<user's chosen style>",
  "gitPreference": <true or false>,
  "showWorkflowExplanation": <true or false>
}
```

Confirm to the user that their profile has been saved and tell them to run `/sdd:discovery` to start their first project.

## Important Reminders

- Follow one-question-at-a-time from sdd-guide. Steps 3, 4, and 5 are each separate questions — ask one, wait for the answer, then ask the next.
- The workflow explanation in step 2 is output text, not a question. Don't pause for acknowledgment.
- For the update flow, show current values and let the user pick which to change.
- Accept any free-form answer for communication style — the examples are suggestions, not constraints.
