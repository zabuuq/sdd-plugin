# Process Notes — Sprint 1 (v5 cycle)

## Decisions

- **smallProject re-evaluation:** confirmed `true` on `/sdd:plan` startup. v5 is a single-command extension (refs handling in `/sdd:archive`), solo maintainer, markdown-only, no new components or integrations. Re-eval is a no-op; value unchanged.
- **Open concern resolved at plan:** "INDEX reference-files group ordering" (targeted /sdd:plan). Confirmed the `reference files:` bullet sits with the other swept-content bullets and before the `project-state.json` / living-doc snapshot bullets. Cosmetic, reads cleanly. Folded into the single sprint item rather than a separate item.
- **Open concerns deferred to /sdd:build:** numbered-list/selection-prompt wording and leftover empty `docs/refs/` directory — both are build-time prose details already pinned by the spec; no planning decision required.
- **Sprint mode:** resolved to `autonomous` from `defaultSprintMode` in `~/.claude/sdd-user-profile.json` (resolution rule 1 — no fresh ask). Toggle offered as confirmation (field already set); user chose "keep" → no profile change. `buildMode` already `autonomous` in project-state.
- **Sprint grouping:** proposed 4 items along the spec's pipeline seams — (1) detection/listing/parse [rfsa–rfsd], (2) plan-gate + move + INDEX + git staging [rfse–rfsi], (3) inert-path preservation [bpra–bpre], (4) version bump [unmapped]. All 14 PRD ACs covered; every AC has a named spec section (no gaps). User accepted the grouping as-is and confirmed all items in one sprint. One sprint for the whole v5 cycle, consistent with smallProject and the v4 precedent (single self-contained SKILL.md edit).
- **No deepening round.** smallProject default-to-close held — grouping is clean, dependencies linear, ACs testable verbatim, verification strategy concrete. No concrete contradicting evidence surfaced. Context recommendation: continue (light session). User chose to generate directly.
- **Verification approach:** kept the spec's per-item verification (structural `claude plugin validate .` + disposable-branch fixture) inside each item rather than a separate dedicated verification item; item 3 carries the inert-path hard line explicitly. The real dogfood run is the end-of-v5 `/sdd:archive` itself.

## Pushback

- User asked mid-planning whether any unchecked PRD items were being left out of the sprint. Confirmed all 14 ACs are in (4 + 5 + 5) and Unvetted is empty — nothing parked outside the sprint. Treated as a clarifying question; interview pointer not advanced.

## Difficult Questions

## Pivots

## Wrap-Up (close-sprint)

**Build execution (autonomous mode).** All 4 sprint items built in a single autonomous run, one subagent per item, strictly sequential because items 1–3 all edit the same file (`plugins/sdd/skills/archive/SKILL.md`). No errors, no Error-Handling stops, no deviations from the spec or sprint plan. `claude plugin validate .` passed after the behavior change (items 1–3) and again after the version bump (item 4).

**Decisions during the sprint:**
- **No auto-commit by subagents.** The autonomous-mode SKILL.md instructs subagents to commit when done, but this project's `AGENTS.md` explicitly forbids auto-commit during `/sdd:build` ("Clean state = last user commit"). Project convention won — subagents were instructed not to commit or run git. Build leaves all changes staged-by-nobody for the user to commit.
- **Execute step renumbering.** Item 2 inserted the refs move as a new Execute step 4 and renumbered INDEX generation 4→5, updating every step-number cross-reference in the file (Execute order summary, the `lastCommand` deferred-write paragraph). Chosen over a "3.5" sub-step for clean reading; verified no stale references remain.
- **Version bump = 3.2.0 (minor).** Strictly additive, backward-compatible feature; matches the v4 pattern (minor bump when a new command was added). `marketplace.json` needs no edit — version is single-sourced in `plugin.json` via `source`. User chose 3.2.0 from the offered options.
- **Open concerns resolved at build.** Both `/sdd:build`-targeted concerns (numbered-list/prompt wording; leftover empty `docs/refs/` directory) resolved — the SKILL.md prose adopts the spec's illustrative list format and explicitly encodes "leave the empty directory in place." Moved to Resolved in `docs/open-concerns.md`.

**Pushback:** none during the build. The one checkpoint (after items 1–3, the full behavior change) passed without change requests.

**Surprises:** none. The spec's pipeline-seam decomposition held — each item integrated where the spec said it would, and the local inertness notes from items 1–2 were already consistent with item 3's consolidated Behavior Preservation section (item 3 needed only one corrective touch-up: a short-circuit-ordering note at the nothing-to-archive exit).

**Notable (W8 response):** nothing notable — user answered "no."

**Tech-debt entries:** none.

**Iteration-candidate dispositions:** none surfaced (W1 found no `[iteration-candidate]` markers).

[close-sprint-manifest] timestamp: 2026-06-22T00:00:00Z
- PRD ACs checked: rfsa, rfsb, rfsc, rfsd, rfse, rfsf, rfsg, rfsh, rfsi, bpra, bprb, bprc, bprd, bpre
- Story splits: (none)
- Iteration candidate dispositions: (none)
- Tech-debt entries: 0 (informational)
- v2-verification rows updated: (none)
[/close-sprint-manifest]
