# /sdd:scope — Session Resume

**Project:** sdd-plugin (SDD v2 cycle)
**Paused at:** 2026-05-01, mid-Beat-3 of the scope interview
**Resume command:** `/sdd:scope` (it will detect this file alongside any seed)

---

## Status

In the middle of the Phase 1 mandatory beats of `/sdd:scope`. Beats 1 and 2 fully covered (with a clarifying follow-up on Beat 2). Beat 3 (Inspiration & References) was just asked when the session paused.

**Beats covered so far:** 1 (the idea), 2 (who it's for) + follow-up
**Beats remaining (Phase 1):** 3 (inspiration & references), 4 (goals), 5 (what "done" looks like), 6 (solo or team)
**After Phase 1:** project size assessment → deepening rounds → document generation

---

## Pre-interview setup already completed

So the resuming agent does not redo any of this:

- v1 planning docs (`scope.md`, `prd.md`, `spec.md`, `sprint-1.md`, `retro.md`) moved to `docs/archive/v1/`.
- v1 process notes moved to `docs/archive/v1/process-notes.md`.
- Fresh `process-notes.md` started in the project root with a `/sdd:scope` section header and v2 cycle context.
- `docs/project-state.json` reset for v2: `currentSprint` = 0, `buildMode` = null, `lastCommand` = `/sdd:scope`, notes field updated to describe the v2 cycle.
- `commandExplanationsShown.scope` was already `true` from v1, so the first-run scope explanation should not be reshown on resume.
- `docs/sdd-feedback.md` is intact and serves as the de facto seed for v2 (the user copied feedback notes from other projects into this repo for v2 scoping). There is no `docs/seed.md`; treat the feedback file as the seed.
- `docs/open-concerns.md` is intact with four open items carried in from v1 retro/scope. The "Post-sprint command ceremony" entry explicitly targets `/sdd:scope or /sdd:refine` — flag it for resolution or principled deferral during this v2 scope pass.

---

## Confirmed decisions (Beats 1–2)

### Beat 1 — The idea
**v2 is a streamlining pass of the v1 plugin, driven by real-use feedback.** Not a feature-expansion. The spine is "refine what already works based on friction surfaced across actual project usage in v1."

### Beat 2 — Who it's for
**Primary audience tightened from v1.** v1 was "Jason + MIT-licensed permissive sharing." v2 is "Jason + a local tech group Jason is a member of, who he plans to actively hand this to."

**Use case (from follow-up):** Both real-project usage AND kick-the-tires feedback. Implication: v2 needs to clear a working-software bar (multi-session reliability, onboarding polish, error messages, etc.) AND its feedback loop (`/sdd:feedback`, cross-project feedback transfer) must be real and easy to use, since invited users will be a feedback source.

---

## Pending — next question on resume

**Beat 3 — Inspiration & references.** Exact question to re-ask:

> v1's references were the hackathon plugin (its parent), your old ChatGPT prompt-builder workflow, agile, and GitHub project management. For v2, the dominant input is obviously v1 itself plus the feedback pile.
>
> Are there **new** references shaping v2? Things you've encountered since v1 shipped — other Claude Code plugins, workflow tools, books or articles, anything from the tech group, patterns you've seen elsewhere that you want v2 to absorb? Or is v2's reference set basically just "v1 + the feedback"?

---

## Feedback themes already noted (not yet discussed in interview)

The agent skimmed `docs/sdd-feedback.md` at the start of the session and surfaced these themes in the opening message. The user has not yet committed any of them as v2 goals; they are on deck for Beat 4 (Goals) and Beat 5 (Done).

- File uploads during planning (scope and elsewhere)
- `/discovery` vs `/scope` rename / split (sdd-plugin self-feedback says: rename `/scope` → `/discovery` and `/prd` → `/scope`)
- Deepening-round prompts should preview topics, not be bare yes/no
- First-class multi-session resume support (this very resume file is dogfooding the request)
- Automatic backlog generation during planning skills
- Cross-project feedback transfer — `/feedback` runnable from either project to move notes to plugin
- Smarter sprint-mode memory ("use last sprint's mode?" / "always use this mode")
- `/sdd:reflect` PRD-state-tracking gap on non-split items (drift across Sprint 1/2 in fiver-gigs)
- Reflect-on-simple-sprint feels heavy; fast-path for short sprints
- `/sdd:feedback` clobbers `lastCommand` — back-to-back feedback entries lose original "after command" context
- Encourage more user input during `/scope` rather than the user feeling they should hold back
- Onboarding feels lacking
- Context management for many deepening rounds (compact / summarize strategies)

---

## Instructions for the next-session agent

1. **Do not rerun the setup.** All archival, state reset, and process-notes scaffolding is done. Do not re-archive v1 or recreate `process-notes.md`.

2. **Do not re-ask Beats 1 or 2.** They are settled. The "Confirmed decisions" section above is canon for the scope doc.

3. **Open with a brief acknowledgment** that you've loaded this resume file, then ask the Beat 3 question verbatim from the "Pending" section.

4. **Continue Phase 1** through Beats 3–6 adaptively. Beat 6 (solo or team) is almost certainly "solo, but designed cleanly so the local tech group can use it" — confirm rather than re-elicit.

5. **Then Phase 2 (deepening rounds)** per `skills/sdd-guide/references/deepening-rounds.md`. The feedback pile gives strong material for deepening — especially around the `/discovery` rename, multi-session resume schema, and the `/sdd:feedback` cross-project transfer.

6. **Address the "Post-sprint command ceremony" open concern** during this scoping pass. Either resolve it (commit to a redesign in v2 scope) or formally defer it with rationale to a later command.

7. **Append to `process-notes.md`** as the conversation progresses — do not batch at the end.

8. **Document generation** at the end: write `docs/scope.md` (overwriting nothing — v1 is in archive), update `docs/open-concerns.md`, and `docs/backlog.md` if anything is deferred.

9. **Delete or archive this file** once the scope doc is generated and approved. It is a transient handoff artifact.

---

## Files the next-session agent should read on startup

In addition to the standard skill startup reads:

- This file (`docs/scope-resume.md`) — first.
- `docs/sdd-feedback.md` — the v2 seed material.
- `docs/open-concerns.md` — four items carried in from v1.
- `process-notes.md` (project root) — v2 process notes already started.
- `docs/archive/v1/scope.md` — for context on what v2 is streamlining (do not treat as v2 input).
- `docs/archive/v1/retro.md` — the retro that motivates the redesign (helpful context for deepening rounds).
