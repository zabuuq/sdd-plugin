# SDD v6 — Brainstorm-First Front End — Scope

## Idea

v6 replaces the front end of SDD. Today the flow is a linear chain of four commands — `discovery` → `scope` → `prd` → `spec` — that each produce a separate document. v6 collapses that into a discovery-first flow built around one continuous idea: dump everything you have, let the AI draft, talk it through before and after the draft, prototype it, then let autonomous agents build it while you review pull requests.

The whole cycle is ten steps, delivered as six commands plus two anytime utilities:

1. **Ingest** the discovery material — inventory the sources and confirm the set.
2. **Pre-draft interview** — discovery-item-driven, data-first.
3. **Auto-draft** → a single planning document (a working draft, marked as draft inside).
4. **Draft review** — walk every assumption/gap marker the draft carries, resolving each in place.
5. **Finalize the document** — the reviewed draft graduates to the final document (draft status dropped). Same file throughout; it carries its final name from the start.
6. **Validate (optional)** — a skippable extra-assurance pass over the final document: self-critique plus any external validation files, then a walk-through of the differences. Run it when you want more assurance; skip it otherwise.
7. **Prototype** — one session, built page-by-page along defined navigation paths.
8. **Prototype refinement** — interview-style, page-by-page, checked against the document.
9. **Issues + build-loop** — refined work becomes GitHub issues; autonomous agents work one issue at a time, each opening a PR you code-review.
10. **Quick retro** — review lessons, promote the ones worth keeping, note the timing.

This is one scope with the full two-part vision, delivered as **one sprint** (the maintainer's cadence is one large sprint per version). Capture-before-consume still orders the work *within* the sprint: lessons are *captured* during discovery/refine/prototype/build and *consumed* at retro, so the capture behaviors land before retro — keeping the "never lose context" constraint intact.

## Who It's For

Jason, solo. No external users, no team, no backward compatibility to preserve — a rewrite of the front end is fine because nobody else depends on the old chain. The plugin dogfoods on itself: v6 is planned with the current SDD commands, and once built, SDD uses its own new flow.

## Inspiration & References

- **The conference SDD flow** (slides in `docs/refs/`): a six-step method — high-level goals → initial requirements → independent AI research across several models → consolidated spec → phased execution plan → coding sprints. This sparked v6, especially the "hand the docs to multiple AI models for a review pass" idea (now step 5) and the phased-execution/sprint model (now the build-loop).
- **The "Prompting Strategies That Worked" slide** (same source): state intent, constraints, and acceptance criteria; ask for a plan before implementation; make the agent surface its assumptions; use small sprints; make outputs inspectable; capture lessons immediately; treat the agent as a junior engineer with superpowers. These shaped the assumption-surfacing constraint, the interview-before-building posture, and the PR-per-issue review model.
- **The `build-loop` and `resolve-pr-feedback` skills** (`~/.claude/skills/`): the existing autonomous build loop — pull a task, plan, build in an isolated worktree, check with a separate reviewer agent, open a PR, never merge — is adopted directly as v6's execution engine for step 9, and its loop skeleton is reused (minus the git machinery) for the prototype in steps 7–8.
- **The two voice-memo transcripts** (`docs/refs/`): the original brain-dump that framed the redesign.
- **The current SDD chain**: v6 keeps what works — the interview, deepening-round mechanics, right-sizing, pause/resume — and replaces the four-document front end.

## Goals

- **Higher throughput.** Fewer commands, fewer hand-off seams, less ceremony between the idea and working code.
- **Brain-dump to working prototype in about a day.** This is the north-star success measure. The flow protects it by design and measures it lightly (see below).
- **One document, not three.** Intent, requirements, and architecture live in a single planning document instead of the scope/prd/spec trio. Removes the "which document does this sentence belong in" burden.
- **Autonomous build with human review as the gate.** Agents work issues one at a time, each producing one PR. Your job shifts to code review through pull requests, not writing every line.
- **Keep the interview.** Brainstorming with the AI is load-bearing, not overhead. The interview survives the rewrite and runs both before and after the draft.
- **Never lose accumulated context.** Every assumption gets surfaced and labeled; key choices persist as project settings; the flow no longer nags you to clear or compact, and gives you a checkpoint command to save context on your terms.

## What "Done" Looks Like

- The six-command flow (`discovery`, `refine`, `validate`, `prototype`, `build`, `retro`) plus `checkpoint` runs end-to-end on a real project, with `validate` optional.
- A discovery session that starts from mixed material (audio, transcripts, images, typed notes) ingests it, inventories it, interviews data-first, and produces a single drafted document — and the same flow works from nothing but a conversation.
- `refine` walks the draft's assumptions and gaps and finalizes the document; `validate` optionally runs an extra self-critique-plus-external-files pass and walks the differences before you commit.
- `prototype` produces a clickable-or-static prototype with a defined happy path, built and reviewed page-by-page, walled off from any real build.
- `build` runs the build-loop over GitHub issues: one issue → one branch → one PR, checked by a separate agent, never auto-merged, waiting on your review; `/sdd:resolve-pr` closes the loop after your comments.
- `retro` lists the lessons `build-loop` captured, prompts per-lesson to promote them (routing plugin-level to `/sdd:feedback`, global to `~/.claude`), notes the dump-to-prototype session count, and reports the issue-queue status.
- `/sdd:archive` handles the new file structure (single document, no sprint files, disposable prototype, the `docs/validation/` folder) and points its handoff at `/sdd:discovery`.
- The old front-end and build commands (`scope`, `prd`, `spec`, `plan`, `polish`) are gone.
- The plugin has been used to plan and build at least one thing on itself (dogfood).

## What's Explicitly Cut

Deferred items: see `docs/backlog.md`.

## Loose Implementation Notes

Early decisions and hunches from the scope interview. Not commitments — starting points for `/sdd:prd` and `/sdd:spec`. The full decision record is in `process-notes-scope.md`.

**Sacred constraints (hard boundaries):**
1. The interview survives the rewrite — before and after the draft.
2. Never lose accumulated context — every assumption surfaced and labeled "an assumption I made," persisted settings as insurance, capture-before-consume within the single sprint (capture behaviors land before retro consumes them).
3. Prototype code is walled off from the real build — a durable, client/investor-facing north-star reference; disposable code; the real product starts fresh and never builds on prototype code.

**The interview, both passes.** Pre-draft (step 2) is discovery-item-driven: for each question, look in the provided material first; if an answer exists, ask the question *and* state what was found, and you confirm, decline, or add; if not, just ask. Post-draft (step 4) is a lighter review focused on the assumptions the AI made and any gaps that only surfaced once the ideas were made concrete. Both reuse the deepening-round mechanics (one question at a time, active prompt, context tiers) but terminate on the gap/assumption list being exhausted, not a fixed round count. The 10-round cap stays as a backstop. When there's no input material, the pre-draft interview runs as a full brainstorm from scratch — a first-class path, not a degraded one.

**Ingestion (step 1).** Accepts text, images, and audio. Audio is transcribed in-flow (faster-whisper / the `transcribe` skill — local, free, private), not rejected. Sources are inventoried and confirmed before drafting so you catch a forgotten source or a missing one.

**Single document (step 3).** One planning document captures intent, requirements, and architecture. Its filename is a decision for `/sdd:spec`; everything downstream (the archive allow-list, the template, the handoffs) keys off it. It carries its **final name from the start** — never named "draft." Draft-vs-final status is the version number *inside* the file: the auto-draft writes it at major `0` (draft, `0.1`), and `refine`'s finalize step (step 5) bumps it to `1.0` (final). Same file throughout; the pipeline only ever updates it in place.

**Validate (optional command, step 6).** A standalone, skippable command — run it when you want an extra level of assurance on the finalized document, skip it otherwise. It starts with its own adversarial self-critique, then scans `docs/validation/` for external validation files (any file in that folder counts — no naming convention to worry about). Those files come from wherever you choose: a person's notes, another AI agent, Claude itself, or an automated agent-to-agent round-trip to ChatGPT/Codex/Gemini if that proves feasible (see `docs/open-concerns.md`). Depth varies — from a light "looks good / I'd change these" to deeper feasibility or tooling-need checks. Some validation files return a full modified copy of the document; others just list suggestions. `/sdd:validate` gathers them all and walks you through the differences one at a time — keep / remove / change. Separately, feasibility and viability pushback stays cross-cutting — the AI can flag "the market may not justify this" or "this is high-effort, do you still want it, or a lighter version?" any time after the brain-dump, not only during validate.

**Prototype (steps 7–8).** One session, one command — no GitHub issues, branches, or PRs (that machinery is for durable, merge-worthy work). Built page-by-page internally when large. The AI asks hi-fi (clickable) vs lo-fi (screenshots) **once**, persists the choice as a project setting, and remembers it on later runs (you switch by saying so). It ships with defined **navigation paths** — the primary end-to-end journeys, one or more, as many as the prototype needs to navigate — authored by the AI from the document and written to a companion file paired with the prototype. Those paths serve as the review spine, the testing script, and the demo script. Step 8 refines page-by-page along them, checking each page against the document. An optional page→issue mapping can seed the real issues for step 9.

**Prototype-loop reuses build-loop.** `prototype` and `build` run the same loop skeleton — plan → make → separate-checker → bounded-retry (≤2) → compound — with different outputs (disposable pages vs durable code) and different failure behavior. **Build** stops and surfaces on a twice-failed item. **Prototype** does not halt: it drops a `[GAP]` marker into the document flagging the too-complex page and keeps building, so the gap resurfaces during prototype review.

**Build (step 9).** `docs/backlog.md` stays as the unrefined holding area, kept local so external agents can't act on raw items. Refinement is the promotion gate: a refined item becomes a GitHub issue (title, solid description, acceptance criteria, link back to the document, labels) — the ready queue. Execution is `build-loop`: one issue at a time = one branch = one PR (the clean code-review unit), checked by a separate agent before it reaches you, never auto-merged. Decisions are front-loaded into the document and the issues so the loop makes none mid-build — the only human touch is the PR review gate. Multiple agents fan out across issues, each in its own worktree. "Sprint" survives only as an optional pull-label, never a planning document. `/sdd:resolve-pr` works your review threads after you comment and cleans up merged-PR branches.

**Retro (step 10).** Streamlined to three parts: lessons review and per-lesson promotion (with routing suggestions, and an active recommendation *against* re-promoting something already in global config), a rough dump-to-prototype timing note (session count, not a stopwatch), and issue-queue status. Drops the long-form structured-retro prose — `build-loop` has already captured lessons to `docs/learnings/` as it ran, so the retro reviews and promotes rather than excavates.

**Commands.**

| Command | Steps | Notes |
|---|---|---|
| `/sdd:discovery` | 1–3 | Refactored. Ingest → pre-draft interview → auto-draft. |
| `/sdd:refine` | 4–5 | Repurposed name (collision with the existing mid-chain `refine` — resolve at `/sdd:spec`). Draft review → finalize the document. |
| `/sdd:validate` | 6 | New, **optional**. Self-critique + external validation files from `docs/validation/` → walk the differences (keep/remove/change). |
| `/sdd:prototype` | 7–8 | New. |
| `/sdd:build` | 9 | Refactored to the build-loop. |
| `/sdd:retro` | 10 | Refactored, streamlined. |
| `/sdd:checkpoint` | anytime | New. Reads the session and writes a tailored `/compact` prompt; does not run compact itself. |
| `/sdd:resolve-pr` | anytime | New. Branch hygiene for closed/merged PRs + works review-feedback threads on its open PRs. |

Going away: `scope`, `prd`, `spec`, `plan`, `polish`. Kept as support/anytime commands: `onboard`, `pause`, `unpause`, `feedback`, `archive`.

**Context-management posture.** The flow no longer suggests `/clear` or `/compact` between commands — that's the user's call. `/sdd:checkpoint` replaces the nagging with an on-demand tool: when the session feels heavy and you want to compact rather than fully clear, it reads the session and prints a tailored `/compact <instructions>` prompt to chat for you to run. It never runs compact itself and writes no file. Persisted state (project settings, the single document, process notes) is the safety net if you do choose to clear.

**New constraints and downstream work.**
- **GitHub is a hard dependency** for the build half — `gh` authenticated and a repo (issues + PRs). The old flow never required it; `/sdd:onboard` should verify it. v6 doesn't run headless without a GitHub remote.
- **`project-state.json` schema grows** — persisted settings (prototype fidelity at minimum) and a changed `commandExplanationsShown` key set (now keyed to the full v6 command set — the chain commands plus the anytime utilities). v5 made no schema change; v6 does.
- **New `docs/validation/` folder** — the drop-zone `/sdd:validate` scans for external validation files. Any file in it counts.
- **`/sdd:archive` must adapt** — sweep the single document instead of the trio, sweep the `docs/validation/` folder, drop the dead `sprint-*.md` glob, leave disposable prototype code out of the versioned archive, and repoint its handoff from `/clear` + `/sdd:scope` to `/sdd:discovery`.
- **Shared-behavior cascade** (for `/sdd:spec` and `/sdd:plan`): update `sdd-guide`'s command chain to the six-command flow (`discovery → refine → validate → prototype → build → retro`, with `validate` optional); `sprint-tags.md` becomes obsolete; `context-management.md` is deleted (the no-auto-suggest posture removes the between-rounds model); `living-documents.md` for the schema; templates drop scope/prd/spec/sprint/discovery and gain a single-document template.
