---
name: discovery
description: Brainstorm-first entry point. Ingests whatever the user has (text, images, audio), interviews from that material, and auto-drafts docs/plan.md — the single planning document — at Version 0.1 with assumptions, gaps, and concerns tagged inline.
disable-model-invocation: true
---

# /sdd:discovery

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, guard rails, and feasibility/viability pushback throughout.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/references/deepening-rounds.md`
3. `skills/sdd-guide/references/right-sizing.md` (discovery writes `smallProject`)
4. `skills/sdd-guide/references/markers.md` (marker syntax for the auto-draft)
5. `skills/sdd-guide/references/backlog.md` (defer-to-backlog write trigger and entry format)
6. `skills/sdd-guide/templates/plan-template.md`
7. user profile (`~/.claude/sdd-user-profile.json`)
8. `docs/refs/` (if non-empty)

## State Updates (immediate)

Update `lastCommand` in `docs/project-state.json` to `"/sdd:discovery"`. If the file does not exist, create it per the schema in `skills/sdd-guide/references/living-documents.md`. This update happens first, before the prerequisite checks below, per the state-tracking rule in sdd-guide.

## Prerequisites

1. `~/.claude/sdd-user-profile.json` must exist. If it does not, stop immediately and output: "Run `/sdd:onboard` first." Do not proceed, create the profile, or work around its absence.
2. The GitHub gate must hold: a Git repository exists (`git rev-parse --is-inside-work-tree`) and `gh` is authenticated (`gh auth status`). `/sdd:onboard` enforces this gate at onboarding; re-verify cheaply here. On either failure, stop immediately and output: "GitHub gate failed. Run `/sdd:onboard` to fix it." This is a block, not a warning.

## docs/plan.md Overwrite Confirmation

Check whether `docs/plan.md` exists.

**If it does not exist**, proceed silently — this is the common first-run case.

**If it exists**, the user is re-running `/sdd:discovery` against a project that already has a plan. Completing this command will overwrite it. Confirm before the interview, not after: name the file, state that completing discovery will overwrite it (resetting its version to `0.1`), and ask for a clear yes/no. On affirmative, proceed — no second confirmation fires at write time. On rejection, exit immediately with a closing message that no changes were made; do not run the interview, write any file, or emit a handoff.

## Step 1: Ingest & Inventory

Discovery accepts **text, images, and audio** as ingest input, from two channels:

- **`docs/refs/`** — any files the user placed there before running the command. Read every file: text files as-is, images visually, audio via transcription (below).
- **Pasted material** — anything the user pasted or attached in the conversation when invoking the command (a brain-dump, a doc, screenshots, a voice memo).

**Audio transcription (in-flow).** Audio files are transcribed during ingest rather than rejected. Use the `transcribe` skill (faster-whisper) to produce the transcript, then use the transcript as interview context exactly like a text source. If a transcript file already sits alongside the audio (e.g. `memo.m4a` + `memo.txt`), use the existing transcript instead of re-transcribing.

### Inventory gate

Before any drafting, emit an **inventory** listing every source found, one line per source, stating what it is and which channel it came from:

```
Sources found:
1. docs/refs/voice-memo.m4a — audio, transcribed in-flow (transcript: N words)
2. docs/refs/sketch.jpg — image
3. Pasted brain-dump — text, ~N words
```

Then **block and wait for an explicit user response** before beginning the interview or drafting anything. The inventory message ends with a direct question — e.g. "Is this everything, or is there more to add before we start?" The user may add material (paste more, drop more files into `docs/refs/` and say so — re-scan and re-emit the inventory), correct the reading of a source, or confirm. Only an explicit user response moves the command forward.

### No-input path (first-class)

If `docs/refs/` is missing or empty **and** nothing was pasted, there is nothing to inventory. **Skip the inventory entirely** — do not emit an empty list, do not prompt the user to go gather material, and do not treat this as an error. Enter the interview (Step 2) as a **full brainstorm**: the interview starts from zero and carries the whole load of surfacing the idea. This is a first-class path — a user with nothing but an idea in their head is exactly who discovery serves.

## Step 2: Data-First Pre-Draft Interview

The interview fills what the material doesn't already cover. Its register is "what is this and what should it be?" — intent, audience, requirements-shaping, and architecture-shaping input all belong here, because the auto-draft (Step 3) produces the whole plan.

### Data-first rule

For **every** interview question, **search the provided material for an answer before asking**:

- **When the material answers it:** still ask the question, but state what was found and where — "The voice memo says this is for your local tech group — confirm, or is the audience broader?" Accept a **confirm / decline / add** response: confirm locks the finding in, decline replaces it with the user's answer, add layers detail on top.
- **When the material doesn't answer it:** ask fresh, with no invented attribution.

Never silently adopt a finding without surfacing it — the confirm step is what turns ingested material into settled interview ground. On the brainstorm path (nothing ingested) every question is asked fresh; the data-first rule simply has nothing to search.

### Question coverage

Compose questions to give the auto-draft what the template needs: the idea in plain language, the why, the audience, what "done" looks like, prior art, constraints and non-negotiables, and any architecture-shaping facts already fixed (stack, platform, deployment). Skip anything the material plus confirmations already settled — the point of data-first is spending questions only on what's still unknown.

### Interview mechanics

Standard mechanics per sdd-guide and `references/deepening-rounds.md`:

- One question at a time, free-form, never multiple-choice.
- **~5 questions by default, hard cap of 10.** Past 10, stop and get explicit permission with a stated reason before continuing.
- After the core questions, emit the structured **deepening-round recommendation** per `references/deepening-rounds.md` — a definite continue-with-topic-preview or close-with-reasoning position, never a bare "want another round?". Deepening rounds run per that reference until the user accepts a close.

### smallProject judgment

At the end of the core interview (before any deepening rounds), write the authoritative initial `smallProject` value to `docs/project-state.json`, per `skills/sdd-guide/references/right-sizing.md` — discovery owns this judgment in v6. Apply the reference's signals holistically; when in doubt, write `false`. If `smallProject: true`, the shortened-round defaults from the reference apply to any deepening rounds that follow.

## Step 3: Auto-Draft

Produce `docs/plan.md` from the interview and ingested material.

## Deferrals

When the user surfaces an idea mid-interview that they explicitly want to push off, route the decision through the defer-to-backlog vs drop prompt defined in `skills/sdd-guide/references/backlog.md > ## Write trigger`. An affirmative "defer to backlog" answer writes an entry to `docs/backlog.md` per the entry schema in that reference; a drop answer writes no entry and records the drop in `process-notes-discovery.md` only.

## Process Notes

Per sdd-guide's `## Process Notes` section, append to `process-notes-discovery.md` at the **project root** throughout the command — real-time, as moments happen, not an end-of-command dump. Capture the four categories (decisions, pushback, difficult questions, pivots). Trigger points: after the inventory gate resolves, after each interview question's answer lands, at each deepening-round close, and at any notable mid-conversation moment. If the file does not exist, create it on first append.

## End-of-Command Handoff

Runs as the final step after the auto-draft is written.

Emit the handoff per the canonical template in `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff`, including the first-handoff explanation and `handoffWarningShown` tracking. The `[next-command]` slot is always `/sdd:refine`. Outcome-summary line: `Plan drafted at Version 0.1.` The handoff fires unconditionally at completion.
