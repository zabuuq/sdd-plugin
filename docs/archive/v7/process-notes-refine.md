# Process Notes — /sdd:refine (v7 cycle)

## Marker Walk
- smallProject re-eval on startup: still false. No-op.
- **a1 (dvaa) RESOLVED.** Issue-existence detection = `sdd` label + AC-ID referenced in issue body (reuses the link-back build already stamps). Not label-only, not title match.
- **g1 (dvae) RESOLVED.** Prototype stamps built-against plan version as a line in navigation-paths.md. On re-run: missing pages → build those; plan version newer → auto-rebuild affected pages (no prompt); versions match + all pages present → auto-advance to review. Only place a rebuild happens without asking — user chose auto over prompt.
- **c1 (clrc) RESOLVED.** Leave dead `handoffWarningShown` inert; no migration. Stray field ignored, never errors. No profile-schema migration ships.
- **a2 (mssb) RESOLVED.** GitHub-native milestones. Build groups by assignments present at loop time → assignable at confirm step (fresh) OR before loop on re-run. Reconciled with dvac: silent auto-advance suppresses only the re-create prompt, not the optional/skippable grouping offer. User wanted re-run grouping.
- **a3 (verb) RESOLVED.** Version-scheme note in AGENTS.md only (per house style: AGENTS.md = source of truth, CLAUDE.md = thin @import pointer). Impact map updated.

All 5 markers resolved. Zero carry-forward.

## Second walk (post-amend, plan 1.1, 8 markers)
- **a1 (amna) RESOLVED.** Amend widened: available any time on any existing plan, draft or finalized — NOT gated to ≥1.0. User: "amend should be available at any time... amend to discovery, amend to prototype." Version bump always single minor point (+0.1), never major (updated amnc too: e.g. 0.2→0.3, 1.1→1.2).
- **a2 (amnb) RESOLVED.** Considered killing amend entirely (user questioned need, thought re-running discovery/refine could append). Corrected premise: discovery OVERWRITES on re-run (the wall user hit); refine resolves markers, never authors scope. Neither appends new items. Amend's keeper case: scope surfaces LATE (during prototype/build) where re-running discovery = destructive restart. DECISION: keep amend, SIMPLIFIED — always discovery's mechanics, stage-independent, plan-items-only, never touches prototype/issues. Dropped the "inherit previous command's mechanics" idea (the source of the over-complication).
- **a3 (amne) RESOLVED.** Amend = anytime utility, chain-independent, alongside checkpoint/resolve-pr. Precondition = existing plan.md at ANY version (fixed "finalized" → "any version" per a1).
- **g1 (amnf) RESOLVED.** Enumerated command-set registration sites via Bash grep (Grep tool blind to .claude hidden path). Sites: amend/SKILL.md (new), living-documents.md (key set + reset list), sdd-guide/SKILL.md (chain + explanations + handoff out-of-pattern + next-command map amend→refine), project-state.json (new key, no migration), manifest (version only). User confirmed complete.
- **a4 (ackd) RESOLVED.** No retroactive migration of other/in-flight plans; only template + this repo's plan convert. Other projects keep boxes until next natural edit; AC-ID lifting unaffected.
- **a5 (rgpa) RESOLVED.** Global dir = ~/.claude (verified git repo: remote zabuuq/claude-config, default main). NEW behavior added by user: retro must ASK before committing to global repo — branch/PR flow fires only on affirmative; on decline, edits left uncommitted in working tree + retro says so (updated rgpa + rgpb).
- **a6 (rgpc) RESOLVED.** Fallback when global dir not a PR-able repo (no remote / gh unauthed / not git) = direct edit + notice "could not open PR", never hard stop, never lost promotion. No repo → nothing to ask.
- **g2 (rgpd) RESOLVED.** Detect remote + default branch at retro end (git symbolic-ref / gh repo view), branch off detected default — not hardcoded origin/main. Detection verified live against ~/.claude.

All 8 markers resolved. Zero carry-forward.
