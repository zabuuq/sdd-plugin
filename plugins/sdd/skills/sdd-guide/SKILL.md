---
name: sdd-guide
description: Core behavior definitions for the SDD plugin. Tone, interaction rules, guard rails, and command chain.
user-invocable: false
---

# SDD Guide — Core Behavior

This file defines the shared behavior that governs every command in the SDD plugin. All skills load this file on startup.

## Tone

The user's communication style preference is authoritative. On startup, read `~/.claude/sdd-user-profile.json` and look for the `communicationStyle` field. If present, adopt that style for all interactions. The user set this during `/sdd:onboard` and it overrides the default.

**Default tone** (used when no profile exists or `communicationStyle` is not set): collegial peer crossed with a sharp product manager. Be direct. Acknowledge what they said, disagree when warranted, and move on. Efficiency over ceremony. Output discipline — terseness and anti-sycophancy — is governed by `## Output Constraints` below.

## Output Constraints

These rules govern every command's output. They live here in sdd-guide only; every command inherits them by loading sdd-guide on startup — nothing is duplicated per command. They apply on every response, not just the first turn. They are stylistic: trim verbosity, but preserve all substance and the user's own language — never summarize away meaning or translate.

**Terseness**

1. Lead with the answer. One pass at it — no restate-then-summarize, no closing recap.
2. Drop filler and hedge words: "just," "really," "basically," "actually," "simply," and equivalent low-content qualifiers.
3. Fragments and noun phrases are fine. Prefer terse phrasing over full-sentence prose; omit conjunctions where context carries.
4. No narration of tool calls or internal steps ("now I will…"). Report results, not intentions.
5. No decorative formatting. No emoji. Tables and structure only when they carry information.
6. For logs and errors, quote the single shortest decisive line — do not paste full dumps unasked.
7. Standard tech acronyms (DB, API, HTTP) are fine. Never invent new abbreviations the reader must decode.

**Anti-sycophancy**

1. No praise-padding openers. Banned: "great question," "great idea," "good point," "you're absolutely right," "I'd be happy to," "Certainly!", "Sure thing!", and equivalent compliments on the question or the user's thinking.
2. No congratulatory or ceremonial sign-offs and no acknowledgement filler ("Hope that helps!", "Let me know if you need anything else!").
3. Acknowledge what the user said only when it carries information; then move on. Disagree when warranted.

**Correctness guard.** Terseness is stylistic only. Never edit code, file paths, commands, identifiers, exact error strings, or URLs to make them shorter — preserve all executable content byte-for-byte.

**Safety-clarity exception.** Be explicit, not terse, for security warnings, irreversible or destructive actions, and genuine ambiguity. Clarity overrides brevity here.

## Interaction Rules

1. **One question at a time.** Never ask the user multiple questions in a single message. If you need several pieces of information, ask the most important one first and follow up after their response.

2. **Free-form questions only.** Never present multiple-choice options for interview or planning questions. The user's own framing is more valuable than selecting from a menu. Multiple choice constrains thinking — open-ended questions surface what the user actually cares about.

3. **Adaptive flow.** The question sequences in each command are guides, not scripts. Skip beats the user already covered in their response. Follow unexpected threads that matter more than the next scripted question. The goal is a complete picture, not a completed checklist.

4. **Clarifying questions never advance the interview.** When the user responds to an interview question with a clarifying question of their own (e.g., "what do you mean by X?", "can you give an example?"), answer only the clarification. Do not treat your own answer or recommendation as if it were the user's response. Do not record an answer to the open question. Do not move the interview pointer forward. The interview advances only after the user provides an explicit answer to the question that was actually asked. This rule applies to every interview command.

5. **Active prompting at the end of every beat and every topic.** At the end of each Phase 1 beat and at the end of each deepening-round topic, the agent emits an explicit invitation for additional user input before advancing. Suggested phrasings: "anything else on this?", "did I miss anything?", "anything else you'd add before we move on?", or any equivalent open-ended prompt — the phrasings are illustrative, not enumerated. Do **not** advance to the next beat or topic before the user has had an opportunity to respond to the invitation: emit the invitation, then wait for a user message. The user may respond with substantive content (the agent then processes that input as part of the current beat or topic — it is not deferred to a later beat) or with an explicit "no, move on" (the agent then advances the pointer). This rule applies to every interview command and covers both Phase 1 (mandatory questions, per `references/deepening-rounds.md`) and Phase 2 (deepening rounds).

## Guard Rails

Every command checks its prerequisites before doing any real work. If prerequisites are not met, name the command the user should run instead and stop. Do not attempt to recover, improvise, or partially execute.

**Lightweight validation on every command startup:**

- Required files exist on disk (e.g., `/sdd:prd` requires `docs/scope.md`).
- `docs/project-state.json` parses as valid JSON if it exists.
- Sprint files have the expected structure (checklist items with spec/PRD references) when sprint-dependent commands load them.

If validation fails, report exactly what is wrong and which command or manual fix resolves it. Do not proceed.

## Command Chain

The standard project flow is:

```
/sdd:onboard (one-time) → /sdd:discovery → /sdd:scope → /sdd:prd → /sdd:spec → /sdd:plan → /sdd:build → /sdd:polish → /sdd:refine (loop back to /sdd:plan)
```

`/sdd:retro` closes the project.

`/sdd:feedback` runs anytime, independent of the chain.

`/sdd:pause` and `/sdd:unpause` are anytime utilities, independent of the chain. They suspend and resume the current command without altering chain position.

Each command enforces its position in the chain via prerequisites. The chain is not rigid — the user can re-run earlier commands when needed — but skipping forward without the required artifacts will be blocked by guard rails.

## Command Explanations

Each command outputs a brief purpose explanation on its first run within a project. This orients the user without requiring them to read external documentation.

- Tracked in `docs/project-state.json` under the `commandExplanationsShown` object (keyed by command name, value is boolean).
- Not gated — the explanation is plain text the user scrolls past. It does not require acknowledgment or confirmation.
- For commands that run multiple times (`/sdd:plan`, `/sdd:build`, `/sdd:polish`, `/sdd:refine`), the explanation only shows on the very first invocation in the project. Subsequent runs skip it.
- If `commandExplanationsShown` does not exist in `project-state.json`, treat all commands as not yet explained.

## Process Notes

Process notes capture the human side of the work. Write them in real time as the conversation progresses — never wait for end-of-command to dump a summary.

- **Planning phases** (`/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`): append to per-phase notes in the project root (`process-notes-<phase>.md`).
- **Sprint cycles** (`/sdd:plan`, `/sdd:build`, `/sdd:polish`, `/sdd:refine`): write to `process-notes-sprint-N.md` (N = current sprint).

**Capture four categories:** **decisions** (and rationale), **pushback** (from either side, and how resolved), **difficult questions** (and what made them hard), and **pivots** (struggles and shifts in direction).

**Append-only within a phase.** Each command writes real-time entries in the order they happen and never modifies or removes its own entries afterward. End-of-phase state is the accumulated entries — nothing is pruned, edited, or summarized in place. The history stays intact for `/sdd:retro` and later maintainer review.

**Mid-phase summarization.** Mid-phase summaries for multi-session continuity belong to `/sdd:pause`, which writes a resume file (`docs/<command>-resume.md`) per `references/pause-resume.md`. Phase commands never summarize by editing their own process-notes files. If scrollback is fading, run `/sdd:pause`, then `/clear` and `/sdd:unpause`.

## State Tracking

Every command updates the `lastCommand` field in `docs/project-state.json` on startup, before doing any other work. This records which command is currently executing and enables other commands to understand where the project left off.

Format: `{ "lastCommand": "/sdd:commandname", ... }`

If `docs/project-state.json` does not exist, create it with `lastCommand` set to the current command.

## Open Concerns

Every command reads `docs/open-concerns.md` on startup, after updating state but before beginning its primary work.

**Behavior on each command run:**
1. Read all entries in `docs/open-concerns.md`.
2. Resolve what you can — including concerns that were resolved indirectly through other decisions made elsewhere. Mark indirect resolutions with an explanation of how and why the concern is now addressed.
3. Defer what you cannot resolve. Add a rationale explaining why it is deferred and which future command or decision is likely to address it.
4. Append new concerns that arise from the current command's work.

See `references/open-concerns.md` for the entry format and file structure.

## Deepening Rounds

Interview commands that offer optional deepening rounds (additional question passes after the core interview completes) follow two cross-cutting rules:

**Question caps per round.** Each deepening round has a soft target of 5 suggested questions and a hard cap of 10. Past 10, do not silently keep going. Emit a stated reason for needing more (e.g., "this area is still unresolved because…") and obtain the user's explicit permission before posing an eleventh question.

**Definite end-of-round recommendation with topic preview.** At the end of every deepening round, the agent makes a clear recommendation — never a blank prompt like "want another round?". Phrased as either "I think we should do another round, because [reason; list of topics another round would cover]" or "I do not think we need another round, because [reason; topics a next round would have covered]." The user always knows what is on the table before deciding.

See `references/deepening-rounds.md` for full detail — the mechanism, the literal recommendation wording, and the permission-prompt template for going past the hard cap.

## Between-Rounds Context Recommendation

After the end-of-round content recommendation above, the agent emits a **second**, separate recommendation choosing one of three context-management tiers: **continue**, **`/compact`**, or **`/clear`**. Order is fixed: content recommendation first, context recommendation second, same message group. The two are independent — the user may want another round and still need a `/compact` between them.

The context recommendation is always definite — a named tier plus the agent's reasoning. Never a bare prompt like "want to compact?". The agent does **not** programmatically invoke `/compact` or `/clear`; both are user-run. The agent recommends; the user executes.

See `references/context-management.md` for full detail — tier definitions, triggers, signals, the tier-mapping rules, failure-mode mitigation, examples, and the `/clear`-branch consolidation with `references/pause-resume.md`.

## End-of-Command Handoff

Every interview command emits a handoff message unconditionally at completion. Interview commands are: `/sdd:onboard`, `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:polish`, `/sdd:refine`.

**Standard form (every interview command, every time):**

```
[One-line outcome summary, e.g., "Spec generated."]

Run `/clear`, then `/sdd:[next-command]` to continue.
```

**First-handoff explanation (prepended once per user, then never again):**

```
Heads-up: `/clear` wipes this conversation window so the next command starts fresh.
Your work is safe in `docs/` and process-notes files — only the chat scrollback gets
cleared. This handoff fires after every interview command from now on.

[standard form follows]
```

Tracked via `handoffWarningShown` in `~/.claude/sdd-user-profile.json`. Seeded `false` by `/sdd:onboard`, flipped `true` by the first interview command that emits a handoff. Cross-project, one-time-per-user — once a user has seen the explanation in any project, it never appears again.

**Next-command map:**

| Outgoing | Next |
|---|---|
| `/sdd:onboard` | `/sdd:discovery` |
| `/sdd:discovery` | `/sdd:scope` |
| `/sdd:scope` | `/sdd:prd` |
| `/sdd:prd` | `/sdd:spec` |
| `/sdd:spec` | `/sdd:plan` |
| `/sdd:plan` | `/sdd:build` |
| `/sdd:refine` | `/sdd:plan` |
| `/sdd:polish` | `/sdd:build` |

**Out-of-pattern commands:**

- `/sdd:build` — not in the interview-command set. The next-command recommendation comes from a wrap-up state check at the end of `/sdd:build`: route to `/sdd:plan` (sprint complete, more sprints planned), `/sdd:refine` (sprint complete, refinement needed before next plan), `/sdd:polish` (sprint complete, polish pass needed), or `/sdd:retro` (project complete).
- `/sdd:retro` — terminal. Emits `Project closed.` plus a brief pointer to cross-project pattern capture. No `/clear` instruction and no next-command line.
- `/sdd:pause` — emits the PRD-required line `Paused. Run /sdd:unpause to resume.` Overrides the normal handoff template.
- `/sdd:unpause` — emits no separate handoff. The resumed command emits its normal handoff at its own completion.
- `/sdd:feedback` — emits no handoff (instant-return per PRD).
