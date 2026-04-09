---
name: feedback
description: Quickly flag something the SDD plugin could do better. Captures the note with context and returns immediately.
disable-model-invocation: true
argument-hint: [your feedback note]
---

# /sdd:feedback

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `docs/project-state.json` — read `currentSprint`, `lastCommand`, and `commandExplanationsShown`

**Skips:** everything else. Do not load the PRD, spec, sprint files, open concerns, deepening rounds, living documents, or any other context. This command is designed to be instant.

## Prerequisites

- `docs/project-state.json` must exist. If missing: "Run `/sdd:scope` first."

If the prerequisite fails, stop immediately. Do not attempt partial execution.

## State Updates (immediate)

Before doing any other work:

1. Set `lastCommand` to `"/sdd:feedback"` in `docs/project-state.json`.

Do NOT read or process `docs/open-concerns.md`. This command skips the open concerns protocol entirely to maintain instant response time.

## First-Run Explanation

Check `docs/project-state.json` for `commandExplanationsShown.feedback`.

**If `feedback` has NOT been shown (or `commandExplanationsShown` does not exist):**

Output the following as plain text. This is not a question — do not wait for acknowledgment. Output it and continue.

---

**What /sdd:feedback does**

Feedback captures a quick note about something the SDD plugin could do better. It saves your note with context (sprint number, last command, timestamp) to `docs/sdd-feedback.md` and returns immediately. These notes are reviewed during `/sdd:retro`.

---

After outputting, set `commandExplanationsShown.feedback` to `true` in `docs/project-state.json`.

**If `feedback` HAS been shown:** Skip the explanation entirely. Do not mention it.

## Behavior

### Step 1: Accept Feedback Note

Read `$ARGUMENTS` for the user's feedback note.

**If `$ARGUMENTS` is empty or not provided**, ask the user a single question:

"What feedback do you have about the SDD plugin?"

Wait for the response. Use that response as the feedback note.

**If `$ARGUMENTS` contains the note**, use it directly. Do not ask any follow-up questions.

### Step 2: Capture Context

Read the following from `docs/project-state.json`:

- **Timestamp:** Generate an ISO-8601 timestamp for the current moment (e.g., `2026-04-09T14:32:00Z`).
- **Current sprint number:** Read from `currentSprint`. If no sprint has been created yet, use `0`.
- **Last command run:** Read from `lastCommand` (the value before this command updated it — use the value that was stored when the file was first read in the Loading step, before the state update).
- **Project directory name:** Derive from the current working directory (the final segment of the path).

### Step 3: Append Entry to sdd-feedback.md

Append the following entry to `docs/sdd-feedback.md`. If the file does not exist, create it with a top-level heading first:

```markdown
# SDD Feedback
```

Then append the entry:

```markdown
### [Timestamp]
- **Sprint:** N
- **After command:** /sdd:[command]
- **Project:** [directory name]
- **Note:** [user's feedback]
```

Where:
- `[Timestamp]` is the ISO-8601 timestamp from Step 2.
- `N` is the current sprint number from Step 2.
- `/sdd:[command]` is the last command run from Step 2.
- `[directory name]` is the project directory name from Step 2.
- `[user's feedback]` is the note from Step 1.

Each new entry is appended to the end of the file. Do not modify existing entries.

### Step 4: Confirm and Return

Output a brief confirmation. Example:

"Feedback saved to `docs/sdd-feedback.md`."

Do not elaborate. Do not suggest next steps. Do not ask follow-up questions. Return immediately.

## Important Reminders

- **Return immediately.** This command must not interrupt the user's workflow. No follow-up questions, no suggestions, no elaboration after the confirmation.
- **No open concerns processing.** This command skips the open concerns protocol entirely.
- **No process notes.** This command does not write process notes.
- **No interview.** The only question this command ever asks is for the feedback note itself, and only when `$ARGUMENTS` is empty.
- **Capture the previous lastCommand.** Read the value of `lastCommand` before overwriting it so the entry reflects what command the user ran before giving feedback.
- **First-run explanation only on the very first `/sdd:feedback` in the project.** Check `commandExplanationsShown.feedback` — do not show it on subsequent runs.
