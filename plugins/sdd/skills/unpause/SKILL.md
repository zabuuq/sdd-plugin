---
name: unpause
description: Zero-argument resume of whatever was paused. Reads lastCommand from docs/project-state.json and the matching docs/<command>-resume.md, loads the source command's SKILL.md, and resumes the interview at the resume file's Pending decision section.
disable-model-invocation: true
---

# /sdd:unpause

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/references/pause-resume.md` (resume-file location, schema, per-section quality bar, distillation discipline)

## State Updates (immediate)

**This command writes nothing to `docs/project-state.json` on startup.** In particular, it does not update `lastCommand`. This is a deliberate exception to the state-tracking rule in `skills/sdd-guide/SKILL.md > ## State Tracking`, which otherwise requires every command to write its own name to `lastCommand` before doing any other work.

`/sdd:unpause`'s job is to delegate to the command named in `lastCommand`, not to act as a separate chain command. The in-flight command's name must stay in `lastCommand` across the unpause invocation — that value is exactly what `/sdd:unpause` reads to know what to resume. If `/sdd:unpause` wrote `"/sdd:unpause"` into `lastCommand`, the name of the command being resumed would be lost.

This follows the same no-clobber pattern established by `/sdd:feedback` (AC `aaaa` in `docs/prd.md`, the Bug Fix 1 design) and by `/sdd:pause` (see `plugins/sdd/skills/pause/SKILL.md > ## State Updates (immediate)`): utility commands that operate alongside an in-flight chain command must not overwrite `lastCommand`.

Do not read or process `docs/open-concerns.md` on startup. `/sdd:unpause` is a resume-and-delegate utility, not a chain command; it does not run the open-concerns protocol. The resumed source command, once dispatched, runs its own startup behavior per its own `SKILL.md`.

## Zero-argument rule

`/sdd:unpause` accepts NO positional arguments and NO flags. It is a zero-argument command. Everything it needs is sourced from `docs/project-state.json` (the `lastCommand` value) and the matching `docs/<command>-resume.md` file. If a user supplies arguments, ignore them — do not branch on them, do not echo them, do not treat them as overrides for the resumed command.

## Behavior

Run the steps below in order.

### 1. Read `lastCommand`

Read `lastCommand` from `docs/project-state.json`. This value names the command to resume. Per `/sdd:pause`'s no-clobber design (see `plugins/sdd/skills/pause/SKILL.md > ## State Updates (immediate)`), `lastCommand` still holds the in-flight command name at this point — `/sdd:pause` did not overwrite it, and `/sdd:unpause` has not overwritten it either.

### 2. Missing-resume-file check

Before loading anything else, verify that a resume file actually exists for the value just read from `lastCommand`. This check fires after step 1's read and before steps 3 and 4 — no source `SKILL.md` is loaded and no interview work begins until the check passes.

**If `lastCommand` is unset, null, or empty** — including the case where `docs/project-state.json` does not exist or has no `lastCommand` field — there is no in-flight command to resume and no resume-file path to derive. Do not attempt to construct a path from an empty value. Emit a single message that states nothing is paused and points the user at starting fresh, for example:

> Nothing to resume — `lastCommand` is unset in `docs/project-state.json`. If no project exists yet, run `/sdd:onboard`; otherwise run the chain command you want to start (e.g., `/sdd:discovery`, `/sdd:refine`).

Then exit cleanly. Do not write to `docs/project-state.json`. Do not write any other files.

**If `lastCommand` is set**, derive the expected resume-file path the same way step 3 does — `docs/<command>-resume.md`, where `<command>` is the `lastCommand` value with the `/sdd:` prefix stripped. Check whether that file exists.

**If the resume file does not exist at the expected path**, exit cleanly. Do not read the source `SKILL.md`. Do not attempt to resume the interview. Emit a message that:

1. Names the missing file path literally — `docs/<command>-resume.md`, with the actual prefix-stripped command name substituted (e.g., `docs/refine-resume.md` when `lastCommand = "/sdd:refine"`).
2. Surfaces two next-step options for the user:
   - **(a)** Run `/sdd:pause` — if a command is currently in-flight and the user meant to pause it before resuming. This covers the case where the user mistakenly ran `/sdd:unpause` first.
   - **(b)** Re-run the original command from scratch — e.g., `/sdd:refine` when `lastCommand = "/sdd:refine"` — if nothing was paused and the user wants to start fresh.

For example:

> No resume file at `docs/refine-resume.md`. Either run `/sdd:pause` first if `/sdd:refine` is in-flight and needs to be snapshotted, or re-run `/sdd:refine` from scratch to start over.

Then exit cleanly. Do not modify `docs/project-state.json` — `lastCommand` must not be clobbered on this exit path, per the no-clobber rule in `## State Updates (immediate)` above. Do not write any other files. Do not append to process notes. Do not emit a handoff.

**If the resume file exists**, fall through to step 3.

### 3. Locate and read the resume file

The resume file lives at `docs/<command>-resume.md`, where `<command>` is the value of `lastCommand` with the `/sdd:` prefix stripped. Concrete examples:

- `lastCommand = "/sdd:discovery"` → `docs/discovery-resume.md`
- `lastCommand = "/sdd:refine"` → `docs/refine-resume.md`
- `lastCommand = "/sdd:discovery"` → `docs/discovery-resume.md`

This path-derivation rule is the same one `/sdd:pause` uses on the write side — see `plugins/sdd/skills/pause/SKILL.md > ### Derive the resume-file path` and `references/pause-resume.md > ## Location convention` for the canonical convention.

Read this file before any other interview work. It is the seed for the resumed context: the locked decisions, the round status, and the literal pending question at pause time.

### 4. Read the source command's `SKILL.md`

Read `plugins/sdd/skills/<command>/SKILL.md`, where `<command>` is the same prefix-stripped `lastCommand` value used in step 3. Concrete examples:

- `lastCommand = "/sdd:discovery"` → `plugins/sdd/skills/discovery/SKILL.md`
- `lastCommand = "/sdd:refine"` → `plugins/sdd/skills/refine/SKILL.md`

Loading the source `SKILL.md` brings in that command's loading directives, behavior, and any reference files it pulls in transitively. The source `SKILL.md` governs the resumed flow from this point forward — `/sdd:unpause` is not a parallel command, it is a dispatcher that hands control back to the paused command.

Steps 3 and 4 must both complete before resuming the interview. Order between them is flexible; the requirement from the spec is that the resume file and the source `SKILL.md` are both loaded before any interview work happens.

### 5. Resume at the `Pending decision` section

Open the resume file's `Pending decision` section. That section captures the literal open question at pause time — the question, what has been weighed, and the options on the table (per `references/pause-resume.md > ## Per-section quality bar`).

Re-pose that question to the user with the same wording, the same context, and the same options as recorded. This is the literal pending decision, not a generic "where were we?" gesture. Once the user answers, proceed with the source command's interview from that point, governed by the source `SKILL.md` loaded in step 4.

The `Confirmed decisions`, `Setup-already-done`, `Round status`, and `Files for next-session` sections of the resume file are the surrounding context the source command needs to pick up cleanly — treat them as already-settled, not as questions to re-ask.

Resume-file cleanup is the resumed source command's responsibility, not `/sdd:unpause`'s. See `references/pause-resume.md > ## Cleanup` for when and how the resumed command deletes `docs/<command>-resume.md`.
