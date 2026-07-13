---
name: checkpoint
description: Reads the current session and prints a tailored /compact <instructions> string naming what to preserve. Runs anytime; writes no file; never runs /compact itself.
disable-model-invocation: true
---

# /sdd:checkpoint

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command.

`/sdd:checkpoint` is the on-demand replacement for the retired between-rounds context auto-suggest: context management in v6 is user-driven, and this command is the tool. The user runs it when the session is getting long; it hands back a `/compact` instruction string tuned to what actually matters right now.

## Anytime

Runs **anytime, independent of chain position**. No prerequisites beyond `skills/sdd-guide/SKILL.md` itself — no `plan.md` check, no state file requirement, no chain-position validation. It works mid-interview, mid-build, or in a session with no SDD project at all.

## State Updates

Update `lastCommand` in `docs/project-state.json` to `"/sdd:checkpoint"` **only if the file already exists**. Do not create it — checkpoint must work outside SDD projects.

## Behavior

1. **Read the session.** Survey the current conversation: what command or task is in flight, what decisions have been settled, what is still open, and which files or artifacts anchor the work.
2. **Compose the instruction string.** Emit a tailored instruction string for Claude Code's `/compact <instructions>` form, naming specifically what the summary must preserve — the in-flight task and its next step, settled decisions, open questions, and load-bearing file paths. Tailored means derived from this session's actual content; never a generic boilerplate string.
3. **Print it to chat.** Present the string ready to copy, e.g.:

   ```
   /compact Preserve: we are mid /sdd:refine marker walk on docs/plan.md, 3 markers resolved, currently on [GAP g2] (session expiry); keep the resolution decisions for a1/a2/g1 and the carry-forward candidates list.
   ```

4. **Stop.** The command returns immediately after printing.

## Hard Constraints

- **Writes no file.** The instruction string goes to chat only — no scratch file, no state write beyond the `lastCommand` touch above.
- **Never runs `/compact` itself.** The user copies and runs the string; the command only composes it.
- **Distinct from `/sdd:pause`.** Checkpoint writes no resume file and is not a session-suspend mechanism. If the user needs durable cross-session state (surviving `/clear`, not just `/compact`), point them at `/sdd:pause` instead.
- **No handoff.** Checkpoint emits no end-of-command handoff — it prints the string and returns.
