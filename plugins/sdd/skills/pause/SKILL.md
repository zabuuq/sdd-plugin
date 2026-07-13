---
name: pause
description: Distill the in-flight command's conversation into a resume file at docs/<command>-resume.md so a fresh, zero-context model can resume in the same decision state via /sdd:unpause.
disable-model-invocation: true
---

# /sdd:pause

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/references/pause-resume.md` (resume-file location, schema, per-section quality bar, distillation discipline)

## State Updates (immediate)

**This command writes nothing to `docs/project-state.json` on startup.** In particular, it does not update `lastCommand`. This is a deliberate exception to the state-tracking rule in `skills/sdd-guide/SKILL.md > ## State Tracking`, which otherwise requires every command to write its own name to `lastCommand` before doing any other work.

Preserving `lastCommand` is what lets `/sdd:unpause` know which in-flight command to resume. If `/sdd:pause` wrote `"/sdd:pause"` into `lastCommand`, the name of the command actually being paused would be lost and resume would have nothing to dispatch on.

This follows the same no-clobber pattern established by `/sdd:feedback` (AC `aaaa` in `docs/prd.md`, the Bug Fix 1 design): utility commands that operate alongside an in-flight chain command must not overwrite `lastCommand`. The in-flight command's name stays in `lastCommand` across the pause boundary.

Do not read or process `docs/open-concerns.md` on startup. The pause command is a snapshot-and-exit utility; it does not run the open-concerns protocol.

## Startup Gating

Read `lastCommand` from `docs/project-state.json`. Branch on its value before doing any further work.

**Pauseable commands** — `/sdd:pause` proceeds when `lastCommand` is one of:

- `/sdd:discovery`
- `/sdd:refine`
- `/sdd:validate`

If `lastCommand` matches one of the three values above, fall through to the `## Behavior` section below — the resume-file write logic runs there.

**Non-pauseable commands** — when `lastCommand` is one of:

- `/sdd:onboard`
- `/sdd:prototype`
- `/sdd:build`
- `/sdd:retro`
- `/sdd:checkpoint`
- `/sdd:resolve-pr`
- `/sdd:feedback`
- `/sdd:archive`

…or when `lastCommand` is unset (the field is missing, `docs/project-state.json` does not exist, or the value is null/empty), `/sdd:pause` has nothing to snapshot. Emit a single line stating that there is nothing in-flight to pause, for example:

> Nothing in-flight to pause.

Then exit cleanly. Write NO resume file. Write NO other `docs/` outputs. Do not modify `docs/project-state.json`. Do not append to any process-notes file. Do not emit a handoff.

## Behavior

When the startup gate above passes — i.e., `lastCommand` is one of the pauseable commands — run the steps below in order.

### Derive the resume-file path

The resume file lives at `docs/<command>-resume.md`, where `<command>` is the value of `lastCommand` with the `/sdd:` prefix stripped. Concrete examples:

- `lastCommand = "/sdd:discovery"` → `docs/discovery-resume.md`
- `lastCommand = "/sdd:refine"` → `docs/refine-resume.md`
- `lastCommand = "/sdd:validate"` → `docs/validate-resume.md`

This naming convention is canonical — see `references/pause-resume.md > ## Location convention`.

### Write the resume file

Write the resume file per `references/pause-resume.md` — its `## Required sections (exact order)`, `## Per-section quality bar`, and `## Distill, don't dump` rules are canonical. Do not restate them here; load and apply them.

For implementation, the file must contain these seven headings, in this exact order:

1. Status
2. Setup-already-done
3. Confirmed decisions
4. Round status
5. Pending decision
6. Files for next-session
7. Instructions for next-session

Missing sections are a defect. Empty sections are acceptable when genuinely empty (e.g., `Setup-already-done` may be empty if the pause happens before any artifact has been touched), but the heading still appears.

Apply the per-section quality bar from the reference:

- **Status** is 1–2 sentences naming the in-flight command and the user-visible position in its flow.
- **Pending decision** is the literal open question plus what has been weighed and the options on the table — not a topic gesture.
- **Confirmed decisions** carry a one-line rationale and a cross-reference to the artifact or process-note line where each was captured.
- **Instructions for next-session** directly address the resumed agent, naming the resumed command, beat or round number, and the immediate next action.

Apply the five distillation rules from the reference: no verbatim conversation, no agent reasoning trail, no re-explanation of plugin behavior, cross-reference don't restate, length target 1–3 pages of markdown. The agent compresses before writing, not after.

The success bar: a fresh, zero-context model reading this resume file alone arrives at the same decision state the paused conversation was in, with no replay needed.

### Overwrite semantics

If `docs/<command>-resume.md` already exists at the target path — for instance, a prior `/sdd:pause` or a three-tier `/clear` consolidation wrote one earlier in the same in-flight session — overwrite it. The new file is the current state. Do NOT append. Do NOT refuse. Do NOT prompt for confirmation. There is one resume file per in-flight command; the latest snapshot replaces any prior snapshot.

### Final message

After the resume file has been written, emit this single line as the LAST output of the command, verbatim:

```
Paused. Run /sdd:unpause to resume.
```

This overrides the standard handoff template. Per `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff`, `/sdd:pause` is listed as an out-of-pattern command that emits `Paused. Run /sdd:unpause to resume.` in place of the normal two-line handoff. Do not emit a `/clear` instruction. Do not emit a next-command line. Do not emit an outcome-summary line. The literal message above is the entire final output.
