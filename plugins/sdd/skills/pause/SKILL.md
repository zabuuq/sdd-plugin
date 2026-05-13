---
name: pause
description: Distill the in-flight command's conversation into a resume file at docs/<command>-resume.md. Stub in this release; full v2 behavior implemented in a later sprint.
disable-model-invocation: true
---

# /sdd:pause

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)

## Status

**This command is a stub.** It is registered in the v2 command chain so the plugin manifest validates and the chain is structurally complete, but its full v2 behavior — the prerequisite check on `lastCommand` (pauseable values only), the schema-driven write per `references/pause-resume.md`, the overwrite semantics on an existing resume file, and the final `Paused. Run /sdd:unpause to resume.` message — is not yet implemented. A later sprint will fill in the behavior.

The command's intended job is to distill the in-flight command's conversation into a resume file at `docs/<command>-resume.md`.

## Behavior

Emit the following message and exit:

> `/sdd:pause` is registered for the v2 chain but not yet fully implemented.

Do not write any files. Do not modify `docs/project-state.json`.
