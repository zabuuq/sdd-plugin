# SDD v6 — Brainstorm-First Front End — Technical Specification

## Spec Posture

This is a **command-rewrite design doc**, not a runtime spec. v6's product is the SDD
plugin's own front end, and the plugin is markdown-only — no runtime code, no build step,
no compiled artifacts. "Architecture" here means SKILL.md files, shared-behavior reference
files, a `project-state.json` schema, and template files. The standard spec template is
bent accordingly:

| Standard section | Here it means |
|---|---|
| Stack | The plugin's structural surface + external dependencies (`gh`, `transcribe`, git worktrees) |
| Runtime & Deployment | Marketplace distribution + `claude plugin validate`; no build step |
| Data Flow | The command pipeline + the marker resolve-loop + the shared build/prototype loop |
| Component sections | One per PRD epic — the SKILL.md and reference designs |
| Testing | Structural validation + dogfood + the verification audit trail |

Per `CLAUDE.md > Spec posture`: cross-cutting "how" lives in `plugins/sdd/skills/sdd-guide/references/*.md`. Where this spec designs a *new* reference file (`markers.md`, `build-loop.md`), it specifies the contract in full because that reference does not exist yet — the spec is its design source. It does **not** restate content of existing reference files that survive unchanged.

**Design directive for this cycle (maintainer):** nothing was deferred to build as an open *question*. Every design question that surfaced was resolved in this spec. Two *features* were explicitly scoped out to `docs/backlog.md` with full design guidance (automated external-AI validation; GitHub-native PR/branch hygiene) — deliberate feature-defers, not loose ends.

## Stack — Structural Surface & Dependencies

| Layer | Choice | Reference Docs |
|-------|--------|----------------|
| Plugin format | Claude Code plugin, markdown SKILL.md + YAML frontmatter, zero runtime | https://docs.claude.com/en/docs/claude-code/plugins |
| Distribution | GitHub marketplace (`.claude-plugin/marketplace.json`) | https://docs.claude.com/en/docs/claude-code/plugins-marketplaces |
| Validation | `claude plugin validate .` (structural) | https://docs.claude.com/en/docs/claude-code/plugins |
| VCS / build engine | git branches + worktrees (build-loop isolation) | https://git-scm.com/docs/git-worktree |
| GitHub integration | `gh` CLI — issues, PRs, branch ops (hard dependency for the build half) | https://cli.github.com/manual/ |
| Audio ingest | `transcribe` skill (faster-whisper, local/free/private) | (personal skill — `~/.claude/skills/transcribe`) |
| Loop engine | Vendored loop skeleton (from `build-loop` skill), owned by the plugin | (vendored — see `## Build-Loop Skeleton`) |
| Prototype output | Plain HTML/CSS/JS (no framework, no toolchain) | https://developer.mozilla.org/en-US/docs/Web |

There is no language runtime, database, ORM, auth layer, or styling framework — those
rows of the standard template are intentionally absent. The plugin's only executable
dependencies are `gh`, `git`, and the `transcribe` skill.

## Runtime & Distribution

- **No build step.** Behavior is defined entirely in markdown. Editing a SKILL.md changes
  behavior directly; there is nothing to compile.
- **Distribution.** The plugin ships via the GitHub marketplace. `plugin.json` and
  `marketplace.json` versions move together and bump for the v6 release.
- **Validation.** `claude plugin validate .` checks structural correctness (frontmatter,
  skill layout). This is the closest thing to a "build gate."
- **Self-hosting.** The plugin dogfoods on its own repository: v6 is planned with the
  current SDD commands, and once built, SDD runs its own new flow.

## Architecture Overview

v6 is six chain commands plus anytime utilities, all loading the shared `sdd-guide` core.

```
                       ┌─────────────────────── sdd-guide (SKILL.md) ───────────────────────┐
                       │ tone · interaction rules · guard rails · command chain · handoff    │
                       │ references/: markers · build-loop · right-sizing · deepening-rounds  │
                       │              living-documents · open-concerns · pause-resume · backlog│
                       │ templates/: plan-template · retro-template                           │
                       └─────────────────────────────────────────────────────────────────────┘

   CHAIN:   discovery ──▶ refine ──▶ [validate?] ──▶ prototype ──▶ build ──▶ retro
             (1–3)        (4–5)        (6, opt)         (7–8)        (9)       (10)

   ANYTIME: checkpoint · resolve-pr · pause · unpause · feedback · archive · onboard(gate)

   ARTIFACT SPINE:  docs/plan.md   (single source of truth, versioned in-file, markers inline)
```

Every chain command reads and/or writes the one document, `docs/plan.md`. Validate is
optional; the chain proceeds `refine → prototype` whether or not it ran.

### Data Flow

**1. Document lifecycle (primary path).**
```
discovery  refs/ + pasted + transcribed audio
           → inventory gate (WAIT for user response)
           → data-first interview (search material first; confirm / decline / add)
           → auto-draft  plan.md @ 0.1   (assumptions/gaps tagged inline as markers)
refine     scan → resolve in place → strip → re-scan  (until none resolvable)
           → finalize:  0.x → 1.0     (unresolved markers carry forward)
validate   offer self-critique (y/n) → if yes, writes a file into docs/validation/
(optional) → gather all docs/validation/ files (self-critique + external)
           → reconcile: walk diffs + markers one at a time, keep / remove / change
           → plan.md @ 2.0            (unresolvable items → carry-forward markers)
prototype  build pages along nav-paths (loop skeleton, no git)
           → too-complex page → [GAP] into plan.md, keep building
           → prototype/ + prototype/navigation-paths.md
build      refined items → GitHub issues → build-loop
           → 1 issue = 1 branch = 1 PR → ⏸ human review gate
retro      replay docs/learnings/ → per-lesson promote → timing + issue-queue status
resolve-pr (async) delete branches of closed PRs + work open-PR feedback threads
```

**2. Marker resolve-loop (refine + validate share it).**
```
scan plan.md  \[(ASSUMPTION|GAP|CONCERN) ([agc]\d+):\]  in document order
  → first marker → resolve with user
       ├─ resolved  → replace marked span in place, strip marker, re-scan
       └─ can't now → leave tagged (optionally + PROPOSED), move on
  → repeat until nothing left resolvable
  → surviving markers persist → carried to the next command
```

**3. Loop skeleton (build + prototype share; git only in build).**
```
plan → make → separate checker → pass? ─yes→ done
                                  └─no→ retry (≤2) → still failing?
        build      → STOP + surface        (durable; git worktree; agents fan out)
        prototype  → [GAP] into plan.md + CONTINUE   (disposable; no git)
```

## Markers

New reference file `plugins/sdd/skills/sdd-guide/references/markers.md` — the single
**parser contract** for inline markers, loaded by every command that writes or resolves
them. Direct replacement for the retired `sprint-tags.md`. This section is the full design
for that file.

Rationale for a shared reference: three-plus commands touch markers (`discovery` writes,
`refine`/`validate` resolve, `prototype`/`validate` write). Copying the format into each
SKILL.md would let writers and readers drift. One contract; each SKILL.md says "emit/scan
markers per `references/markers.md`."

### Marker syntax

Three types, inline and in place, machine-findable by a start-of-token scan:

- `[ASSUMPTION a3: <text>]` — a choice the AI made that the user has not confirmed.
- `[GAP g2: <text>]` — something the AI could not resolve and needs input on.
- `[CONCERN c1: <text>]` — a feasibility/viability pushback (`ffea`), kept visible so it
  survives a clear/compact.

Optional in-line proposed solution, worded so it reads "here's the gap, and here's what
I'd do about it," without removing the marker:

```
[GAP g2: the plan doesn't say how sessions expire — PROPOSED: default to a 30-day idle window]
```

### Marker IDs (stateless, round-local)

- A new marker's ID = (max **live** ID of that type currently in the document) + 1, or
  `1` if none of that type are live.
- IDs are **recycled** across rounds: if a resolution round clears every marker of a type,
  the next one starts at `1` again. The ID's only job is to find/reference a marker during
  a resolution pass — it is **not** a permanent citation (contrast AC IDs, which are stable
  and never recycled).
- A marker that persists (carried-forward, unresolved) keeps its number as long as it
  stays live; only a type with zero live markers restarts.
- IDs are stable **within** a single resolution walk (no mid-walk renumber).
- No persisted roll-up list of markers exists anywhere. Enumeration is always by fresh
  scan (`dasm`/`dgap`) — a separate list is a drift trap and a context-loss vector.

### Resolve-loop rules

- Scan regex: `\[(ASSUMPTION|GAP|CONCERN) ([agc]\d+):`, iterate in document order.
- Resolve first → replace the marked span in place with settled content → strip the marker,
  leaving no breadcrumb (`rres`) → re-scan → repeat (`rlop`).
- A marker added mid-loop (including a `prototype` `[GAP]`) is caught on a later re-scan.
- **Hard constraint:** the AI never strips a marker on its own; a marker is removed only
  through explicit user resolution (`rnau`). No silent closures.
- **Nothing is force-decided at any level, including prototype.** An item that genuinely
  can't be settled stays tagged and carries forward indefinitely (`rcnt`). The AI may
  append a `PROPOSED` solution but must keep the marker (`rpsl`).

### Writers and resolvers

- **Writers:** `discovery` (auto-draft), `prototype` (`[GAP]` on a too-complex page),
  `validate` (carry-forward markers out of its reconciliation).
- **Resolvers:** `refine` (scan loop), `validate` (inside its reconciliation walk).
- `validate`'s self-critique does **not** write markers into `plan.md` — it writes a
  validation file into `docs/validation/` (see `## Validate`). Markers only (re)enter
  `plan.md` as the carry-forward mechanism for unresolved reconciliation items.

## Document Model — `plan.md`

Implements `prd.md > Discovery` (`draf`, `dnam`) and the single-source-of-truth posture.

`docs/plan.md` is the one planning document — intent, requirements, and architecture in
one file. It carries its final name from creation; there is never a `plan-draft.md` or
`plan-v2.md` sibling. The pipeline only ever updates it in place.

### Template

New template `plugins/sdd/skills/sdd-guide/templates/plan-template.md`. Section order:

```
**Version:** 0.1

# [Project] — Plan

## Overview            problem, who it's for, north-star
## Goals & Success     goals + what "done" looks like
## Requirements        epic → story → acceptance criteria (stable 4-char AC IDs)
## Architecture        project-shaped: stack table (w/ doc URLs), file structure,
                       data flow, component ## / ### sections + inline data shapes/APIs
## Key Decisions       decision | rationale | tradeoff
## Non-Goals           + conditional docs/backlog.md pointer
```

- **Requirements stay epic-grouped with stable 4-char AC IDs**, exactly like today's PRD,
  because `build` lifts GitHub issues and ACs from here and links back by ID (`biss`). The
  document **owns** the ACs; issue creation lifts, never authors.
- **Markers live inline** wherever the content they annotate lives. There is no roll-up
  "open markers" section — enumeration is by scan.
- The template is general enough for any v6 project, not only this plugin.

### Versioning (in-file, single source)

A single visible line at the top: `**Version:** N.M`. Numbers only, no status words.

- **Major = document maturity:** `0` = draft, `1` = finalized, `2` = validated.
- **Minor = revision counter** within the current maturity; bumped on every command run
  that writes `plan.md`; resets to `0` when major bumps.
- Walk: discovery → `0.1` (re-draft `0.2`…); refine finalize → `1.0` (re-run `1.1`…);
  prototype `[GAP]` pre-validate → `1.1`; validate → `2.0` (re-run `2.1`…); prototype
  `[GAP]` post-validate → `2.1`.
- **The version number absorbs the draft/final flag.** Major `0` = draft; major `≥1` =
  final. One line, one source of truth for maturity — no separate draft marker.
- Milestone-major (not command-major) is deliberate: it stays monotonic on out-of-order
  re-runs, encodes "never validated" as "never reached `2.x`" (no confusing major gap when
  optional validate is skipped), and makes a prototype `[GAP]` a minor bump (annotating ≠
  maturing).
- `plan.md`'s version is **not** duplicated in `project-state.json` — it lives only in the
  document, so the two can't drift.

**PRD aligned (done during `/sdd:plan`):** `dnam`/`rfin` now describe draft/final as the
major digit of the version — `dnam` writes the draft at major `0` (`0.1`); `rfin` finalizes
via the `0.x → 1.0` bump. No separate draft-status marker.

## Discovery

Rewritten `plugins/sdd/skills/discovery/SKILL.md`. Implements `prd.md > Discovery`.
Steps 1–3: ingest → data-first pre-draft interview → auto-draft.

### Ingest & inventory
- Accepts text, images, and audio (`ingm`). Audio is transcribed in-flow via the
  `transcribe` skill / faster-whisper; the transcript becomes interview context (`ausd`).
- Before drafting, emit an inventory of every source found — files under `docs/refs/`,
  pasted material, in-flow transcriptions (`invl`) — then **block and wait** for an
  explicit user response (`ingw`).
- **No-input path:** empty/absent `docs/refs/` and nothing pasted → skip the inventory and
  run a full brainstorm interview as a first-class path, not an error (`noin`).

### Data-first pre-draft interview
- For each question, search the provided material for an answer before asking (`dfsc`).
- When the material answers it, ask the question *and* state what was found; accept
  confirm / decline / add. When it does not, ask fresh (`dfst`).
- Standard interview mechanics: one question at a time, ~5 default / 10 hard cap, then the
  deepening-round recommendation (`dfrq`). Reuses `references/deepening-rounds.md`.
- `discovery` inherits scope's former role as the **`smallProject` writer** (see
  `## Foundations & Migration`); right-sizing may trim this interview for small projects.

### Auto-draft
- Produces `docs/plan.md`, written to disk at `Version: 0.1` (`draf`, `dnam`).
- Every AI assumption is tagged inline as `[ASSUMPTION a#: …]` (`dasm`); every identified
  gap as `[GAP g#: …]` (`dgap`); feasibility/viability pushback as `[CONCERN c#: …]`.
- Draft status is the version major `0` (no separate marker).

## Refine

Rewritten `plugins/sdd/skills/refine/SKILL.md`. Implements `prd.md > Refine`.
**Naming collision resolved:** the old mid-chain `/sdd:refine` (living-documents
refinement, loop-to-plan) is fully replaced; the SKILL.md is rewritten from scratch and no
old behavior carries over. Name reused, behavior new — clean break, no aliasing.

### Marker walk (step 4)
- Scan `plan.md` for markers, present one at a time in document order (`rwlk`).
- Resolving replaces the marked text in place and strips the marker, no breadcrumb
  (`rres`). Re-scan and repeat until none remain resolvable; markers added mid-loop are
  caught on a later re-scan (`rlop`).
- Never remove a marker autonomously (`rnau`). Unresolvable markers stay tagged and carry
  to `prototype` (`rcnt`); the AI may append `PROPOSED` but must keep the marker (`rpsl`).

### Finalize (step 5)
- Drop draft status by bumping the version `0.x → 1.0` and updating the same file in place
  — no separate "final" file (`rfin`).

## Validate

New `plugins/sdd/skills/validate/SKILL.md`. Implements `prd.md > Validate (optional)`.
Optional and user-initiated; the chain proceeds `refine → prototype` whether or not it ran
(`vopt`).

### Three beats
1. **Offer self-validation (ask-first).** Ask whether to run Claude's adversarial
   self-critique now. Yes → run it and write the findings as a **validation file into
   `docs/validation/`**, uniform with external sources. No → skip. *(PRD `vslf` updated
   during `/sdd:plan` to "offers to run, asks first" — spec and PRD now agree. Rationale:
   the maintainer may have gathered enough external sources and not want another Claude
   pass, but must still run the command to pull all sources back in.)*
2. **Gather.** Scan `docs/validation/`; any file there is a validation file, no naming
   convention (`vscn`). Handle both a full modified copy of the document (extract changes
   by diff against `plan.md`) and a suggestion-list file (`vbth`).
3. **Reconciliation interview.** Present differences between the validations and `plan.md`
   one at a time (`vdif`); for each, accept keep / remove / change and apply in place
   (`vkrc`). Walk carried-over markers in the same pass. Output: `plan.md` updated in place,
   version bumped to `2.x`. Anything unresolvable → a carry-forward marker (never forced).

### Source-agnostic drop-zone (resolves the standing open concern)
`docs/validation/` is source-agnostic: validate treats every file identically regardless
of producer (Claude self-critique, another AI, a person). This makes any future automated
external-AI round-trip a pure external producer writing to the same folder — no structural
change needed. The standing open concern (free-tier ChatGPT/Gemini automation) is therefore
**closed**: the load-bearing manual + self-validation file-drop path is complete, and the
automation feature is captured in `docs/backlog.md` with full design guidance, deferred by
maintainer decision.

## Prototype

New `plugins/sdd/skills/prototype/SKILL.md`. Implements `prd.md > Prototype`. Steps 7–8.

### Fidelity (persisted)
- First prototype run in a project asks hi-fi (clickable) vs lo-fi (screenshots) (`pfid`).
- Persist to `project-state.json` under `settings.prototypeFidelity`; reuse on later runs
  without re-asking (`pfps`). Switch only when the maintainer explicitly asks (`pfsw`).

### Build & navigation
- Prototype is plain HTML/CSS/JS (`pweb`), output to `prototype/` at the repo root
  (walled off from any real build; nothing marks it as a foundation — `pwal`).
- Author navigation path(s) from `plan.md` and nothing else (`pnav`); write them to a
  companion `prototype/navigation-paths.md`, not into `plan.md`, and surface them (`pcmp`).
- Optionally produce a page→issue mapping that seeds the real build issues (`pmap`).

### Loop behavior
- Reuse the vendored loop skeleton (plan → make → separate checker → bounded retry ≤2)
  without git machinery (`plop`).
- On a page that fails its checker after the bounded retry, write a `[GAP: …]` marker into
  `plan.md` and **continue** building the rest — do not halt (`pfal`). The gap resurfaces
  in prototype review (`prvw`).

## Build

Rewritten `plugins/sdd/skills/build/SKILL.md`. Implements `prd.md > Build`. Step 9.

### Issues from refined work
- `docs/backlog.md` stays the local, unrefined holding area, never itself acted on by
  build agents (`bgat`).
- A refined item becomes a GitHub issue: title, description, acceptance criteria (lifted
  from `plan.md`), a link back to `plan.md`, and labels (`biss`).

### Build-loop
- One issue → one branch → one PR (`blop`). Uses the vendored loop skeleton **with** git
  machinery: each item runs in its own git worktree; multiple agents fan out across issues
  in parallel (`bfan`).
- Each PR is checked by a separate agent before it reaches the maintainer (`bchk`).
- Never auto-merge; wait on the maintainer's review (`bmrg`).
- No decisions mid-build: an under-specified issue is surfaced, not guessed; required
  decisions are expected to already live in `plan.md` and the issue (`bnod`).
- **PR/branch stamping convention (`rpst`):** the build-loop stamps its PRs/branches with an
  `sdd` label and a branch prefix `sdd/<issue>-<slug>` so `/sdd:resolve-pr` can recognize its own.

## Resolve-PR

New `plugins/sdd/skills/resolve-pr/SKILL.md`. Implements `prd.md > Resolve-PR` (`rpvc`, `rpbh`, `brpf`, `rpst`).
Separate anytime command (not a step inside `build`) because the review gate is
asynchronous and usually a fresh session. Two jobs:

1. **Branch hygiene** — find PRs it created (via the stamping convention) that are now
   closed/merged, and delete their local + remote branches.
2. **Feedback resolution** — find its still-open PRs carrying review comments and work each
   thread: fix → reply → resolve → push, in bounded rounds.

Vendored SDD command by necessity (tracks PRs *it* created — beyond the personal
`resolve-pr-feedback` skill), reusing that skill's thread-resolution logic. Single consumer,
so no `references/` file — the logic lives in the command SKILL. GitHub-native automation of
this hygiene is deferred to `docs/backlog.md`.

## Retro

Rewritten `plugins/sdd/skills/retro/SKILL.md`. Implements `prd.md > Retro`. Step 10.
Streamlined to three parts; drops the long-form structured-retro prose (`rtdr`).

- List the lessons captured to `docs/learnings/` during the project (`rtls`).
- Per lesson, prompt to promote and suggest routing: plugin-level → `/sdd:feedback`,
  global → `~/.claude` (`rtpr`). Actively recommend *against* re-promoting a lesson already
  present in global config (`rtre`).
- Report a rough dump-to-prototype timing as a session count, not a stopwatch (`rttm`),
  and the current issue-queue status (`rtiq`).

## Checkpoint

New `plugins/sdd/skills/checkpoint/SKILL.md`. Implements `prd.md > Checkpoint`. Anytime,
independent of chain position (`ckan`).

- Read the session and emit a tailored instruction string for Claude Code's
  `/compact <instructions>` form, naming what to preserve (`ckrd`).
- Print the string to chat; write no file (`ckpr`). Never run `/compact` itself (`cknr`).
- Distinct from `/sdd:pause` — writes no resume file (`ckps`). One-directional; no pull-in
  mode. Replaces the removed between-rounds auto-suggest.

## Lesson Capture

Cross-cutting behavior in the build-loop and the doc-creation commands. Implements
`prd.md > Lesson Capture`.

- The AI detects lesson-worthy events (lost-context / redundant-question events, repeated
  roadblocks, course corrections, reusable designs/practices/preferences) and writes them
  to `docs/learnings/` without prompting (`lcau`).
- Active during doc creation (`discovery`, `refine`) and item build (`prototype`, `build`)
  (`lcsp`). Captured lessons surface at `/sdd:retro` for promotion (`lcsr`).
- The maintainer's ad-hoc "record this globally now" move stays a manual action, not
  implemented or watched for by the plugin (`lcno`).
- `docs/learnings/` is a **permanent carry-forward store** — never swept by archive.

## Foundations & Migration

Implements `prd.md > Foundations & Migration`.

### GitHub gate (onboard)
- `/sdd:onboard` verifies `gh` is authenticated and a repository exists (`fgit`).
- On either failure it **hard-blocks**: v6 does not start and `/sdd:discovery` cannot
  proceed. A block, not a warning (`fgbl`).

### project-state.json schema (v2)
New reference behavior in `references/living-documents.md`. Full v6 shape:

```jsonc
{
  "version": 2,                    // bumped — schema shape changed
  "cycleNumber": 6,                // top-level; archive increments
  "smallProject": false,          // top-level; AI-derived, right-sizing (stays top-level)
  "lastCommand": "/sdd:discovery",
  "settings": {                    // NEW — persisted USER choices
    "prototypeFidelity": null      //   "hifi" | "lofi" | null
  },
  "commandExplanationsShown": {    // rekeyed to the v6 command set
    "discovery": false, "refine": false, "validate": false,
    "prototype": false, "build": false, "retro": false,
    "checkpoint": false, "resolve-pr": false, "onboard": false,
    "pause": false, "unpause": false, "feedback": false, "archive": false
  },
  "notes": "..."
}
```
- **Added** `settings` (persisted user choices; `pfps`/`pfsw` read/write
  `settings.prototypeFidelity`) (`fsch`).
- **Dropped** `currentSprint` and `buildMode` — vestigial (no sprint docs; build is always
  autonomous).
- **Rekeyed** `commandExplanationsShown` to the v6 set (`fcek`).
- `smallProject` stays top-level: top-level = project identity / AI-derived state;
  `settings` = sticky user choices. `smallProject` is machine-owned and auto-re-evaluated —
  semantically wrong to bucket with a user preference.

### Shared-behavior cascade (`fchn`, `fcas`, `ftpl`)
- **`sdd-guide/SKILL.md`:** command chain → `discovery → refine → validate → prototype →
  build → retro` (validate optional); update the interview-command set and end-of-command
  handoff next-command map; **remove the Between-Rounds Context Recommendation section**.
- **`references/sprint-tags.md`:** retired (deleted); `references/markers.md` replaces it.
- **`references/context-management.md`:** retired (deleted outright). The whole file is the
  between-rounds three-tier auto-suggest model; v6 drops auto-suggest, leaving no residue.
  The one-liner ("context is user-driven; use `/sdd:checkpoint`") lives in checkpoint's
  SKILL.md. **Two reference files retire.**
- **`references/right-sizing.md`:** kept in full (no simplification). Mechanical remap only:
  `discovery` inherits scope's role as `smallProject` writer; re-evaluators become the
  surviving v6 interview commands (`refine`, `validate`); skippable-beat lists remap to v6
  commands.
- **`references/living-documents.md`:** schema v2 above + updated archive reset map (drop
  `currentSprint`/`buildMode`; add `settings` carry; `smallProject` reset to null;
  `docs/learnings/` as carry-forward never-swept).
- **`references/deepening-rounds.md`:** kept (discovery pre-draft, refine, validate); trim
  its coupling to the deleted context recommendation.
- **Templates:** drop scope/prd/spec/sprint/discovery templates; add `plan-template.md`;
  keep/update `retro-template.md`.
- **New references:** `markers.md`, `build-loop.md`.

### Archive adaptation (`farc`)
- Sweep allow-list: trio → **`plan.md`**; **add `docs/validation/`**; **drop `sprint-*.md`**
  and **drop `discovery.md`**; keep `retro.md`, `open-concerns.md`, `*-resume.md`, root
  `process-notes-*.md`.
- **Never-swept carry-forward set** grows to include `docs/learnings/` alongside
  `backlog.md`, `sdd-feedback.md`, `project-state.json`.
- `prototype/` (repo root) is outside `docs/`, so archive never touches it — no special
  exclusion logic; it stays live as the durable north-star reference.
- Handoff repoint: `/clear` + `/sdd:scope` → `/clear` + `/sdd:discovery`.
- Reset map: drop `currentSprint`/`buildMode`; carry `settings`; rekey
  `commandExplanationsShown`.

### Command removals (`frem`)
- Delete `scope`, `prd`, `spec`, `plan`, `polish` skill dirs. Keep `onboard`, `pause`,
  `unpause`, `feedback`, `archive`. Add `validate`, `prototype`, `checkpoint`, `resolve-pr`;
  rewrite `discovery`, `refine`, `build`, `retro`.

### Feasibility/viability pushback (`ffea`)
Cross-cutting behavior any command from `discovery` onward can raise; not gated to a single
step. Written into `plan.md` as `[CONCERN c#: …]` markers so it survives clear/compact.

## Build-Loop Skeleton

New reference file `plugins/sdd/skills/sdd-guide/references/build-loop.md` — the vendored
loop skeleton, owned by the plugin (not the personal `~/.claude/skills/build-loop`). Loaded
and specialized by `build` and `prototype`.

Shared skeleton: **plan → make → separate checker → bounded retry (≤2) → compound (capture
lessons)**. Specializations:

| | build (durable) | prototype (disposable) |
|---|---|---|
| Isolation | git worktree per item; agents fan out | none |
| Output | branch + PR per issue | pages in `prototype/` |
| On twice-failed item | **stop and surface** | write `[GAP]` into `plan.md`, **continue** |
| PR/branch stamping | `sdd` label + `sdd/<issue>-<slug>` | n/a |

## File Structure

**Plugin source — `plugins/sdd/` after v6:**
```
plugins/sdd/
  .claude-plugin/plugin.json          # version bump
  CLAUDE.md                           # loader pointer (unchanged)
  skills/
    sdd-guide/
      SKILL.md                        # chain updated; context-rec section removed
      references/
        markers.md                    # NEW — marker parser contract
        build-loop.md                 # NEW — vendored loop skeleton
        right-sizing.md               # kept; writer remap scope→discovery
        deepening-rounds.md           # kept; context coupling trimmed
        living-documents.md           # schema v2
        open-concerns.md  pause-resume.md  backlog.md   # kept
        ── sprint-tags.md             DELETED
        ── context-management.md      DELETED
      templates/
        plan-template.md              # NEW
        retro-template.md             # kept/updated
        ── scope/prd/spec/sprint/discovery templates  DELETED
    discovery/SKILL.md                # rewritten
    refine/SKILL.md                   # rewritten
    validate/SKILL.md                 # NEW
    prototype/SKILL.md                # NEW
    build/SKILL.md                    # rewritten
    retro/SKILL.md                    # rewritten
    checkpoint/SKILL.md               # NEW
    resolve-pr/SKILL.md               # NEW
    onboard/SKILL.md                  # updated — GitHub hard gate
    pause/ unpause/ feedback/ archive/  SKILL.md   # kept (archive updated)
    ── scope/ prd/ spec/ plan/ polish/  DELETED
.claude-plugin/marketplace.json       # version bump (moves with plugin.json)
```

**Project docs — what the v6 commands read/write:**
```
docs/
  plan.md              # single source of truth (Version line + inline markers)
  validation/          # drop-zone: self-critique + external validation files
  refs/                # ingest input (audio / images / text)
  backlog.md           # carry-forward — never swept
  learnings/           # carry-forward — never swept
  sdd-feedback.md      # carry-forward — never swept
  project-state.json   # carry-forward — never swept
  open-concerns.md     # swept at archive
  archive/vN/          # prior cycles
prototype/             # ROOT — disposable code, durable north-star reference
  navigation-paths.md  # companion (nav paths)
process-notes-*.md     # ROOT — swept at archive
```

## Testing Strategy

No automated test suite — the plugin is markdown with no runtime. Verification is
structural and behavioral, matching this repo's established practice.

### Structural
- `claude plugin validate .` — frontmatter and skill-layout correctness. Run after every
  SKILL.md change and before release.

### Behavioral (dogfood)
- The plugin runs its own commands on this repository as it is built. v6 is planned with
  the current SDD commands; once the new commands exist, exercise them end to end on a real
  project (the north-star: brain-dump → working prototype in ~a day, at least once).
- The `docs/v2-verification.md` audit trail carries forward: implementing commands populate
  rows; the maintainer marks a row verified after an end-to-end exercise.

### Coverage expectations
- Every new/rewritten SKILL.md must pass `claude plugin validate`.
- Each of the nine epics must be dogfooded at least once before the cycle closes.
- The marker resolve-loop and the versioning walk are the highest-risk mechanisms — verify
  them explicitly (write markers, resolve some, leave some, confirm carry-forward and
  version bumps).

## Key Technical Decisions

| Decision | Why | Tradeoff |
|---|---|---|
| Single `plan.md`, one file in place | Kills "which doc does this belong in"; one source of truth | Large single file; relies on markers + headings for navigation |
| Filename `plan.md` | The doc *is* the plan of record | `/sdd:plan` was a retired command name — mild echo |
| In-file numeric version, milestone-major | Monotonic across re-runs; encodes maturity incl. "never validated" | Matches PRD `dnam`/`rfin` (aligned during `/sdd:plan`) |
| Version absorbs draft/final flag | One source of truth for maturity, not two | Slightly less explicit than a named "draft" marker |
| Markers as inline parser contract (`markers.md`) | Shared by 4+ commands; prevents writer/reader drift | New reference file to maintain |
| Stateless, round-local marker IDs | No hidden counter; always derivable by scan | IDs are not permanent citations (fine — that's not their job) |
| Nothing force-decided; markers carry forward | Some things can't be decided until building | A plan can ship with open markers |
| Vendor the loop skeleton into the plugin | Self-contained plugin; no dependency on a personal skill | A second copy of the loop to keep current |
| `resolve-pr` a separate command | Review gate is async / fresh session; keeps build single-job | One more command surface |
| Self-validation → a file in `docs/validation/` | Uniform source handling; makes automation orthogonal | Claude's critique is a file to read, not inline notes |
| Source-agnostic `docs/validation/` drop-zone | Any producer (self, AI, person, future automation) is identical | No provenance tracking of who validated |
| Prototype at repo root, disposable + durable | Walled off from real build; auto-excluded from archive | Lives outside `docs/` tidy tree |
| `docs/learnings/` never swept | A learning is useful regardless of cycle | The pile grows unbounded across cycles |
| Drop `currentSprint`/`buildMode` from schema | No sprint docs; build always autonomous | Schema-v2 migration for existing state files |
| Delete `context-management.md` outright | Whole file was the auto-suggest model v6 removes | Loses the tier taxonomy (unused in v6) |
| Keep right-sizing in full | Small-project courtesy still matters for discovery | Four-command lifecycle machinery for fewer readers |
| GitHub hard gate at onboard | v6's build half can't finish without it | Blocks even the local front half without `gh` + repo |

## Open Issues

No unresolved *design* questions — the maintainer's directive was to close every question
in this spec, and each was resolved. Two follow-on items remain, both tracked, neither
blocking v6:

- **PRD wording tweaks — DONE during `/sdd:plan`.** `vslf` (self-critique is offered, ask-
  first, not auto-run) and `dnam`/`rfin` (draft/final is the version major digit, not a
  separate marker) are now aligned in `prd.md`. A resolve-pr epic (`rpvc`, `rpbh`, `brpf`,
  `rpst`) was also added to `prd.md` so the vendored `/sdd:resolve-pr` command has ACs.
- **Deferred features (in `docs/backlog.md`, by maintainer decision):** automated
  external-AI validation runs in `/sdd:validate`; GitHub-native PR/branch-hygiene
  automation. Both have full design guidance captured; both are follow-on runs.

## Out of Scope

Deferred items: see `docs/backlog.md`.
