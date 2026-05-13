---
name: unpause
description: Zero-argument resume of whatever was paused, sourced from lastCommand. Stub in this release; full v2 behavior implemented in a later sprint.
disable-model-invocation: true
---

# /sdd:unpause

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)

## Status

**This command is a stub.** It is registered in the v2 command chain so the plugin manifest validates and the chain is structurally complete, but its full v2 behavior — reading `lastCommand` from `docs/project-state.json`, loading the matching `docs/<command>-resume.md`, loading the source command's `SKILL.md` before any interview work, resuming at the resume file's `Pending decision` section, and post-completion cleanup of the resume file — is not yet implemented. A later sprint will fill in the behavior.

The command's intended job is a zero-argument resume of whatever was paused, sourced from `lastCommand`.

## Behavior

Emit the following message and exit:

> `/sdd:unpause` is registered for the v2 chain but not yet fully implemented.

Do not write any files. Do not modify `docs/project-state.json`.
