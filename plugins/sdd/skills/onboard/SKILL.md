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

None beyond the GitHub gate below. This is the first command in the SDD workflow.

## Behavior

### Step 0: GitHub Gate (hard block)

GitHub is a hard dependency of the SDD workflow — `/sdd:build` creates issues, branches, and PRs, and `/sdd:resolve-pr` manages them. Verify both of the following before anything else:

1. **A Git repository exists.** Run `git rev-parse --is-inside-work-tree` in the project directory.
2. **`gh` is authenticated.** Run `gh auth status`.

**If either check fails, hard-block.** This is a block, not a warning: do not continue onboarding, and tell the user that no SDD command — starting with `/sdd:discovery` — can proceed until the gate passes. Emit a clear remediation message naming exactly what failed:

- No repository: "No Git repository found. Run `git init` (and connect a GitHub remote) first, then re-run `/sdd:onboard`."
- `gh` unauthenticated or missing: "GitHub CLI is not authenticated. Run `gh auth login` first, then re-run `/sdd:onboard`."

Only when both checks pass, continue to Step 1.

### Step 1: Check for Existing Profile

Check if `~/.claude/sdd-user-profile.json` exists.

**If it exists (update flow):**

- Read the file and announce: "I found an existing SDD profile."
- Surface the user's current preferences from **two sources**, with each preference clearly **labeled by its origin** so the user can tell where it came from:

  1. **From `~/.claude/sdd-user-profile.json`** — display every field present in the file, each tagged as **`originally-onboarded`** (the value was set during an `/sdd:onboard` run). Fields currently in scope: `communicationStyle`, `gitPreference`, `showWorkflowExplanation`, `feedbackLocalPath`, `handoffWarningShown`, `defaultSprintMode`. Skip any field that is absent from the file (don't display a placeholder).
  2. **From `~/.claude/sdd-cross-project-patterns.md`** — read the file as plain markdown and surface its high-level content (entry titles or one-line summaries — whatever is structurally available). Tag each surfaced item as **`retro-written`** (the pattern was written by `/sdd:retro`, not by onboarding).

  Use the literal label strings `originally-onboarded` and `retro-written` (or display them as "from your onboarding profile" / "from your cross-project retro patterns" — both phrasings are acceptable as long as the origin is unambiguous to the user).

- **Silent missing-file branch:** if `~/.claude/sdd-cross-project-patterns.md` does not exist (the user has never run `/sdd:retro`), **surface only the profile preferences and say nothing about the missing patterns file.** Do not emit a warning, do not emit a "no patterns yet" header, do not mention the file. The absence is invisible to the user.
- **Read-only on cross-project patterns:** the update flow **never writes** to `~/.claude/sdd-cross-project-patterns.md`. That file is owned by `/sdd:retro`. The update flow only reads it for display, and only ever writes back to `~/.claude/sdd-user-profile.json`.
- Ask which preference they'd like to update. The user can update any individual preference without re-answering everything. Only profile fields are editable here — cross-project retro patterns are surfaced for context but cannot be edited in this flow.
- After updates, apply the **seed-only-when-missing rule** from Step 6 (see "No-overwrite rule" there) for `handoffWarningShown` and `defaultSprintMode`: if either field is already present in the existing profile, preserve its current value; only seed defaults for fields that are missing.
- Write the file back with the updated `updatedAt` timestamp.
- Done — skip steps 2-6.

**If it does not exist (full onboarding):**
- Continue to step 2.

### Step 2: Workflow Explanation

Output the following as plain text. This is NOT a question — do not wait for acknowledgment. Just output it and continue to step 3.

---

**The SDD Workflow**

SDD (Spec-Driven Development) is a structured, interview-driven process for planning and building software. Here's how it works:

**Planning:**
- `/sdd:discovery` — The brainstorm-first entry point. Ingests whatever you have (text, images, audio in `docs/refs/` or pasted), interviews you from that material, and auto-drafts `docs/plan.md` — one document carrying intent, requirements, and architecture, with the AI's assumptions, gaps, and concerns tagged inline as markers.
- `/sdd:refine` — Walks the markers in `plan.md` one at a time, resolves them with you in place, and finalizes the document (version `0.x` → `1.0`).
- `/sdd:validate` (optional) — Offers an adversarial self-critique and reconciles external validation files dropped into `docs/validation/`, one difference at a time.

**Build:**
- `/sdd:prototype` — Builds a disposable HTML/CSS/JS prototype at `prototype/` with AI-authored navigation paths, so you can walk the product before the real build.
- `/sdd:build` — Turns refined work into GitHub issues and runs the autonomous build-loop: one issue → one branch → one PR, each checked by a separate agent. Your PR review is the only gate — nothing auto-merges.

**Project Close:**
- `/sdd:retro` — Lists the lessons captured during the project for promotion, and reports timing and issue-queue status.

**Anytime:**
- `/sdd:checkpoint` — Prints a tailored `/compact` instruction string when context is getting long.
- `/sdd:resolve-pr` — Works review feedback on build-loop PRs and cleans up branches of merged ones.
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

"How do you prefer your AI collaborator to communicate? Describe it in your own words — this is your preference and it'll be applied across all SDD commands."

If the user asks for examples or seems unsure, you may offer a few suggestions conversationally (e.g. terse and direct, conversational and collaborative, pushes back hard, patient and thorough), but don't lead with them — the question itself stays open.

Store the answer as `communicationStyle` (string — their exact words or chosen style).

### Step 5a: `/sdd:feedback` Beat

Output the following as plain text. This is NOT a question — do not wait for acknowledgment. Just output it and continue to step 5b.

---

**About `/sdd:feedback`**

`/sdd:feedback` captures a feedback note along with the surrounding context — what command you were in, what was happening — and returns immediately. It does not advance the SDD chain or interrupt whatever you were doing.

Use it anytime, independent of the chain, when something the plugin does feels off, surprising, or could be better. Don't save it for the end of a sprint — flag it the moment you notice it.

Notes are appended to the current project's `docs/sdd-feedback.md`. If you set `feedbackLocalPath` (next question), a copy is also forwarded to `<feedbackLocalPath>/docs/sdd-feedback.md` so you can collect feedback from across every project that uses the plugin in one central place.

---

### Step 5b: Feedback Local Path

Ask the user (one question):

"Do you want `/sdd:feedback` to forward a copy of every feedback note to a separate project — typically a personal plugin-development project where you collect feedback from across all the projects you use SDD in? If so, give me the absolute path to that project's root. Leave the answer blank to skip — `/sdd:feedback` will still write to the current project's `docs/sdd-feedback.md`."

Write logic:

- If the answer is a non-empty path, store it as `feedbackLocalPath` (string) in the profile.
- If the answer is empty, whitespace-only, or the user explicitly skips, omit the `feedbackLocalPath` field from the profile entirely (writing `null` is semantically equivalent per spec — either is acceptable).
- **Do not re-ask within the same run.** A skip here is final for this onboarding pass. The user can re-run `/sdd:onboard` later to set it.

### Step 5c: Plugin Update Mechanics

Output the following as plain text. This is NOT a question — do not wait for acknowledgment. Just output it and continue to step 6.

---

**About plugin updates**

In Claude Code, third-party plugin auto-update is off by default, so SDD will not pull new versions on its own unless that setting is changed. Auto-update behavior can be toggled via `/plugin` → Marketplaces. Separately, `/reload-plugins` refreshes loaded plugins from disk — handy after an out-of-band install or a local edit to a plugin source.

---

### Step 6: Write User Profile

Write `~/.claude/sdd-user-profile.json` with the following schema:

```json
{
  "version": 1,
  "createdAt": "<current ISO-8601 timestamp>",
  "updatedAt": "<current ISO-8601 timestamp>",
  "communicationStyle": "<user's chosen style>",
  "gitPreference": <true or false>,
  "showWorkflowExplanation": <true or false>,
  "feedbackLocalPath": "<absolute path or null>",
  "handoffWarningShown": false,
  "defaultSprintMode": null
}
```

**Seed values for new v2 fields:**

- `handoffWarningShown`: write the literal value `false` on profile creation. This flag is flipped to `true` later by the first interview command that emits a handoff warning to the user.
- `defaultSprintMode`: write the literal value `null` on profile creation. (Omitting the field entirely is semantically equivalent to `null` per spec — either is acceptable.) This field can later be set to a user-chosen sprint mode by `/sdd:plan`.

**No-overwrite rule (seed-only-when-missing):**

When the profile **already exists** (the update flow from Step 1), do **not** overwrite `handoffWarningShown` or `defaultSprintMode` if they are already present in the file with any value. Seed defaults **only when the field is missing**.

Concretely:

- If `handoffWarningShown: true` has been written by a downstream command (e.g., the first interview that emitted a handoff), **leave it as `true`**. Do not reset it to `false`.
- If `defaultSprintMode` has been set to a value by `/sdd:plan` (e.g., `"step-by-step"` or `"autonomous"`), **leave that value in place**. Do not reset it to `null`.
- Only when a field is absent from the existing profile JSON does `/sdd:onboard` write the default (`false` or `null` respectively).

This rule applies to both newly-created profiles (where all fields are absent and therefore all defaults are seeded) and to updates of existing profiles (where any already-present value wins over the default).

The same no-overwrite rule does **not** apply to `communicationStyle`, `gitPreference`, `showWorkflowExplanation`, or `feedbackLocalPath` — those are the editable preferences the user explicitly chose to update in the update flow, and the update flow writes the user's new chosen value as normal.

Confirm to the user that their profile has been saved at `~/.claude/sdd-user-profile.json` and mention that re-running `/sdd:onboard` at any time will update that same profile file.

Then close with a heads-up that names `/sdd:discovery` as the recommended next command to start their first project, and flags that `/sdd:discovery` is an interview — not a single-prompt command — so they should expect a back-and-forth interview flow rather than a one-shot question.

## Important Reminders

- Follow one-question-at-a-time from sdd-guide. Steps 3, 4, 5, and 5b are each separate questions — ask one, wait for the answer, then ask the next.
- The workflow explanation in step 2, the `/sdd:feedback` beat in step 5a, and the plugin update mechanics beat in step 5c are output text, not questions. Don't pause for acknowledgment on any of them.
- For the update flow, surface current values from **both** `~/.claude/sdd-user-profile.json` (labeled `originally-onboarded`) and `~/.claude/sdd-cross-project-patterns.md` (labeled `retro-written`), then let the user pick which profile field to change. Cross-project patterns are read-only here — `/sdd:retro` owns that file.
- If `~/.claude/sdd-cross-project-patterns.md` does not exist, **stay silent about it** — surface only the profile preferences. No warning, no empty section, no mention.
- For `handoffWarningShown` and `defaultSprintMode`, follow the **seed-only-when-missing** rule: write the default (`false` / `null`) only when the field is absent. Never overwrite an existing value — downstream commands (`/sdd:plan`, the first handoff-emitting interview) own those fields after the seed.
- Accept any free-form answer for communication style — the examples are suggestions, not constraints.
- If the user skips the `feedbackLocalPath` question in step 5b, omit the field (or write `null`) and do not re-ask in the same onboarding run.
