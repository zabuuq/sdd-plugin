---
name: discovery
description: Open exploration before structural decisions. Interviews the user about the nature of the project — the idea, the why, audience, prior art, constraints, and starting context — and hands off to /sdd:scope.
disable-model-invocation: true
---

# /sdd:discovery

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/references/deepening-rounds.md`
3. `skills/sdd-guide/references/backlog.md` (defer-to-backlog write trigger and entry format)
4. `skills/sdd-guide/templates/discovery-template.md`
5. user profile (`~/.claude/sdd-user-profile.json`)
6. `docs/refs/` (if non-empty)

## State Updates (immediate)

Update `lastCommand` in `docs/project-state.json` to `"/sdd:discovery"`. If the file does not exist, create it. This update happens first, before the prerequisite check below and before any other work, per the state-tracking rule in sdd-guide.

## Read `smallProject` (startup)

Immediately after the `lastCommand` state update, read the `smallProject` value from `docs/project-state.json` (top-level key). If the key is absent or `null`, treat it as "no judgment yet" — standard cadence applies. See `skills/sdd-guide/references/right-sizing.md > ## The smallProject field` for what the value means and how it shapes downstream behavior.

**Read only — no detection, no re-evaluation, no write.** Per `skills/sdd-guide/references/right-sizing.md > ## The smallProject field > Authoring lifecycle`, `/sdd:discovery` is explicitly excluded from the authoring lifecycle. A scale judgment at the discovery stage is premature and would bias the very framing discovery is meant to surface. The value is read so this command honors any prior judgment a re-run of `/sdd:scope` may have written; it is never authored or modified here. `/sdd:scope` writes the authoritative initial value at the end of its Phase 1.

## Prerequisites

`~/.claude/sdd-user-profile.json` must exist.

**If the file does not exist**, stop immediately and output:

> Run `/sdd:onboard` first.

Do not proceed. Do not attempt to create the profile or work around its absence. Do not write any other files. Do not perform an interview.

## Read-Only After /sdd:scope

Only run this check if the user-profile prerequisite above passed.

Check whether `docs/scope.md` exists in the project.

**If `docs/scope.md` exists**, discovery is closed for this project. Stop immediately and output:

> `docs/scope.md` exists; discovery is closed for this project. `/sdd:discovery` is now read-only — see `docs/discovery.md` for the captured context.

Do not proceed. Do not write `docs/discovery.md`. Do not append to `process-notes-discovery.md`. Do not perform an interview. The `lastCommand` state update above has already run and is not undone — only writes to discovery artifacts are skipped. The user may still read `docs/discovery.md` directly to review the captured context.

If `docs/scope.md` does not exist, proceed to the next section.

## docs/refs/ Acknowledgment

Only run this step if both the user-profile prerequisite and the read-only-after-scope check above passed (i.e., `~/.claude/sdd-user-profile.json` exists and `docs/scope.md` does not exist).

Check the `docs/refs/` directory in the project.

**If `docs/refs/` exists and contains one or more files**, read every file in the directory as context for the interview. Then emit a single one-line acknowledgment in this LITERAL format, with the bracketed comma-separated filename list filled in:

> Read N files from docs/refs/: [filename1, filename2, ...]. I'll reference them as we go.

Do not summarize the content of the files. Do not describe what was in them. The acknowledgment is one line and only names the files. After emitting it, proceed to Phase 1.

**If `docs/refs/` is missing or empty**, proceed silently to Phase 1. Do not emit a message. Do not prompt the user to upload anything. Do not nudge or hint that the directory exists. The directory is user-managed between commands; its absence is a normal state and triggers no output.

The acknowledgment (or silent proceed) fires once, before Phase 1 opens — not partway through the interview.

### Using the loaded reference content

When `docs/refs/` was non-empty, the agent now holds the content of those files as context for the rest of the command. Use that knowledge to shape Phase 1 question framing: if an uploaded file already answers a basic "what is this?" or "who is it for?" question, do not ask it as if the user had said nothing. Ask the deeper follow-up the uploaded content opens up instead. The point of reading `docs/refs/` before Phase 1 is to skip questions the user has effectively already answered through the upload and to spend the interview on what is still unknown.

## docs/discovery.md Overwrite Confirmation

Only run this check if the user-profile prerequisite, the read-only-after-scope check, and the `docs/refs/` acknowledgment step above have all passed. The read-only-after-scope check has already established that `docs/scope.md` does not exist — so reaching this point means a mid-flow re-run of `/sdd:discovery` is in progress.

Check whether `docs/discovery.md` exists in the project.

**If `docs/discovery.md` does not exist**, proceed silently to Phase 1. Do not emit a message. This is the common first-run case.

**If `docs/discovery.md` exists**, the user is re-running `/sdd:discovery` against a project that already has a captured discovery artifact. The wrap-up step at the end of this command will overwrite `docs/discovery.md` with new content drawn from this interview. Confirmation must fire **before the write**, so it fires here, at startup — before the interview runs — so the user does not sit through an interview that ends in a refused write.

Emit an explicit overwrite-confirmation prompt that:

- Names the file: `docs/discovery.md`.
- States that completing this interview will **overwrite** the existing file.
- Asks for a clear yes/no confirmation to proceed.

Wait for the user's response.

**On affirmative confirmation**, proceed to Phase 1. The interview runs normally. The write at the end of `## Wrap-Up` will overwrite the existing file — no second confirmation fires at write time, because the user has already authorized the overwrite here at startup.

**On rejection (or any non-affirmative response)**, exit immediately. Do not run Phase 1. Do not append to `process-notes-discovery.md`. Do not write `docs/discovery.md`. Emit a closing message stating that no changes were made and the existing `docs/discovery.md` was preserved. The `lastCommand` state update at the top of this skill has already run and is not undone — only the discovery-artifact writes are skipped. Do not emit an end-of-command handoff on the reject path; the command did not complete an interview.

## Interview — Phase 1: Mandatory Beats

After the startup checks above pass (user-profile prerequisite, read-only-after-scope check, `docs/refs/` acknowledgment or silent proceed, and discovery-overwrite confirmation or silent proceed), begin the Phase 1 interview.

The job of `/sdd:discovery` is **open exploration before structural decisions**: "what is this?" The beats below surface the project's NATURE — not its boundaries (that is `/sdd:scope`'s job), not its requirements (`/sdd:prd`), not its architecture (`/sdd:spec`). Stay in the "what and why" register. If the conversation drifts into "what's in vs. out" or "how will it be built," let the answer land and move on — those questions belong to later commands.

The beats are mandatory in Phase 1 — every project gets the same baseline coverage. Ask them one at a time, free-form, never multiple-choice. Stay adaptive: if the user's answer to an earlier beat already covers a later one, or if `docs/refs/` content already answers a beat, skip it rather than ask a question the user has effectively already answered. If a thread surfaces that matters more than the script, follow it before returning to the beats. End each beat with an active-prompting close ("anything else on this?" or a variation that fits the beat) before moving to the next.

### Beats to cover:

1. **The idea** — "In your own words, what are you trying to build?" Get a plain-language description before any structure is imposed. If the answer is abstract ("a tool that helps people"), ask what it actually does in concrete terms.

2. **The why** — "What makes this worth doing?" Probe the motivation — the itch, the gap, the frustration, the opportunity. A project's "why" shapes every later decision; do not let this beat land on "it would be cool."

3. **The audience** — "Who is this for?" If the user is the only intended user, say so and move on — solo-use projects are a valid answer, not a failure of the beat. If others are intended, push for specificity: who benefits most, who would miss it if it disappeared?

4. **Prior art and inspiration** — "What existing things does this resemble, borrow from, or react against?" Tools, products, patterns, papers, hobbies — anything that informed the shape of the idea. If the user names something, ask what it gets right and what it gets wrong from their perspective.

5. **Constraints** — "What limits or non-negotiables are already in play?" Time, tech stack, budget, platform, deployment, regulatory, personal energy — anything the user already knows is fixed. This is constraint surfacing, not scope-setting; capture what is fixed without yet drawing the boundary of what is in.

6. **Starting context** — "What context do you already have on this?" Existing code, sketches, notes, prior conversations, half-built prototypes, references already in `docs/refs/`. If `docs/refs/` was non-empty at startup, acknowledge that those files were already read and ask whether there is anything else the user holds in their head or elsewhere.

7. **Anything else** — "Before we move on, is there anything I haven't asked about that I should know?" A brief close. This is the last beat in Phase 1 and exists to catch material the script missed.

The agent may reference content loaded from `docs/refs/` (per the acknowledgment step above) when framing any beat — phrase the question around what is still unknown rather than re-asking what the uploaded content already answers.

## Interview — Phase 2: Deepening Rounds

Once Phase 1 is complete, transition to Phase 2 by following the deepening rounds protocol defined in `skills/sdd-guide/references/deepening-rounds.md` — including the Phase 1 → Phase 2 transition, the per-round question count (5 default, up to 10 with explicit reason+permission past the cap), and the structured end-of-round content recommendation (continue-with-topic-preview or close-with-reasoning). Do not emit a bare transition prompt; the reference defines the recommendation wording, and the Phase 1 → Phase 2 transition fires the same structured recommendation even though no deepening round has run yet.

Deepening questions for `/sdd:discovery` should target the project's nature — unexplored angles of motivation, audience nuance, prior-art tensions, hidden assumptions about what the project IS. They should not drift into scope, requirements, or architecture; if a thread starts pulling that way, note it for the appropriate later command and steer back to "what is this?" Ask one question at a time per the reference.

## Deferrals

When the user surfaces an idea mid-interview that they explicitly want to push off rather than capture in `docs/discovery.md` (or when a thread pulls toward scope/requirements/architecture and the user wants to set the topic aside), route the decision through the defer-to-backlog vs drop prompt defined in `skills/sdd-guide/references/backlog.md > ## Write trigger`. An affirmative "defer to backlog" answer writes an entry to `docs/backlog.md` per the entry schema in that reference; a drop answer (or any non-affirmative response) writes no entry and records the drop in `process-notes-discovery.md` only.

## Process Notes

Per sdd-guide's `## Process Notes` section, append to `process-notes-discovery.md` at the **project root** (not `docs/`, not the plugin tree) throughout the interview. This is real-time wiring — appends fire during the interview as moments happen, not as a single end-of-command summary dump.

**Trigger points** (append at each):

- End of every Phase 1 mandatory beat (after the user's answer lands and any active-prompting close resolves).
- End of every Phase 2 deepening round (after the round closes, alongside the structured end-of-round content recommendation).
- Notable mid-interview moments — don't wait for a beat or round boundary if one lands mid-conversation.

**What each entry captures:** the four categories from sdd-guide's `## Process Notes` (decisions, pushback, difficult questions, pivots). Distill, don't transcribe — no verbatim conversation.

**Format:** append-only markdown. Each entry opens with a timestamp or beat marker (e.g., `### Beat 3 — The audience` or `### Phase 2, Round 1 close`) so the reader can locate the moment in the interview's shape.

If `process-notes-discovery.md` does not yet exist at the project root, create it on the first append.

## Wrap-Up

Process-notes appends to `process-notes-discovery.md` have already been happening throughout the interview per the `## Process Notes` section above. No end-of-command summary dump is needed here — confirm the file exists and the latest beat/round/pivot was captured before this wrap-up began.

### Write docs/discovery.md

Once the user accepts the close-recommendation at the end of Phase 2, write `docs/discovery.md` using `skills/sdd-guide/templates/discovery-template.md` as the schema. Populate every section from content captured in Phase 1 beats and any Phase 2 deepening rounds. Keep the artifact lean — it is a context-grounding document for `/sdd:scope` and later commands, not a comprehensive product write-up. Distill, do not transcribe.

Map content into the template's sections as follows:

- **One-line summary** (under the title) — a single sentence from the Phase 1 idea beat, refined through any deepening that touched it.
- **The Idea** — longer-form description from Beat 1 plus relevant deepening.
- **Why It Matters** — Beat 2 plus relevant deepening.
- **Audience** — Beat 3 plus relevant deepening. If solo-use, state that plainly.
- **Prior Art and Inspiration** — Beat 4. For each named item, capture what it gets right and wrong per the user.
- **Constraints** — Beat 5. Surfaced limits only; this is not scope-setting.
- **Starting Context** — Beat 6. If `docs/refs/` was non-empty at startup, note that here so downstream commands know the files were read.
- **Open Questions** — material from Beat 7 ("anything else") or any Phase 2 round that the user flagged but did not commit to. Remove the section if there are no open questions.
- **Deferred Topics** — conditional pointer to `docs/backlog.md`. Before writing the artifact, check `docs/backlog.md` against the heading-scan rule in `skills/sdd-guide/references/backlog.md > ## Parser note` — if the file exists and has at least one entry by that rule, emit the section heading and the literal line `Deferred items: see \`docs/backlog.md\`.`. Otherwise omit both the heading and the body. Do not restate the parser rule inline; the reference is canonical.

If `docs/discovery.md` did not exist at startup, write the file directly. If it existed at startup, the overwrite was already authorized by the user during the `## docs/discovery.md Overwrite Confirmation` step — no second prompt fires here. Overwrite the file.

### Delete docs/discovery-resume.md

After `docs/discovery.md` has been written, delete `docs/discovery-resume.md` if it exists. A missing file is not an error — continue silently. Per `skills/sdd-guide/references/pause-resume.md > ## Cleanup`, the resumed command owns this deletion; `/sdd:pause` is the only writer.

### Update state

Confirm `lastCommand` is set to `"/sdd:discovery"` in `docs/project-state.json` (the immediate state update at the top of this skill set it already).

### Next step

Emit the handoff per `## End-of-Command Handoff` below.

## End-of-Command Handoff

Runs as the final step after Phase 2 closes and any discovery artifact writes have completed.

Emit the handoff per the canonical template in `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff`. That template defines the two-line standard form, the first-handoff explanation paragraph (prepended exactly once per user), and the `handoffWarningShown` tracking convention in `~/.claude/sdd-user-profile.json`. Do not restate that mechanism here.

### Next-command target

The `[next-command]` slot in the template is always `/sdd:scope` for `/sdd:discovery`.

### Outcome-summary line

Use a one-line outcome summary in the form `Discovery captured.` Substitute project-specific phrasing only if it preserves the one-line, declarative shape.

### Unconditional emission

The handoff fires unconditionally at completion. No context-weight heuristic, deepening-rounds outcome, or `docs/refs/` presence causes it to be skipped.
