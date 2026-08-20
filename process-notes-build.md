# Process Notes — /sdd:build (v7 cycle, SDD 4.1)

## Issue queue
- Plan `docs/plan.md` v1.2, 10 epics / 40 ACs. Prereqs pass: plan ≥1.0, git repo, gh auth (zabuuq), remote zabuuq/sdd-plugin.
- **Decision (issue granularity):** the 10 epics overlap heavily on shared files (sdd-guide, refine, discovery, build, prototype, living-documents) and the loop never merges mid-build, so epic-granular PRs would collide at merge time. User chose **4 file-disjoint issues** over 10 epic-issues or 1 combined PR.
- Partition (each file owned by exactly one issue): A=build subsystem (build/SKILL.md, build-loop.md), B=prototype (prototype/SKILL.md), C=refine+markers (refine/SKILL.md, markers.md), D=shared behavior (sdd-guide, onboard, discovery, validate, archive, plan-template, living-documents, amend[new], retro, AGENTS.md, plugin.json, this repo plan.md).
- Grounding checks: refine/SKILL.md has NO literal `/clear` (clra satisfied via sdd-guide template alone → D-only, no C conflict); handoffWarningShown lives in onboard/discovery/sdd-guide/archive (all D); refine owns its handoff wording (pqrb → C); pqrc → D (sdd-guide Command Chain is where optional/required is stated).
- Issues created: #18 (A), #19 (B), #20 (C), #21 (D), all `sdd`-labeled. AC text lifted verbatim minus the `[ ]` box (per ackc target behavior).

## Git-base decision
- Found: local `main` was stale (43 behind origin/main); the "catch main up" concern was a false alarm. `origin/main` (06a2f0d, "Merge PR #17 from archive-v6") already contains the full v6/4.0.0 baseline. No catch-up PR needed (GitHub rejected it as empty). Deleted the redundant archive-v6 branch I'd pushed. Fast-forwarded local main to origin/main.
- Build branches base on `main`, PRs target `main`, clean diffs.

## Issue #18 — build subsystem
- Edited build/SKILL.md (prototype-optional prereq; Progress-Aware Startup section; milestone grouping in Step 2; milestone offer at issue-confirm) + build-loop.md (Output/stamping rows note grouped builds).
- Separate-checker agent: PASS on all 10 ACs (pqra, pqrd, dvaa-dvac, dvaf, mssa-mssd), no contradictions. No retries needed.
- Commit 19fc8b5 → branch sdd/18-build-subsystem → PR #22.

## Issue #19 — prototype subsystem
- Edited prototype/SKILL.md: Progress-Aware Startup (detect prototype/, plan-version stamp in navigation-paths.md driving missing/affected/current re-run branches, silent) + version-stamp write in Step 2 + split review into Phase A (unchanged AI walk) and new interactive Phase B (step-through, live fixes, re-walk-to-confirm, both fidelities).
- Checker: PASS all 7 ACs (dvad, dvae, wlka-wlke); confirmed Phase A preserved (not replaced) and version-stamp write present. No retries.
- Commit a67f5a7 → sdd/19-prototype → PR #23.

## Issue #20 — refine + markers
- Edited refine/SKILL.md (Step 1 "Attempt every marker"; Step 2 carry-forward = maintainer-only, dropped AI-initiated triggers; PROPOSED never shelves; handoff prototype-optional) + markers.md (new "Carry-forward is the maintainer's call" resolve-loop rule).
- Checker: PASS all 5 ACs (pqrb, mdfa-mdfd) + regression check confirming the two old AI-deferral phrases now appear only in negated form in both files. No retries.
- Commit 99842d0 → sdd/20-refine-markers → PR #24.

## Issue #21 — shared behavior (largest slice)
- Edited 10 files + new amend/SKILL.md: sdd-guide (drop /clear template + first-handoff block + handoffWarningShown tracking; prototype-optional chain note; amend registration across chain/explanations/handoff-map), onboard (retire handoffWarningShown seeding), discovery (plain-bullet ACs + handoff), amend/SKILL.md (NEW), retro (global promotions via batched PR, ask-first, default-branch detection, fallback), archive (drop handoffWarningShown mention), living-documents (amend key + reset list), plan-template (plain-bullet ACs), plugin.json (4.1.0), AGENTS.md (versioning subsection). Converted this repo's docs/plan.md 44 ACs [ ]→plain bullets.
- Structural: `claude plugin validate .` PASS. Checker: PASS all 24 ACs (clra-clrd, amna-amnf, acka-ackb, ackd, rgpa-rgpd, vera-verc, pqrc); no handoffWarningShown seed/read/flip remains; no template checkboxes remain. No retries.
- Commit 6bbb240 → sdd/21-shared-behavior → PR #25.
- **Surfaced (not fixed, pre-existing):** archive's cycle-reset handoff still emits /clear (impact map excluded archive from clr*); AGENTS.md "Coding conventions" + living-documents "Update Ordering for /sdd:refine" carry stale v5 sprint/PRD checkbox formats. Flagged in PR #25 body for maintainer review.

## Completion
- 4 issues → 4 PRs (#22-#25), all open, zero merged (human gate). Zero retries across all four; every checker passed first pass. File-disjoint partition held — no cross-PR conflicts.
- Base note: origin/main already had the v6/4.0.0 baseline; local main was just stale. No catch-up PR. All build branches off main.
- Untracked planning docs (process-notes-*, docs/refs/) left in working tree; docs/plan.md captured into repo via PR #25 (ackd).
