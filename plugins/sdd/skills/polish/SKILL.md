---
name: polish
description: Polish and improve what was built in the current sprint. Bugs, new features, UX improvements — scoped through a brief interview and added to the sprint file.
disable-model-invocation: true
---

# /sdd:polish — Iterate Command

## Startup Sequence

Execute these steps in order before beginning the iteration interview.

### 1. Load shared behavior

Read `skills/sdd-guide/SKILL.md` and follow all rules defined there (tone, interaction rules, guard rails, process notes, open concerns, state tracking). Everything in sdd-guide applies to this command.

### 2. Load living-documents reference

Read `skills/sdd-guide/references/living-documents.md`. This defines the protocol for updating living documents during iteration. Follow its default stance: resist changes to scope, PRD, and spec. New ideas surface through the backlog or unvetted section, not through direct edits.

Read `skills/sdd-guide/references/context-management.md`. Polish does not run formal deepening rounds, but the iteration interview (Step 5) and the end-of-iteration wrap (Step 11) are natural transition points. At those points, the agent may emit the three-tier between-rounds context recommendation (continue / `/compact` / `/clear`) when warranted — this is discretionary for polish, not round-by-round mandatory. Use the reference's triggers and signals to judge whether a recommendation is appropriate at a given transition.

### 3. Read user profile

Read `~/.claude/sdd-user-profile.json`.

**If the file does not exist**, stop immediately and output:

> Run `/sdd:onboard` first.

Do not proceed. Do not attempt to create the profile or work around its absence.

If the file exists, parse it and use the `communicationStyle` field to set tone for this session (per sdd-guide rules).

### 4. Read project state

Read `docs/project-state.json`.

**If the file does not exist**, stop immediately and output:

> Run `/sdd:scope` first.

Do not proceed.

If the file exists, read `currentSprint` to determine the current sprint number.

### 5. Load current sprint file

Read `docs/sprint-N.md` where N is `currentSprint` from `docs/project-state.json`.

**If the file does not exist**, stop immediately and output:

> Run `/sdd:plan` first.

Do not proceed.

### 6. Load CLAUDE.md

Read `CLAUDE.md` in the project root. This provides project conventions and coding standards relevant to iteration work.

If the file does not exist, skip this step without error.

### 7. Detect closed-sprint state (re-open escape hatch)

Scan `process-notes-sprint-N.md` for `[close-sprint-manifest] ... [/close-sprint-manifest]` blocks using the parser documented in `skills/sdd-guide/references/sprint-tags.md > [close-sprint-manifest] / [/close-sprint-manifest] block`. The sprint is "closed" iff at least one such block exists.

**Branch:**

- **No manifest present** → fall through to the `## Prerequisites` block unchanged. This is the open-sprint path.
- **Manifest present** → run the re-open flow in `## Re-Open Flow` below. The `## Prerequisites` "all items complete" check does NOT fire on this path. If multiple manifests exist (the sprint has been opened and re-closed before), the **most recent** manifest is authoritative; earlier manifests stay as history but do not drive re-open logic.

The end-to-end mechanism is specified in `docs/spec.md > Cross-Cutting Mechanisms > Close-Sprint Manifest > Re-open behavior`. Follow it exactly.

## Prerequisites

This block fires only on the open-sprint path (no `[close-sprint-manifest]` block in `process-notes-sprint-N.md`). On the closed-sprint path, `## Re-Open Flow` runs instead.

All checklist items in the current sprint file must be complete (all status checkboxes checked). Scan every item under `## Checklist` and any existing `## Iteration N` sections.

**If any items are incomplete**, stop immediately and output:

> Not all sprint items are complete. Run `/sdd:build` first.

Do not proceed. Do not offer to skip incomplete items or work around them.

## Re-Open Flow

Runs in place of `## Prerequisites` when Step 7 detected a `[close-sprint-manifest]` block in `process-notes-sprint-N.md`. Reverses (selectively) the effects of a prior `/sdd:build` close-sprint so the iteration interview can proceed against a re-opened sprint.

The end-to-end mechanism is documented in `docs/spec.md > Cross-Cutting Mechanisms > Close-Sprint Manifest > Re-open behavior`. Steps below describe the user-facing flow; do not re-derive the manifest or marker formats here.

### Step A. Ask to re-open

Emit the literal prompt:

> Re-open the sprint?

One question. Wait for the user's response. (Satisfies `aaev`.)

### Step B. On rejection

If the user answers no, declines, or exits, stop immediately. Emit a single-line message:

> Re-opening the sprint is required to proceed with `/sdd:polish` on a closed sprint.

Do not run the iteration interview. Do not modify any files. Exit `/sdd:polish`. (Satisfies `aaex`.)

### Step C. On confirmation — surface the manifest's checked ACs

Parse the most recent `[close-sprint-manifest]` block (per `skills/sdd-guide/references/sprint-tags.md > [close-sprint-manifest]`). Read its `PRD ACs checked: ...` line and surface that list to the user verbatim.

Then ask a single open-ended question:

> Which (if any) should be re-opened?

Free-form response. Parse the user's answer against the AC IDs from the manifest. If the user names IDs not in the manifest, ignore them with a one-line note. If the user names none, treat the answer as "nothing to re-open" and continue to Step E.

### Step D. Reverse selected ACs

For each AC ID selected by the user:

1. **Un-check the AC in `docs/prd.md`.** Locate the line matching the AC ID (format defined in `skills/sdd-guide/references/sprint-tags.md > PRD Acceptance Criteria IDs`) and flip its checkbox from `[x]` to `[ ]`.
2. **Reverse the story split (if applicable).** If the manifest's `Story splits: ...` line maps this AC to a split-off story, restore the AC to its original story anchor. If the split-off story is left with no remaining ACs and `/sdd:refine` has not added any new ACs to it since close, delete the empty split-off story. ACs added by `/sdd:refine` to the split-off story since close are not touched — they stay.
3. **Handle `/sdd:refine`-deleted ACs.** If the AC ID from the manifest no longer exists in `docs/prd.md` (deleted by `/sdd:refine` since close), emit a one-line informational warning naming the missing AC ID; skip un-check for that AC; do not block.

### Step E. Append `[sprint-reopened]` marker

Append a `[sprint-reopened]` marker entry to `process-notes-sprint-N.md` using the format defined in `skills/sdd-guide/references/sprint-tags.md > [sprint-reopened] process-notes marker`. The marker carries the current timestamp, the un-checked AC IDs, and the splits-undone mapping for the selected ACs. Do not re-derive the format here.

### Step F. Proceed with iteration

Continue with `## Behavior > Step 1: Read Open Concerns` and run the standard iteration flow to completion. (Satisfies `aaew`.)

### What re-open does NOT reverse

Per `docs/spec.md > Cross-Cutting Mechanisms > Close-Sprint Manifest > What re-open does NOT reverse`, the following are append-only history and/or manual-disposition records; re-litigation is the user's manual responsibility:

- **Iteration-candidate dispositions** captured at the prior close.
- **Tech-debt entries** written at the prior close.
- **`docs/v2-verification.md` "Shipped" rows** written at the prior close.

Do not touch any of these during re-open.

## State Updates (immediate)

Before doing any other work:

1. Set `lastCommand` to `"/sdd:polish"` in `docs/project-state.json`.
2. Read and process `docs/open-concerns.md` per sdd-guide open concerns protocol.

## Read `smallProject` (startup)

After the `lastCommand` state update, read the top-level `smallProject` value from `docs/project-state.json`. If the key is absent or `null`, treat it as "no judgment yet" — standard cadence applies. See `skills/sdd-guide/references/right-sizing.md > ## The smallProject field` for what the value means and how it shapes downstream behavior.

Per `skills/sdd-guide/references/right-sizing.md > ## The smallProject field > Authoring lifecycle`, `/sdd:polish` **may re-write `smallProject` if observed signals contradict the current value** — this is permissive, not mandated. The right-sizing reference's authoring lifecycle does not require `/sdd:polish` to re-evaluate; it only requires `/sdd:prd`, `/sdd:spec`, and `/sdd:plan` to do so. If during the iteration interview the items surfaced make the current judgment plainly wrong (e.g., a polish round introduces a new third-party integration on a `smallProject: true` project, or a `false` project's remaining work collapses to one focused beat), the agent may write the new value to `docs/project-state.json` and append a one-line entry to `process-notes-sprint-N.md` noting the flip direction and rationale. Confirm = no-op. Bias toward humility — when in doubt, leave the value alone and proceed with standard cadence.

## First-Run Explanation

Check `docs/project-state.json` for `commandExplanationsShown.polish`.

**If `polish` has NOT been shown (or `commandExplanationsShown` does not exist):**

Output the following as plain text. This is not a question — do not wait for acknowledgment. Output it and continue.

---

**What /sdd:polish does**

Iterate is for polishing and improving what was built in the current sprint. Bugs, new features, UX improvements — anything that makes the sprint output better. I'll do a quick review of the current state, surface any backlog items worth pulling in, and then interview you about what you want to work on. That conversation produces a mini-checklist that gets appended to the sprint file. Then you run `/sdd:build` again to execute it. You can iterate as many times as you want within a single sprint.

---

After outputting, set `commandExplanationsShown.polish` to `true` in `docs/project-state.json`.

**If `polish` HAS been shown:** Skip the explanation entirely. Do not mention it.

## Behavior

### Step 1: Read Open Concerns

Read `docs/open-concerns.md` (if it exists). Resolve what you can based on the current sprint's completed work. Defer what you cannot. Note any new concerns that arise during iteration.

This was initiated during the state updates phase. Continue applying the protocol throughout this command.

### Step 2: Surface Backlog Items

Read `docs/backlog.md` (if it exists).

If the backlog contains items, surface 2-4 small items that could reasonably fit into an iteration cycle. Present them briefly — one line each with enough context for the user to decide.

Frame this as optional: "A few backlog items that might fit into an iteration round:" followed by the items. Do not push the user to accept any of them. If the user wants to pull one in, it gets added to the iteration checklist in Step 6.

If `docs/backlog.md` does not exist or is empty, skip this step without comment.

### Step 3: Quick Review of Current State

Review the completed sprint items and the current state of the project. Surface 2-3 observations about what was built. These should be concrete and specific — not generic praise. Focus on:

- Areas that might benefit from polish or hardening.
- Patterns or decisions that could cause problems later if not addressed now.
- Rough edges visible from the sprint work.
- Anything that feels incomplete even though it technically passes its acceptance criteria.

Present these observations to the user as a starting point, not as a mandate. The user decides what matters.

### Step 4: Ask What the User Wants to Work On

Ask the user a single, open-ended question about what they want to improve, fix, or add. Do not present categories or options. Let the user name what matters to them.

Example: "What do you want to work on in this iteration?"

One question. Wait for the response.

### Step 5: Scope Through Brief Interview

Based on the user's response, conduct a brief interview to scope the iteration work. This is not the full deepening rounds protocol — it is a focused conversation to clarify what needs to be done.

- Ask clarifying questions one at a time.
- Push back on vague descriptions. "Make it better" is not actionable. What specifically needs to change?
- If the user names multiple things, help them prioritize. All items can go into the iteration, but ordering matters for the checklist.
- If the work implies spec or PRD changes, follow the living-documents protocol: default to adding items to the backlog or unvetted section rather than modifying approved documents directly.

Keep this interview short and focused. The goal is enough clarity to write actionable checklist items, not a full planning session.

### Step 6: Generate Iteration Checklist

Determine the iteration number by scanning the current sprint file for existing `## Iteration N` headers. The new iteration is N+1 (or 1 if no iterations exist yet).

Selectively load relevant sections of `docs/spec.md` — only the sections that pertain to the iteration items being created. Do not load the full spec.

Generate a checklist and append it to the current sprint file under a new `## Iteration N` header. Each item follows this format:

```markdown
## Iteration N

### [Item Title]
- **What to do:** Brief, concrete description of the work
- **Spec ref:** `spec.md > [Section] > [Subsection]` (if applicable)
- **Acceptance criteria:**
  - [ ] Criterion 1
  - [ ] Criterion 2
- **Verification:** How to verify the item works
- **Status:** [ ] Not started
```

Rules for iteration items:
- **No artificial cap on items.** The checklist is sized by what the work requires. If the user wants 2 items, write 2. If they want 10, write 10.
- **Spec ref is included when applicable.** Bug fixes and UX tweaks may not have a spec section — that is fine. New features should reference the relevant spec section.
- **PRD ref is optional.** Iteration items are often polish work that does not map to a PRD story. Include it only when a clear mapping exists.
- **Acceptance criteria must be concrete and verifiable.** No vague criteria like "looks better." What specifically should be true when the work is done?

Present the iteration checklist to the user for review before appending it to the sprint file. Ask if anything needs to change. Iterate until they approve.

### Step 7: Append to Sprint File

Once the user approves, append the `## Iteration N` section to the end of `docs/sprint-N.md`.

Do not modify any existing content in the sprint file. Iteration sections are append-only.

### Step 8: Update Open Concerns

Update `docs/open-concerns.md` per sdd-guide protocol:

- Resolve any concerns addressed by iteration planning decisions.
- Defer concerns that belong to future iterations or commands.
- Append new concerns that arose during the iteration interview.

### Step 9: Process Notes

Append iteration process notes to `process-notes-sprint-N.md` where N is the current sprint number, per sdd-guide's `## Process Notes` (four categories, real-time, append-only). Capture polish specifics: what the user wanted to work on and why; how the iteration scope was narrowed or clarified; any backlog items pulled in and the rationale; concerns raised or deferred.

### Step 10: Delete docs/polish-resume.md

After the iteration checklist has been appended to `docs/sprint-N.md` (per Step 7 above), delete `docs/polish-resume.md` if it exists. The single resume file applies to `/sdd:polish` regardless of which iteration number was just produced. A missing file is not an error — continue silently. Per `skills/sdd-guide/references/pause-resume.md > ## Cleanup`, the resumed command owns this deletion; `/sdd:pause` is the only writer.

### Step 11: Wrap Up

Tell the user:

1. Iteration N has been appended to `docs/sprint-N.md`.
2. How many items are in the iteration.

Then emit the handoff per `## End-of-Command Handoff` below.

If the user wants to add more iteration work after this, they can run `/sdd:polish` again. Each run produces a new `## Iteration N` section. There is no limit on iteration cycles within a sprint.

## End-of-Command Handoff

Runs as the final step after Step 11 (the iteration checklist has been appended to the sprint file, open concerns have been updated, process notes have been written, and the resume-file cleanup has fired).

Emit the handoff per the canonical template in `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff`. That template defines the two-line standard form, the first-handoff explanation paragraph (prepended exactly once per user), and the `handoffWarningShown` tracking convention in `~/.claude/sdd-user-profile.json`. Do not restate that mechanism here.

### Next-command target

The `[next-command]` slot in the template is always `/sdd:build` for `/sdd:polish`. The iteration checklist needs to be executed before any further polish or planning.

### Outcome-summary line

Use a one-line outcome summary in the form `Polish iteration N appended.` Substitute the actual iteration number determined in Step 6 (e.g., `Polish iteration 2 appended. 4 items.`). Keep the shape declarative and single-line.

### Unconditional emission

The handoff fires unconditionally at completion. No context-weight heuristic, backlog-presence signal, or iteration-size threshold causes it to be skipped.

## Important Reminders

- One question at a time. Steps 4 and 5 involve user input — never combine them into a single message or ask multiple questions at once.
- Free-form questions only. No multiple-choice for scoping decisions.
- All sprint items must be complete before iterating. This includes items from previous iterations within the same sprint. No exceptions.
- Iteration items are appended — never modify existing sprint content.
- Mini-checklist size is driven by the work, not by an arbitrary cap. Do not limit iterations to a fixed number of items.
- Follow living-documents protocol for any changes that touch the spec or PRD. Default stance is to resist direct edits and route through the backlog.
- Selectively load spec sections. Only load what is relevant to the iteration items. Do not load the full spec, full PRD, or other sprint files.
- First-run explanation only on the very first `/sdd:polish` in the project. Check `commandExplanationsShown.polish` — do not show it on subsequent runs.
- Multiple iteration cycles are expected and normal. Each `/sdd:polish` run is a discrete cycle that appends a new section to the sprint file.
