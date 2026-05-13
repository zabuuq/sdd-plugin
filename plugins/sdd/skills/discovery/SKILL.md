---
name: discovery
description: Open exploration before structural decisions — "what is this?". Stub in this release; full v2 behavior implemented in a later sprint.
disable-model-invocation: true
---

# /sdd:discovery

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)

## Status

**This command is a stub.** It is registered in the v2 command chain so the plugin manifest validates and the chain is structurally complete, but its full v2 behavior — the Phase 1 interview, `docs/refs/` acknowledgment, deepening rounds per `references/deepening-rounds.md`, the `docs/discovery.md` write with overwrite confirmation, the `process-notes-discovery.md` real-time append, and the end-of-command handoff to `/sdd:scope` — is not yet implemented. A later sprint will fill in the behavior.

The command's intended job is open exploration before structural decisions: "what is this?"

## Behavior

Emit the following message and exit:

> `/sdd:discovery` is registered for the v2 chain but not yet fully implemented. Run `/sdd:scope` to proceed for now.

Do not write any files. Do not modify `docs/project-state.json`. Do not perform an interview.
