# Open Concerns

**Used by:** all commands

This reference defines the cross-phase concern tracking mechanism that carries unresolved questions and feedback items across command boundaries.

## File Location

`docs/open-concerns.md` in the project root.

## Purpose

Projects generate questions, doubts, and unresolved issues at every stage. Some can be addressed immediately; others belong to a future phase. The open concerns file ensures nothing falls through the cracks when crossing phase boundaries. A question raised during scoping that can only be answered during spec writing will persist until it reaches the right command.

## Entry Format

Each concern is recorded as a structured entry:

```markdown
### [Short descriptive title]

- **Source:** [Command that raised this concern, e.g., `/sdd:scope`]
- **Target:** [Command expected to resolve this, if known, e.g., `/sdd:spec`. Use "Unknown" if unclear.]
- **Status:** [open | resolved | deferred]
- **Concern:** [The actual question, issue, or unresolved item. Be specific.]
- **Resolution:** [How this was resolved, including indirect resolution explanations. Empty if open.]
- **Deferral rationale:** [Why this was deferred and what is expected to resolve it. Empty if not deferred.]
```

## Command Behavior

Every command follows this protocol on startup, after updating `lastCommand` in `docs/project-state.json`:

1. **Read** all entries in `docs/open-concerns.md`. If the file does not exist, create it with an empty structure.

2. **Resolve** what you can. This includes:
   - Concerns directly addressed by the current command's work.
   - Concerns resolved indirectly — where a decision made in another context or an earlier part of the current session effectively answers the question. When marking an indirect resolution, explain how and why the concern is now addressed so the reasoning is traceable.

3. **Defer** what you cannot resolve. Add a rationale that explains:
   - Why this command cannot resolve it.
   - Which future command or decision point is likely to address it.
   - Whether the concern is blocking or informational.

4. **Append** new concerns that arise from the current command's work. Fill in all fields. Set status to `open`. Set target to the command most likely to resolve it, or `Unknown` if unclear.

## File Lifecycle

- Created during `/sdd:scope` (or whichever command first encounters an unresolved concern).
- Updated by every subsequent command.
- Reviewed comprehensively during `/sdd:retro`.
- Any concerns still open at `/sdd:retro` are flagged as unresolved project debt.
