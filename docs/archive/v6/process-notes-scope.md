# Process Notes — Scope (v6)

## Startup
- Ran `/sdd:scope` for the v6 cycle. No `docs/seed.md`, but `docs/discovery.md` is rich and explicitly carried forward for scope. Used it as primary starting context instead of re-interviewing.
- Phase 1 mandatory beats (idea, audience, inspiration, goals, done-looks-like, solo/team) were all answered by discovery. Presented a synthesis for confirmation rather than re-asking; user confirmed ("looks good").

## smallProject judgment
- Wrote `smallProject: false`. Only one of six small-project signals present (solo/no external users). Contradicted by: multi-part workflow replacement, core GitHub-issues integration, rich reference uploads (transcripts + slides), long multi-paragraph framing, prototype shown to clients/investors. Clearly not small.

## Size assessment
- Raised the size fork: one cycle (sequence with sprints) vs split the back half into a v7. User first said "split," then corrected: keep it as **one scope with the full two-part vision, sequenced as two sprints** — not two cycles. No project split, no backlog deferral. Resolved to my original recommendation.
- Sprint seam recorded for `/sdd:plan`: sprint 1 = front half (brain-dump → tri-doc draft → draft-based interview → research-validate → prototype → refine, plus lessons-**capture**); sprint 2 = back half (GitHub-issues-as-backlog, sprint-pull loop, streamlined retro that **consumes** captured lessons). Capture-before-consume ordering keeps the "don't lose context" sacred part intact.

## Deepening Round 1

### Topic 1 — Lessons-as-they-happen mechanism
- Capture during a project is **always project-local**. Nothing globalizes mid-project.
- The **retro is the single promotion gate**: it lists lessons learned in the project and asks, per lesson, whether to make it global.
- Destination routing is also decided at retro: AI suggests whether a promoted lesson is **plugin-level** (→ `/sdd:feedback`) or **global-to-Claude** (→ `~/.claude/` memory); user confirms.

### Topic 2 — Research / validate (step 4)
- Reframed by user: step 4 is the conference flow's **independent-AI-research + consolidate** beats — an **optional external multi-AI refinement pass** on the three already-drafted docs (checks inconsistencies + suggested additions). External AIs flag; Claude makes the doc edits, reconciling flags against what the interview settled.
- Mechanics: **default = Claude's own adversarial self-critique** (load-bearing, no external dep). The step is a **stop-point** — Claude reports where the docs are and offers a manual round-trip to other models. **Orchestrated** handoff (MCP/API to free ChatGPT/Gemini) is best-effort, capability-dependent; Grok/paid models out (no subscription). Logged as open concern.
- **Feasibility/viability pushback is cross-cutting, not confined to step 4**: Claude has standing license to flag "market may not justify this" / "high-effort — still want it, or a lower-effort version?" any time after brain-dump, especially during initial drafting and right after the refinement interview.

### Topic 3 — One-pass tri-doc draft + draft-based interview
- **Three-vs-one docs is deliberately open** for spec to decide. User doesn't care whether it's three files (scope/PRD/spec) or one merged doc — the invariant is that intent, requirements, and architecture all get captured coherently. Removes the "which doc does this sentence go in" burden.
- **Draft posture:** AI drafts what it knows; each gap gets a stated assumption **if** the provided data supports a valid one, else a well-formed open question.
- **First-class scope constraint (guards the "don't lose context" sacred part):** every gap must be addressed, and **every assumption must be surfaced back to the user** — labeled "this is an assumption I made" and raised in the interview. The AI may never make an assumption and bury it.

### Topic 4 — Prototype
- **AI always asks** hi-fi (clickable) vs lo-fi (screenshots) before building. No silent default.
- **Sacred constraint (confirmed):** prototype code is walled off from the real build. It's a durable, client/investor-facing north-star reference that grows on feedback; the real product starts fresh and never builds on prototype code.
- **Fidelity choice persists as a project setting** (alongside `smallProject`, `buildMode` in project state) so it survives `/clear` and later prototype passes — another instance of the don't-lose-context posture. General pattern to record: v6 uses a small set of persisted per-project settings as context-loss insurance; spec should give them a home rather than scattering them.

### Topic 5 — Backlog vs GitHub issues (user reversed discovery's "no backlog file")
- **`docs/backlog.md` stays** as the **unrefined** holding area. Kept local precisely because unrefined items must not be actionable by external agents (Copilot/Codex) — an agent shouldn't pick a raw idea and build the wrong thing.
- **Refinement is the promotion gate:** backlog item → (refine) → **GitHub issue** = the ready/actionable queue.
- **The real change is store-of-ready-work, not backlog-vs-issues:** refined work lives as **GitHub issues instead of a large local checklist file**. Why: richer data presented cleanly + less context per session (agent opens one issue vs loading a whole checklist).
- **Issue contents:** title, solid description, acceptance criteria, link back to relevant doc, labels. Issue need not inline all context — the **repo carries it**: AGENTS file (project rules) + requirements/single planning doc live in the repo and are referenced, so any agent has access.
- Pipeline: brain-dump → backlog (unrefined) → refine → GitHub issue (ready) → agent works it from the repo.

### End of Round 1 — decisions
- Recommended another round (5 topics still open: brain-dump ingestion, command surface after the rewrite, draft-interview cadence, one-day success metric, sprint-loop + retro). User agreed.
- Recommended `/compact` between rounds; user asked for a tailored compaction prompt and will run it before round 2.
- **User instruction for end of Round 2:** list the NEW STEPS OUTLINE and ask whether he wants to refine further — he may have 1–2 more changes before doc generation. Honor this at round 2 close.

## Deepening Round 2

### Topic 1 — Brain-dump ingestion
- **Audio is in-scope, not rejected.** Checked installed tooling (`~/.claude/AI-TOOLING.local.md`, JASON-DESKTOP): `faster-whisper 1.2.1` + `FFmpeg 8.1` installed, plus a global `transcribe` skill and reusable driver at `C:\Users\jcmcc\OneDrive\Coding\cbus-pa\transcribe.py`. So step 1 **transcribes audio itself** (local, free, private — offers to run the transcribe skill / faster-whisper) rather than requiring the user to transcribe first. Text + images ingest directly. Supersedes the Round-1-era "transcripts only, no audio" line.
- **Inventory-first, confirmed.** Step 1 inventories all provided sources before drafting ("I see 3 transcripts, 1 doc, 2 images — here's what each covers") and confirms the set with the user. User wants this explicitly: it surfaces sources he forgot he added AND lets him notice something he *meant* to add but didn't ("remind me I need to add something"). Worth the extra beat.
- **Interview always runs, but data-first (cross-cutting refinement, bigger than Topic 1).** The draft-based interview (step 3) is never skipped — brainstorming with Claude is itself valued. But it changes shape based on inputs:
  - **With data:** Claude scans everything first, then runs the *normal* interview beats — but for each question it pre-fills from the provided data and presents it as "here's what I found in your material for X — good, or anything to add?" User confirms ("nope, that's good") and moves on, or extends. Speeds the interview without skipping it.
  - **Without data (pure brainstorm):** occasionally the user shows up with nothing provided. Same interview runs from scratch as a normal brainstorming session. This is a first-class supported path, not a degraded one.
  - Invariant: the interview is always present; data only changes whether each beat starts pre-filled-for-confirmation or open.

### Topic 2 — Command surface (DEFERRED to end of round)
- User's lean: **#2, a small set of commands** (not one mega-command, not one-per-step). But he wants to **defer the actual command↔step mapping to the end of the round**, deciding it against the finalized workflow outline rather than in the abstract. Honor at round close: when listing the NEW STEPS OUTLINE, also propose which command owns which step(s) and settle it then.

### Topic 3 — Interview cadence (hybrid)
- Draft-based interview (step 3) **reuses the deepening-rounds *mechanics*** — one question at a time, free-form, active prompt at end of each beat, the three-tier context management (continue / `/compact` / `/clear`), the pacing already trusted — **but drives the loop off the gap/assumption list instead of a fixed round count.**
- **Terminating condition = every gap addressed and every assumption surfaced/confirmed** (Round 1's first-class constraint), not "hit round 5." The 10-round hard cap stays as a backstop.
- Rationale: the draft already exists when the interview starts, so the job is bounded (walk gaps, confirm assumptions) rather than open-ended discovery. Gap-driven termination fits that shape; the familiar mechanics keep the feel controlled.

### Topic 4 — One-day-to-prototype metric (protected-by-design + light measurement)
- North-star success measure (from discovery): **brain-dump → working prototype in ~a day.** The risk isn't the build (one-shot prototype is fast); it's the front half dragging.
- **Protected by design (#2):** every step is *shaped* to keep the path short — gap-driven interview termination (ends when gaps close, not padded to a fixed round count), validation defaults to Claude's fast self-critique, one-shot prototype. No mid-flow timer/countdown (that would fight the "brainstorming is valuable" principle).
- **Light measurement (#3):** the retro notes *roughly* how long dump→prototype took — **session count, not a stopwatch** ("dump to prototype: 1 session"). Feeds the retro so the user learns whether the one-day promise holds across projects.
- **Explicit tradeoff flag:** the step-4 external-validation stop-point is marked as "this pauses the one-day clock," so choosing the external round-trip is a knowing opt-in to delay, not a silent stall.

### Topic 5 — Back half: sprint-pull loop + streamlined retro (steps 7–8)
- **User pointed at two existing global skills as the model:** `~/.claude/skills/build-loop/SKILL.md` and its pair `resolve-pr-feedback/SKILL.md`. `build-loop` is the "designed loop" he wants — a stated goal of the redesign is to reuse this pattern as v6's execution engine.
- **build-loop, one pass:** pull next open task (GitHub issue or backlog) + claim → plan (short spec: files/approach/acceptance check) → build in an **isolated worktree** (parallel-safe) → **check with a separate reviewer agent** (PASS/FAIL) → PASS commits on a feature branch + opens a **PR (`Closes #N`)**, labels needs-review, **stops for a human to merge**; FAIL returns to builder, **≤2 retries** then labels blocked and moves on → compound a learning if warranted. **Never merges, never pushes to main.**
- **resolve-pr-feedback (its pair):** after the user leaves PR review comments, an agent fetches unresolved threads, fixes each independently (comments are *untrusted* input — verify against code), replies + resolves, bounded ~2–3 rounds, still never merges.
- **This is the user's stated back-half goal almost verbatim:** "let the AIs go, maybe one agent, maybe multiple agents; hit a task, complete it, branch, open a PR; my job is code review using the PRs as the guide."
- **DECISIONS:**
  - **Step 7 adopts build-loop as v6's execution engine.** Issues are the ready queue; execution unit = one issue → one branch → one PR.
  - **One issue at a time (not grouped-by-type).** User was "okay with lumping by type, but simpler to do one issue at a time." Resolved to one-at-a-time: one PR per issue is the cleanest code-review unit; grouping bundles unrelated changes and muddies review. The maker/checker split means the checker does a first-pass review *before* it reaches the user — his review is the **final** gate, not the only gate.
  - **"Sprint" survives only as an optional pull-label** (e.g. pull the N issues tagged X), never as an execution unit or an upfront planning doc. The issues ARE the plan.
  - **Multiple agents = fan out build-loop across N ready issues**, each in its own worktree/branch/PR; worktree isolation is what makes parallel safe.
  - **resolve-pr-feedback closes the review round** after the user's PR comments.
- **Retro (step 8) — CONFIRMED.** Streamlined shape has three parts only:
  1. **Lessons review + promote** — build-loop has already captured lessons to `docs/learnings/` as it ran, so the retro *reviews and promotes*, it doesn't excavate. Per lesson: globalize prompt + routing suggestion (plugin-level → `/sdd:feedback` vs global-to-Claude → `~/.claude` memory). Each promotion is a labeled choice the user confirms; nothing globalizes without a yes. The retro **actively recommends against** promoting a lesson that merely restates something already in global config (offer keep-local or drop).
  2. **Timing note** — rough dump→prototype **session count** (Topic 4), plus optionally prototype→first-merged-PR.
  3. **Issue queue status** — closed / still-open (with refined-vs-unrefined) / blocked counts.
  - **Drops** the long-form structured-retro prose (what-went-well/poorly/action-items). The concrete lessons ARE that content; the promotion prompts are the only decisions.
  - Confirmed against a worked example (invoice-reminder-tool retro) the user reviewed.

### End of Round 2 — outline revision (user reworked the step outline)
- **Naming: the phase is "discovery," NOT "brainstorm."** Brainstorming is the *activity*; discovery is what's *happening*. Front-half command stays **`/sdd:discovery`** (v6 redefines it). Supersedes the earlier `/sdd:brainstorm` proposal.
- **Two interviews, before AND after the draft (confirmed):**
  - **Pre-draft interview** — discovery-item-driven, data-first. For each question: look in the provided documents first; if an answer exists, ask the question AND state what was found → user confirms / declines / adds; if no answer, just ask. Runs to completion before drafting.
  - **Post-draft draft-review** — a quick round specifically about **assumptions Claude made + closing remaining gaps** (not a second full interview).
- **SINGLE DOCUMENT — decided (closes a Round 1 open question).** Round 1 left "one merged doc vs three (scope/prd/spec)" open for spec. User has now decided: **one document.** v6 produces a single planning doc, not the scope→prd→spec trio. (Design shift for the plugin.)
- **User validates the document** (step 5) before the prototype is built. Optional external-AI refinement pass lives here.
- **Prototype refinement (step 7) is interview-style and prototype-specific** — walk the prototype, likely **page by page from the start** ("here's page one — what works, what's different?"), checking it against the discovery doc ("this mattered and I put it here — correct?"), incl. testing direction. User likes the interview style and wants it kept here.
- **RESOLVED — prototype build shape:** prototype stays **one command / one session**, generated **page-by-page internally** when large. **No GitHub issues, no branches, no PRs** — that machinery is reserved for the real build (durable, merge-worthy work). Rationale: the prototype is disposable/walled-off; routing throwaway code through worktrees+PR+review would burn the one-day budget and mean reviewing PRs for code that gets deleted. If a screen is too big even for one page-pass, that signals the *page* is too complex → feedback for the doc, not a reason to spin up issue tracking.
  - **Optional page→issue handoff:** step 7 may emit a page-to-issue mapping that step 8 picks up, so the prototype's structure can *seed* the real issues later — the "issues" benefit without building the prototype through the issue machinery.
- **Prototype-loop (step 6 — reuses build-loop's skeleton, drops its git machinery):** `/sdd:prototype` and `/sdd:build` run the **same loop skeleton** — plan → make → separate-checker → bounded-retry → compound — with different outputs (disposable prototype pages via internal passes vs durable code via PRs). One mental model, two commands. Per page:
  - **Plan first** — lay out the page set + happy-path sequence before generating (this IS the prototype's plan, mirroring build-loop's per-task short spec).
  - **Generate a page.**
  - **Check with a separate agent** — before a page reaches the user in the step-7 interview, a checker verifies it against the document and its spot on the happy path (rubric: "does this page satisfy the doc items mapped to it + sit correctly on the happy path?"). Same maker/checker split as build-loop → **user's review is the final gate, not the only gate**; catches prototype-drifted-from-doc before it burns review time.
  - **Bounded retries, then stop-and-surface** — if a page can't pass its check in ≤2 tries, stop and surface it (signals the page is too complex → feedback for the doc). build-loop's "≤2 retries then block/surface" guardrail, reused.
  - **Compound** — capture non-obvious lessons as you go, feeding the retro.
  - **Dropped from build-loop (tied to durable code):** worktree isolation, PR-per-task, never-merge, issue-queue pulling. Prototype code stays disposable/walled-off.
- **Prototype happy path (new, spans steps 6–7):** the prototype ships with a **defined happy path** — the primary end-to-end journey through the screens, in order. Purposes: (1) gives the step-7 page-by-page interview a spine — user knows what to click and in what sequence; (2) doubles as the **testing script** (ties to the "testing direction" beat); (3) doubles as the **demo script** for client/investor north-star showings (prototype is durable, outlives the build). Works for both fidelities — hi-fi: literal clickable sequence; lo-fi: ordered screen-by-screen walkthrough. Generating along the happy path pairs with page-by-page generation (build the path, then review the path).

**REVISED FLOW (9 steps):**
1. Ingest discovery items — inventory + confirm the set
2. Pre-draft interview — discovery-item-driven, data-first
3. Auto-draft → single document
4. Draft review — quick interview on assumptions + gaps
5. Validate — user validates the document (optional external-AI pass)
6. Prototype — build *(shape under discussion)*
7. Prototype refinement — interview-style, page-by-page, checked against the doc
8. Create issues + build-loop — autonomous execution, PR-per-issue, user code-reviews
9. Quick retro

### Command surface — RESOLVED (Topic 2 close)
User's lean was "a small set." Settled on **five commands**, mapped to the 9-step flow:

| Command | Steps | Owns |
|---|---|---|
| `/sdd:discovery` | 1–3 | Ingest → pre-draft interview → auto-draft. Ends with the **drafted** document. |
| `/sdd:refine` | 4–5 | Draft review (assumptions + gaps) → user validation → **final** document. |
| `/sdd:prototype` | 6–7 | Build along the happy path → page-by-page refinement interview. |
| `/sdd:build` | 8 | Build-loop over the GitHub-issue queue; PR-per-issue; user code-reviews. |
| `/sdd:retro` | 9 | Quick streamlined retro. |

- Discovery **ends at the auto-draft** (step 3), not at validation — user's explicit call. Review + validation are their own command.
- The old four-command front end (`discovery`→`scope`→`prd`→`spec`) collapses into **`/sdd:discovery` + `/sdd:refine`** (two commands producing one document).
- **FLAG for `/sdd:spec`:** `/sdd:refine` is an existing mid-chain command in the current plugin (not part of the front end being replaced). Reusing the name means repurposing it or renaming. Recorded user intent as `refine`; collision to be resolved at spec.

### Context-management posture — CHANGED (outlier discussion)
- **Drop the between-command context-management suggestions entirely** — no more nagging to `/clear` OR `/compact` between commands. Left fully to the user's discretion. User calls the current suggestions "extra fluff, not necessary."
- **Does NOT weaken the "never lose context" sacred part:** the persisted state (project settings, the single planning doc, process notes) is the safety net if the user chooses to clear. Dropping the suggestion just stops the tool from managing the user's session for them.
- **Design framing: push → pull.** Replace the tool *pushing* context management (automatic between-command nags) with a tool the user *pulls* on demand (the new checkpoint command below).

### New command — `/sdd:checkpoint` (anytime, outside the linear chain)
- Anytime, no preconditions (like `/sdd:archive` / `/sdd:pause`). Not part of the 5-command flow chain.
- **Behavior:** reads the session so far — decisions made, current step, what's pending, what the next round/command needs — and **writes a tailored `/compact` prompt** (what to preserve, what to drop, locked decisions, pending work). Hands the user the prompt; **does NOT run `/compact` itself** — the user controls when context actually collapses.
- Productizes exactly what was done by hand this session (the compact prompt written before Round 2).
- Name confirmed: **checkpoint** (rejected `/sdd:compact` — collides with the built-in `/compact` and this *writes a prompt for* it, isn't it).

### Command inventory — full fate (user confirmed all)
| Fate | Commands |
|---|---|
| **Going away (deleted)** | `scope`, `prd`, `spec`, `plan`, `polish` |
| **Same name, refactored** | `discovery` (steps 1–3), `refine` (steps 4–5), `build` (build-loop over issues), `retro` (streamlined) |
| **New** | `prototype` (steps 6–7), `checkpoint` (anytime) |
| **Kept (support / anytime)** | `onboard`, `pause`, `unpause`, `feedback`, `archive` |
- **`plan` dies** — issues ARE the plan; no sprint-planning doc.
- **`polish` dies** — build-loop + `resolve-pr-feedback` cover the build/fix/review cycle.
- **`refine` moves front-half** — old `refine` was build-half; v6 repurposes the name for doc review+validation (steps 4–5). Name collision flagged for `/sdd:spec`.

### `/sdd:archive` must adapt to the v6 file structure (recorded scope)
Current sweep allow-list: `scope.md`, `prd.md`, `spec.md`, `discovery.md`, `retro.md`, `open-concerns.md` (exact); `sprint-*.md`, `*-resume.md` (docs globs); `process-notes-*.md` (root glob). Under v6:
- **Planning trio → single document.** The three exact names collapse to the single planning doc's name (name TBD at `/sdd:spec`). Allow-list + the "planning trio" plan group change accordingly.
- **`sprint-*.md` is dead** — no sprint files (issues are the plan). Glob sweeps nothing; drop the "N sprint files" plan group.
- **New artifacts:** single planning doc, prototype, happy-path. **Prototype is disposable/walled-off — do NOT sweep prototype code into the versioned archive** (throwaway); at most keep the happy-path doc. (Lean, confirm at spec.)
- **Reset + handoff point at dead commands.** Reset map references `/sdd:plan` (sprint compute); handoff says "Run `/clear`, then `/sdd:scope`." Both `/clear` (dropped per context-posture change) and `/sdd:scope` (deleted) are gone → **handoff becomes `/sdd:discovery`, drop the `/clear` line.**
- **`commandExplanationsShown` keys** must match the new command set.
- **`currentSprint` reset logic** references `/sdd:plan`'s `currentSprint + 1`; sprint is now only an optional pull-label — revisit whether `currentSprint` stays in the schema.

### Four downstream flags (recorded; mostly resolved at spec/plan)
1. **Single-document name is a required decision at `/sdd:spec`.** Everything keys off it — archive allow-list, template, handoffs. `scope.md`/`prd.md`/`spec.md` → one file, name TBD. Archive can't be finalized without it.
2. **GitHub is a hard dependency (new constraint).** Build half needs `gh` authenticated + a repo (issues + PRs). Old flow never required GitHub. `/sdd:onboard` should verify it; v6 doesn't run headless without a GitHub remote. → belongs in scope's constraints.
3. **`project-state.json` schema grows.** v5 made no schema change; v6 does — gains persisted settings (prototype fidelity hi/lo at minimum) and its `commandExplanationsShown` keys change. Archive reset map + `living-documents.md` encode that schema.
4. **Cascade of shared-behavior edits (implementation scope for spec/plan, noted not decided):** `sdd-guide` command-chain description; references — `sprint-tags.md` becomes obsolete (no sprint files), `context-management.md` changes (no auto-suggest), `living-documents.md` (schema), `pause-resume.md`/`deepening-rounds.md`/`right-sizing.md` still apply; templates drop scope/prd/spec/sprint and gain a single-doc template (+ possible happy-path/prototype template).
- **Audio-ingestion dependency (soft):** faster-whisper / `transcribe` skill needed for `.m4a` ingestion; `/sdd:onboard` could check.

## Wrap-up
- `docs/scope.md` written from `scope-template.md`. All template sections emitted. "What's Explicitly Cut" pointer block **included** (backlog.md has ≥1 entry) — exact single line `Deferred items: see \`docs/backlog.md\`.`, no inline list.
- v6-specific design boundaries (Grok/paid AI out, prototype-as-issues rejected, no `/clear`-`/compact` nagging) recorded as decisions in Loose Implementation Notes, NOT in the cut section (they're design choices, not deferred backlog items).
- `docs/open-concerns.md` unchanged — one open concern (free-tier ChatGPT/Gemini automation) still stands; resolver `/sdd:spec` or `/sdd:build`.
- `docs/project-state.json`: `lastCommand`/`commandExplanationsShown.scope`/`smallProject` already written at startup; no further change.
- Handoff target: `/sdd:prd` (next in chain). `handoffWarningShown` already true → no first-handoff explanation paragraph.
