---
name: scope
description: Collaborative brainstorm that produces a scope document. Interviews the user, pushes back on vague thinking, and distills the conversation into a clear project definition.
disable-model-invocation: true
---

# /sdd:scope — Scope Command

## Startup Sequence

Execute these steps in order before beginning the interview.

### 1. Load shared behavior

Read `skills/sdd-guide/SKILL.md` and follow all rules defined there (tone, interaction rules, guard rails, process notes, open concerns, state tracking). Everything in sdd-guide applies to this command.

### 2. Load deepening rounds reference

Read `skills/sdd-guide/references/deepening-rounds.md`. This defines the two-phase interview pattern used later in this command.

Read `skills/sdd-guide/references/context-management.md`. This defines the three-tier between-rounds context recommendation (continue / `/compact` / `/clear`) emitted alongside the end-of-round content recommendation.

Read `skills/sdd-guide/references/backlog.md`. This defines the defer-to-backlog vs drop confirmation prompt, the 6-field entry schema, and the bootstrap rule for `docs/backlog.md`.

### 3. Read user profile

Read `~/.claude/sdd-user-profile.json`.

**If the file does not exist**, stop immediately and output:

> Run `/sdd:onboard` first.

Do not proceed. Do not attempt to create the profile or work around its absence.

If the file exists, parse it and use the `communicationStyle` field to set tone for this session (per sdd-guide rules).

### 4. Create docs directory and project state

If the `docs/` directory does not exist, create it.

If `docs/project-state.json` does not exist, create it with this content:

```json
{
  "version": 1,
  "currentSprint": 0,
  "buildMode": null,
  "lastCommand": "scope",
  "commandExplanationsShown": {
    "scope": true,
    "prd": false,
    "spec": false,
    "sprint": false,
    "build": false,
    "iterate": false,
    "reflect": false,
    "refine": false,
    "retro": false,
    "feedback": false
  }
}
```

If `docs/project-state.json` already exists, update `lastCommand` to `"scope"`.

### 4a. Read `smallProject`

After the `lastCommand` update, read the top-level `smallProject` value from `docs/project-state.json`. If the key is absent or `null`, treat it as "no judgment yet" — standard cadence applies for this startup phase. See `skills/sdd-guide/references/right-sizing.md > ## The smallProject field` for what the value means.

`/sdd:scope` does not perform a separate startup re-evaluation. Per `skills/sdd-guide/references/right-sizing.md > ## The smallProject field > Authoring lifecycle`, the authoritative write at end of Phase 1 (see `## End of Phase 1: Authoritative smallProject Write` below) **is** this command's re-evaluation step — on a re-run, the Phase 1 write overwrites the prior value based on the fresh answers. The startup read here is informational so any pre-Phase-1 behavior (e.g., prompt framing) can honor an existing judgment, but no separate flip-and-log step fires at startup.

### 5. First-run explanation

Check `docs/project-state.json` for `commandExplanationsShown.scope`.

If `scope` has not been shown (`false` or missing), output this explanation and then set `commandExplanationsShown.scope` to `true`:

> **/sdd:scope** is the starting point for a new project. I'll interview you about what you want to build — your idea, who it's for, what success looks like, and where the boundaries are. At the end, I'll generate a scope document that captures the project definition. This becomes the foundation for everything that follows.

This is not gated. Do not ask the user to confirm or acknowledge it. Just output it and continue.

### 6. Git check

Read the user profile's `gitPreference` field. If git preference is enabled (`true` or `"enabled"`):

- Check whether the current directory is a git repository.
- If it is not, offer to initialize one: "This directory isn't a git repo. Want me to initialize one?"
- If the user declines, continue without git. Do not block on this.

### 7. Workflow overview

Read the user profile's `showWorkflowExplanation` field. If it is `true`, output this overview:

> **SDD Workflow Overview**
>
> **Planning phase:** scope → PRD → spec
> **Sprint loop:** sprint → build → iterate → reflect → refine
> **Project close:** retro
> **Anytime:** feedback

Then continue. This is informational — do not gate on it.

### 8. Open concerns

Follow the open concerns protocol from sdd-guide: read `docs/open-concerns.md` (if it exists), resolve what you can, defer what you cannot, and note new concerns as they arise during this command.

---

## Seed File Detection

Before starting the interview, check for `docs/seed.md`.

If `docs/seed.md` exists:
- Read its contents.
- Use it as starting context for the interview. The seed file contains the user's initial thoughts, notes, or rough description of the project.
- Reference seed content during the interview — ask clarifying questions about what's in the seed rather than re-asking things the seed already covers.
- Do not treat the seed as a complete scope. It is raw input that needs refinement through the interview.

If `docs/seed.md` does not exist, proceed directly to the interview with no starting context.

---

## Interview — Phase 1: Mandatory Questions

The interview covers these beats. Ask one question at a time. All questions are free-form — never present multiple choice. Stay adaptive: skip beats the user has already covered (including content from the seed file), and follow threads that matter more than the scripted sequence.

### Small-project right-sizing (conditional)

Consult the `smallProject` value read at startup (see `### 4a. Read \`smallProject\`` above).

- **When `smallProject` is `true`:** the beats designated in `skills/sdd-guide/references/right-sizing.md > ## Skippable Phase 1 beats per command > ### \`/sdd:scope\`` default to skipped. Do not restate that list here — the reference is canonical.
- **When `smallProject` is `false`, `null`, or absent:** run every beat below as normal.

These beats are **skippable, not required-skipped**. If the user's earlier answers (including seed-file content) leave a designated beat genuinely open, ask it anyway. The skip is the default behavior at `smallProject=true`, not a prohibition on inquiry.

### Beats to cover:

1. **The idea** — What are you building? Get a plain-language description. Push back if it's vague. "An app that helps people" is not enough — what does it actually do?

2. **Who it's for** — Who is the target user or audience? Be specific. "Everyone" is not an answer. If the user says everyone, push: who benefits most? Who would be upset if this didn't exist?

3. **Inspiration and references** — Are there existing products, patterns, or examples that inform this? What do they get right? What do they get wrong?

4. **Goals** — What should this project accomplish? Push for concrete, observable goals over abstract aspirations. "Be useful" is not a goal. "Let a user do X without needing Y" is a goal.

5. **What "done" looks like** — How will you know the project is finished? Describe the finish line in terms of what a user can do or what exists in the world.

6. **Solo or team** — Is this a solo project or will others contribute? This affects scope decisions and documentation depth.

### Interview conduct:

- One question at a time. Wait for the user's response before moving on.
- Free-form only. No multiple choice, no numbered options, no "pick A or B."
- Push back on vague answers. If the user says something ambiguous, ask them to sharpen it. Do not accept hand-waving — the scope document depends on clarity here.
- Follow unexpected threads. If the user reveals something important that isn't in the script, explore it before returning to the beats.
- Reference seed file content when relevant. Don't re-ask questions the seed already answered — instead, confirm or probe deeper on what it said.

### Small-project signal observation (during Phase 1):

While the mandatory beats above are being answered, observe the small-project signals defined in `skills/sdd-guide/references/right-sizing.md > ## Signals for the heuristic judgment`. Do not restate the signal list here — the reference is canonical. Track which signals are present, which are absent, and whether any is strongly contradicted by what the user says. This observation feeds the authoritative write in the next section; do not surface the judgment to the user during Phase 1 questioning, and do not write `smallProject` until the mandatory beats have all landed.

---

## End of Phase 1: Authoritative `smallProject` Write

Runs exactly once per `/sdd:scope` invocation, immediately after the Phase 1 mandatory beats have all landed and before the Project Size Assessment, Phase 2 deepening rounds, or any document generation. Fires unconditionally — do not skip this step even if Phase 1 was abbreviated, the seed file was rich, or the user's answers came quickly. If a prior `/sdd:scope` run in the same project already wrote a value, this run overwrites it with a fresh judgment based on the answers just gathered.

Apply the heuristic defined in `skills/sdd-guide/references/right-sizing.md > ## Signals for the heuristic judgment` against the signals observed during Phase 1. Use the rule of thumb stated in that section (four or more of the six signals present, with none strongly contradicted → `true`; three or fewer, or any single strong contradiction such as the user explicitly naming external paying customers → `false`).

Write the result to `docs/project-state.json` under the top-level `smallProject` key:

- **Signals present → `"smallProject": true`.** The project is judged small. Downstream commands will right-size their interviews per `right-sizing.md`.
- **Signals absent or any strong contradiction → `"smallProject": false`.** Standard cadence applies downstream. (Omitting the key is also acceptable — `null` / absent is treated the same as `false` for cadence purposes per `right-sizing.md > ## The smallProject field`. Prefer writing `false` explicitly so the judgment is visible in the project state.)

This is the authoritative initial value per `skills/sdd-guide/references/right-sizing.md > ## The smallProject field > Authoring lifecycle`. `/sdd:prd`, `/sdd:spec`, and `/sdd:plan` may re-evaluate on startup and flip the value if their material contradicts it; this write is the baseline they read from.

Do not announce the value to the user as a separate beat. If you want to acknowledge the judgment briefly in the natural flow of the conversation (e.g., when transitioning into the Project Size Assessment), that is fine, but the write itself is silent infrastructure.

---

## Project Size Assessment

After the mandatory beats are covered, assess whether the project is too large for a single pass.

Signs a project is too large:
- Multiple distinct user-facing products or systems
- More than ~8-10 weeks of estimated work for the team size described
- The user describes features that could each be their own project
- Dependencies between subsystems that would complicate a single linear build

**If the project is appropriately sized:** Continue to deepening rounds.

**If the project is too large:**

1. Name the problem directly. Tell the user the project as described is too large for a single scope-to-ship cycle.
2. Propose specific boundaries for splitting. Don't just say "break it up" — suggest where the cuts should be, based on what the user told you.
3. The user makes the final call on how to split. Do not override their decision.
4. The scope document captures the full vision AND what is being built now vs. later.
5. For each deferred piece, route the decision through the defer-to-backlog vs drop prompt defined in `skills/sdd-guide/references/backlog.md > ## Write trigger`. On an affirmative "defer to backlog" answer, append an entry to `docs/backlog.md` using the schema in that reference, with enough context that a future `/sdd:scope` session could pick the item up without re-interviewing the user. On a drop answer (or any non-affirmative response), write no entry to `docs/backlog.md` and record the drop in `process-notes-scope.md` only. The file location and entry format are unchanged from prior behavior; the new addition is that every write is gated by the prompt.

---

## Interview — Phase 2: Deepening Rounds

Follow the deepening rounds protocol defined in `skills/sdd-guide/references/deepening-rounds.md` — including the Phase 1 → Phase 2 transition, the per-round question count (5 default, up to 10 with explicit reason+permission past the cap), and the structured end-of-round content recommendation (continue-with-topic-preview or close-with-reasoning). Do not emit a bare transition prompt; the reference defines the recommendation wording.

After the end-of-round content recommendation fires, emit the three-tier between-rounds context recommendation per `skills/sdd-guide/references/context-management.md`. Order is fixed: content recommendation first, then context recommendation, in the same end-of-round message group. The two are separate and independent.

Deepening questions for scope should target edge cases, ambiguities, thin areas, hidden assumptions, and unexplored angles. Ask them one at a time per the reference.

Log the number of deepening rounds completed and what each round surfaced in process notes.

---

## Document Generation

When the user is ready to proceed, generate the following documents.

### Scope document

Generate `docs/scope.md` using the template at `skills/sdd-guide/templates/scope-template.md`.

Fill in every section based on what was discussed in the interview:

- **Project Name:** Use whatever the user called their project, or propose a working title if they didn't name it. Confirm with the user.
- **Idea:** Synthesize the user's description into a clear, concise explanation of what is being built.
- **Who It's For:** Capture the target audience as discussed.
- **Inspiration & References:** List anything the user mentioned. Include links if they provided them.
- **Goals:** List the concrete goals that emerged from the interview.
- **What "Done" Looks Like:** Describe the finish line in the user's terms.
- **What's Explicitly Cut:** This section is the conditional backlog pointer. Before writing the artifact, check `docs/backlog.md` against the heading-scan rule in `skills/sdd-guide/references/backlog.md > ## Parser note` — if the file exists and has at least one entry by that rule, emit the section heading and the literal line `Deferred items: see \`docs/backlog.md\`.`. Otherwise omit both the heading and the body. Do not inline-list cut items here; deferred items live in `docs/backlog.md` and are the single source of truth. Do not restate the parser rule inline; the reference is canonical.
- **Loose Implementation Notes:** Capture any technical hunches, constraints, or architecture leanings the user mentioned. These are not commitments.

Present the scope document to the user for review. Ask if anything needs to change. Iterate until they approve.

### Open concerns

If `docs/open-concerns.md` does not exist, create it from the template at `skills/sdd-guide/templates/open-concerns-template.md`.

Add any unresolved questions or concerns that surfaced during the interview as entries under the "Open" section. Use the entry format defined in the template.

### Backlog (if applicable)

If the project was split during size assessment, the deferred pieces are written to `docs/backlog.md` via the defer-to-backlog vs drop prompt described in `## Project Size Assessment` above (item 5). The prompt, entry schema, and bootstrap behavior are defined in `skills/sdd-guide/references/backlog.md`. Items the user chooses to drop are not written to `docs/backlog.md`; the drop decision is recorded in `process-notes-scope.md` only.

Additionally, if a non-split topic surfaced during the interview that the user explicitly wanted to push off rather than fold into the scope document, route that decision through the same prompt — `skills/sdd-guide/references/backlog.md > ## Write trigger` is the single gate for any write to `docs/backlog.md` from this command.

---

## Wrap-Up

### Delete docs/scope-resume.md

After `docs/scope.md` has been written (per `## Document Generation` above), delete `docs/scope-resume.md` if it exists. A missing file is not an error — continue silently. Per `skills/sdd-guide/references/pause-resume.md > ## Cleanup`, the resumed command owns this deletion; `/sdd:pause` is the only writer.

### Process notes

Append to `process-notes-scope.md` in the project root. Capture:
- Key decisions made during the interview and the rationale behind them.
- Any pushback (from either side) and how it was resolved.
- Questions that were difficult or revealing.
- Number of deepening rounds and what they surfaced.
- Whether the project was split and why.

If `process-notes-scope.md` does not yet exist at the project root, create it on the first append.

### Update project state

Set `lastCommand` to `"scope"` in `docs/project-state.json` (if not already set during startup).

### Next step

Emit the handoff per `## End-of-Command Handoff` below.

## End-of-Command Handoff

Runs as the final step after `## Wrap-Up` (process notes, state update, and any scope/backlog writes have completed).

Emit the handoff per the canonical template in `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff`. That template defines the two-line standard form, the first-handoff explanation paragraph (prepended exactly once per user), and the `handoffWarningShown` tracking convention in `~/.claude/sdd-user-profile.json`. Do not restate that mechanism here.

### Next-command target

The `[next-command]` slot in the template is always `/sdd:prd` for `/sdd:scope`.

### Outcome-summary line

Use a one-line outcome summary in the form `Scope locked.` Substitute project-specific phrasing only if it preserves the one-line, declarative shape (e.g., `Scope locked. Project split into two phases.` when the size assessment forced a split).

### Unconditional emission

The handoff fires unconditionally at completion. No context-weight heuristic, deepening-rounds outcome, or seed-file presence causes it to be skipped.
