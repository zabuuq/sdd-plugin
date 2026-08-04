**Version:** 1.2

# SDD Plugin 4.1 — Flow Tweaks & Resumability — Plan

## Overview

This is a targeted update to the SDD plugin, taking it from **4.0.0 to 4.1** — tweaks to the existing six-command flow, not a new version of the product. The plugin's flow (`onboard → discovery → refine → validate → prototype → build → retro`) is sound; this pass fixes friction the maintainer hit while working with it and closes gaps between the intended design and what actually shipped in 4.0.0.

Six changes, driven by two voice memos and a working session:

1. **Prototype becomes explicitly optional** — not every job needs a throwaway prototype (a client's finished website needing text edits doesn't). Skip straight to tickets/build when the work doesn't warrant it.
2. **Commands stop redoing finished work** — re-running `prototype` or `build` after a stop currently rebuilds the prototype or re-creates duplicate issues. They should detect prior progress and pick up where they left off.
3. **`/clear` leaves the handoffs** — the maintainer decided against pushing `/clear` between commands; it never got removed. Remove it.
4. **Build gains opt-in milestone grouping** — keep the clean one-issue/one-PR default, but allow grouping issues under a milestone when the maintainer wants fewer, themed PRs.
5. **The prototype walkthrough becomes interactive** — after the AI's own review, the maintainer walks the prototype with the AI stepping them through each path, fixing issues live.
6. **Version-scheme confusion gets documented** — the plugin's semver (`4.x`) and the internal dev `cycleNumber` (`7`) are different things that looked like they should match. Make the distinction explicit.

The north star: the maintainer can run this workflow on real client work — including quick modification jobs — without the process fighting them, and without losing work when a command is interrupted.

## Goals & Success

**Goals**

- Make the flow fit modification jobs, not only greenfield builds.
- Guarantee that stopping and re-running a command never destroys or duplicates completed work.
- Remove the `/clear` nudge the maintainer rejected, cleanly, including its now-dead supporting machinery.
- Give the prototype review the interactive, human-in-the-loop shape it was always meant to have.

**Success looks like**

- Running `refine → build` (no prototype) is a supported, documented path — nothing implies prototype is mandatory.
- Re-running `/sdd:build` after issues exist goes straight to the build loop and creates zero duplicates.
- Re-running `/sdd:prototype` after a prototype exists goes straight to review, rebuilding nothing.
- No command handoff mentions `/clear`; `/sdd:checkpoint` still works for on-demand context management.
- A prototype review is a two-phase exchange: AI reports, then the maintainer walks it with live fixes.
- `plugin.json` reads `4.1.0`; the docs state plainly that semver and `cycleNumber` are separate.

## Requirements

### Epic: Optional prototype

- As the maintainer, I want to skip the prototype when a job doesn't need one, so I can go straight from planning to build on modification work.
  - `pqra` — `refine → build` runs end-to-end with no prototype step; `/sdd:build`'s only chain prerequisite stays `plan.md` at version ≥ 1.0.
  - `pqrb` — `/sdd:refine`'s handoff presents prototype as optional — names both "prototype it" and "go straight to build" as valid next steps rather than nudging toward prototype.
  - `pqrc` — No command SKILL, handoff, or doc states or implies the prototype is a required step in the chain.
  - `pqrd` — Skipping the prototype requires no extra interview or confirmation gate; it is the maintainer's decision, taken by simply running `/sdd:build`.

### Epic: Progress-aware commands (detect and auto-advance)

- As the maintainer, I want a re-run of `build` to skip work already done, so stopping after issue creation and coming back later never creates duplicate issues.
  - `dvaa` — On startup, `/sdd:build` detects issues it already created for the current plan and does not re-create them. A plan item counts as "already an issue" when an open `sdd`-labeled issue in the repo references that item's AC ID(s) in its body (the link-back build stamps at creation) — detection is `sdd` label plus AC-ID match, not title similarity.
  - `dvab` — When issues already exist, `/sdd:build` auto-advances to the build loop without prompting; it creates issues only for genuinely new plan items not yet represented.
  - `dvac` — Detect-and-auto-advance is silent — no "resume, skip, or start over?" prompt.
- As the maintainer, I want a re-run of `prototype` to skip rebuilding, so returning to a finished prototype goes straight to review.
  - `dvad` — On startup, `/sdd:prototype` detects an existing prototype under `prototype/` and auto-advances to the review phase rather than rebuilding pages from scratch.
  - `dvae` — `/sdd:prototype` records the `plan.md` version it built against as a line in `prototype/navigation-paths.md`. On a re-run: if some pages are missing, it builds only those; if the current `plan.md` version is newer than the recorded one, it auto-rebuilds the pages affected by the changed plan sections (no prompt); then it proceeds to review. When the recorded version matches the current plan and all pages exist, it auto-advances straight to review without rebuilding.
  - `dvaf` — The existing `/sdd:pause` → `/sdd:unpause` path remains the resume mechanism for `discovery`, `refine`, and `validate`; those commands are not given artifact-detection auto-advance. Refine and validate already resume through the live document; discovery relies on pause/unpause.

### Epic: Remove /clear from handoffs

- As the maintainer, I want `/clear` gone from the flow, so the process stops pushing a step I decided against.
  - `clra` — No command handoff emits a `/clear` instruction. Interview-command handoffs read "Run `/sdd:[next-command]` to continue." with nothing about context.
  - `clrb` — The first-handoff `/clear` explanation block is removed from `sdd-guide/SKILL.md`.
  - `clrc` — The `handoffWarningShown` field and its tracking are removed from the plugin: `/sdd:onboard` stops seeding it, and no command reads or flips it. A stray `handoffWarningShown` left in an existing `~/.claude/sdd-user-profile.json` is ignored as inert data — no command reads it, no migration strips it, and its presence never errors. No profile-schema migration ships with this work.
  - `clrd` — `/sdd:checkpoint` is unchanged and remains the on-demand context tool; nothing about context management is removed beyond the `/clear` handoff nudge.

### Epic: Opt-in milestone grouping in build

- As the maintainer, I want to group issues under a milestone when I choose, so related work can land as one PR instead of many.
  - `mssa` — `/sdd:build` defaults to one issue → one branch → one PR, unchanged, when no milestone grouping is declared.
  - `mssb` — When issues are grouped under a milestone, that group builds under one branch and one PR for the milestone; the PR closes all issues in the group. Grouping uses GitHub-native milestones. `/sdd:build` groups by whatever milestone assignments exist on the issues at loop time, so the maintainer can assign them at the issue-confirm step on a fresh run or on a later re-run before the loop starts. The re-run grouping offer is distinct from the silent creation auto-advance in `dvac`: auto-advance suppresses only the re-create/"start over?" prompt, not the optional, skippable chance to assign milestones before building.
  - `mssc` — Milestone branches and PRs carry the `sdd` stamping convention so `/sdd:resolve-pr` recognizes them, consistent with per-issue branches/PRs.
  - `mssd` — Grouping is opt-in per run; the maintainer is not forced to define milestones and is not prompted to when they don't want them.

### Epic: Interactive prototype walkthrough

- As the maintainer, I want to walk the prototype with the AI after its own review, so I catch issues by using the product and get them fixed on the spot.
  - `wlka` — The prototype review keeps its current Phase A: the AI walks each page against `plan.md` along the navigation paths and reports matches, divergences, and gaps.
  - `wlkb` — A new Phase B follows Phase A: the AI steps the maintainer through each navigation path one step at a time; the maintainer reports pass, fail, or change-wanted per step.
  - `wlkc` — When the maintainer flags an issue mid-walk, it is fixed live in the prototype before the walk continues.
  - `wlkd` — Confirming a live fix re-walks the affected path or restarts the affected step, so the fix is verified in place before moving on.
  - `wlke` — Phase B runs for both fidelities: hi-fi the maintainer clicks through; lo-fi the AI steps through screens in order and the maintainer confirms by eye. Live fixes and re-walk-to-confirm are identical for both.

### Epic: Marker deferral is the maintainer's call only

- As the maintainer, I want the marker walk to attempt every marker and defer only when I say so, so nothing gets shelved without my decision.
  - `mdfa` — `/sdd:refine`'s marker walk attempts a genuine resolution of every marker in document order; no marker is skipped or set aside by the AI.
  - `mdfb` — A marker carries forward only on the maintainer's explicit decision that it can't be resolved yet. The AI never initiates deferral on its own judgment — not for a stalled discussion, not because it reads as downstream, not as cleanup.
  - `mdfc` — The AI may still offer a `PROPOSED` resolution inside a marker, but offering one never substitutes for attempting resolution with the maintainer and never shelves the marker.
  - `mdfd` — `refine`'s Step 2 (Carry-Forward) and `markers.md` are updated so carry-forward triggers are maintainer-driven only, removing the AI-initiated conditions ("discussion stalls without a ruling," "depends on something downstream") as independent grounds for the AI to defer.

### Epic: `/sdd:amend` — add items to an existing plan

- As the maintainer, I want a command that adds new items to a finalized plan without disturbing what's there, so scope discovered after finalizing has a clean home instead of forcing an overwrite or a misuse of refine.
  - `amna` — `/sdd:amend` appends new epics/stories/ACs with fresh 4-char IDs and inline markers, and never edits, reorders, or removes existing plan content. Its only precondition is an existing `docs/plan.md`; it works at any version, draft (`0.x`) or finalized (`≥1.0`).
  - `amnb` — `/sdd:amend` runs a short data-first interview scoped to the additions — plus optional `docs/refs/` ingest — reusing discovery's mechanics (one question at a time, data-first confirm, deepening-round recommendation per `deepening-rounds.md`) over the new items only. It uses these same mechanics regardless of which command last ran; it does not inherit or adapt to the prior command's behavior. Amend only ever appends plan items to `plan.md` — it never touches prototype pages, navigation paths, or GitHub issues.
  - `amnc` — On completing the interview, `/sdd:amend` writes the new items into `plan.md` tagged with markers per `markers.md`, then bumps the version by a single minor point from wherever it is — never a major bump (e.g. `0.2 → 0.3`, `1.1 → 1.2`).
  - `amnd` — `/sdd:amend`'s handoff points to `/sdd:refine`, so the newly added markers get walked like any others.
  - `amne` — `/sdd:amend` is an anytime utility, chain-independent, sitting alongside `checkpoint` and `resolve-pr`. Its single precondition is an existing `docs/plan.md` at any version; it is not a fixed step in the linear chain.
  - `amnf` — Adding `/sdd:amend` updates the command set at every place it is enumerated:
    - `skills/amend/SKILL.md` — the new command skill itself (frontmatter, loading, prerequisites, interview, write, handoff → refine).
    - `references/living-documents.md` — add `amend` to the `commandExplanationsShown` key set and to the reset-normalization enumeration (the canonical v6 command list).
    - `sdd-guide/SKILL.md` — the Command Chain section (list amend as an anytime utility alongside `checkpoint`/`resolve-pr`), the Command Explanations key list, and the End-of-Command Handoff "out-of-pattern commands" list plus next-command map (`amend → refine`).
    - `docs/project-state.json` — a fresh `commandExplanationsShown` gains an `amend` key; an existing state file missing the key treats amend as not-yet-explained (no migration).
    - The plugin manifest version bumps to `4.1.0`; no per-command list in the manifest needs editing (skills auto-discover).

### Epic: Remove acceptance-criteria checkboxes

- As the maintainer, I want ACs written as plain list items instead of checkboxes, so the plan reads as a spec and "what's done" lives only in GitHub.
  - `acka` — `plan-template.md` writes acceptance criteria as plain list items — `` - `abcd` — <criterion> `` — with no `[ ]` checkbox. The 4-char backtick ID stays exactly as-is.
  - `ackb` — No command emits AC checkboxes: `/sdd:discovery` and `/sdd:amend` write ACs as plain bullets.
  - `ackc` — AC-ID lifting is unaffected — `build` and `refine` key off the 4-char backtick ID, not checkbox state; issue creation copies the AC text (without a box) with its ID.
  - `ackd` — This repo's existing `docs/plan.md` ACs are converted from `[ ]` to plain bullets when this work ships. The plugin does not retroactively rewrite any other or in-flight plans — only the template and this project's own plan change; other projects keep their checkboxes until their next natural edit, and AC-ID lifting is unaffected either way.

### Epic: Retro promotes global learnings through a PR

- As the maintainer, I want global learning-promotions during retro to land as a single PR in my global repo, so my "PR-only, never push to main" rule holds for `~/.claude` too.
  - `rgpa` — When `/sdd:retro` promotes one or more learnings to global scope, it edits the files under the global directory (`~/.claude`) but never commits directly to the global repo's default branch, and never commits without asking. At the end of retro it asks whether to commit the promotions to the global repo; only an affirmative answer triggers the branch/push/PR flow.
  - `rgpb` — On an affirmative answer, retro batches all global promotions from the session into **one branch**, applies the changes, pushes, and opens a **single PR** against the global repo — one PR per retro, not one per learning. On a decline, the promotion edits are left uncommitted in the `~/.claude` working tree for the maintainer to handle manually, and retro says so.
  - `rgpc` — The commit prompt and branch/PR flow fire only when the global directory is a git repo with a remote and `gh` is authenticated for it. Otherwise retro falls back to editing the global files directly (today's behavior) and tells the maintainer it could not open a PR — never a hard stop, never a lost promotion. With no repo to commit to, there is nothing to ask.
  - `rgpd` — The branch and PR follow the maintainer's conventions (`type/short-desc` branch, imperative subject); the PR body lists the promoted learnings. Retro detects the global repo's remote and default branch at retro end (via `git`/`gh` — e.g. `git symbolic-ref refs/remotes/origin/HEAD`, `gh repo view --json defaultBranchRef`) and branches off the detected default rather than hardcoding `origin`/`main`.

### Epic: Version-scheme clarity

- As the maintainer, I want the two version numbers explained, so "version 4" stops being ambiguous.
  - `vera` — `plugins/sdd/.claude-plugin/plugin.json` version is bumped to `4.1.0` as part of shipping this work.
  - `verb` — `AGENTS.md` states the distinction plainly in a short subsection: `plugin.json` semver (`4.x`) is the published plugin version; `project-state.json` `cycleNumber` is a separate internal dev-loop counter; they are not meant to match. `CLAUDE.md` needs no separate note — it imports `AGENTS.md` via `@AGENTS.md`.
  - `verc` — No machinery change: the `cycleNumber`/archive numbering and `/sdd:archive` are untouched; the two schemes stay separate by design.

## Architecture

This is a markdown-only plugin — no runtime code, no build step. "Architecture" here is the file-impact map: which SKILLs and references change, and the contracts that must stay intact.

### File-impact map

```
plugins/sdd/
  .claude-plugin/plugin.json              4.0.0 → 4.1.0                              [vera]
  skills/
    sdd-guide/SKILL.md                    handoff template: drop /clear + first-     [clra clrb clrc]
                                          handoff explanation + handoffWarningShown
    onboard/SKILL.md                      stop seeding handoffWarningShown           [clrc]
    discovery/SKILL.md                    handoff wording (no /clear)                [clra]
    refine/SKILL.md                       handoff: prototype optional + no /clear;   [pqrb clra
                                          Step 2 carry-forward = maintainer-only      mdfa-mdfd]
    sdd-guide/references/markers.md       carry-forward triggers maintainer-driven    [mdfd]
    sdd-guide/templates/plan-template.md  ACs = plain bullets, drop [ ] checkbox      [acka ackd]
    validate/SKILL.md                     handoff wording (no /clear)                [clra]
    prototype/SKILL.md                    optional framing; startup auto-advance;    [pqrc dvad dvae
                                          Phase B interactive walkthrough            wlka-wlke]
    amend/SKILL.md (new)                  new command: append items to a plan,       [amna-amnf]
                                          short interview, minor bump, → refine
    retro/SKILL.md                        global promotions → batched branch+PR      [rgpa-rgpd]
    build/SKILL.md                        issue-existence detection + auto-advance;  [pqra dvaa-dvac
                                          milestone grouping; optional-prototype     mssa-mssd]
                                          prereq framing
    sdd-guide/references/
      build-loop.md                       milestone-group specialization (if needed) [mssb]
      living-documents.md                 note version-scheme distinction (optional) [verb]
docs/
  AGENTS.md (project)                     version-scheme distinction subsection      [verb verc]
~/.claude/sdd-user-profile.json           handoffWarningShown retired (tolerate old) [clrc c1]
```

### Contracts that must not break

- **The chain still runs in order.** These are tweaks inside commands, not a re-architecture. `plan.md` remains the single source of truth; markers, AC IDs, and the state schema are unchanged except where a requirement names them.
- **`sdd` stamping convention.** Milestone branches/PRs must stamp exactly like per-issue ones so `/sdd:resolve-pr` still recognizes loop output.
- **`project-state.json` v2 schema** is untouched by this work — no new fields, no dropped fields. `cycleNumber` and the archive machinery stay as-is.
- **Profile back-compat.** Removing `handoffWarningShown` must tolerate existing profiles that still carry it (see `c1`).

## Key Decisions

| Decision | Rationale | Tradeoff |
|---|---|---|
| Prototype optional via framing, not new machinery | `/sdd:build` already only requires `plan.md` ≥ 1.0 — `refine → build` already runs; only the wording implied prototype was mandatory | Relies on the maintainer knowing when to skip; no gate catches a job that should have been prototyped |
| Detect-and-auto-advance, silent | The maintainer wants zero friction on re-run; a prompt every time is the friction being removed | Less explicit — a re-run does something without asking; mitigated by it only ever *skipping* redundant work, never destroying |
| Auto-advance scoped to prototype + build only | Those two create external artifacts (files, GitHub issues) that get duplicated; refine/validate already resume through the document; discovery's interview state is genuinely hard to auto-detect | Discovery still needs `pause`/`unpause` foresight to resume mid-interview |
| `/clear` removed entirely, checkpoint kept | The maintainer rejected the `/clear` nudge; `/sdd:checkpoint` already covers on-demand context management | Users who relied on the automatic reminder lose it; context hygiene becomes fully self-directed |
| Milestone grouping opt-in, per-issue default | Keeps the clean isolated-PR default for the common case; grouping serves the occasional themed feature build | Two build shapes to maintain and stamp correctly; grouping mechanism still needs pinning (see `a2`) |
| Interactive walkthrough added *after* AI review, not replacing it | The maintainer wants both: the AI's own pass catches obvious divergence; the human walk catches lived-experience issues | Longer prototype phase; live-fix loop can extend a review session |
| Global retro promotions go through a batched PR, not direct edits | Applies the maintainer's "PR-only, never push to main" rule to `~/.claude`; batching at retro end gives one reviewable PR per session | Retro gains git/`gh` machinery and a fallback path for when the global dir isn't a PR-able repo |
| AC checkboxes removed rather than wired to a checkoff mechanism | A checkbox reads as "done"; v6 dropped checkoff, and mirroring GitHub state into the plan invites two-places-to-update drift. Plan = spec, GitHub = tracking | No at-a-glance progress inside `plan.md`; the maintainer reads status from issues/PRs |
| Adding items to a finalized plan is a new command (`/sdd:amend`), not a refine extension | refine's v6 identity is a narrow marker-walk; discovery is overwrite-from-scratch — neither has a home for "add scope after finalizing." A dedicated command keeps each command single-purpose | One more command to build, plumb into the command set, and maintain |
| Semver and cycleNumber kept separate, documented | They measure different things — published version vs. internal loop count; collapsing them would force a semver judgment every archive | The two numbers keep diverging; the only guard against confusion is the doc note |

## Non-Goals

- **No new command version / no 5.0.** This is a 4.0 → 4.1 tweak pass. Nothing here rewrites the flow or introduces a new command set.
- **No change to `cycleNumber` or `/sdd:archive` machinery.** The internal dev-loop counter and archive numbering stay exactly as they are.
- **No collapsing of the two version schemes.** Considered and rejected — semver and cycleNumber stay separate.
- **No change to `project-state.json` v2 schema.**
- **No forced prototyping and no forced milestones.** Both are opt-in; the defaults (skip-able prototype, per-issue build) stand on their own.

Deferred items: see `docs/backlog.md`.
