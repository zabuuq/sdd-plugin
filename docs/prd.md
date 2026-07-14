# SDD v6 — Brainstorm-First Front End — Product Requirements

## Problem Statement

SDD's front end is a linear chain of four commands — `discovery` → `scope` → `prd` → `spec` — each running its own 5–20 question interview and producing its own document. That structure taxes the maintainer before any code gets written: roughly 8 hours across 2 days assembling documents, repetition of the same ground across four interviews, and cross-phase context loss where a spec-flavored thought raised during scope gets deferred and then lost before its command arrives. The retros come up hollow, and some questions can't be answered until you're building anyway. The result: only a few projects ever reach "finished."

v6 replaces the front end with a brainstorm-first, draft-first flow optimized for **throughput of finished projects**, not per-step speed. You dump what you have, the AI drafts a single source-of-truth document, you talk it through before and after the draft, you prototype it, and autonomous agents build it while you review pull requests. The north-star measure: get from brain-dump to a working prototype in about a day, at least once. The audience is Jason, solo — no external users and no backward-compatibility obligation, which is what makes a clean-break rewrite acceptable. The plugin dogfoods on itself.

## User Stories

### Discovery

- As the maintainer, I want to hand the flow whatever material I already have and know it was all picked up, so I don't silently lose a source.
  - [x] `ingm` — `/sdd:discovery` accepts text, images, and audio as ingest input.
  - [x] `ausd` — Audio sources are transcribed in-flow (via the `transcribe` skill / faster-whisper) rather than rejected; the resulting transcript is used as interview context.
  - [x] `invl` — Before drafting, the command emits an inventory listing every source it found: files under `docs/refs/`, pasted-in material, and in-flow transcriptions.
  - [x] `ingw` — The command blocks after presenting the inventory and waits for an explicit user response before it begins drafting.
  - [x] `noin` — When there is nothing to ingest (empty/absent `docs/refs/` and no pasted material), the command skips the inventory and enters a full brainstorm interview as a first-class path, not an error state.

- As the maintainer, I want the pre-draft interview to use what I already gave it instead of making me repeat myself, so the interview is shorter and less draining.
  - [x] `dfsc` — For each interview question, the command first searches the provided material for an answer before asking.
  - [x] `dfst` — When the material answers a question, the command asks the question *and* states what it found, and accepts a confirm / decline / add response; when the material does not answer it, the command asks the question fresh.
  - [x] `dfrq` — The pre-draft interview uses standard interview mechanics: one question at a time, a default of ~5 questions with a hard cap of 10, followed by the deepening-round recommendation.

- As the maintainer, I want the AI to draft one document from that conversation, so intent, requirements, and architecture live in a single source of truth.
  - [x] `draf` — The command produces one planning document capturing intent, requirements, and architecture, written to disk.
  - [x] `dnam` — The document file carries its final name from creation (never named "draft"); draft-vs-final status is the version major digit *inside* the file (major `0` = draft), written as a draft (`0.1`) at this stage.
  - [x] `dasm` — Every assumption the AI makes is tagged inline, in place, with a machine-findable marker carrying a short ID (e.g. `[ASSUMPTION a3: ...]`).
  - [x] `dgap` — Every gap the AI identifies is tagged inline, in place, with a machine-findable marker carrying a short ID (e.g. `[GAP g2: ...]`).

### Refine

- As the maintainer, I want to walk the draft's assumptions and gaps one at a time and resolve them in the document itself, so nothing gets lost and the document improves in place.
  - [x] `rwlk` — `/sdd:refine` scans the document for markers and presents them one at a time in document order.
  - [x] `rres` — Resolving a marker replaces the marked text in place with the settled content and strips the marker, leaving no breadcrumb.
  - [x] `rlop` — After each resolution the command re-scans and repeats until no markers remain except those explicitly left tagged; a marker added mid-loop is picked up on a later re-scan.
  - [x] `rnau` — The command never removes a marker on its own; a marker is removed only through explicit user resolution.

- As the maintainer, I want items that can't be settled yet to survive rather than get force-closed, so decisions that depend on building aren't guessed away.
  - [x] `rcnt` — When a marker can't be resolved during refine, it stays tagged in the document and carries forward to `prototype`.
  - [x] `rpsl` — The AI may append a proposed solution next to an unresolved marker, but only if it also keeps the marker in place.

- As the maintainer, I want refine to hand off a finalized document, so downstream commands work against a committed source of truth.
  - [x] `rfin` — At the end of the review pass the command finalizes by bumping the version major (`0.x → 1.0`, draft → final) and updates the same file in place — no separate "final" file is created.

### Validate (optional)

- As the maintainer, I want validation to be a separate, skippable command, so I only pay for an extra assurance pass when I want one.
  - [x] `vopt` — `/sdd:validate` is optional: the flow can proceed from `refine` to `prototype` whether or not `validate` has run, and running it is user-initiated.

- As the maintainer, I want validate to combine the AI's own critique with outside opinions and let me adjudicate each difference, so I control what changes.
  - [x] `vslf` — On start, the command offers to run its own adversarial self-critique of the finalized document — it asks first and does not auto-run; on decline it goes straight to gathering external validation files.
  - [x] `vscn` — The command scans `docs/validation/` for external validation files; any file present in that folder is treated as a validation file, with no naming convention required.
  - [x] `vbth` — The command handles both a validation file that is a full modified copy of the document (extracting changes by diff against the source of truth) and a validation file that only lists suggestions.
  - [x] `vdif` — The command presents the differences between the validations and the document one at a time.
  - [x] `vkrc` — For each difference, the command accepts a keep / remove / change decision and applies it to the document in place.

### Prototype

- As the maintainer, I want the fidelity choice remembered after I make it once, so I'm not re-answering it every run.
  - [x] `pfid` — On the first prototype run in a project, the command asks hi-fi (clickable) vs lo-fi (screenshots).
  - [x] `pfps` — The choice is persisted to `docs/project-state.json` and reused on later runs without re-asking.
  - [x] `pfsw` — The maintainer can switch the persisted fidelity by explicitly asking; the command does not re-surface the choice each run.

- As the maintainer, I want a prototype built from the document with its own navigation map, so I can review, test, and demo along a defined path.
  - [x] `pweb` — The prototype is built in plain HTML/CSS/JS.
  - [x] `pnav` — The command authors the navigation path(s) from the source-of-truth document and nothing else, producing one or more paths as needed to navigate the prototype.
  - [x] `pcmp` — The navigation paths are written to a companion file paired with the prototype (not into the source-of-truth document) and surfaced to the maintainer.
  - [x] `pwal` — The prototype's code is kept walled off from any real build; nothing marks it as a foundation for the final product.

- As the maintainer, I want the prototype loop to keep going when a page is too hard and record why, so one stubborn page doesn't stall the whole prototype.
  - [x] `plop` — The command reuses the build-loop skeleton (plan → make → separate-checker → bounded retry ≤2) without the git machinery.
  - [x] `pfal` — When a page fails its checker after the bounded retry, the command writes a `[GAP: ...]` marker into the source-of-truth document and continues building the rest of the prototype rather than halting.
  - [x] `prvw` — The prototype-review step walks each page along the navigation paths and checks it against the document.
  - [x] `pmap` — The command can optionally produce a page→issue mapping that seeds the real build issues.

### Build

- As the maintainer, I want only refined work to become GitHub issues, so autonomous agents act on ready items and never on raw ideas.
  - [x] `bgat` — `docs/backlog.md` remains the local, unrefined holding area and is never itself acted on by build agents.
  - [x] `biss` — A refined item becomes a GitHub issue carrying a title, a description, acceptance criteria, a link back to the document, and labels.

- As the maintainer, I want the build to run autonomously and reach me only at the PR, so my job is code review, not writing every line or answering mid-build questions.
  - [x] `blop` — `/sdd:build` runs the build-loop such that one issue maps to one branch and one pull request.
  - [x] `bchk` — Each PR is checked by a separate agent before it reaches the maintainer.
  - [x] `bmrg` — The build-loop never auto-merges a PR; it waits on the maintainer's review.
  - [x] `bnod` — The build-loop makes no decisions mid-build; when an issue is under-specified it surfaces rather than guessing, and required decisions are expected to already live in the document and the issue.
  - [x] `bfan` — Multiple agents can work different issues in parallel, each in its own worktree.

### Resolve-PR

- As the maintainer, I want a dedicated command to clean up after my PR reviews, so merged-branch cruft and unresolved feedback threads get handled without manual git work.
  - [x] `rpvc` — `/sdd:resolve-pr` is a vendored anytime SDD command — distinct from the personal `resolve-pr-feedback` skill, whose thread-resolution logic it reuses — runnable independent of chain position.
  - [x] `rpbh` — Branch hygiene: the command finds PRs it created that are now closed or merged and deletes their local and remote branches.
  - [x] `brpf` — Feedback resolution: after the maintainer leaves review comments, `/sdd:resolve-pr` works each open-PR review thread (fix → reply → resolve → push) in bounded rounds.
  - [x] `rpst` — The build-loop stamps its PRs and branches (an `sdd` label plus an `sdd/<issue>-<slug>` branch prefix) so `/sdd:resolve-pr` can recognize its own.

### Retro

- As the maintainer, I want the retro to replay the lessons already captured and help me promote the keepers, so it surfaces something real instead of asking "did it go well?" cold.
  - [x] `rtls` — `/sdd:retro` lists the lessons captured to `docs/learnings/` during the project.
  - [x] `rtpr` — For each lesson, the command prompts to promote it and suggests routing: plugin-level lessons to `/sdd:feedback`, global lessons to `~/.claude`.
  - [x] `rtre` — The command actively recommends against re-promoting a lesson already present in global config.
  - [x] `rttm` — The command reports a rough dump-to-prototype timing as a session count, not a stopwatch time.
  - [x] `rtiq` — The command reports the current issue-queue status.
  - [x] `rtdr` — The retro omits the old long-form structured-retro prose.

### Checkpoint

- As the maintainer, I want an on-demand way to compact context on my terms, so a heavy session doesn't force a full clear or nag me between commands.
  - [x] `ckan` — `/sdd:checkpoint` runs anytime, independent of chain position.
  - [x] `ckrd` — The command reads the session and emits a tailored instruction string for Claude Code's `/compact <instructions>` form, naming what to preserve.
  - [x] `ckpr` — The instruction string is printed to chat; the command writes no file.
  - [x] `cknr` — The command never runs `/compact` itself.
  - [x] `ckps` — The command is distinct from `/sdd:pause`: it does not write a resume file.

### Lesson Capture

- As the maintainer, I want lessons captured automatically as they happen, so mistakes and roadblocks get recorded without me having to notice and ask.
  - [x] `lcau` — The AI detects lesson-worthy events (lost-context / redundant-question events, repeated roadblocks, course corrections, reusable designs/practices/preferences) and writes them to `docs/learnings/` without prompting.
  - [x] `lcsp` — Automatic capture is active during doc creation (`discovery`, `refine`) and during item build (`prototype`, `build`).
  - [x] `lcsr` — Captured lessons surface at `/sdd:retro` for promotion.
  - [x] `lcno` — The maintainer's ad-hoc "record this globally right now" move is left as a manual action and is not implemented or watched for by the plugin skills.

### Foundations & Migration

- As the maintainer, I want GitHub verified up front, so I never get partway through a flow that can't finish.
  - [x] `fgit` — `/sdd:onboard` verifies that `gh` is authenticated and a repository exists.
  - [x] `fgbl` — When either check fails, `/sdd:onboard` hard-blocks: v6 does not start and `/sdd:discovery` cannot proceed. It is a block, not a warning.

- As the maintainer, I want the shared state and behavior updated to the new flow, so every command agrees on the structure.
  - [x] `fsch` — `docs/project-state.json` gains persisted settings (prototype fidelity at minimum).
  - [x] `fcek` — The `commandExplanationsShown` key set is rekeyed to the full v6 command set — the six chain commands plus the anytime commands (`checkpoint`, `resolve-pr`, `pause`, `unpause`, `feedback`, `archive`, `onboard`).
  - [x] `fchn` — `sdd-guide`'s command chain is updated to `discovery → refine → validate → prototype → build → retro`, with `validate` marked optional.
  - [x] `fcas` — The shared-behavior references are updated: `context-management.md` deleted outright (v6 drops the between-rounds auto-suggest model), `sprint-tags.md` retired, `markers.md` and `build-loop.md` added as new reference files, `living-documents.md` updated for the schema change, `deepening-rounds.md` trimmed of its context-recommendation coupling, and `right-sizing.md` remapped (scope→discovery as the `smallProject` writer).
  - [x] `ftpl` — The templates drop scope/prd/spec/sprint/discovery, add the single planning-document template (`plan-template.md`), and keep/update the retro template.

- As the maintainer, I want archive and the retired commands handled cleanly, so the repo doesn't carry dead machinery.
  - [x] `farc` — `/sdd:archive` sweeps the single document (not the scope/prd/spec trio), sweeps `docs/validation/`, drops the dead `sprint-*.md` glob, excludes disposable prototype code from the versioned archive, and repoints its handoff from `/clear` + `/sdd:scope` to `/sdd:discovery`.
  - [x] `frem` — The commands `scope`, `prd`, `spec`, `plan`, and `polish` are removed; `onboard`, `pause`, `unpause`, `feedback`, and `archive` are kept.
  - [x] `ffea` — Feasibility/viability pushback is available as cross-cutting behavior any command from `discovery` onward can raise when warranted; it is not gated to a single step.

## Unvetted

<!-- Items added during sprints that have not yet been refined. /sdd:refine processes these before future sprints. Do not remove this section. -->

## What We're Building

A clean-break replacement for SDD's front end, delivered as six commands plus two anytime utilities. `/sdd:discovery` ingests mixed material (text, images, transcribed audio), interviews data-first, and auto-drafts a single source-of-truth document with every AI assumption and gap tagged inline. `/sdd:refine` walks those markers, resolving each in place, and finalizes the document. `/sdd:validate` is an optional extra-assurance pass that combines the AI's self-critique with external validation files dropped into `docs/validation/` and lets the maintainer adjudicate each difference. `/sdd:prototype` builds a disposable, client-facing HTML/CSS/JS prototype page-by-page along AI-authored navigation paths, marking too-complex pages as gaps instead of stalling. `/sdd:build` turns refined items into GitHub issues and runs an autonomous build-loop — one issue, one branch, one PR, checked by a separate agent, never auto-merged, no decisions made mid-build — with the maintainer's PR review as the only gate. `/sdd:resolve-pr` cleans up merged-PR branches and works review-feedback threads on its open PRs. `/sdd:retro` replays automatically-captured lessons for promotion. `/sdd:checkpoint` prints an on-demand `/compact` prompt when a session gets heavy. Lessons are captured automatically throughout, GitHub is a hard prerequisite verified at onboard, and the source docs, project state, archive, and templates all migrate to the new structure. The whole thing ships as one sprint (the maintainer's cadence is one large sprint per version). Capture-before-consume orders the work within the sprint: lesson capture (in discovery/refine/prototype/build) lands before retro consumes it.

## What We'd Add With More Time

Deferred items: see `docs/backlog.md`.

## Non-Goals

- No backward compatibility with the old four-command front end. v6 is a clean-break replacement; the old chain is retired, not preserved.
- Not built for external or team users. It's a solo tool for the maintainer; multi-user concerns are out.
- The prototype is not a code foundation. Its code is disposable; the real product starts fresh, using the prototype only as a north-star reference.
- No paid external-AI models in validation. External validation files may come from free tools or people; paid models are out.
- Automated agent-to-agent external validation is not a load-bearing requirement. The manual file-drop into `docs/validation/` is the load-bearing path; automated orchestration is best-effort and unresolved (see `docs/open-concerns.md`).
- No sprint planning documents. "Sprint" survives only as an optional PR label, never a planning artifact; `sprint-*.md` files are gone.
