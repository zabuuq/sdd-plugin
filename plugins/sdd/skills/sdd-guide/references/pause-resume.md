# Pause / Resume

**Used by:** `/sdd:pause` (writes), `/sdd:unpause` (reads and deletes), and the `/clear` branch of the three-tier between-rounds context-management model (writes — see `references/context-management.md`).

This reference defines the resume-file location, schema, per-section quality bar, and the distillation discipline that makes resumption reliable. The goal: a fresh, zero-context model reading the resume file alone must arrive at the same decision state the paused conversation was in, with no replay needed.

## Location convention

A resume file lives at:

```
docs/<command>-resume.md
```

`<command>` is the in-flight command name without the `/sdd:` prefix. Examples:

- A pause during `/sdd:scope` writes `docs/scope-resume.md`.
- A `/clear` consolidation during `/sdd:prd` writes `docs/prd-resume.md`.
- A pause during `/sdd:sprint` writes `docs/sprint-resume.md`.

One resume file per in-flight command. If a resume file already exists for that command (e.g., a prior `/clear` consolidation), the writer overwrites it — the new file is the current state.

## Required sections (exact order)

A resume file MUST contain these sections, in this order, with these exact heading names:

1. Status
2. Setup-already-done
3. Confirmed decisions
4. Round status
5. Pending decision
6. Files for next-session
7. Instructions for next-session

Missing sections are a defect. Empty sections are acceptable when genuinely empty (e.g., `Setup-already-done` may be empty if the pause happens before any artifact has been touched), but the heading still appears.

## Per-section quality bar

| Section | Quality bar |
|---|---|
| **Status** | 1–2 sentences. Names the in-flight command and the user-visible position in its flow. Example: "Paused mid-`/sdd:prd`, Phase 2 deepening round 3 of an open-ended deepening sequence." |
| **Setup-already-done** | Bullets of completed, self-contained actions a fresh agent would otherwise re-do. Each bullet is an outcome, not a narration. Example: "Loaded `docs/scope.md` and confirmed the three personas listed there are in scope for the PRD." |
| **Confirmed decisions** | The substantive core. Locked decisions with one-line rationale plus a cross-reference to the artifact or process-note line where each was captured. Example: "Resume-file cleanup is the resumed command's responsibility, not `/sdd:pause` — see `docs/spec.md > Reference: pause-resume.md`." |
| **Round status** | Phase name, round number (where applicable), and the last completed beat or question. Example: "PRD Phase 2, deepening round 3, last question answered: 'How should the plugin behave when a resume file is stale?'" |
| **Pending decision** | The literal open question, what has been weighed about it, and the options on the table. NOT a topic gesture. Example: "Pending: should `/sdd:unpause` warn or silently proceed when the resume file is older than 7 days? Weighed: false-positive noise vs. catching genuinely stale state. Options: (a) silent, (b) warn over 7 days, (c) prompt user." |
| **Files for next-session** | Absolute or repo-relative paths the resumed agent must read before resuming. Group by purpose if more than three. Example: "`docs/prd.md`, `docs/process-notes-prd.md`, `plugins/sdd/skills/prd/SKILL.md`." |
| **Instructions for next-session** | Direct address to the resumed agent. Names the resumed command, beat or round number, and the immediate next action. Example: "You are resuming `/sdd:prd` at Phase 2 deepening round 3. Read the files above, confirm the decisions in this file with the user, then ask the pending question." |

The quality bar applies whether the file was written by `/sdd:pause` or by a three-tier `/clear` consolidation — same schema, same rigor, same length target.

## Distill, don't dump

A resume file is a compressed seed for fresh context, not a transcript. Five rules:

- **No verbatim conversation.** Capture outcomes, not the back-and-forth.
  - Anti-pattern: "User said 'I think we should...', then agent replied 'That makes sense because...'"
  - Corrected: "Decided to scope v2 to single-user resume only."
- **No agent reasoning trail.** Record the conclusion, not the chain of thought that produced it.
  - Anti-pattern: "Considered three options. Option A had drawback X, option B had drawback Y, so option C..."
  - Corrected: "Chose option C (per-command resume file) — rationale: matches one-command-in-flight invariant."
- **No re-explanation of plugin behavior.** Point at the source `SKILL.md` or reference file; don't paraphrase it.
  - Anti-pattern: "The `/sdd:prd` command runs a two-phase interview where phase 1 is mandatory questions..."
  - Corrected: "Resuming `/sdd:prd` — see `plugins/sdd/skills/prd/SKILL.md` and `references/deepening-rounds.md`."
- **Cross-reference, don't restate.** When a decision is already captured in `docs/prd.md` or a process-note, cite the line and move on.
  - Anti-pattern: Reproducing an acceptance criterion verbatim in the resume file.
  - Corrected: "Confirmed AC `a7kp` (see `docs/prd.md`)."
- **Length target: 1–3 pages of markdown.** Significantly longer means distillation failed. The agent compresses before writing, not after the user complains.

## Cleanup

The resumed command is responsible for deleting its own resume file. The delete fires when the underlying interview completes its document-generation step — i.e., once the resumed command has successfully produced or updated its target artifact (scope.md, prd.md, sprint-N.md, etc.).

- `/sdd:pause` does NOT delete. It only writes.
- `/sdd:unpause` does NOT delete on entry. It reads, resumes, then the resumed command deletes on completion.
- If the resume file is missing when cleanup runs, that is not an error. Cleanup is a best-effort `delete-if-exists`. A missing file means either the user already cleared it manually or the consolidation path never wrote one — both are valid states.

## Three-tier `/clear` consolidation

The three-tier between-rounds context-management model (see `references/context-management.md`) may recommend `/clear` after a deepening round when context pressure is high. Before instructing the user to `/clear`, the agent writes a resume file using this exact schema and location convention. After `/clear`, the user runs `/sdd:unpause`, which reads that file and resumes.

This is the same file format, same quality bar, and same cleanup rule as a manual `/sdd:pause`. The only difference is the trigger. There is no separate "round-summary schema" — the schema in this reference is canonical for both paths.
