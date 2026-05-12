# Process Notes — SDD v2

v1 process notes archived to `docs/archive/v1/process-notes.md`.

## /sdd:scope

- **Cycle context:** v2 scoping driven by feedback collected in v1 across three projects (Event Planning App, fiver-gigs, sdd-plugin self-use). Feedback pile lives at `docs/sdd-feedback.md` and serves as the de facto seed for this scope phase. v1 planning artifacts archived to `docs/archive/v1/`.
- **Open concerns carried in:** Four open items from v1 retro/scope flagged at startup: clean-state definition for `/sdd:build` revert, post-sprint command ceremony heaviness, spec/runtime duplication, and soft acceptance criteria for MIT distribution. The post-sprint ceremony concern explicitly targets this command — it should get resolved or deferred during this v2 scoping pass.

### Beat 1 — The idea
- **Captured:** v2 is a streamlining pass of v1 driven by real-use feedback. Not a feature expansion; refinement of what already works based on friction surfaced across actual project usage.
- **User framing was tight and unprompted** — single sentence, no probing needed. Spine of v2 is clear from the start.

### Beat 2 — Who it's for
- **Captured:** Jason + a local tech group he is a member of and plans to actively hand the plugin to.
- **Audience shift from v1:** v1 was "Jason + MIT-licensed permissive sharing (others *could* adopt it)." v2 actively pursues an audience of peer developers.
- **Follow-up surfaced a critical bar-setting decision:** when asked whether the tech group would use it on real projects vs. kick-the-tires for feedback, user answered "probably both." Implication captured for downstream beats — v2 has to clear a working-software bar AND its feedback loop has to be real, since invited users are a feedback source.

### Session pause
- **Paused mid-Beat 3** when user needed to switch computers. Resume artifact written to `docs/scope-resume.md` using the schema proposed in feedback note `2026-04-24T00:05:00Z` (status / confirmed / pending / instructions-for-next-session). Convenient dogfooding of the v2 multi-session-resume feature request.
- **Setup work captured in resume doc** so the next-session agent does not redo archival, state reset, or scaffolding.

### Beat 3 — Inspiration & references (resumed 2026-05-01)
- **External recommendations surfaced:** Get Shit Done (https://github.com/gsd-build/get-shit-done) and Superpowers (https://github.com/obra/superpowers). Both came from people the user told about the plugin; user has not looked into either yet.
- **Sharpened on shaping vs. evaluating:** Asked whether these were absorbed influences or leads. They are leads, not influences.
- **Scope decision:** Evaluating these is **deferred to v3** as a backlog item. Not in v2 scope, since v2's spine is "streamlining v1 from feedback" — adding a research beat would dilute it.
- **Net references for v2:** v1 itself + the feedback pile in `docs/sdd-feedback.md`. No new external references shape v2.

### Beat 4 — Goals
- **User framing:** Goals = address the feedback. Two implicit pillars in his answer: (1) fix bugs (items that are functionally broken) and (2) remove flow interruptions / pacing friction. Unifying outcome: improve start-to-finish flow.
- **Pushback consideration:** I tried to elicit "goals beyond addressing feedback" — user reasonably refused the framing. The feedback *is* the goal. Reflecting his structure back rather than imposing a different shape.
- **Audience-shift goal:** User confirmed "implicit" — handing off cleanly to the tech group is an emergent property of the two pillars, not a separate third goal.

### In-scope triage of the feedback pile
- **Default:** all feedback items in scope. Nothing silently dropped.
- **Bugs (in, no discussion):** /sdd:feedback clobbering lastCommand; /sdd:reflect PRD state-tracking gap on non-split items.
- **Flow improvements (in, no discussion):** deepening-round previews; multi-session resume; cross-project feedback transfer; automatic backlog generation; sprint-mode memory; fast-path /sdd:reflect for simple sprints; encourage user input during scope; file uploads during /sdd:scope; onboarding polish.
- **In, but framed as "investigate and decide":** file uploads at other workflow points; context management for many deepening rounds.
- **Bigger items deferred to first deepening round (per user request):** the /scope→/discovery + /prd→/scope rename; the post-sprint command ceremony open concern. User wants to flesh these out before locking — recommendation is to do the rename in v2 (both halves), but final call after deepening discussion.
- **Open concerns staying deferred to later commands:** clean-state for /sdd:build revert (target /sdd:refine or /sdd:spec); spec/runtime duplication (target /sdd:spec); soft acceptance criteria for MIT distribution (target /sdd:refine).

### Backlog candidates surfaced so far
- **Evaluate Get Shit Done (https://github.com/gsd-build/get-shit-done) and Superpowers (https://github.com/obra/superpowers).** Recommended to user by people he told about the plugin. Not yet read by user. Targeted at v3 — a research beat in v2 would dilute the streamlining spine.

### Beat 5 — What "done" looks like
- **Initial framing:** User said he wanted "the flow improved" but flagged it as not tangible. Specific friction he could name: post-sprint flow feels like an unnecessary step / could fold into something else.
- **Sharpened proposal accepted as-is:**
  1. Every triaged-in-scope feedback item has shipped and been exercised end-to-end at least once.
  2. A new project run from /sdd:onboard through /sdd:retro produces no new feedback note that says "this interrupted my flow" or "this slowed my pace." The dogfood run becomes the acceptance test; new pace/flow complaints become v2.x or v3.
  3. The post-sprint flow specifically feels like one cohesive close-out, not three stacked ceremonies.
- **Notable:** Pillar 3 explicitly bakes the user's spontaneous friction call-out into the done definition, which also lines up with the post-sprint command ceremony open concern flagged for resolve-or-defer this pass.

### Beat 6 — Solo or team
- **Confirmed:** v2 is a solo build (Jason), designed cleanly so the local tech group can pick it up. The tech group are consumers / feedback sources, not contributors.
- **Resume doc anticipated this exactly** — confirmed without re-eliciting.

### Phase 1 close + project size assessment
- **All six mandatory beats covered.** Resume file's "Beats covered so far" was 1 + 2; this session added 3, 4, 5, 6.
- **Project size: appropriately sized.** No split needed. ~13 in-scope feedback items + 2 deferred-to-this-pass open-concern decisions. Items are bounded and largely independent. Solo build, fresh domain knowledge from real v1 use, no subsystem dependencies blocking a linear path. Estimate ~2–4 sprints depending on grouping. No multi-product surface area.
- **Transition to Phase 2 (deepening rounds)** with two anchor topics already pre-flagged by the user (rename, post-sprint ceremony) plus three further-flesh-out topics where v2 ambiguity is highest.

### Phase 2 — Round 1 opened, then session paused
- **Round 1 topics previewed** (per the deepening-round-preview-topics feedback convention): rename / post-sprint ceremony / cross-project feedback transfer / automatic backlog generation / onboarding polish.
- **Topic 1 (rename) opened.** Agent's standing recommendation: rename both halves in v2. First Round 1 question asked the user to choose v1-project handling (clean break / one-time migration / other). Asked as multiple-choice — slip from the free-form-questions rule, flagged in the resume file for the next-session agent to re-ask open-ended.
- **Session paused before answer.** User asked to resume from a fresh session. `docs/scope-resume.md` rewritten in full to reflect current state: Phase 1 done, size assessment done, Round 1 in progress, pending question captured, topics 2–5 pre-thinking included for the next-session agent.

### Phase 2 — Round 1 resumed (2026-05-01)
- **Resume from `docs/scope-resume.md` confirmed.** No setup re-run; picked up at Round 1 topic 1 with the rename question re-asked open-ended.
- **Topic 1 — V1-project handling: clean break, no migration code.** User pointed out he's a population of one right now (tech group hasn't been handed v1 yet); his own v1 projects are effectively closed-out. Agent's pre-flagged leaning toward a one-time migration was overcautious — building migration code for zero users contradicts the v2 streamlining spirit. If timing shifts (group lands on v1 before v2 ships), revisit.
- **Topic 1 — Migration guide doc added as the soft landing.** User asked for a single markdown file in the plugin that any future AI can read cold and use to upgrade a v1 project's state and references to v2 conventions. Lightweight, discoverable, extensible to future renames or schema changes.
- **Topic 1 — Transition strategy: atomic rename, no aliases.** Inferred from the migration-guide decision — aliases and a guide are alternative soft-landings; user chose the guide, so atomic is consistent. Stated assertively; user can push back.
- **Topic 1 — Doc/code sweep scope: pushback exchange.** User's first instinct was full sweep including `docs/archive/v1/`, applying the same "I'm the only user" logic. Agent pushed back: that logic worked for the migration-code call because the cost was external; for the archive, the cost is informational — rewriting v1 archive material falsifies a historical snapshot, since the archive describes a past world where the old names actually were the names. Consistency win is cosmetic, archive isn't load-bearing for active work, and old names *in the archive* read as information ("pre-rename") not friction. User accepted the pushback.
- **Topic 1 — Doc/code sweep scope (locked):** full sweep on active material (skill files, command headers, sdd-guide, README, all prose in `docs/` outside the archive, live `process-notes.md`). `docs/archive/v1/` untouched, with a one-line note at the top of the v1 archive index explaining the rename.
- **Topic 1 closed.** Four sub-decisions: clean break, migration guide doc, atomic rename, full sweep excluding v1 archive.

### Phase 2 — Round 1, Topic 2: post-sprint command ceremony (in progress)
- **Carried-in open concern targeting this command.** User's Beat 5 pillar 3 already biases the answer toward consolidation; direction locked, shape is the question.
- **Opening question (asked):** what's the load-bearing work between sprints — strip away the ritual, what would the user genuinely miss?
- **User reframed: absorption, not merge.** Three load-bearing items and where each belongs:
  - (1) Sprint problems carried forward → handled by next `/sdd:sprint` (recognize out-of-scope items, write notes for next `/sdd:build`).
  - (2) Marking sprint done → already in `/sdd:sprint` or made the last step of it.
  - (3) Cross-project pattern capture → done at project close, stored at user-profile level alongside onboarding notes. New capability that doesn't exist today.
- **Implication (read by agent, surfaced to user):** `/sdd:reflect` dissolves — its story-splitting and carry-forward absorbs into next-`/sprint` startup, its per-sprint process beats need a new home. `/sdd:refine` stays as-is; the friction is that reflect *recommends* it, not that it exists. `/sdd:retro` stays, grows a cross-project-patterns step that writes to `~/.claude/sdd-user-profile.json` (or sibling). Retro also stops being recommended per-sprint — only fires at actual project close.
- **Pending decision:** where per-sprint process content (smoothness/sizing/blockers/wins) lives post-dissolution. Option (a) light "anything notable?" beat at start of next `/sprint`, output becomes retro's substrate. Option (b) drop it; retro reconstructs from process notes + sprint files. Agent leans (a) — matches feedback note `2026-04-21T23:55:22Z`.
- **User chose (a) with refinement.** `/sdd:sprint` startup also surfaces sprint-affecting issues if it detects any (silent otherwise) — bounded to: things that could affect future sprints, items not completed, items that need refining. End of `/sdd:sprint` recommends the next command (could be `/build`, `/refine`, `/retro`, etc., depending on detected state).
- **Topic 2 closed. Final shape:**
  - `/sdd:sprint` becomes the sprint-boundary orchestrator: carry-forward check (story splitting moves here), "anything notable?" beat, surfaces sprint-affecting concerns only if non-empty, PRD state check (all complete → recommend retro; unvetted → recommend refine; otherwise plan), end-of-run next-command recommendation.
  - `/sdd:reflect` dissolves entirely.
  - `/sdd:refine` stays as-is, runs on demand when unvetted items exist; recommended by `/sprint` not by reflect.
  - `/sdd:retro` stays, only fires at project close, grows a final cross-project-patterns step that writes preferences to `~/.claude/sdd-user-profile.json` (or sibling file). Future `/sdd:onboard` reads those.
- **Carried-in open concern resolved by topic 2 closure.** Post-sprint command ceremony concern (target: `/sdd:scope` or `/sdd:refine`) is fully addressed by the dissolution of reflect + retro becoming project-close-only.
- **Side effect for v1→v2 migration guide (topic 1 deliverable):** must note `/sdd:reflect` is gone in v2 and direct any future v1-state references at `/sdd:sprint` (and `/sdd:build` — see correction below).

### Topic 2 correction — user caught a `/sprint` vs `/build` conflation
- **User instinct:** several items the agent assigned to `/sprint` should actually be in `/build`. Right read.
- **Principle clarified:** `/build` executed the sprint and has freshest context for closing it; `/sprint` should be a clean planning command that *reads* what `/build` left behind, not an interview about the previous sprint.
- **Items moved from `/sprint` → `/build` wrap-up:** sprint completion marking; "anything notable?" beat; story-splitting on unfinished items (with user confirmation, PRD update); capture of quality concerns / tech debt.
- **Revised topic 2 final shape:**
  - `/sdd:build` grows a wrap-up phase: declares sprint closed, story-splits unfinished items + updates PRD, asks single "anything notable?" (drills in only if yes), captures quality/tech-debt for future planning, writes everything to `process-notes-sprint-N.md`.
  - `/sdd:sprint` stays slim and purely planning: reads previous sprint's process notes (populated by `/build`), surfaces carry-forward + concerns by reading, PRD state check (plan / recommend `/refine` / recommend `/retro`), plans the new sprint, end-of-run next-command recommendation.
  - `/sdd:reflect` still dissolves — work splits across `/build` (heavier interactive pieces) and `/sprint` (lightweight reading), landing the human-in-the-loop bits where the human is already engaged.
  - `/sdd:refine` and `/sdd:retro` unchanged from the prior topic 2 closure.
- **Identity restored:** `/sprint` = "what are we building?", `/build` = "build it and close it out."

### Topic 1 expansion — `/sprint` → `/plan` rename added
- **Driver:** the topic 2 reshuffle made `/sprint`'s name fit worse — it's now purely a planning command, but "sprint" in agile parlance contains both planning and execution. The name itself helped fuel the conflation the user caught.
- **Considered:** keep `/sprint`, rename to `/checklist` (hackathon plugin's term), or rename to `/plan`.
- **Rejected `/checklist`:** names the artifact, not the action; breaks the verb-pattern other SDD commands follow (`/scope`, `/build`, `/refine`, `/retro`, `/feedback`); understates the command's job after the topic 2 reshuffle (also reads previous-sprint outputs, runs PRD state check, recommends next step).
- **Picked `/plan`:** verb, says what it does, pairs cleanly with `/build` (`/plan` → `/build`), removes the agile-term ambiguity, aligns with topic 1's rename principle.
- **Sprint concept preserved.** Artifact filenames stay `docs/sprint-N.md`, state field stays `currentSprint`, process notes stay `process-notes-sprint-N.md`, "sprint N" remains the natural way to refer to a unit of work. Docs add a one-liner: "A sprint is the unit of work between one `/plan` and the close of `/build`."
- **Final v2 rename list:** `/scope` → `/discovery`, `/prd` → `/scope`, `/sprint` → `/plan`, `/reflect` removed (work absorbed into `/build` and `/plan`).
- **Migration guide scope grows by one entry.** Pattern is the same; no new mechanism needed.

### Phase 2 — Round 1, Topic 3: cross-project feedback transfer (in progress)
- **Question framed.** Where does the link from "any project" to "the plugin repo" live? Three options surfaced: (a) user profile stores plugin path, set during `/sdd:onboard`; (b) auto-discovery via Claude Code's plugin marketplace path; (c) per-project `.sdd-config` file.
- **Agent's leaning: (a).** Profile is already loaded by every command, single source of truth, one onboarding question. (b) couples SDD to a Claude Code internal path convention. (c) requires per-project ceremony — antithetical to v2's streamlining spine.
- **User question expanded the topic.** "Can feedback be shared back to me/repo if it's with someone else?" Caught a real gap in agent's topic 3 framing — option (a) only solves the case where the user has a local plugin clone (Jason). Tech group members install from the marketplace and have no writable plugin repo on disk; their feedback would die locally.
- **Refactored topic 3 shape: two destinations, both opt-in via user profile.**
  - `feedbackLocalPath`: filesystem path to a local plugin clone. Used by Jason (and any tech group dev who clones for development). Forwards local-file writes when running outside that path.
  - `feedbackShareUrl`: GitHub repo URL of the plugin upstream. Used by anyone. When set, `/feedback` asks per-note whether to submit as a `gh issue create` to the repo. Local file write still happens; submission is additive.
  - User can have either, both, or neither configured. Asked during `/sdd:onboard` with skip-friendly defaults.
- **Wrinkles surfaced:** `gh` auth requirement (onboarding should check); project-name redaction toggle for users who don't want project names leaking into upstream issues; potential issue-tracker spam → optional batching via `/feedback-submit` (defer decision).
- **Pending decision:** build share-back in v2 or defer to v3? Agent leans v2 because the audience-shift goal is the only reason v2 exists; share-back is the load-bearing feedback channel for tech group users. But it's the first item in this scope cycle that's new capability rather than refinement — real call.
- **Plugin update mechanics looked up via claude-code-guide agent.** Findings: auto-update is enabled by default for official Anthropic marketplaces, **disabled by default for third-party marketplaces** (Jason's plugin is third-party). Users toggle on via `/plugin` → Marketplaces tab. When updates pull, Claude Code notifies the user to run `/reload-plugins`. Git-based marketplaces track default branch by default. Implication: tech group enabling auto-update closes the iteration loop in real-time once share-back exists. **New v2 deliverable surfaced:** onboarding (and migration guide / README) should nudge tech group members to enable auto-update for the SDD marketplace. Cheap one-liner.
- **Strengthens agent's lean toward v2 share-back** — fast distribution + share-back = real iteration loop; building one without the other is half-built.

### Session pause #2 (2026-05-01, mid-Round 1 topic 3)
- **User asked to pause** to think through the v2-vs-v3 share-back decision and is moving back to his other computer.
- **Resume artifact: `docs/scope-resume.md` rewritten in full** to reflect topic 1 + 2 closures, topic 3 mid-flight state, plugin update mechanics, pending decision on user's desk, and topics 4-5 still queued. Previous resume file's pre-thinking for topics 4-5 carried forward.
- **Working hypothesis on v2 deliverables list captured in resume file** to make doc generation cheap when scope.md is eventually written.

### Phase 2 — Round 1 resumed (2026-05-02)
- **Resume from `docs/scope-resume.md` confirmed.** No setup re-run; jumped straight to topic 3's pending decision.
- **Topic 3 — share-back deferred to v3.** User chose scope discipline. v2 keeps the audience-shift goal but tech group sends feedback files manually for now (Slack, email). If that proves painful, v3 picks it up. Defers to v3 backlog: `feedbackShareUrl` profile config, `gh issue create` integration, project-name redaction toggle, `/sdd:feedback-submit` batching. Agent's lean had been v2 (the loop is "half-built" without share-back); user overrode on the new-capability-vs-refinement principle.
- **Topic 3 — auto-update mention stays in v2, but framed informationally.** User's distinction: "here's how third-party plugin updates work in Claude Code" (information) rather than "you should turn this on" (suggestion). Sits in onboarding and migration guide as a one-paragraph note. Reasoning matches the v3 deferral — without share-back, auto-update is one-way distribution; pushing users to flip it on starts to feel like advocacy for an architecture choice that isn't fully wired yet. Information now, recommendation when v3 closes the loop.
- **Topic 3 closed.** v2 cross-project feedback piece reduces to: `feedbackLocalPath` profile config (Jason's case) + informational auto-update note in onboarding/migration guide. Rest deferred.

### Phase 2 — Round 1, Topic 4: automatic backlog generation (closed)
- **Trigger model: user-confirmed before write.** Agent recognizes a deferral, asks "want me to capture this on backlog?", writes only on yes. Lower automation, no surprise entries. User picked this over agent-detected-automatic.
- **Drops live in process notes only.** No second tracker for actively rejected ideas. If a future scope cycle re-raises a dropped idea, process notes are the trail; backlog stays exclusively a "yes, defer" record.
- **In-flight planning docs use pure reference.** `scope.md` (and PRD/spec/plan) say "deferred items: see backlog.md" with no inline restatement. Backlog entries must be fully self-contained as a result.
- **Entry format locked:** Title / What / Why deferred / Trigger to revisit / Dependencies / Source (command + session + thread). Source field exists so future agents can trace original context.
- **Applies to all four planning commands** (`/sdd:discovery`, `/sdd:scope`, `/sdd:spec`, `/sdd:plan`) — wherever a deferral can surface.
- **Auto mode reminder fired mid-topic.** Locked the entry format as a proposal-with-off-ramp instead of running another open question; consistent with auto-mode minimize-interruptions guidance and with the user's pattern of accepting agent proposals when well-reasoned.

### Phase 2 — Round 1, Topic 5: onboarding polish (closed)
- **Read `/sdd:onboard` skill before opening the topic** per resume file's instruction. Current state: 6 steps, biggest gap is the communication-style question presenting four bulleted examples (free-form-rule violation even though "or describe your own" is offered).
- **Topic 5 fixes (existing flow):**
  1. Communication-style question goes fully open-ended; drop the bulleted example menu.
  2. Surface where preferences live (`~/.claude/sdd-user-profile.json`) and that re-running `/sdd:onboard` updates them.
  3. End onboarding with a heads-up that `/sdd:discovery` is an interview, not a one-shot.
- **Topic 5 new beats (v2 additions):**
  4. Lift `/sdd:feedback` out of the workflow-bottom one-liner into its own short beat (what / when / where notes go).
  5. New optional question for `feedbackLocalPath` (skip-friendly).
  6. Informational auto-update paragraph: third-party plugins default to manual update in Claude Code; how `/plugin` → Marketplaces toggles auto-update; what `/reload-plugins` does. **No "you should turn this on" framing** — locked at topic 3 closure.
  7. Update flow surfaces retro-written cross-project pattern prefs alongside originals.
- **User accepted all seven without pushback.** Side conversation on iterate/refine distinction (sprint-level polish vs PRD-level shaping; refine is mini-PRD+spec compressed and targeted, handles all unvetted items in one run, looping internally) — not an onboarding question, but worth carrying forward as a candidate Round 2 topic if naming overlap warrants v2 attention.

### Side thread — iterate/refine naming overlap (deferred to Round 2 candidate)
- **User probed the architectural logic** behind /refine vs re-running /scope+/spec. Resolved by walking through three reasons: surgical vs wholesale, per-doc confirmation gates, foundational vs mid-flight lifecycle role. Also addressed why unvetted items exist post-spec (emergent requirements during iterate/build/retro/user-notes).
- **Side question surfaced:** does "iterate vs refine" naming overlap warrant a v2 thread? Both sound like "make it better." User did not commit either way; flagging for Round 2 if relevant.

### Sprint-loop ordering wrinkle (resolved in Round 2 — see topic 2 below)
- **Surfaced when listing the v2 chain.** `/sdd:iterate` is "optional polish after a sprint" but `/sdd:build` now declares the sprint closed in its wrap-up phase. If iterate runs *after* build wrap-up, the sprint reopens; if iterate runs *before*, build needs to know "are you done done, or just done with the checklist?"
- **Originally flagged for spec pass.** User chose to address in Round 2 instead.

---

## Phase 2 — Round 2 (deepening)

### Round 2 — opened
- **Topics queued (6):** /sdd:pause+unpause; sprint close vs iterate ordering; feedbackLocalPath failure modes; process notes growth; migration guide structure; iterate/refine naming overlap.
- **Feedback audit ran first** at user request — confirmed 14 of 15 feedback notes covered by Round 1 + Phase 1; the gap was the `/sdd:pause` bonus sub-ask in note 13. Triggered user to add /sdd:pause to Round 2.

### Round 2 — Topic 6: /sdd:pause + /sdd:unpause (closed)
- **Decision: formalize as commands** rather than leave as soft "user says pause, agent writes file" convention. Discoverability + consistency across model versions + schema enforcement carried the day. Soft convention worked twice in this project but is fragile for tech group rollout.
- **User correction on resume mechanism.** Original proposal had user typing `/sdd:scope Resume from docs/scope-resume.md` to come back. User rejected — too much to memorize. Replaced with dedicated `/sdd:unpause` command that auto-detects which resume file to load via `lastCommand` in `docs/project-state.json`.
- **Mechanism:** `/sdd:pause` runs against the in-flight command; the active model (which has conversation context) distills the conversation into `docs/<command>-resume.md` using the schema we've been dogfooding (Status / Setup-already-done / Confirmed decisions / Round status / Pending decision / Files for next-session / Instructions for next-session). Tells user "Paused. Run `/sdd:unpause`."
- **`/sdd:unpause` flow:** read `lastCommand`, load the corresponding `<command>-resume.md`, load the source command's SKILL.md, pick up the conversation. No flag, no path, no name to remember.
- **Cleanup:** resumed command deletes its own resume file when underlying work completes (e.g., `/sdd:scope` deletes `docs/scope-resume.md` after `scope.md` is approved).
- **Scope of applicability:** discovery, scope, spec, plan, refine, iterate (interview-heavy commands). Excluded: build (sprint file is the resume mechanism), retro/onboard/feedback (short, single-session). Between commands → "nothing to pause," exits.
- **User scenarios that pinned the design:**
  1. **Two computers via OneDrive.** Resume files live in project's `docs/` folder; OneDrive transports them. No new infrastructure.
  2. **Context-rot mitigation.** User runs /pause, then /clear, then /unpause to reset accumulated context mid-phase. **Implication:** the resume file isn't just a status snapshot — it must be a summary good enough to seed a brand-new model from zero context. The dogfooded schema does meet that bar but pause needs to distill, not dump. Worth flagging at spec time.

### Round 2 — Topic 2: sprint close vs iterate ordering (closed)
- **Was flagged at v2-chain listing as an ambiguity for spec phase**; user chose to resolve in Round 2 directly.
- **User-authored design** (clean and complete on first pass — no agent pushback needed):
  - When `/sdd:build` finishes its checklist, it asks: "Satisfied with this sprint, or are there items to iterate on?"
  - If build noticed legitimate iteration candidates during execution (rough edges, half-done bits), it surfaces them with the question. **No fabrication** — if nothing came up, ask the question bare.
  - **Satisfied** → sprint closes (story-split unfinished items, capture tech debt, write `process-notes-sprint-N.md`); system suggests next action based on PRD state (more sprints → `/sdd:plan`; project done → `/sdd:retro`).
  - **Not satisfied** → system suggests `/sdd:iterate`. Sprint stays open.
  - **Re-open escape hatch:** if user later wants to iterate after saying satisfied, system asks "Re-open the sprint?" before proceeding.
- **Why this resolves cleanly:** wrap-up fires on user's explicit "satisfied" answer (not automatically), iterate naturally chains in for the unsatisfied case, re-open path covers the "I thought I was done" case without making every build invocation gate on a question.

### Round 2 — Topic 3: feedbackLocalPath failure modes (closed)
- **Locked as proposal-with-pushback** (mechanical behavior, not a judgment call). User accepted as written.
- **Behavior:** `/sdd:feedback` writes the note locally to current project's `docs/sdd-feedback.md` AND forwards to `<feedbackLocalPath>/docs/sdd-feedback.md` if path is set.
- **Inside-the-clone detection:** if cwd equals `feedbackLocalPath` (or descendant), local write IS the forward — no double-write.
- **Failure handling:** if path is set but invalid (doesn't exist, target unwritable, target file missing), command saves locally as normal, warns user once per session ("`feedbackLocalPath` is invalid (`<reason>`). Saving locally only. Run `/sdd:onboard` to update."), does not fail. **The note is never lost.**
- **Principle:** no retry, no half-success — either forward succeeds or user sees the warning. Don't paper over partial failures.

### Round 2 — Topic 4: process notes growth (closed)
- **Resolution: split planning notes per phase.** New convention: `process-notes-discovery.md`, `process-notes-scope.md` (which is the renamed PRD), `process-notes-spec.md`. Sprint notes continue as `process-notes-sprint-N.md` (already the convention).
- **Retro reads selectively** — loads each phase file as needed instead of scanning a monolithic notes file.
- **No within-phase compaction.** Multi-session resume (`/sdd:pause`) covers the in-flight summarization need; phase notes are "done growing" once the phase ends.
- **Migration note for v2:** existing `process-notes.md` files (like this very project's) need a one-time split or a "this is the legacy monolithic format" pointer. v1→v2 migration guide should cover this.

### Round 2 — Topic 6: iterate/refine naming overlap (closed)
- **Decision: rename `/sdd:iterate` → `/sdd:polish`.** Verb form, consistent with the rest of the v2 command set (`/discovery`, `/scope`, `/spec`, `/plan`, `/build`, `/polish`, `/refine`, `/retro`, `/feedback`).
- **Why polish over the alternatives:** captures sprint-cleanup nature without narrowing the command's scope (`/harden` would imply bugs/robustness only and squeeze out small UX additions; `/punchlist` is more vivid but longer and less universally understood).
- **Distinction this draws:** `/polish` = build-layer (post-sprint cleanup of just-shipped work, append-only on the sprint file); `/refine` = planning-layer (PRD unvetted items → vetted user stories, cross-doc updates with confirmation gates). Self-documenting verbs at the right layer.
- **Add to v2 rename map:** `iterate → polish`. Migration guide covers it the same way as the other renames (state-aware table, resume-file rename rules).

### Round 3 — opened (paused mid-Topic 1)
- **Topics queued (4):** /sdd:discovery content design; v2 ship/validate criteria; backwards compat for sdd-plugin project itself; naming pass on unrenamed commands.
- **Topic 1 in flight, awaiting user decision:** three shapes presented for `/sdd:discovery` redesign — A (cosmetic rename only), B (reframe prompts/outputs, keep structure), C (true discovery step, splits work between /discovery and renamed /scope). My lean is C is the honest pick but cascades into /scope content design we also haven't done; B is the safe pick. **Session paused here at user request — fresh session needed.**

### Round 3 — resumed (2026-05-04)
- **Resume from `docs/scope-resume.md` confirmed.** Topic 1 re-opened with A/B/C summary. User asked for dangers and advantages of Shape C before deciding.
- **Agent's read offered:** five advantages (honors the rename; real home for file uploads; lower-stakes entry; cleaner chain; reusable discovery artifact) and five dangers (cascade into /scope design; no natural termination; two-artifact smear risk; doubled migration surface; pure-exploration framing is a small lie). Bottom line: C wins if user is willing to take the cascade in this round; otherwise B.
- **Topic 1 — Shape C selected.** User accepted the cascade explicitly: "I am up for the cascade. I want to make this concept work." Discovery becomes a true exploration step distinct from defining; renamed `/scope` (v1 `/prd`) absorbs the definition work v1 `/scope` used to do.
- **Topic 1.5 opened: `/discovery` ↔ `/scope` boundary.** Load-bearing sub-decision of Topic 1. Once we lock what each command commits vs leaves open, the interview shape and artifact structure for both fall out. Boundary proposal on user's desk.
- **Topic 1.5 — PRD-artifact placement question raised.** User asked whether PRD-shaping should stay at `/scope` (one artifact: `scope.md`) or push forward to `/spec`. Three options framed: A keep PRD at `/scope`, B push PRD into `/spec`, D split into two docs at `/scope` (`scope.md` + `prd.md`) within one command. Agent's lean: A is cleanest, D is honest middle ground, B introduces architectural debt (worsens the spec/runtime duplication open concern). User paused this thread to add a new feedback item before answering.

### Mid-Topic-1.5 — new in-scope feedback item added
- **New feedback note logged** (`docs/sdd-feedback.md`, 2026-05-04T00:00:00Z): clarifying questions must not advance the interview. Cross-cutting behavior bug observed across projects and across interview commands — when user asks a mid-interview clarifying question, agent has tendency to (a) answer it, (b) substitute its own recommendation as the user's answer, (c) advance the question pointer. User wants this behavior pinned in the plugin.
- **Disposition:** in-scope per Phase 1 default. Lands in `sdd-guide` interaction rules as a new rule — "clarifying questions don't advance the interview." Behavioral fix; doesn't require a Round 3 design topic. Detailed rule shape to be drafted at spec time. If user wants a Round design pass on it before then, slot as Round 4 candidate.
- **Returning to Topic 1.5** — user asked to restate options A/B/D and continue from there.
- **Side question: can `/clear` be invoked from a plugin command?** Dispatched claude-code-guide subagent. Answer: no. Plugin commands cannot programmatically trigger built-in slash commands; hooks/subagent-fork/dynamic-injection don't reach the goal either. Implication: Round 2 Topic 6 closure stands as designed (three user actions: `/sdd:pause` → `/clear` → `/sdd:unpause`). No revision needed.

### Mid-Topic-1.5 — user laid out comprehensive vision via remote-control
- **User submitted full architectural vision** for the discovery / scope / spec triplet, plus a round-level context-management mechanism. Pre-existing closures NOT invalidated; vision builds on top of them and sharpens several places.
- **User's vision summary:**
  - **/discovery:** collaborative brainstorm with documents, interview pattern. Output: `discovery.md` + soft scope doc.
  - **/scope:** full scope collaboration, reads discovery + soft scope, interview pattern, NO technical questions. Output: defined scope + non-technical PRD.
  - **/spec:** technical session, reads scope + non-technical PRD, interview pattern. Output: full PRD (technical augmentation of the non-technical one).
  - **Per-round mechanics across all three commands:** 5-question cap per round with overflow to later rounds; per-round summary saved to file at round close; recommend `/clear` + re-run command for next round; agent issues importance rating + topic preview for any next-round recommendation.
- **Alignment with prior closures:** three-step chain ✓, discovery as true exploration ✓, document context in discovery ✓, interview pattern preserved ✓, scope absorbs project-definition work ✓.
- **Sharper than agent's prior proposals (3 places):**
  1. Discovery's soft-scope handoff — structured artifact rather than rough idea/audience notes scattered inside discovery.md.
  2. /scope's two-artifact output — picks Topic 1.5 Option D cleanly.
  3. Non-technical / technical split as the /scope/spec boundary — clean architectural cut not previously framed.
- **New structural moves added by user (not previously discussed):**
  4. /spec output is "the full PRD" — implies v2 has no `spec.md`; spec act produces technical layer on top of `prd.md`.
  5. 5-question cap per round.
  6. Per-round summary + /clear-recommend mechanism with end-of-round importance rating and topic preview.
- **Open questions raised by agent:**
  - a) Does /discovery's soft `scope.md` get overwritten by /scope or do we keep two files?
  - b) Same for the PRD — overwrite or sibling?
  - c) Does /spec only append technical sections, or revise non-technical content?
- **Concerns flagged by agent:**
  - α) 5-question cap may trade context rot for context fragmentation; per-round summary must clear the same bar as `/sdd:pause` resume files.
  - β) `/clear`-between-rounds adds friction unless the importance rating is honest — needs a rubric.
  - γ) Interaction with `/sdd:pause` — they're complementary, not redundant; pause covers cross-computer + end-of-day, round summaries cover in-session rot.
  - δ) Spec/runtime duplication open concern (v1 retro carry-in) likely worsens with full-PRD-as-runtime-artifact; flag at spec-design time.
- **Proposed split:** moves 1–3 close Topic 1.5; moves 4–6 open as new Round 3 topic (Topic 1.6: round-level context management). Awaiting user's direction.

### Topic 1.5 — partial closures from user
- **(a) Soft scope lifecycle — closed.** Soft scope and final scope share the same filename (`scope.md`). /discovery writes the soft version; /scope hardens it (overwrite). No history retained — once hardened, the soft version is gone.
- **(b) PRD lifecycle — closed.** Same filename (`prd.md`) used across /scope and /spec. /scope writes non-technical PRD; /spec appends technical content. Document evolves through both steps.
- **(c) /spec output count — OPEN, next question.** User signaled openness to /spec producing two artifacts (i.e., `prd.md` PLUS something else). Wants discussion. Agent will frame requirements-vs-implementation split as the question shape.
- **(α) Question cap rule — closed (rule revised).** 5 questions per round suggested, 10 hard cap. Past 10, agent must explain why and get explicit user permission. Replaces my prior bare 5-cap.
- **(β) End-of-round recommendation — closed (rule pinned).** `/clear` between rounds is mandatory — user has overloaded context within a single round many times. Agent's recommendation must always be definite with reasoning: "I think we should do another round, because..." or "I do not think we need another round, because..." Never a blank "continue?" prompt.
- **(γ) Pause + round-summaries — implicitly accepted.** User skipped γ in answer; agent is treating silent acceptance as agreement that the two mechanisms are complementary, not redundant.
- **(δ) Spec/runtime duplication concern — deferred.** User confirmed willingness to dig deeper at spec-design time. Stays open as a known concern.
- **(c) /spec output count — closed: c-2 selected.** Two artifacts. /spec appends technical requirements (performance, security, scale, integrations) to `prd.md` and writes a new `spec.md` for implementation content (architecture, behavioral specs, runtime details). Symmetrical with /scope (both interview commands write one new doc + continue one growing doc). Final v2 planning artifact lineage:
  - `docs/discovery.md` (written by /discovery)
  - `docs/scope.md` (soft from /discovery, hardened by /scope, overwrite same file)
  - `docs/prd.md` (non-technical from /scope, technical appended by /spec)
  - `docs/spec.md` (written by /spec)

### Topic 1.5 — closed
- **All sub-decisions resolved.** Discovery↔scope boundary, scope↔spec split (c-2), 5/10 question rule, mandatory /clear-between-rounds with definite recommendation framing. v2 planning chain shape locked.
- **Topic 1.6 (round-level context management) collapsed into Topic 1.5.** Originally proposed as a separate topic to handle moves 4–6 from user's vision. In practice, α (cap rule) and β (recommendation framing) covered the load-bearing behavioral mechanics. Remaining items are spec-time details — per-round summary file location, schema, naming convention, and how it interacts with `/sdd:pause` resume files (potential schema reuse). Carrying as a spec-time concern, not a separate scope topic.

### Round 3 — Topic 2 opened: v2 ship/validate criteria
- **Framing.** Beat 5 pillar 1 says "every triaged-in-scope feedback item has shipped and been exercised end-to-end at least once." Topic 2 sharpens what that verification looks like in practice — what's the concrete process Jason runs before declaring v2 ready for the tech group?
- **Opening question (asked).** Walk-through of the verification approach the user has in mind.
- **User answer:** small website project as the primary end-to-end dogfood. For conditional behaviors that won't fire naturally (cross-machine pause, 10-question cap, per-round /clear-and-continue, cross-project pattern capture), user will rely on other in-progress projects in various states of completion to exercise those naturally. Will also deliberately exercise `/sdd:pause` + `/sdd:unpause` even if not needed.
- **Plan reads as reasonable.** Multiple projects at varying completion states is good natural coverage for items that need scale or cross-project context. Deliberate pause/unpause exercise covers the specific mechanism. Remaining thin spot: how coverage gets tracked across this distributed verification — without some form of artifact, "I exercised everything" is a fuzzy ship signal against Beat 5 pillar 1's "every triaged-in-scope feedback item" bar.
- **Next probe:** how does the user confirm each item has actually been hit?
- **User answer:** new deliverable — `docs/v2-verification.md` checklist that maps each in-scope feedback item. Built up as items ship (implementing command writes the row). User checks off manually outside the plugin's normal workflow. User acknowledged this is out-of-pattern with the rest of the plugin's flow; that's fine — the artifact lives in the project, the user drives the verification.

### Topic 2 — closed
- **v2 verification approach (locked):**
  1. Website project as primary end-to-end dogfood for the planning chain and most flow-improvement items.
  2. Other in-progress projects (varying completion states) cover items that won't fire naturally on a small build (cross-project pattern capture, multi-session pause across machines, longer deepening rounds).
  3. Deliberate `/sdd:pause` + `/sdd:unpause` exercise even if not needed.
  4. `docs/v2-verification.md` checklist as the audit trail — one entry per in-scope feedback item, populated by the implementing command when work ships, checked off manually by the user when verified end-to-end.
- **New v2 deliverable surfaced:** `docs/v2-verification.md` and the build-time write behavior to populate it. Schema/granularity (1:1 mapping vs grouped, columns, who writes when exactly) deferred to spec time.

### Round 3 — Topic 3 opened: sdd-plugin's own migration path
- **Framing.** sdd-plugin (this project) is currently mid-v2-scope on the v1 plugin. When v2 ships, the user will be running v2 commands against project state created by v1 commands — including the in-flight `process-notes.md`, current `docs/project-state.json`, and any v1-named docs already written. Round 2 Topic 5 specced a general v1→v2 migration guide; this topic asks whether the meta-case (the plugin building itself migrating to itself) needs special handling beyond the general guide.
- **Opening question (asked).** User's instinct on how the plugin's own self-migration should work.
- **User answer:** standard migration via the general guide, unless I see something different needed. Open to my judgment.
- **Agent take:** nothing different needed. The meta-case is actually easier than most projects — user has already manually archived v1 planning to `docs/archive/v1/` and started v2 with a clean slate. Docs being produced right now (`docs/scope.md` at wrap, eventually `docs/prd.md`, `docs/spec.md`) are already v2-shaped, so there's no v1 artifact in active use needing translation. One observation, not a migration concern: the sdd-plugin project becomes a permanent dogfood target — every v2 cycle here exercises v2 on the project that built v2. Asset, not a problem.

### Topic 3 — closed
- **Sdd-plugin self-migration:** runs the standard v1→v2 migration guide like any other project. No special handling required. Meta-case is simpler than typical because manual archival of v1 planning artifacts has already been done.

### Round 3 — Topic 4 opened: naming pass on unrenamed commands
- **Framing.** v2 renames so far have been driven by name-meets-reality friction. Remaining commands without a rename: /onboard, /build, /refine, /retro, /feedback. Topic asks if any feel off when said aloud against the new chain.
- **Agent take:** no naming friction surfaced for these in feedback; recommend leaving alone unless something catches when reading the full chain.
- **Opening question (asked).** Does anything in `/sdd:onboard → /sdd:discovery → /sdd:scope → /sdd:spec → /sdd:plan → /sdd:build → /sdd:polish → /sdd:refine → /sdd:retro` (plus utilities) catch?
- **Paused for mid-round revisit (see Topic 5).** User raised a structural question about /prd that needed to be resolved before naming could be finalized.

### Round 3 — Topic 5 opened: /prd as a dedicated step (revisit)
- **Framing.** User had a settled gut feeling that requirements thinking was missing in the current 3-interview shape (discovery → scope → spec), and that planning context for downstream AI work was underdeveloped as a result. Asked for honest pros/cons of bringing /prd back as a dedicated step vs keeping the current shape.
- **Agent presented:** pros/cons of current shape, pros/cons of /prd-as-step, and a middle-ground (requirements-elicitation beat embedded in /scope and /spec). Recommendation withheld; question of fit returned to user.
- **User decision:** /prd back as a dedicated step. Explicit rejection of middle ground. Reasoning: gut shifted from "too many steps" to "requirements step is missing" as the design progressed. Willing to accept the rework cost. Paired with a new requirement (Topic 6) that interview commands recognize small projects and right-size accordingly.
- **Honest note recorded:** this is **not** "restoring" v1 /prd. v1's /prd was actually doing scope work (which is why feedback called for the /prd→/scope rename in the first place). v2's /prd is genuinely new — a focused product-requirements pass that v1 never really had.
- **Sub-decision (artifact ownership).** With /prd back, the Topic 1.5 c-2 split (prd.md grown across /scope and /spec, /spec writes two artifacts) dissolves. Two options surfaced:
  - **A (clean ownership):** /prd writes entire prd.md (non-tech + tech requirements). /spec writes only spec.md.
  - **B (split tech into spec):** /prd writes non-tech requirements. /spec appends tech requirements to prd.md AND writes spec.md.
- **User selection: A.** Each command owns one document. No cross-command writes.

### Topic 5 — closed
- **v2 planning chain (locked):** `/sdd:discovery → /sdd:scope → /sdd:prd → /sdd:spec`. Four interview commands, each with one clean job:
  - /discovery: what is this? (explore)
  - /scope: what's the boundary? (in/out)
  - /prd: what must it do? (requirements — non-tech + tech)
  - /spec: how does it work? (implementation — architecture, behaviors, runtime)
- **Artifact lineage (locked):**
  - `docs/discovery.md` (written by /discovery)
  - `docs/scope.md` (soft draft from /discovery, hardened by /scope, overwrite same file)
  - `docs/prd.md` (written by /prd, owns the doc)
  - `docs/spec.md` (written by /spec, owns the doc)
- **Topic 1.5 c-2 dissolution recorded.** Earlier closure that had /spec writing two artifacts is superseded by Topic 5. Each interview command now owns exactly one artifact end-to-end.

### Round 3 — Topic 6 opened: right-size detection for small projects
- **Framing.** User paired the /prd-back decision with a new requirement: planning steps must recognize when a project is small/simple and not go overboard with rounds, questions, or ceremony. Explicit motivation: adding /prd lengthens the chain; the chain length must not punish small projects. Encouragement to move on when there isn't much more to surface should be baked in.
- **In-scope behavior locked (no further interview needed — user stated direction clearly):**
  - Interview commands detect small-project signals during /discovery (and optionally re-evaluate at each subsequent step) and propagate a "small project" hint downstream.
  - When small-project hint is set: lower round-count expectations, fewer Phase 1 beats fire, agent leans harder toward "I do not think we need another round, because..." closure, agent picks fewer than the suggested 5 questions per round when answers are obviously thin.
  - Generalizes the existing /sdd:reflect-for-simple-sprints feedback to every interview command in the chain.
- **Detection signals + threshold definitions deferred to spec time.** Don't lock specific signals here; the right shape is a small heuristic the agent applies, not a numeric rule.

### Topic 6 — closed
- **Right-size detection** is an in-scope v2 behavior. Logged as a new feedback entry in `docs/sdd-feedback.md` for traceability. Will land as one row in `docs/v2-verification.md`.

### Topic 4 — paused
- **Naming pass on unrenamed commands.** With /prd back, the full v2 chain is now `/sdd:onboard → /sdd:discovery → /sdd:scope → /sdd:prd → /sdd:spec → /sdd:plan → /sdd:build → /sdd:polish → /sdd:refine → /sdd:retro` plus `/sdd:pause`, `/sdd:unpause`, `/sdd:feedback`.
- **Resolution pending — /clear topic intervened.** Agent recommendation stands: leave /onboard, /build, /refine, /retro, /feedback alone. No naming friction in feedback. Will resume after Topic 7.

### Topic 4 — resumed and closed
- **No naming changes.** /onboard, /build, /refine, /retro, /feedback retained as-is. None of these have surfaced naming friction in the feedback pile, and the full v2 chain reads cleanly with the renames already in place (/scope→/discovery, /prd→/scope, /sprint→/plan, /iterate→/polish, /reflect removed, /prd added). User implicitly accepted by closing the round without revisiting.

### Round 3 — closed (no Round 4)
- **Reason for closing:** every in-scope feedback item triaged and decided; load-bearing structural decisions all locked (chain shape, artifact lineage, /pause-and-unpause, three-tier context management, right-size detection, verification approach); remaining unknowns are explicit spec-time concerns (per-round summary schema, v2-verification.md columns, right-size signals, end-of-command handoff wording).
- **User confirmation:** "no Round 4" — direct quote ("3 rounds, not 3 interviews").

### Wrap actions (completed)
- **`docs/scope.md` generated.** Canon for v2: cycle context, audience, two-pillar goals, three-pillar done definition, command chain with /prd reintroduced, artifact lineage (Topic 5 Option A: each interview owns one artifact), in-scope deliverables organized by group (bug fixes, new commands, behavioral rules, context management, /onboard polish, /plan-and-/build, /retro, /feedback transfer, backlog generation, process notes, migration, v2 release verification), out-of-scope-to-v3, open concerns pointer.
- **`docs/open-concerns.md` updated.** Post-sprint command ceremony **moved to Resolved** (cite v2 Round 1 Topic 2). Three v1 carry-overs kept in Open (clean-state for /build revert, spec/runtime duplication with v2 reaffirmation note, soft acceptance criteria). Added seven new spec-time concerns surfaced during v2 scoping (per-round summary schema, right-size signals, three-tier recognition heuristic, end-of-command handoff wording, v2-verification.md columns, file uploads at non-discovery points, pause distillation quality bar).
- **`docs/backlog.md` written.** Three entries: GSD/Superpowers evaluation (v3 trigger), bidirectional share-back (v3 trigger or tech-group pain), recommend auto-update (dependent on share-back).
- **`docs/scope-resume.md` deletion pending** — final wrap action below.

### Post-wrap framing correction (user-caught, 2026-05-11)
- **Issue.** scope.md described v2 changes using the intermediate "rename" frame (v1 /scope→/discovery + v1 /prd→/scope, then /prd reinstated). With /prd back, that framing is misleading — the two renames net out to nothing useful.
- **Honest framing locked.** Net change vs v1: **added `/discovery`** at the start (new step — open exploration with file uploads; v1 had no equivalent); **`/scope` narrowed** (still named `/scope`, but the exploration half of v1 `/scope`'s job moved to `/discovery`; the project-definition half stayed); **`/prd` and `/spec` unchanged** in name and intent; **`/sprint` → `/plan`**, **`/iterate` → `/polish`** (real renames); **`/reflect` removed**; **`/pause` and `/unpause` added**.
- **Files updated:** scope.md "Changes vs v1" section restructured; scope.md migration guide section reframed to lead with the user-facing description; process-notes Round 1 Topic 1 closure remains historically accurate for the moment-in-time but is superseded by this post-wrap clarification for any downstream artifact (migration guide, README, onboarding).

### Round 3 — Topic 7 opened: between-step context management (revisit of 1.5 β)
- **Framing.** Topic 1.5 β locked "/clear between rounds is mandatory" to address user's repeated in-round context overload. User proposed revisiting with a three-tier model: continue / /compact / /clear, agent recognizes which is appropriate.
- **Agent surfaced three caveats:** (1) plugin can recommend but cannot programmatically invoke /compact or /clear — both are CLI-level commands. (2) AI self-assessment of context weight is imperfect — replacing mandatory clear with judgment call re-introduces overload risk. (3) Scope ambiguity — does this apply between rounds within a command, between commands, or both?
- **User decision:**
  - **Between rounds inside a command:** three-tier model. Agent recognizes appropriate tier (continue / compact / clear) based on round weight, artifact coverage, and conversation noise. Recommendation must be definite with reasoning: "I think /compact because..." — never a blank prompt. User retains override.
  - **Between commands (end of /discovery, /scope, /prd, /spec):** default to /clear. Not user discretion — the plugin must explicitly tell the user to run /clear before launching the next command. Motivation: user has personal experience (hackathon-plugin) of forgetting the clear and regretting it. New users especially need the explicit prompt.
- **Topic 1.5 β superseded.** "Mandatory /clear between rounds" replaced by "three-tier recommendation between rounds." Between-command behavior (which 1.5 β did not explicitly cover) added: default /clear with explicit user-facing instruction.

### Topic 7 — closed
- **Context-management rules (locked):**
  - **Between rounds (within a command):** Three-tier — continue / /compact / /clear. Agent recommends with definite reasoning. User can override upward.
  - **Between commands:** Default /clear. Plugin must emit explicit end-of-command handoff message instructing the user to run /clear, then invoke the next command. Same pattern as the existing recommendation framing — definite, reasoned.
- **Implementation concern carried to spec time:** the agent's heuristic for recognizing tier needs concrete signals (round length, artifact-vs-conversation coverage, noise ratio). Don't pin specifics here; specify at spec time. Also: end-of-command handoff message wording (what exactly the agent says) is a spec-time concern.

---

## /sdd:prd

- **Cycle context:** v2 PRD interview opened against the v2 scope (`docs/scope.md`). Scope is unusually tight — most "brainstorm language" was hammered out during /sdd:scope, so /prd's job is mostly structural: persona selection, epic grouping, AC-quality tightening.
- **Open concerns carried in:** All 10 currently-open concerns target /sdd:spec or /sdd:refine. None target /sdd:prd. All deferred forward.
- **Tone:** user profile `communicationStyle` is "Conversational and collaborative" — applied throughout.

### Phase 1 — Persona framing (closed)
- **4-persona model accepted unchanged.** Plugin user (default for most stories), v1 user (migration stories only), new SDD user (onboarding stories where orientation matters), plugin maintainer (verification epic only).
- **Rationale:** Audience is largely homogeneous (developers using SDD). Splitting Jason vs. tech-group at story level would over-specify; story behavior is the same. Maintainer persona is real for verification, since /v2-verification.md is not a user-project artifact.

### Phase 1 — Epic structure (closed after correction)
- **First proposal: 12 epics including "/sdd:prd command (new dedicated requirements step)".** Wrong framing — replayed the intermediate-rename confusion.
- **User correction (critical):** /sdd:prd is "unchanged in name and intent" per the post-wrap scoping framing. The genuinely new command is /sdd:discovery, which was missing entirely. Treating /prd as new is the same mistake the post-wrap correction fixed.
- **Corrected list: 13 epics.** Slot 2 became /sdd:discovery. /sdd:prd's v2-specific behaviors (AC quality check, epic-count monitoring, Unvetted section, phase-split recommendation) folded into "Interview command behaviors" epic as /prd-specific stories.
- **Phase-split recommendation surfaced (13 > 10 threshold).** Two reads offered: (a) split into command-level / workflow polish / infrastructure cycles, (b) keep one cycle, accept the over-threshold count. User picked (b). Rationale: epic count crosses threshold but complexity doesn't — many small refinement-shaped items, not several large independent ones. Phase-split would fragment a coherent streamlining pass.
- **Final 13 epics:** Bug Fixes / /sdd:discovery / Multi-Session Resume / Interview Command Behaviors / Context Management / /sdd:onboard Polish / Sprint Loop / /sdd:retro at Project Close / Cross-Project Feedback Transfer / Automatic Backlog Generation / Process Notes Growth / v1→v2 Migration / v2 Release Verification.

### Bug Fixes epic (closed)
- **Bug Fix #1 reframed.** Scope's literal wording — "/sdd:feedback no longer clobbers lastCommand when the previous value was already /sdd:feedback" — was traced and shown not to solve the stated problem. Trace: first /sdd:feedback writes /sdd:feedback to lastCommand (the skip rule doesn't apply since prev was /sdd:scope), so the second /sdd:feedback still reads /sdd:feedback as "After command." Doesn't fix the back-to-back case the original feedback note describes.
- **Cleaner fix locked instead:** /sdd:feedback never updates lastCommand. Same lines of code, narrower behavior, fully resolves the stated bug.
- **Bug Fix #2 (PRD state-tracking for non-split sprint items)** locked as-scoped. Cross-cutting story partial-checkoff explicit in AC.
- **Wrap note:** scope.md's bug-fix-#1 wording is now stale and should be updated at PRD-generation wrap to reflect the cleaner fix.

### /sdd:discovery epic (closed after correction)
- **Soft-scope handoff story dropped (user-caught).** Originally Story 4 had /sdd:discovery write a soft draft of docs/scope.md alongside docs/discovery.md. User pointed out: that move only made sense back when /scope was absorbing scope+PRD work. With /prd back as a dedicated step, /scope's load is smaller and doesn't need a pre-draft handoff.
- **Wrap notes for PRD generation:** scope.md's artifact lineage table row for docs/scope.md ("`/discovery` (soft) → `/scope` (hard)") is now stale — collapse to "`/scope`". Soft-vs-hardened marker concern (which Story 4 AC had deferred to /spec) goes away entirely; nothing to carry forward.
- **Final 4 stories:** Exploration interview at project start / File uploads as discovery context / discovery.md as orientation artifact / Right-size detection origin.
- **discovery.md freeze trigger** reworded: was "frozen at /scope start" via soft-scope hardening; now "frozen once /sdd:scope has written docs/scope.md" (decoupled from the dropped soft-scope mechanism).

### Multi-Session Resume epic (closed)
- 3 stories: /pause captures in-flight state / /unpause auto-resumes / Resume-file cleanup on completion.
- **Auto-mode reasonable-assumption picks (called out to user):**
  - /sdd:discovery re-runs require explicit user confirmation (not blocked).
  - /sdd:pause overwrites an existing resume file (not appends, not refuses).
- Pause distillation quality bar deferred to /spec (matches existing open concern).
- Schema locked from dogfooded shape: Status / Setup-already-done / Confirmed decisions / Round status / Pending decision / Files for next-session / Instructions for next-session.

### Interview Command Behaviors epic (closed)
- 8 stories total. Meta-statement at epic intro: stories apply to all 7 interview commands (/discovery, /scope, /prd, /spec, /plan, /refine, /polish) unless otherwise noted.
- 5 cross-cutting stories: clarifying questions don't advance / 5-suggested-10-hard-cap question rule / definite end-of-round recommendation with topic list / right-size detection applies during interview / encourage user input throughout.
- 3 /prd-specific stories: AC quality check (specific/observable/verifiable) / phase-split recommendation at 10+ epics / Unvetted section creation.
- **User refinement on Story 3:** end-of-round reasoning can include a list of items either way — required preview when recommending continue, optional list when recommending close to justify why the items don't warrant a round.

### Context Management epic (closed)
- 2 stories: three-tier between-rounds recommendation / default-/clear between commands.
- Tier signals (round length, artifact-vs-conversation coverage, noise ratio) deferred to /spec. End-of-command handoff wording deferred to /spec. Both match existing open concerns.

### /sdd:onboard Polish epic (closed)
- 7 stories, one per scope bullet.
- **Sprint-mode memory item relocated.** Scope listed it under "/sdd:onboard polish" but the behavior is on /sdd:plan. Folded into the Sprint Loop epic (Story 4: /sdd:plan offers sprint-mode memory) where it logically belongs.

### Sprint Loop epic (closed)
- 7 stories: /plan reads prior process notes / /plan PRD state check / /plan next-command recommendation / /plan sprint-mode memory (relocated from /onboard polish) / /build wrap-up satisfied prompt / /build close-sprint behavior / /build re-open escape hatch.
- /build re-open mechanism details deferred to /spec (PRD checkoff reversal, process-notes annotation).

### /sdd:retro at Project Close epic (closed)
- 2 stories: fires only at project close (recommendation-timing) / cross-project pattern capture.
- **Interpretation choice flagged to user (not pushed back):** scope says "Fires only at project close (when all PRD criteria checked)." Read as recommendation-timing (upstream commands suggest /retro only when criteria met) rather than a hard internal gate. Rationale: users wanting to retro a paused/abandoned project shouldn't be blocked. User did not push back.

### Cross-Project Feedback Transfer epic (closed)
- 3 stories: dual-write to local and forwarding path / inside-clone detection / failure handling on invalid feedbackLocalPath.
- **Remote-control intervention confirmed:** feedbackLocalPath is the **root of the local plugin clone**, not the full path to the feedback file. Encoded in scope and reflected in AC (`<feedbackLocalPath>/docs/sdd-feedback.md`).
- Path-comparison normalization (case sensitivity by OS, trailing slash, symlinks) deferred to /spec.

### Automatic Backlog Generation epic (closed after near-miss)
- 3 stories: user-confirmed deferral writes / entry schema / in-flight planning docs reference not restate.
- **Story 4 (agent recommends backlog candidates) added during remote-control intervention, then dropped after user-prompted pros/cons review.** Story 4 was in tension with Round 1 Topic 4 closure ("user picked this over agent-detected-automatic; lower automation, no surprise entries"). Even with confirmation gate, agent-initiated recommendations nudge toward higher automation than the locked model. User chose to honor the earlier closure. Story 1 + user's ability to say "defer X" anytime covers the engaged-user case.

### Process Notes Growth epic (closed)
- 3 stories: planning notes split per phase / /retro reads selectively / no within-phase compaction.

### v1→v2 Migration epic (closed)
- 6 stories: migration doc with honest framing / state-aware migration table / rename pair + resume-file mapping / legacy process-notes.md and v1 archive handling / new v2 capabilities introduced / atomic rename with full sweep.
- Originally drafted as 8 stories; consolidated to 6 by merging pause+feedbackLocalPath intros into "new v2 capabilities" story.
- sdd-plugin's own self-migration: not a PRD-level story; runs the standard guide; meta-case noted in process notes only.

### v2 Release Verification epic (closed after lifecycle addition)
- 4 stories: docs/v2-verification.md populated by implementing commands / manual checkoff by maintainer / verification approach documented / **lifecycle and persistence (added via remote-control intervention)**.
- **Story 4 (lifecycle) added** to clarify: file is sdd-plugin-specific (no creation in user projects); implementing-command write fires only when the file already exists in the project's docs/; file persists as historical record after v2 ships (no automatic deletion); future versions follow same out-of-pattern allowance.
- Story 1 AC 1 sharpened: file exists before v2 implementation begins, seeded with one row per in-scope feedback item.

### Phase 1 close
- All 13 epics drafted in-conversation. Approximate total: 52 stories.
- Offered Phase 2 deepening rounds via standard pattern. User opted in to **one round, scoped tightly to two topics**: (a) PRD-AC matching mechanism for Bug Fix #2 (how does /sdd:build figure out which PRD AC a sprint item maps to?) and (b) /sdd:build's "this came up during execution" testable bar for legitimate iteration candidates.
- Round 1 paused before opening any questions — user moving to a fresh session for the round.
- Resume file written: `docs/prd-resume.md`.

### Phase 2 Round 1 (resumed in fresh session)

Resumed via `/sdd:prd` re-invocation. Resume file (`docs/prd-resume.md`) was load-bearing — Phase 1 state was complete and intact. Confirmation message kept brief per resume-file instructions; did not re-explain Phase 1.

**Topic A — PRD-AC matching mechanism for Bug Fix #2**

- **Question 1** posed (suggested wording from resume file): explicit machine-readable refs / heuristic text-matching / user-confirmed manual mapping / something else.
- User asked for agent's opinion before answering. Agent gave full take recommending **explicit refs at `/sdd:plan` time with an explicit `[unmapped]` allowance**, with reasoning (Bug Fix #2 is explicitly about state-tracking drift → fix should be deterministic; heuristic matching reintroduces the kind of drift the fix is meant to eliminate; manual mapping moves friction into wrap-up which v2 is trying to streamline; unmapped allowance covers scaffolding/refactor items; heading-stability concern is already locked, doesn't add new fragility). Held position on the original question per "clarifying questions don't advance" rule.
- User agreed with the take. Locked: **explicit refs at `/plan` time + explicit `[unmapped]` tag for items that don't map.**
- **Real-time refinement raised by user:** cross-sprint AC coverage case — sprint 1 has 2 items contributing to PRD AC X, sprint 2 will have a 3rd. Will we need to "reopen" a closed PRD item? How do we know more items are coming?
- Agent walked the case through:
  - Cross-sprint coverage is already in the locked scope (Story 2 AC 3 about "when the final sprint item contributing to a cross-cutting PRD AC completes, that criterion gets checked").
  - Mechanism handles it natively: wrap-up logic queries present state across all sprint files; doesn't need to predict the future. AC stays unchecked iff any item across any sprint file referencing it is still incomplete.
  - True "reopen" only arises if sprint 1 items fully satisfy AC X as written, then sprint 2 discovers AC X needs more work. That's a PRD-evolution case → Unvetted + `/sdd:refine` handles it. No special "reopen" mechanism needed.
- Surfaced AC wording fix: Story 2 AC 2's "other still-incomplete sprint items" is ambiguous (other-in-sprint vs other-across-all). User approved sharpening to "any sprint item across any sprint file." **Locked.**
- **Question 2** posed: should `/sdd:plan` actively enforce ref presence (hard refusal to finalize without refs) or soft prompt (agent asks "intentional?" but user can proceed)? Agent gave preference for hard enforcement with reasoning (deterministic by construction; missing refs never reach `/build`).
- User: "I think I like the harder enforcement." **Locked.** New Sprint Loop story added: `/sdd:plan` validates every sprint-item line carries either a `[PRD: ...]` ref or `[unmapped]` tag; refuses to finalize the sprint file until validation passes.
- New open concern flagged: exact tag syntax → `/sdd:spec`.

**Topic B — `/sdd:build`'s "came up during execution" testable bar**

- **Question 1** posed: how do we make "came up during execution" testable? Three options offered with agent's preference for **option 1 (in-flight tagging)** — agent writes structured marker (`[iteration-candidate: ...]` or equivalent) into `process-notes-sprint-N.md` at the moment of observation; wrap-up reads only tagged entries. Reasoning: deterministic input for wrap-up; keeps friction off user during execution; testable bar is "marker exists before wrap-up began."
- User: "I agree with your assessment. Option one." **Locked.** Sprint Loop Story 5 (now Story for `/build` wrap-up satisfied prompt) AC tightened with marker mechanism.
- New open concern flagged: exact iteration-candidate marker syntax → `/sdd:spec`.
- **Question 2** posed: what happens to tagged candidates when user picks "satisfied" while candidates are still outstanding? Three options offered with agent's preference for **option 1 (resolve each candidate explicitly: discard / capture as tech-debt / actually iterate)** — eliminates orphaned candidates, complements "never fabricates" with "never orphans," self-limits because the number of candidates is bounded by what the user explicitly flagged in-flight.
- User: "option one." **Locked.** Sprint Loop story for `/build` wrap-up extended with disposition behavior: each surfaced candidate gets an explicit user disposition before close-sprint actions begin. "Actually iterate" → switch to iterate branch. "Capture as tech-debt" → record in `process-notes-sprint-N.md` during close-sprint. "Discard" → record discard decision.

**End-of-Round 1 recommendation**

- Definite recommendation: "I do not think we need another round, because [reasoning + list of topics a Round 2 would have covered and why each is spec-time]." Listed: pause/unpause edge cases, right-size hint lifecycle, v1 `/sdd:feedback` migration state mapping, backlog entry "Source" field structure. Each is spec-time per existing open concerns or already-deferred AC.
- User pre-scoped tightly because most concerns were already known to be spec-time. Closure was the natural call.

**Document generation**

- Read `skills/sdd-guide/templates/prd-template.md`. Read `docs/backlog.md` (3 entries — v3-deferred items) to confirm "What We'd Add With More Time" should be a pointer, not inline restatement (per Automatic Backlog Generation Story 3).
- Applied AC quality check (specific / observable / verifiable) to every AC during write. Notable rewrites at generation time:
  - Bug Fix #2 Story 1 AC 1 reframed from "/sdd:feedback's startup sequence... no lastCommand update step" to a behaviorally-observable check on the sequence content (the prior wording referenced internal implementation, not observable behavior; rewrote to make the absence directly observable via file contents).
  - Discovery Story 1 AC 1 reframed from "runs only after `/sdd:onboard`" to a concrete observable behavior ("exits with message and writes nothing when `~/.claude/sdd-user-profile.json` doesn't exist").
  - Pause Story 1 ACs sharpened from "runs only when" (intent) to "proceeds with its work only when" / "writes no resume file and exits when" (observable outcomes).
  - Multiple "is presented" / "is enforced" passive phrasings rewritten as concrete observable artifacts (file contents, message text, command output).
- PRD written to `docs/prd.md`.
- Three scope.md wrap-time corrections applied: Bug Fix #1 wording cleaned to "never updates lastCommand"; artifact lineage table `docs/scope.md` row collapsed to `/scope` end-to-end (soft-scope dropped); sprint-mode memory bullet moved from `/onboard` polish to the sprint loop section with note explaining the move.
- Two new open concerns appended to `docs/open-concerns.md`: PRD-AC reference tag syntax; iteration-candidate marker syntax. Both target `/sdd:spec`.
- Resume file `docs/prd-resume.md` deleted.
- Next: user runs `/clear`, then `/sdd:spec`.
