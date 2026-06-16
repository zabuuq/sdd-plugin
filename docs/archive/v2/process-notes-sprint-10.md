# Process Notes — Sprint 10

## Sprint planning (2026-05-14)

### Context

Sprint 9 closed 24 ACs across the cross-cutting Interview Command Behaviors + Context Management epics. With that landed, the user asked "am I at a point with this project that I could upload it to repo main and install the new features?" The honest read: technically installable, substantively not v2 GA — `/sdd:discovery`, `/sdd:pause`, `/sdd:unpause` are explicit stubs; `MIGRATION-v1-to-v2.md` doesn't exist; zero rows in `docs/v2-verification.md` have a checked Verified box. Sprint 9 notes explicitly named the natural next-sprint candidates.

### PRD scan result

- **Incomplete:** ~108 ACs across 13 stories. Big-ticket epics still open: `/sdd:discovery` Command (10 ACs), Multi-Session Resume (17), v1→v2 Migration (21), Process Notes Growth (10), Automatic Backlog Generation (12), v2 Release Verification (~12 remaining).
- **Unvetted:** 0 (section present, empty). No `/sdd:refine` recommendation issued.

### Open concerns scan

One open concern remained at sprint planning: "Soft acceptance criteria for MIT distribution" — target is `/sdd:refine`, not addressable by sprint planning. Left in place; no new concerns surfaced during this planning session.

### Proposed grouping

Agent proposed `/sdd:discovery` command implementation (10 ACs `aaah`–`aaaq`) as Sprint 10. Rationale captured in the user-facing message:
- Sprint 9 explicitly unblocked it per AC `aaaj`
- Biggest user-facing gap in the v2 chain (currently a 29-line stub)
- Spec resolutions for all supporting mechanisms (`docs/refs/`, cross-cutting behaviors, handoff, three-tier context) already in place
- Self-contained: one command, mostly one SKILL.md file, one new template
- Closes the entire `/sdd:discovery Command` epic in the PRD (10 ACs)
- Tangible outcome: the v2 chain becomes runnable end-to-end through discovery → scope

Agent also surfaced why other candidates were second-tier for this sprint: Multi-Session Resume is larger and second-most-visible but discovery is the just-unblocked work; migration guide is mechanical single-file work suited for a later GA-prep sprint; right-sizing/backlog/process-notes are cross-cutting and benefit from `/sdd:discovery` landing first since they all touch it.

### User response

"works for me" — accepted the proposal without modification.

### Build mode

Agent recommended autonomous given the single-command scope, pre-vetted ACs, and well-specified spec resolutions. User chose autonomous.

### Deepening rounds

Agent recommended against another round with reasoning: pre-vetted ACs, spec resolutions in place, single-command scope, well-understood pattern. Topics a next round would have covered (and were deferred to `/sdd:build` implementation context rather than sprint-planning context):
- Phase 1 beat design specifics — what beats does `/sdd:discovery` use? Deferred to subagent implementation.
- `docs/refs/` mechanism specifics — per-file vs. directory listing, size limits.
- Cross-cutting behavior verification approach for Item 7.
- Story-split potential — concluded that the 7-item granularity is appropriate; sub-splitting fragments a single coherent command implementation.

User accepted: "proceed."

### Sprint file generation

Sprint file written at `docs/sprint-10.md`. Initial draft contained tag errors:
- Items 1, 2, 7 had `[PRD: ...]` `[unmapped]` co-tagged. Per CLAUDE.md and AC `aadw`, `[unmapped]` is the alternative to `[PRD: ...]`, not a co-tag. Corrected to `[PRD: ...]` `[FB: FB-003]`.
- Items 4 and 5 had non-standard `-adjacent` suffix on FB tags (`[FB: FB-003-adjacent]`, `[FB: FB-037-adjacent]`). Standardized to `[FB: FB-003]` and `[FB: FB-003, FB-037]`.

Final tag set:
- Item 1: `[PRD: aaah]` `[FB: FB-003]`
- Item 2: `[PRD: aaaq]` `[FB: FB-003]`
- Item 3: `[PRD: aaal, aaam, aaan]` `[FB: FB-013]`
- Item 4: `[PRD: aaai]` `[FB: FB-003]`
- Item 5: `[PRD: aaak]` `[FB: FB-003, FB-037]`
- Item 6: `[PRD: aaao, aaap]` `[FB: FB-003]`
- Item 7: `[PRD: aaaj]` `[FB: FB-003]`

All items carry a `[PRD: ...]` tag — sprint validation per `aadw` passes.

### Notable decisions

- **Item 2 (`aaaq` read-only after `/sdd:scope`) and Item 6 (`aaap` overwrite confirmation) are distinct guards** with overlapping concerns. The sprint file documents the boundary: Item 2's guard fires when `docs/scope.md` exists (project structurally past discovery); Item 6's overwrite-confirmation fires when `docs/discovery.md` exists but `docs/scope.md` does not (mid-flow re-run). Item 2 fires first; Item 6 only reaches its check if Item 2's guard didn't exit.
- **Item 4's Phase 1 beats are deferred to subagent design** during `/sdd:build`. The sprint file gives the subagent constraints (open exploration, not boundaries, not requirements, not architecture) and reasonable candidate beats (the idea, the why, the audience, prior art, constraints, starting context) but the final list is implementation work, not sprint-planning work. This is the first sprint in this project where Phase 1 beats are designed from scratch — prior commands inherited beats from v1.
- **Item 5 closes `aage` as a side effect.** `aage` is in the Process Notes Growth epic, which is otherwise out of scope. The sprint file notes the side effect and instructs `/sdd:build` wrap-up to check off `aage` alongside `aak` when Item 5 closes. The rest of the Process Notes Growth epic (`aagf`–`aagn`) stays out of scope.
- **Item 7 may file follow-ups.** Cross-cutting behavior verification is mostly inheritance-checking, but if a reference load is missing (e.g., `references/context-management.md` not loaded by `discovery/SKILL.md` per the Sprint 9 wiring pattern) the subagent fixes it in Item 7 rather than deferring. The whole point of `aaaj` is that inheritance is real, not theoretical.

### Build mode caveat

Same caveat as Sprints 5–9: the cached marketplace plugin is still v1, so Sprint 10's edits to `plugins/sdd/skills/discovery/` ship to disk but are not live as agent behavior until the maintainer reinstalls. First post-Sprint-10 invocation of `/sdd:discovery` will be the first real test of the implementation. The user has not yet reinstalled despite multiple sprints worth of edits — the plugin cache reinstall is its own deferred maintenance task.

---

## Sprint build (2026-05-14)

Autonomous-mode build of all seven items. Sequential dispatch to subagents (single primary file with cumulative edits across items, race-free). All seven items closed; the entire `/sdd:discovery Command` PRD epic is complete; side-effect `aage` closed alongside.

### Per-item build summary

**Item 1 — `/sdd:onboard` prerequisite check `[PRD: aaah]`.** Subagent added `## State Updates (immediate)` and `## Prerequisites` sections. State update writes `lastCommand = "/sdd:discovery"` first per sdd-guide; prereq check fires next: missing `~/.claude/sdd-user-profile.json` emits the literal `Run /sdd:onboard first.` and exits without other writes. Phrasing matched scope/prd/spec convention.

**Item 2 — Read-only after `/sdd:scope` `[PRD: aaaq]`.** Added `## Read-Only After /sdd:scope` between Prerequisites and Behavior. When `docs/scope.md` exists, emits a read-only message naming both `docs/scope.md` and `docs/discovery.md` and exits without writes beyond the already-completed `lastCommand` update. Branch only fires if user-profile prereq passed.

**Item 3 — `docs/refs/` reading `[PRD: aaal, aaam, aaan]`.** Updated `## Loading` to include `deepening-rounds.md`, the user profile, and `docs/refs/`. Added `## docs/refs/ Acknowledgment` section emitting the literal one-line message `Read N files from docs/refs/: [filename1, filename2, ...]. I'll reference them as we go.` when non-empty; silent proceed when empty/missing. Added `### Using the loaded reference content` sub-section guiding Phase 1 framing. Orchestrator stripped backticks the subagent put around `docs/refs/` inside the message to match the spec literal exactly.

**Item 4 — Phase 1 beats + interview shell `[PRD: aaai]`.** Largest item. Subagent designed seven Phase 1 mandatory beats: the idea / the why / the audience / prior art and inspiration / constraints / starting context / anything else. Notable beat-design calls: cut "goals" and "what done looks like" (overlap `/sdd:scope`); cut "solo or team" (scope-affecting); audience beat explicitly green-lights "just me"; constraints beat frames as surfacing, not negotiating. `## Status` section deleted; frontmatter description rewritten (no more "Stub in this release"); stub `## Behavior` replaced with `## Interview — Phase 1`, `## Interview — Phase 2`, `## Wrap-Up`, `## End-of-Command Handoff`. Phase 2 delegates wholly to deepening-rounds.md and context-management.md; bare transition prompt explicitly forbidden.

**Item 5 — `process-notes-discovery.md` real-time append `[PRD: aaak]`.** Added `## Process Notes` section between Phase 2 and Wrap-Up. Trigger points: end of every Phase 1 beat, end of every Phase 2 round, notable mid-interview moments. Content rules referenced from sdd-guide without restatement. Format: append-only markdown with timestamp or beat marker; create on first append. Wrap-Up placeholder line replaced with a back-reference noting appends already happened. Side-effect `aage` closed alongside.

**Item 6 — `docs/discovery.md` + template + overwrite confirmation `[PRD: aaao, aaap]`.** Subagent created `plugins/sdd/skills/sdd-guide/templates/discovery-template.md` with eight sections aligned 1:1 with Phase 1 beats (Title + one-line summary, The Idea, Why It Matters, Audience, Prior Art and Inspiration, Constraints, Starting Context, Open Questions — removable if empty). Overwrite confirmation placed at **startup** rather than at end-of-interview: better UX, and the spec only requires the prompt precede the write, not the interview. Reject path emits a "no changes; existing `docs/discovery.md` preserved" message and exits with no handoff (no interview ran). Wrap-Up's write step uses the template; no second confirmation at write time. Loading updated to include the template.

**Item 7 — Cross-cutting behavior inheritance verification `[PRD: aaaj]`.** Audit + minimal gap-fill. Five of six cross-cutting behaviors inherited cleanly: clarifying questions don't advance; 5/10 bounded rounds; end-of-round structured recommendation; active prompting at beat/topic close; end-of-command handoff (`/sdd:scope`, unconditional emission). Gap found and fixed in Item 7 itself: `references/context-management.md` was referenced in the Phase 2 body but NOT in the `## Loading` section's load directives. Added at position 3, matching the Sprint 9 canonical wiring across scope/prd/spec/plan/refine. sdd-guide line 121's interview-command set already includes `/sdd:discovery` — no edit there.

### Pivots / decisions / pushback during the build

- **Item 3 backticks fix-up.** Subagent put backticks around `docs/refs/` inside the literal acknowledgment message. Spec literal (`docs/spec.md` line 340) has no backticks around the path inside its inline code span. Orchestrator tightened to match the spec literal exactly. Pattern observation worth carrying forward: when a SKILL.md says "emit message X" as a literal, the agent emits exactly the literal — markdown formatting around path tokens inside the message is a divergence even when ergonomic. Beat: this same pattern should be checked in future SKILL.md edits that quote spec literals.

- **Item 6 overwrite-confirmation placement.** Open question in the item description: fire the prompt at startup (before interview) or at end of interview (before write). Sprint plan acknowledged either was valid; recommended startup for UX. Subagent chose startup with explicit rationale captured in the SKILL.md. Decision rationale: the PRD AC text describes a startup-time detection, so startup placement also fits the AC wording. No second confirmation at write time since authorization captured at startup.

- **No deepening rounds requested.** Phase 1 beat design — the only open call — was handled cleanly by the Item 4 subagent. The seven-beat result respects the "what is this?" register and does not bleed into `/sdd:scope`'s boundary-drawing territory.

- **FB-003 sprint-plan tag mismatch (discovered during wrap-up).** Sprint plan tagged items 1, 2, 4, 5, 6, 7 with `[FB: FB-003]` on the assumption that FB-003 was the headline-new-command tag for `/sdd:discovery`. Reviewing `docs/v2-verification.md`, FB-003 actually describes `/sdd:prd` ("New `/sdd:prd` command owns `docs/prd.md`"). Sprint 10 implements `/sdd:discovery` which has no separate FB row of its own. v2-verification row for FB-003 was NOT marked shipped; a note in its Notes column flags the sprint-plan slip. Sprint 10 legitimately ships FB-013 (sprint 10, item 3 — file uploads) and FB-037 partially (sprint 10, item 5 — discovery process-notes wiring; remaining commands tracked in Process Notes Growth).

### PRD checkoffs

Closed during the build: `aaah`, `aaai`, `aaaj`, `aaak`, `aaal`, `aaam`, `aaan`, `aaao`, `aaap`, `aaaq` (all 10 ACs of the `/sdd:discovery Command` epic). Side-effect closed: `aage`.

### v2-verification updates

- **FB-003** — not marked shipped (labelling slip; note appended).
- **FB-013** — marked `sprint 10, item 3`. Notes: discovery reads `docs/refs/` before Phase 1; one-line literal acknowledgment when non-empty, silent proceed when empty.
- **FB-037** — marked `sprint 10, item 5 (partial — discovery only)`. Notes: discovery wires real-time append to `process-notes-discovery.md`; other commands tracked separately.

### Open concerns

No new entries appended to `docs/open-concerns.md`. Sprint 10's items resolved per the sprint plan; no new questions surfaced that need cross-phase carry.

### CLAUDE.md / AGENTS.md updates

None. Every item followed existing conventions; no new project-wide patterns surfaced.

### Cached marketplace plugin caveat (still)

Sprint 10's edits ship to disk in `plugins/sdd/skills/discovery/` and `plugins/sdd/skills/sdd-guide/templates/discovery-template.md`. They remain not-live as agent behavior until the maintainer reinstalls the cached plugin. First post-Sprint-10 invocation of `/sdd:discovery` will be the first verification of the implementation in production. The cache reinstall is its own deferred maintenance task that has spanned multiple sprints now.

### Iteration candidates surfaced

None. No `[iteration-candidate]` markers fired during build. The Item 3 backticks fix-up was a spec-literal correctness call, not an iteration candidate.

### Next sprint candidates (from sprint plan's list)

The freshest unblocked work:
- **Multi-Session Resume** (`aaar`–`aabg`, 17 ACs) — pause/unpause SKILL.md stubs already exist.
- **Right-sizing wire-through** (`aabr`–`aaby`, 8 ACs) — touches every interview command including the freshly-implemented `/sdd:discovery`.
- **`/sdd:prd`-only polish** (`aacd`–`aacl`, 9 ACs) — single-file work.

Maintainer picks the next focus during `/sdd:plan`.
