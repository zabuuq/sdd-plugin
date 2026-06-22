# Project Retrospective

## Project Metadata
- **Project:** sdd-plugin — v5 cycle (`/sdd:archive` refs selection)
- **Total sprints:** 1
- **Date range:** 2026-06-17 to 2026-06-22 (v4 archived 2026-06-17; v5 sprint closed 2026-06-22)
- **Date of retro:** 2026-06-22

## Process Patterns Across Sprints

### What the Process Notes Revealed
A uniformly clean cycle. Across scope, PRD, spec, and the single sprint, the notes show the same shape repeating: `smallProject: true` re-confirmed at every phase startup (always a no-op), early deepening-round closure on the smallProject default, terse and directional user answers, and no pushback or pivots. The design was pinned in scope (per-item selection over whole-directory sweep) and never wavered — PRD, spec, plan, and build each integrated exactly where the prior phase said they would. The only mid-stream decisions were narrow "how" details (two-step plan gate, reply-parsing syntax, dotfile handling, git staging), each resolved into existing archive mechanics without surprise.

### Planning Phase Assessment
Planning set the project up well. Scope: 0 deepening rounds. PRD: 0 rounds, 2 epics / 5 stories / 16 ACs. Spec: 1 round (3 questions on ref-file definition, "left in place" interaction, git staging), all resolved cleanly. The lean planning was right-sized to a single-command, markdown-only extension — nothing turned out underspecified once the SKILL.md edit began (user: "everything worked"). No over-specification either; the spec held its command-extension-design-doc posture and didn't duplicate reference-file content. Notably, **no PRD divergence** this cycle — unlike v4's allow-list/deny-list split, v5 implemented the PRD ACs verbatim, so nothing routed back to `/sdd:refine`.

### Sprint Cadence
One sprint for the whole cycle, consistent with smallProject and the v4 precedent (single self-contained SKILL.md edit). Four items grouped along the spec's pipeline seams (detect/list/parse → plan-gate+move+INDEX+git → inert-path preservation → version bump). Built in a single autonomous run, one subagent per item, strictly sequential because items 1–3 edit the same file. No errors, no Error-Handling stops, no checkpoint change requests. Cadence was right — the user confirmed "smooth" across the entire process arc.

### Workflow Friction
Effectively none in practice. The one structural friction point: the autonomous-build SKILL.md instructs subagents to commit when done, but this repo's `AGENTS.md` forbids auto-commit during `/sdd:build`. The project convention won (subagents didn't commit), but only because the maintainer knew the override existed. **The user's position: the plugin should defer to project conventions automatically** rather than relying on the maintainer to know `AGENTS.md` overrides the built-in behavior. This is the cycle's clearest plugin-improvement candidate. It caused no friction this time but is latent risk for any future autonomous build in a repo with its own commit discipline.

### Communication Patterns
Worked as the user wanted ("smooth"). Terse, directional answers throughout; the agent adapted by right-sizing the interviews and not belaboring a clean cycle. No back-and-forth misfires noted.

## Product Assessment

### Outcome vs. Intent
Matches intent fully (user: "it matches"). The feature delivers exactly what scope set out: per-file maintainer control over `docs/refs/` at archive time — flattened numbered listing, two-step plan gate with a single confirmation, selected refs moved into `docs/archive/v{N}/refs/` with relative paths preserved, unselected refs left live, and complete inertness when `docs/refs/` is empty/absent or the sweep set is otherwise empty.

### Scope Changes
None. Nothing added, cut, or changed after scope. The Unvetted section stayed empty; no backlog deferrals this cycle.

### Quality Assessment
High on structural and behavioral grounds: all 16 ACs checked, `claude plugin validate .` passed after the behavior change and again after the version bump, no stale step-number cross-references after the Execute-step renumber. **One standing caveat:** the feature has not had a live dogfood run against an actual populated `docs/refs/`. Its first real exercise will be the next genuine `/sdd:archive` run with reference files present. Not a defect — unverified in the wild.

### Technical Debt
None. No tech-debt entries logged in the sprint; the autonomous build reported zero debt.

## SDD Workflow Performance

### What Worked
- **`smallProject` right-sizing** carried the whole cycle — lean planning, early round closure, single sprint — without skimping on the design.
- **Spec pipeline-seam decomposition** held end to end: every sprint item integrated where the spec predicted.
- **Two-step plan gate** design preserved the single-confirmation contract while adding the refs selection step — clean reconciliation of a new interaction with an existing guarantee.
- **PRD ACs implemented verbatim** — no divergence, no `/sdd:refine` round-trip.

### What Didn't Work
- **Autonomous-build vs. project convention conflict** — the built-in "subagents commit when done" instruction collides with a repo's own `AGENTS.md` commit discipline, resolved only by maintainer knowledge. Should be automatic deference to project conventions.

### Workflow Recommendations
1. Make `/sdd:build` detect and defer to project-convention files (e.g., `AGENTS.md` no-auto-commit) automatically, instead of relying on the maintainer to know the override.
2. Schedule a live dogfood of the refs-selection feature against a populated `docs/refs/` at the next real archive, to close the in-the-wild verification gap.

## Plugin Feedback Summary

`docs/sdd-feedback.md` exists but **no new feedback was logged during the v5 cycle** — the latest entries are from 2026-05-12 (v4 era); v5 ran in 2026-06. The file is the carried-forward cross-cycle log spanning multiple projects (Event Planning App, fiver-gigs, sdd-plugin).

### Feedback Themes
- **Right-sizing small work** — fast-path for simple sprints/projects. *Shipped as `smallProject`; drove v5's lean planning.*
- **Interview discipline** — clarifying questions must not advance the interview; deepening rounds should preview topics; encourage more user input. *Clarifying-question rule and deepening-preview now in sdd-guide.*
- **Chain structure** — rename scope→discovery / prd→scope; add discovery, env-setup, `--help`, `/sdd:backlog`, `/sdd:viability`. *Discovery, pause/unpause, and backlog automation shipped; `--help`, env-setup command, `/sdd:backlog`, `/sdd:viability` remain open.*
- **State/transport bugs** — `/sdd:feedback` clobbers `lastCommand` (loses "after command" context on back-to-back entries); cross-project feedback transfer; `/sdd:reflect` doesn't check off PRD criteria for whole (non-split) sprint completions, causing PRD/sprint drift. *All still open.*
- **Multi-session resume & file uploads** — first-class resume support (shipped as `/sdd:pause`/`/sdd:unpause`); file uploads during scope (open).

### How to Act on Feedback
Already in the plugin's own repo — no directory switch needed. Start a new SDD cycle with `/sdd:scope` (or `/sdd:prd`) and use the open `docs/sdd-feedback.md` entries as planning input. The highest-value open items: automatic deference to project conventions in `/sdd:build` (surfaced this cycle), the `/sdd:feedback` `lastCommand` clobber, and the `/sdd:reflect` PRD-checkoff gap.

## Open Concerns — Final Status

### Resolved During Retro
- (none — all concerns were already resolved before retro)

### Carried to Retro (Unresolved)
- (none)

### Previously Resolved
- **Numbered-list / selection-prompt wording** — resolved at `/sdd:build`; SKILL.md adopts the spec's illustrative numbered-list + numeric/keyword-reply format, flagged as a build-time authoring choice.
- **Leftover empty `docs/refs/` directory after a full selection** — resolved at `/sdd:build`; the command leaves the empty directory in place (acts on files, not the directory).
- **INDEX reference-files group ordering** — resolved at `/sdd:plan`; the `reference files:` bullet groups with the other swept-content bullets, before the snapshot/meta bullets.
- **INDEX.md enumeration of swept refs** — resolved at `/sdd:spec`; full per-file enumeration by flattened relative path, group emitted only when ≥1 ref swept, swept refs counted in the "Artifacts archived: N" total.
- **Refs prompt placement vs. the single-confirmation plan gate** — resolved at `/sdd:spec`; two-step plan gate with one confirmation (selection collected first, folds into the plan, single final confirmation). Reply-parsing syntax settled in the same pass.
- **Refs-only "nothing to archive" interaction** — resolved at `/sdd:prd` (ACs `bprd`, `bpre`); the empty-sweep-set exit wins; a populated `docs/refs/` alone is not "something to archive."

## Key Takeaways

### Top Wins
1. `smallProject` right-sizing delivered a complete, correct feature with minimal ceremony — lean but not thin.
2. Clean spec-to-build traceability: pipeline-seam decomposition meant every sprint item landed exactly where the spec said, with no surprises and zero tech debt.
3. PRD ACs implemented verbatim — no divergence, no refine round-trip, all 16 checked.

### Top Lessons
1. Built-in autonomous-build behavior can collide with project conventions; the workflow should defer to project-convention files automatically rather than rely on maintainer knowledge.
2. Structural/behavioral verification (validate + fixture branch) is not the same as a live dogfood — schedule the real-world exercise explicitly so it doesn't sit as a standing caveat.
3. A tightly-scoped, well-pinned design front-loads almost all the value: pinning per-item selection in scope made every downstream phase a no-drama integration.

### If Starting Over
Little to change for a cycle this clean. The one adjustment: fold a live dogfood of the new behavior (run `/sdd:archive` against a populated `docs/refs/` fixture) into the sprint's verification rather than leaving it for "the next real archive," so the feature ships verified in the wild rather than only structurally.
