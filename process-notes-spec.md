# Process Notes — /sdd:spec (v6)

## Framing

- **Spec is a design doc, not a runtime spec.** v6's "architecture" is markdown: six
  SKILL.md files, a shared-behavior cascade, a `project-state.json` schema change, and
  template swaps. Standard template sections bent accordingly — Stack table → plugin
  structural surface + external dependencies (`gh`, `transcribe`, `build-loop`);
  Runtime & Deployment → marketplace distribution + `plugin validate`; Data Flow →
  command pipeline + marker resolve-loop; Testing → structural validation + dogfood.
  Component sections map to the nine PRD epics. Matches CLAUDE.md spec posture.
- **smallProject re-evaluation:** confirmed `false` (six commands, migration, schema
  change, substantial reference uploads). No-op, no write.

## Phase 1 decisions

- **NO DEFERRALS THIS CYCLE (maintainer directive).** "Things pushed to the build get
  lost in the shuffle." Every question that surfaces during this spec must be resolved
  by the time the spec is done — including the parked open concern (external
  ChatGPT/Gemini automation for `/sdd:validate`), which must now be settled here rather
  than punted to `/sdd:build`. No `docs/backlog.md` writes this session.
- **Single-document filename: `docs/plan.md`.** Chosen over `blueprint.md`. Carries this
  name from creation; draft status is an internal marker. Everything downstream keys off
  it (archive allow-list, template, load lists, handoffs).
- **build-loop dependency: VENDOR.** The plugin owns its own loop skeleton rather than
  depending on the personal `~/.claude/skills/build-loop`. Keeps v6 self-contained.
  Natural home: a new cross-cutting reference `references/build-loop.md` holding the
  shared plan→make→separate-check→bounded-retry(≤2)→compound skeleton, loaded and
  specialized by both `build/SKILL.md` (durable, git machinery, stops-on-fail) and
  `prototype/SKILL.md` (disposable, no git, marks-gap-and-continues). `resolve-pr-feedback`
  vendored alongside (build-only). Confirm homes during architecture proposal.
- **Marker machinery → new reference `references/markers.md`** (single parser contract,
  direct replacement for the retiring `sprint-tags.md`). Settled details:
  - Three marker types: `[ASSUMPTION a3: ...]`, `[GAP g2: ...]`, `[CONCERN c1: ...]`.
    CONCERN is new — holds feasibility/viability pushback (`ffea`) so it stays visible
    through a clear/compact. All three use the same machinery.
  - **IDs (REVISED — stateless, round-local):** a new marker's ID = (max *live* ID of
    that type in the document) + 1, or 1 if none of that type are live. Purely derived
    from current document state — no persisted counter, no memory. **Numbers are recycled
    across rounds:** if a round clears every marker of a type, the next one starts at 1
    again. That's fine and intended — the ID's only job is to find/reference a marker
    during a resolution pass, not to be a permanent citation. A marker that persists
    (carried-forward, unresolved) keeps its number as long as it stays live; only a type
    with zero live markers restarts. IDs stay stable within a single walk (no mid-walk
    renumber). **Contrast:** AC IDs are permanent/never-recycled; marker IDs are ephemeral
    working handles. This fits the "always scan, never persist a roll-up" philosophy.
  - **In-line PROPOSED form:** `[GAP g2: <the gap found> — PROPOSED: <suggested
    solution>]`, worded so it reads "here's the gap, and here's what I'd do about it."
    Keeps the proposal machine-attached to the marker (satisfies `rpsl`).
  - **Scan/resolve (refine):** regex over `\[(ASSUMPTION|GAP|CONCERN) ([agc]\d+):`,
    document order; resolve first → replace span in place → strip marker, no breadcrumb
    → re-scan → repeat. AI never strips a marker without explicit user resolution
    (`rnau`). Unresolved markers survive and carry forward.
  - **Writers:** `discovery` (auto-draft), `prototype` (`[GAP]` on too-complex page).
    **Resolvers:** `refine` (scan loop), `validate` (in its reconciliation walk).
  - **REVISED (validate + markers):** validate's self-critique does NOT write markers
    into `plan.md`. Instead it writes its findings as a **validation FILE into
    `docs/validation/`**, uniform with external validation sources. Markers only
    (re)enter `plan.md` as the carry-forward mechanism for items the reconciliation
    interview can't settle — same role they play as refine's leftovers.

## Validate flow (settled)

Three beats:
1. **Offer self-validation (ask-first, not auto).** Softens PRD `vslf` ("on start,
   runs...") to an offer: validate asks whether to run Claude's adversarial self-critique
   now. Yes → Claude runs it and writes findings as a validation file in
   `docs/validation/` (same path as any external source). No → skip. Rationale: the
   maintainer may have already gathered enough external sources and not want another
   Claude pass — but must still be able to run validate purely to pull all sources back
   into the system. **PRD `vslf` needs a one-line wording tweak** (offer, not auto-run).
2. **Gather.** Scan `docs/validation/` — Claude's self-validation file (if it ran) plus
   all external files. Any file counts, no naming convention. Handles both full modified
   copies (diff vs `plan.md`) and suggestion-list files.
3. **Reconciliation interview.** Compare all sources against `plan.md`, walk differences
   and carried-over markers one at a time, keep/remove/change. Output: `plan.md` updated
   **in place** — the internal version marker bumps (see versioning). Unresolved items
   left as markers, carry to `prototype`.

## Document versioning (settled — internal, single file)

`plan.md` is one file throughout; "versions" are an **internal status marker**, not new
files or renames:
- `discovery` auto-draft → **draft**.
- `refine` finalize (`rfin`) drops draft status → **v1** (final).
- each `validate` run → increments (**v2**, v3, …), noted inside the document.
No `plan-draft.md` / `plan-v2.md` siblings ever exist.

### Numbering scheme (settled)

Milestone-based, monotonic. A single visible line at the top of `plan.md`:
`**Version:** 0.1`. Numbers only, no words.
- **Major = document maturity:** `0` = draft, `1` = finalized, `2` = validated.
- **Minor = revision counter** within the current maturity; bumped on every command run
  that writes to `plan.md`; resets to `0` when major bumps.
- Walk: discovery → `0.1` (re-draft `0.2`…); refine finalize → `1.0` (re-run `1.1`…);
  prototype `[GAP]` pre-validate → `1.1`; validate → `2.0` (re-run `2.1`…); prototype
  `[GAP]` post-validate → `2.1`.
- **Why milestone over command-as-major:** stays monotonic on out-of-order re-runs;
  handles optional validate cleanly (doc never reaching `2.x` = never validated — no
  confusing major gap); prototype `[GAP]` is a minor bump, never a new major (annotating
  ≠ maturing).
- **Version number ABSORBS the draft/final flag.** Major `0` = draft, major `≥1` = final.
  One line, one source of truth for maturity — no separate draft marker.
- **PRD reinterpretation (`dnam`/`rfin`):** draft-vs-final status is the major digit of
  the version, not a distinct marker. `rfin`'s "drop the draft-status marker" = the
  `0.x → 1.0` bump. Flag `dnam`/`rfin` for wording alignment (same class of tweak as
  `vslf`).

### plan.md template (settled)

Single doc, intent → requirements → architecture. Sections: `**Version:**` line →
`## Overview` (problem, audience, north-star) → `## Goals & Success` → `## Requirements`
(epic → story → ACs with stable 4-char IDs; `/sdd:build` lifts issues + ACs from here,
doc owns the ACs) → `## Architecture` (project-shaped: stack table w/ doc URLs, structure,
data flow, component `##`/`###` sections + inline data shapes/APIs; markers live inline)
→ `## Key Decisions` (decision | rationale | tradeoff) → `## Non-Goals` (+ conditional
`docs/backlog.md` pointer). Requirements stay epic-grouped (clean issue-lift contract).
**No roll-up marker list** — markers inline only, enumeration always by scan (`dasm`/`dgap`).
General enough for any v6 project, not just this plugin.

### refine naming collision (RESOLVED)

Old mid-chain `/sdd:refine` (living-documents refinement, loop-to-plan) is **fully
replaced**. Its `SKILL.md` is rewritten from scratch for draft-review + finalize (steps
4–5); no old behavior carries over. Name reused, behavior entirely new. Clean break, no
aliasing/migration — nobody depends on the old one.

### project-state.json schema v6 (settled)

Bump `version` → 2. Keep top-level `cycleNumber`, `smallProject` (right-sizing survives),
`lastCommand`, `notes`. **Drop** `currentSprint` and `buildMode` (vestigial — no sprint
docs, build always autonomous). **Add** `settings` object (persisted *user* choices) with
`prototypeFidelity: "hifi"|"lofi"|null` (`pfps`/`pfsw`). **Rekey** `commandExplanationsShown`
to the v6 set: discovery, refine, validate, prototype, build, retro, checkpoint, onboard,
pause, unpause, feedback, archive (drop scope/prd/spec/plan/polish).
- **`smallProject` stays top-level** (decided after pros/cons): top-level = project
  identity/derived state; `settings` = remembered user choices. `smallProject` is an
  AI-derived, auto-re-evaluated heuristic (machine-owned, volatile) — semantically wrong
  to bucket with a sticky user preference like fidelity. Line is "who owns the value."
- `plan.md` version is NOT duplicated in project-state (lives in the doc's top line only —
  no drift).

### /sdd:archive v6 adaptation (settled)

Mechanical `farc` changes:
- Sweep allow-list: trio (`scope/prd/spec.md`) → **`plan.md`**; **add `docs/validation/`**
  (whole folder); **drop `sprint-*.md`** (dead) and **drop `discovery.md`** (no separate
  discovery doc in v6); keep `retro.md`, `open-concerns.md`, `*-resume.md`, root
  `process-notes-*.md`.
- Handoff repoint: `/clear` + `/sdd:scope` → **`/clear` + `/sdd:discovery`**.
- Reset map: drop `currentSprint` and `buildMode` rows; add `settings` row; rekey
  `commandExplanationsShown` to the v6 set (drop the sprint/iterate/reflect legacy-key
  cleanup — those keys won't exist in v6).

Three decided details:
1. **Prototype output → `prototype/` at repo ROOT** (own directory, outside `docs/`).
   Holds the HTML/CSS/JS + the navigation-paths companion file. Outside `docs/` so
   archive's `docs/`-based sweep never touches it — no special exclusion logic. Walled
   off from any real build (sacred constraint #3). Durable north-star reference; not
   swept, not deleted.
2. **`settings.prototypeFidelity` on reset → CARRY** (stable user pref, like old
   `buildMode`). Not re-asked each cycle; user switches by saying so.
3. **`docs/learnings/` → NEVER SWEPT.** Joins the carry-forward set (with
   `backlog.md`, `sdd-feedback.md`, `project-state.json`). Maintainer's call: a learning
   is useful regardless of cycle, so learnings stay live permanently across all cycles.
   Retro promotes keepers out during the cycle; the raw store persists.

### /sdd:checkpoint (settled)

Anytime command, no precondition (like `feedback`/`pause`). Reads the session, emits a
tailored `/compact <instructions>` string **to chat** naming what to preserve. Writes no
file. Never runs `/compact` itself. One-directional (no pull-in mode). Distinct from
`/sdd:pause` (which writes a durable resume file).

### Shared-behavior cascade (settled)

- **`sdd-guide/SKILL.md`:** command chain → `discovery → refine → validate → prototype →
  build → retro` (validate optional); update interview-command set + end-of-command
  handoff next-command map; **remove the "Between-Rounds Context Recommendation" section**.
- **`references/sprint-tags.md`:** RETIRED (deleted). `references/markers.md` replaces it.
- **`references/context-management.md`:** RETIRED (deleted outright, not rewritten). The
  whole file is the between-rounds three-tier auto-suggest model; v6 drops auto-suggest,
  so nothing meaningful remains. The one-liner ("context is user-driven; use
  `/sdd:checkpoint`") lives in `checkpoint`'s SKILL.md. **Two reference files retire.**
- **`references/right-sizing.md`:** KEPT AS-IS (full mechanism — option 1, not
  simplified/cut). Only mechanical remap: **`discovery` inherits scope's role as the
  `smallProject` writer**; re-evaluators become the surviving v6 interview commands
  (`refine`, `validate`); per-command skippable-beat lists remap to v6 commands. Mechanism
  whole, command names track the new chain.
- **`references/living-documents.md`:** schema → v2 (settings object; drop
  `currentSprint`/`buildMode`; rekey `commandExplanationsShown`; updated archive reset map
  incl. `settings` carry, `smallProject` reset-to-null, `docs/learnings/` as carry-forward
  never-swept).
- **`references/deepening-rounds.md`:** KEPT (discovery pre-draft, refine, validate); trim
  its coupling to the deleted context recommendation.
- **Templates:** drop scope/prd/spec/sprint/discovery templates; add `plan-template.md`;
  keep/update retro template.
- **Removed command dirs:** `scope`, `prd`, `spec`, `plan`, `polish` (`frem`). Kept:
  `onboard`, `pause`, `unpause`, `feedback`, `archive`.
- **Manifest:** bump `plugin.json` + `marketplace.json` versions together.

### /sdd:resolve-pr (settled)

Separate command (name `/sdd:resolve-pr` — no "feedback" suffix), NOT a step inside
`/sdd:build`. Rationale: the PR review gate is asynchronous / often a separate session,
so responding to review is a fresh human-initiated action; keeps `/sdd:build` a clean
issues→PRs engine. Two-part job:
1. **Branch hygiene** — find PRs it created that are now closed/merged, delete their
   (local + remote) branches.
2. **Feedback resolution** — find its still-open PRs carrying review comments, work each
   thread (fix → reply → resolve → push), bounded rounds.
**Vendored SDD command** (`skills/resolve-pr/SKILL.md`) by necessity — the branch-cleanup
job is SDD-specific (tracks PRs *it* created), beyond the personal `resolve-pr-feedback`
skill; reuse that skill's thread-resolution logic inside it. No `references/` file (single
consumer) — logic lives in the command SKILL.
**Identification convention:** `/sdd:build`'s build-loop stamps its PRs/branches with an
`sdd` label + branch prefix (`sdd/<issue>-<slug>`); `resolve-pr` filters on it.
**Backlog:** GitHub-native automation for this hygiene deferred ("ideal, not there yet").

### Nothing is force-decided (settled, universal)

Markers can persist indefinitely and carry forward through every command including
`prototype`. No command forces a difference/marker to a decision. Validate's
reconciliation drops a carry-forward marker into `plan.md` for anything it can't settle,
same role as refine's leftovers.

## Document generation

- **Interview shape.** Effectively one long, high-engagement Phase 1 driven by the
  maintainer's "resolve everything, defer nothing" directive. No fixed deepening rounds
  run — the maintainer directed generation once all ten open architectural items plus
  file-structure and data-flow were closed. Closure rationale: every surfaced question
  was settled in-session; remaining items are mechanical PRD-wording tweaks and two
  explicitly-deferred *features* (both in backlog with design guidance), not open
  questions. No further round would surface load-bearing design.
- **Ten open items all closed:** filename (`plan.md`), build-loop vendor, marker
  machinery (`markers.md`), `plan.md` template, schema v2, validate design + external-AI
  automation (closed/backlogged), checkpoint, archive adaptation, shared-behavior
  cascade, refine naming collision. Plus versioning scheme, `resolve-pr` command,
  prototype location, learnings-never-swept.
- **spec.md generated** — command-rewrite design doc. Component section per epic with
  granular `###` subsections + AC-ID traceability; two file trees (plugin source +
  project docs); three data-flow diagrams; Key Technical Decisions table (~18 rows);
  Testing = validate + dogfood + verification trail; Open Issues = none (design), only
  PRD-wording tweaks + tracked deferred features. Out-of-Scope backlog pointer emitted
  (backlog non-empty).
- **Two reference files RETIRE** (not one): `sprint-tags.md` and `context-management.md`.
  Two NEW: `markers.md`, `build-loop.md`.
- **CLAUDE.md + AGENTS.md** merged surgically — v6 added as current cycle, v5 demoted to
  prior; structural sections left describing the *live* (v5) repo with an explicit
  "planning in progress, not yet built" status flag. Did not regenerate to the v6 target
  structure (would misdescribe the live repo).
- **Downstream PRD-wording tweaks flagged (mechanical, for `/sdd:plan`/build):** `vslf`
  (self-critique offered, not auto-run); `dnam`/`rfin` (draft/final status = version
  major digit, not a separate marker). Alignment edits, not open questions.
- **No mid-spec doubts unresolved.** The one pushback of note was the maintainer's early
  correction that `smallProject` stays top-level (not in `settings`) and that
  `docs/learnings/` must never be swept — both adopted.

### Automated third-party validation → BACKLOG (explicit maintainer defer)

Maintainer explicitly deferred the *automation* of external-AI validation runs
(ChatGPT/Gemini round-trip) to a follow-on run — wants v6 finished on the manual +
self-validation-file path. Backlog entry written with full design guidance (inside
`/sdd:validate`; per-source front-of-command yes/no asks; one agent per yes; each agent
writes a file into `docs/validation/`; extensible source list). Open concern closed:
architecture is source-agnostic so automation is a pure external producer, no structural
change needed. This is a scoped feature-defer, not a punted design question — consistent
with the no-loose-ends directive.
