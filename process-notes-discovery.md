# Process Notes — /sdd:discovery (v7 cycle)

## Ingest
- Decision: transcribed `SDD plugin update ramblings .m4a` myself via faster-whisper (distil-large-v3) per user's explicit instruction; existing `.txt` kept as backup/cross-check.
- My transcription matched the backup. One meaningful correction: audio says "make the prototype **optional**" (backup), not "an opportunity" (my first pass) — context confirms "optional."

## Interview
- Surfaced version-scheme drift: plugin.json semver (4.0.0) vs. project-state `cycleNumber` (7) / archive dirs v1-v6. Two schemes never matched, now visibly diverged. No git tags; marketplace.json has no version field.
- Decision: collapse to ONE scheme — plugin **semantic version**. Drop the cycle-number scheme.
- Open thread: user asked whether this should become a **plugin-wide standard** (SDD versions every project it manages by semver, replacing the `cycleNumber`/archive machinery) vs. a one-off fix for this repo.
- RESOLVED: keep the two schemes separate. No machinery change. "Version 4" = plugin semver (4.0.0); this work bumps to 4.1. `cycleNumber` stays a separate dev-loop counter. Action = document the distinction so it stops confusing; bump plugin.json to 4.1 when this ships.

- **Prototype optional (2nd memo).** Decision: prototype is skippable. When the user knows a job doesn't need it (text edits, small tweaks, existing working system), skip straight to tickets/build. No extra interview, no gate, no confirmation — user's call when they make it. NOTE: mechanically build already only requires plan.md ≥1.0 (does NOT require prototype), so refine→build already runs; the gap is framing/handoff wording, not machinery.
- **Pushback moment:** I over-engineered the prototype-optional question (asked about "reality-check gate" / when-to-trigger) — user was already explicit. User frustrated. Corrected course, captured plainly. Lesson: when the memo already answers it, confirm and move — don't relitigate with design theory.

- **Flow audit (user asked me to double-check the whole chain against installed 4.0.0):**
  - Onboard, discovery→draft(0.1), refine→v1.0, validate(skippable), issue-creation, build-loop all present.
  - Refine = marker walk (interview-driven, one marker at a time), not open "rounds" — matches intent, different framing.
  - **Issue creation timing (memo-1 question ANSWERED):** issues created at START of /sdd:build Step 1, lifted from refined plan.md, shown to user for confirm before creation.
  - **Prototype happy-path walkthrough = PARTIAL.** navigation-paths.md + a review step exist, but AI does the walk and reports; the interactive "AI steps user through each click, user confirms pass/fail/change per step" loop is NOT specified. User's instinct correct — it's thin.
  - **Build granularity MISMATCH.** Current = one issue→one branch→one PR, parallel worktrees. User's older mental model = milestones grouping, branch/PR per milestone. User flagged as undecided ("figure that part out"). OPEN DECISION.
  - **/clear NOT removed** — still in every interview handoff. User believed it was decided against; that decision never landed. Needs user ruling: still want it gone?
  - **"Context checker" not found** by that name; there's /sdd:checkpoint (emits /compact). Need user to clarify what it was.

- **Resumability / idempotency (big finding).** Cold re-run of prototype or build REDOES work — prototype rebuilds from scratch; build re-proposes issues and creates DUPLICATES (no dedup vs existing sdd-labeled issues). Only resume path is explicit /sdd:pause→/sdd:unpause (needs foresight). 
  - DECISION: make commands **progress-aware, detect-and-auto-advance** on cold re-run. Detect existing artifacts (prototype/ dir, sdd-labeled issues), jump to next incomplete step, create only genuinely-new work. No prompt.
  - ASSUMPTION to resolve in refine: "already done" is clean for build (issues exist or not) but fuzzy for prototype when plan.md changed since build — auto-advance could show a stale prototype.

- **/clear removal.** DECISION: remove /clear entirely from all handoffs. Replacement = nothing about context; handoff just says "Run /sdd:[next] to continue." Consequence: retire the first-handoff /clear explanation block AND the `handoffWarningShown` tracking (profile + SKILL.md) — both obsolete once /clear is gone.
- **Build granularity.** DECISION: default per-issue (one issue→branch→PR, unchanged), with **opt-in milestone grouping** — user assigns issues to a milestone, grouped issues build together under one branch + one PR per milestone. ASSUMPTION for refine: mechanism = GitHub-native milestones, declared/assigned at the issue-confirm step in build Step 1; stamping + resolve-pr recognition extend to milestone branches.
- **Side Q (git hygiene):** answered — yes, delete merged PR branches; /sdd:resolve-pr automates this for sdd-stamped branches. Not a plan item.
- **Prototype walkthrough.** DECISION: two-phase, additive. Phase A = keep existing AI review (AI walks pages vs plan, reports matches/divergences/gaps). Phase B (new) = interactive human walkthrough: AI steps user through each navigation path, user clicks + reports pass/fail/change per step. Issues fixed LIVE; re-walk path or restart a step to confirm the fix landed before continuing.

## Deepening Round 1 (3 questions)
- smallProject judged **false** (multi-command, cross-cutting changes). Standard cadence.
- **Q1 Resumability scope.** DECISION: detect-and-auto-advance applies to **prototype + build only**. refine/validate already resume via the live document (markers/differences persist). discovery relies on **pause/unpause** — acceptable, auto-detecting interview progress is genuinely hard.
- **Q2 Lo-fi + walkthrough.** DECISION: Phase B applies to **both fidelities**. Hi-fi = click through; lo-fi = AI steps through screens in order, user confirms by eye. Live fixes + re-walk identical either way.
- **Q3 /clear ripple.** DECISION: /clear gone from handoffs; **/sdd:checkpoint stays unchanged** as the on-demand context tool. Context management not pushed after every command, but still reachable.
- Round closed on close-recommendation (accepted). Remaining detail (milestone mechanism, stale-prototype edge, issue-detection precision) parked as markers for refine.

## Draft
- Wrote docs/plan.md v0.1. Six epics: optional prototype, progress-aware commands, /clear removal, milestone grouping, interactive walkthrough, version-scheme clarity. 23 ACs.
- Markers: 3 assumptions (a1 issue-detection=sdd-label, a2 milestone=GH-native, a3 doc location), 1 gap (g1 stale-prototype on plan change, PROPOSED version-compare), 1 concern (c1 handoffWarningShown profile back-compat).

## Post-draft addition
- User surfaced 7th requirement AFTER draft: marker **deferral** must be maintainer-only. Current contract already blocks AI-initiated *resolution* (markers.md), but refine Step 2 lets the AI *carry forward* on its own judgment ("stall," "downstream"). Gap confirmed.
- DECISION: refine attempts every marker; carry-forward only on explicit maintainer call; AI never initiates deferral. Added Epic "Marker deferral is the maintainer's call only" (mdfa-mdfd). Impact adds markers.md + refine Step 2.
- Re-draft in same maturity → version bumped 0.1 → 0.2.

## Post-finalize amend session (manual, dogfooding the proposed command)
- User re-ran /sdd:discovery but declined overwrite (plan already finalized at 1.0). Wanted to ADD items, not restart.
- **Design gap surfaced:** no command adds net-new items to a finalized plan. refine = narrow marker-walk (doesn't ingest scope); discovery = overwrite-from-scratch. DECISION: new command **/sdd:amend** (user corrected spelling from "ammend" → "amend"). Shape: requires existing plan, short data-first interview, appends marked epics/ACs without touching existing content, minor-version bump, hands off to refine. Anytime utility.
- Added Epic "/sdd:amend" (amna-amnf) directly to plan.md (manual amend — no command exists yet). Bumped plan 1.0 → 1.1. New markers: a1, a2, a3 (assumptions), g1 (gap: enumerate command-set locations).
- User has MORE items to add this session — continuing in the same 1.1 before refine.
- **AC checkboxes.** User noticed plan ACs have `[ ]` boxes never checked (v6 dropped v5 PRD-checkoff; boxes left vestigial). Options weighed: remove / check-at-creation / check-at-merge. I surfaced that check-at-creation makes "checked" mean "ticketed" not "done" (misleading). DECISION: **remove checkboxes** — plan = spec, GitHub = tracking; avoids drift. Added Epic "Remove acceptance-criteria checkboxes" (acka-ackd), marker a4 (don't retroactively rewrite other plans). Still 1.1.
- **Retro global promotions via PR (last item).** Applies user's PR-only rule to ~/.claude. Retro batches global promotions, opens ONE PR at retro end against the global repo; conditional on global dir being a git repo w/ remote + gh auth, else falls back to direct edit + notice. Added Epic "Retro promotes global learnings through a PR" (rgpa-rgpd), markers a5, a6, g2. Still 1.1.
- Amend session complete: 4 new epics (amend, AC-checkbox removal, version-scheme was pre-existing; plus retro-PR), plan 1.0 → 1.1. Open markers: a1-a6, g1-g2 → refine walks these next.
