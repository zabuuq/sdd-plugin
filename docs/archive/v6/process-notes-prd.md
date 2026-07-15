# Process Notes — /sdd:prd (v6)

## Deepening Round 1

- **Epic structure approved (Q1).** Eight epics: (1) Discovery [steps 1–3],
  (2) Refine [4–5], (3) Prototype [6–7], (4) Build [8], (5) Retro [9],
  (6) Checkpoint [anytime], (7) Lesson Capture [cross-cutting], (8) Foundations &
  Migration [shared-behavior cascade, project-state schema growth, onboard GitHub
  check, archive adaptation, old-command removal, template changes]. Sprint mapping
  noted: Sprint 1 = 1,2,3,6,7(capture side) + foundational parts of 8; Sprint 2 =
  4,5, archive (from 8), retro consume-side of 7. Sprint mapping is /sdd:plan's job;
  PRD just fixes the epic addresses.
- **Prototype fidelity persistence (Q2).** Ask once (first prototype run), persist
  to `project-state.json`, remember on later runs — no re-prompt each run. User
  switches fidelity by explicitly saying so; the command does not re-surface the
  stored choice every run.
- **Ingest gate (Q3).** Hard gate: AI lists every source it found (docs/refs/,
  pasted material, in-flow-transcribed audio) and WAITS for user response before
  drafting. No-input path (empty docs/refs/, nothing pasted): auto-skip the
  inventory, go straight to full brainstorm discovery as a first-class path.
- **STRUCTURAL CHANGE (Q4): validation split into its own command (`/sdd:validate`).**
  User caught a mistake in the scope. Validation is no longer the back half of
  `refine`. New shape:
  - `/sdd:refine` = step 4 (draft review — walk markers, resolve in place; some may
    remain unresolved) + step 5 (create/finalize the document from the reviewed draft).
  - `/sdd:validate` = NEW step-6 command: (1) run its own self-validation (adversarial
    self-critique), (2) look for external validation files from other AI validation
    runs, (3) walk-through interview of the DIFFERENCES between the validations and the
    document — user decides per difference: keep / remove / change.
  - Command count 5 → 6: discovery, refine, validate, prototype, build, retro (+
    checkpoint + support commands). Chain: onboard → discovery → refine → validate →
    prototype → build → retro.
  - Connects the parked open concern: external ChatGPT/Gemini round-trip produces
    **validation files** that `/sdd:validate` ingests — the concrete integration point.
  - Epic list grows 8 → 9: redefine "Refine" (review + finalize, no validation), add
    "Validate" as a ninth epic.
  - scope.md is now out of sync (still says 5 commands, validation inside refine).
    Flag to user; scope may need an update pass. PRD reflects the corrected structure.
  - **User authorized the scope update; DONE.** Rewrote docs/scope.md: ten-step /
    six-command framing, renumbered steps, new `/sdd:validate` row + implementation
    note, `docs/validation/` folder, single-file-final-name-with-internal-draft-marker,
    prototype navigation-paths + companion file + ask-once fidelity, build vs prototype
    failure divergence, no-mid-build-decisions, checkpoint description, dropped the
    "push out, pull in" phrasing, schema/archive/cascade deltas. Open concern re-pointed
    at `/sdd:validate`.
- **Validate details (settled, Q4 revised).**
  - **OPTIONAL command** — not required in the chain; run for extra assurance, else skip.
  - Refine step 5 "finalize" = update the draft file in place; file carries its final
    name from the start (never named "draft"); draft status is an internal marker that
    finalize drops. One file, updated in place throughout.
  - Validate: (1) auto self-critique at start; (2) scan `docs/validation/` — any file
    there is a validation file, no naming convention; (3) walk the differences one at a
    time, keep/remove/change. Sources of validation files are open (person, other AI,
    Claude, or automated agent round-trip). Depth varies (light "looks good" → deep
    feasibility / tooling-need). Some files are a full modified copy of the document
    (diff against the source of truth to extract changes); others are just suggestions —
    validate handles both.
- Epic count now **9** (added Validate).
- **Feasibility/viability pushback (Q4 half).** Standing cross-cutting behavior —
  any command from discovery onward can raise it. Not gated to validate. In scope.
- **GitHub gate (Q5).** HARD BLOCK at `/sdd:onboard`. No authenticated `gh` + repo →
  no v6 at all; even the local front half (discovery/refine/validate/prototype) won't
  start. Not a deferred-to-build check, not a warning. onboard verifies and stops.

## Document generation

- **1 deepening round run** (5 questions). Surfaced the biggest item of the session:
  the validation split into `/sdd:validate` (a scope-level correction the maintainer
  caught mid-interview), plus the epic structure, prototype fidelity/ingest mechanics,
  and the GitHub hard-gate.
- **Phase-split check:** 9 epics, below the 10-epic threshold — no phase-split
  recommendation fired. The two-sprint split stays as one PRD (maintainer's intent).
- **Pre-write AC quality pass:** all 69 ACs drafted to be specific / observable /
  verifiable by inspection or dogfooding; no failing criteria carried into the file.
- **PRD generated:** `docs/prd.md` — 9 epics, 19 stories, 69 ACs. Unvetted section
  seeded empty with marker. "What We'd Add With More Time" pointer emitted (backlog
  non-empty). Non-Goals pulled from scope's cuts + PRD conversation.
- **Notable divergence from scope-as-written, now reconciled in scope.md:** validation
  moved out of `refine` into its own optional `/sdd:validate` command; step count 9→10,
  command count 5→6; `docs/validation/` folder added; single-file-final-name rule;
  build vs prototype failure divergence; no-mid-build-decisions; checkpoint one-way.
- **Open concern carried forward:** free-tier ChatGPT/Gemini automation for
  `/sdd:validate` external files — deferred to `/sdd:spec` (validate design) or
  `/sdd:build` (automation feasibility). Manual file-drop is the load-bearing path.

## Phase 1 — Section-by-section conversion

- **Single document is the source of truth (settled).** Q1 asked where acceptance
  criteria originate — in the document or at GitHub-issue-creation time. User
  corrected the framing: the single planning document *is* the source of truth. It
  starts as a draft and `refine` builds it into a solid document holding all scope,
  all spec detail, and all testable acceptance criteria. Issue creation lifts ACs
  that already exist in the document; it does not author them. Requirements will be
  written so the document owns the ACs.
- **Build reconciled (settled).** `/sdd:build` stays. Not going away — changing.
  It becomes more autonomous, and the load-bearing rule is **no human decisions
  mid-build**. All decisions are front-loaded into the source-of-truth document and
  the issues; the build-loop executes without stopping to ask. Human touch is the PR
  review gate only. Requirement: document + issues must be complete enough that
  agents don't need in-flight decisions. ("The old build was going away" was loose
  phrasing for this change.)
- **Two interviews, two terminations (settled).** Q3 conflated them.
  - *Pre-draft interview* (discovery, step 2): standard interview mechanics —
    ~5 questions, max 10, then deepening-round recommendation. Standard termination.
    Only difference from today: data-first. It answers each question from provided
    material first and asks user to confirm / decline / add, rather than making the
    user re-state what's already in the material.
  - *Post-draft review* (refine, step 4): auto-draft emits the document WITH an
    explicit list of assumptions and gaps. Review walks every item on that list;
    done when the list is worked through. This is the "gap/assumption list
    exhausted" criterion — it lives in refine, not discovery.
- **Inline assumption/gap markers (settled, Q4–Q6).**
  - Auto-draft tags each assumption and gap inline, in place, with a
    machine-findable marker carrying a short ID (e.g. `[ASSUMPTION a3: ...]`,
    `[GAP g2: ...]`). No persisted roll-up list — a separate list is a sync-drift
    trap and a context-loss vector. The enumeration is generated on demand by
    scanning for markers (always current, never stale). The ID is co-located with
    the content, so it doubles as the anchor for in-place edits.
  - Refine resolution loop: scan for first marker → resolve with user → replace in
    place → strip marker (no breadcrumb) → re-scan → repeat until none remain. New
    markers surfaced mid-loop are tagged inline and caught on the next re-scan.
  - **Can't-resolve-now exit:** leave the marker tagged. It survives into the
    document, carried to `prototype` and raised during prototype review. The AI may
    append a proposed solution next to the marker, but MUST keep the marker and
    write the proposed solution into the document alongside it.
  - **Hard constraint:** the AI never removes a marker autonomously. Markers are
    removed only by explicit user resolution. No silent closures. This is the
    testable teeth of sacred constraint #2 (never lose accumulated context).
- **Lesson capture (settled, Q7).** Automatic and unconditional — AI detects
  lesson-worthy events while running (the build-loop agent/skill mechanism) and
  writes to `docs/learnings/` without prompting. Surfaces only at `retro` for
  per-lesson promotion (plugin → `/sdd:feedback`, global → `~/.claude`). The user's
  ad-hoc "record this globally now" move is a separate manual path, routed by the
  user, and explicitly NOT implemented or watched for by the plugin skills.
  Span assumption (stated, awaiting contradiction): auto-capture runs during doc
  creation (discovery/refine) and item build (build/prototype), not build alone.
- **Happy path(s) (settled, Q8).** The AI authors the navigation path(s) as it
  builds the prototype, derived from the source-of-truth document and nothing more,
  and must surface what it created. Stored as a **separate companion file** paired
  with the prototype, NOT inside the source-of-truth document. Not limited to one —
  as many paths as needed to properly navigate the prototype. "Happy path" generalizes
  to "the set of navigation paths (one or more)." Prototype review (step 7) walks
  pages along these paths, each checked against the document.
- **Prototype retry escape (settled, Q9).** When a page fails its separate-checker
  after the bounded retry (≤2), the AI writes a `[GAP: ...]` marker into the
  source-of-truth document (too-complex-to-prototype-cleanly) using the existing
  marker machinery, then **continues** building the rest of the prototype — it does
  NOT halt. The gap resurfaces during prototype testing/review (step 7). Distinction
  to write explicitly: **build** (durable, step 8) stops-and-surfaces on bounded-retry
  failure; **prototype** (disposable, step 6) marks-the-gap-and-continues.
- **Checkpoint (settled, Q10–Q11).** Anytime command for when the session feels
  heavy and the user wants to compact (not full clear). Reads the session and emits
  a tailored instruction string for Claude Code's `/compact <instructions>` form
  (what to preserve); user then runs `/compact`. Never runs compact itself.
  One-directional — the "push out, pull in" phrasing in scope was AI embellishment,
  NOT user intent; there is no pull-in mode to build. Distinct from `/sdd:pause`
  (which writes a durable resume file for a full clear). Output: **printed to chat**,
  no file written.
