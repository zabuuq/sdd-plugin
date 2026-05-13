# Sprint 7 — Process Notes

## Planning Session — 2026-05-13

### Context

Seventh v2 implementation sprint, planned via the cached v1 `/sdd:sprint` command (the v2 `/sdd:plan` was just shipped in Sprint 6 but is not yet live as a slash command — plugin cache reinstall pending). Sprint 6 closed earlier today: `/sdd:plan` full v2 implementation in `plugins/sdd/skills/plan/SKILL.md` (5 new top-level sections + revised Step 5 + gating lines for Step 7 and Step 11), closing 19 PRD ACs (`aado`–`aadv`, `aadw`–`aadz`, `aaea`–`aaeb`, `aaec`–`aaeg`). Sprint 6's wrap-up was the first real dogfood of Sprint 5's `/sdd:build` close-sprint phase — fired clean (auto-checked 19 ACs, no `[FB:]` rows updated since no FB tags, no story splits, no tech-debt).

### Why /sdd:onboard Polish next

Five reasons `/sdd:onboard` Polish beats the alternatives for Sprint 7:

1. **Unblocks the new `/sdd:plan` v2 mechanics.** Sprint 6 shipped `/sdd:plan` v2 with sprint-mode memory that reads `defaultSprintMode` and writes to it. It also references `handoffWarningShown` for the first-handoff explanation. Neither field is currently seeded by `/sdd:onboard`. Once Sprint 7 lands, the next `/sdd:onboard` run (post-reinstall) produces a profile that has all the fields downstream commands need. Without this, every new project starts with a stale-shaped profile that downstream commands have to handle defensively.

2. **Single file, well-bounded.** All 18 ACs live in `plugins/sdd/skills/onboard/SKILL.md`. No cross-file edits, no reference-file updates, no plugin manifest changes. Clean execution profile. Matches the Sprint 5 and Sprint 6 single-file pattern.

3. **Directly user-visible.** Unlike most sdd-internal mechanics sprints, `/sdd:onboard` Polish is what a new user actually experiences when they install the plugin. It's the first impression of v2. Closing it raises the user-facing polish bar.

4. **Sets up Cross-Project Feedback Transfer.** Item 2's `feedbackLocalPath` question lands the profile field that the Cross-Project Feedback Transfer epic (`aaff`–`aafr`) reads when forwarding feedback notes. Without that field seeded, the future CPFT sprint has nothing to read. Sprint 7 puts the data in; the future sprint consumes it.

5. **Mechanical-vs-exploratory: mechanical.** Each AC names a specific output sentence, beat, or field. The spec already documents the v2 `/sdd:onboard` changes (Beat-aligned bullets in `spec.md > Command Skills > /sdd:onboard`). No design ambiguity at planning time. Same character as Sprints 5 and 6.

### PRD scan

- 13 epics in `docs/prd.md`, 206 ACs total.
- **Checked ACs:** 40 (21 from Sprint 5 + 19 from Sprint 6).
- **Unchecked ACs across remaining epics** (rough count): Bug Fix 1 (3); /sdd:discovery (9); Multi-Session Resume (17); Interview Command Behaviors (24); Context Management (10); /sdd:onboard Polish (18); /sdd:polish re-open (3); /sdd:retro at Project Close (5); Cross-Project Feedback Transfer (11); Automatic Backlog Generation (9); Process Notes Growth (7); v1→v2 Migration (14); v2 Release Verification (~8 — many preservation-style ACs already passively true).
- **Unvetted: empty** (line 363 of prd.md, unchanged).
- **Open concerns:** 1 Open item (`Soft acceptance criteria for MIT distribution`), targets `/sdd:refine`. Not in this sprint's scope. No action.
- **Historical-AC backfill gap:** Sprints 1–4 closed work but predate Bug Fix 2's auto-checkoff mechanism. Their ACs remain unchecked in `docs/prd.md`. Not Sprint 7 work — PRD hygiene the maintainer handles manually.

### Open concerns processed

Read `docs/open-concerns.md`. Single Open item targets `/sdd:refine`. Not in this sprint's scope. No update made.

### Sprint theme proposed

**`/sdd:onboard` Polish — full v2 implementation.** Four items, sequential because they all edit `plugins/sdd/skills/onboard/SKILL.md`. Splitting horizontally by user-experience phase (intro question + path metadata → feedback context → plugin-update context + next-step heads-up → re-run flow + new-field seeding):

- **Item 1: Communication-style open prompt + profile path output + re-run statement.** Rewrite Step 5 to open-ended; output profile path; emit re-run sentence. 4 ACs (`aacw`–`aacz`).
- **Item 2: `/sdd:feedback` beat + `feedbackLocalPath` question.** Add dedicated feedback beat (what/when/where); add skip-friendly `feedbackLocalPath` question with write logic. 6 ACs (`aada`–`aadf`).
- **Item 3: Plugin auto-update paragraph + next-command heads-up.** Add recommendation-free auto-update paragraph; replace closing line with explicit `/sdd:discovery`-is-an-interview heads-up. 4 ACs (`aadg`–`aadj`).
- **Item 4: Profile re-surface + new-field seeding.** Expand Step 1's update flow to surface both profile JSON and cross-project-patterns markdown with source labels; seed `handoffWarningShown: false` and `defaultSprintMode: null` on create/update. 4 ACs (`aadk`–`aadn`).

Rationale for picking this grouping over alternatives:

- **Why four items, not one?** A single 18-AC item would be hard to verify granularly. Four logical chunks — intro/metadata, feedback, plugin/next-step, re-run/seeding — map to natural mini-stories in the PRD and to natural beats in the SKILL.md.
- **Why this ordering?** Items 1–3 attach to the new-profile flow (Steps 2–6 of the existing SKILL.md). Item 4 attaches to the update flow (Step 1) plus the write step (Step 6). Doing Items 1–3 first lets Item 4 reference and update the schema/write the four items have built up. Reordering Item 4 ahead would create awkward forward references.
- **Why `/sdd:onboard` Polish now, not Bug Fix 1 or `/sdd:polish` re-open?** Both alternatives are smaller (3 ACs each) and lower-payoff. Bug Fix 1 closes a latent bug but has no immediate user impact (the bug only fires when `/sdd:feedback` runs back-to-back without other commands). `/sdd:polish` re-open is useful but niche (only fires on user remorse mid-`/polish`). `/sdd:onboard` Polish lifts the v2 entry-point experience and unblocks downstream commands. Bigger payoff per sprint.
- **Why exclude Bug Fix 1?** Different file (`feedback/SKILL.md`), unrelated mechanics. Adding it would diffuse Sprint 7's focus. Cleaner as a small standalone sprint.
- **Why exclude Interview Command Behaviors?** Largest cross-cutting payoff in the PRD (24 ACs) but touches sdd-guide core plus 4+ reference files. Different scope character — better as its own large sprint, possibly the next one.

### Out of scope (explicit)

- Cross-cutting interview command behaviors (clarifying-questions-don't-advance, 5/10 caps, end-of-round recommendations, active prompting, 3-tier context management). These ship in a sdd-guide-focused sprint and apply to all interview commands at once. `/sdd:onboard` references them but doesn't embed them.
- Bug Fix 1 (`aaaa`–`aaac`). Separate file, separate sprint.
- `/sdd:polish` re-open mechanism (`aaev`–`aaex`). `/polish` work.
- `/sdd:discovery` command implementation (`aaah`–`aaaq`). Separate command, separate sprint.
- Cross-Project Feedback Transfer dual-write logic (`aaff`–`aafr`). Separate sprint. `/sdd:onboard` only writes the `feedbackLocalPath` field; CPFT consumes it.
- Cross-project-patterns file write — that's `/sdd:retro`'s responsibility, not `/sdd:onboard`. Sprint 7 only reads from the file if present (Item 4).
- Manual PRD-hygiene backfill of historical ACs.

### Proposed grouping

4 items, sequential execution (same-file edits):

1. `plugins/sdd/skills/onboard/SKILL.md` Step 5 rewrite + profile path output + re-run sentence. 4 ACs.
2. `plugins/sdd/skills/onboard/SKILL.md` /sdd:feedback beat + feedbackLocalPath question. 6 ACs.
3. `plugins/sdd/skills/onboard/SKILL.md` auto-update paragraph + next-command heads-up. 4 ACs.
4. `plugins/sdd/skills/onboard/SKILL.md` profile re-surface (Step 1 update flow) + new-field seeding (Step 6 write). 4 ACs.

User confirmation: planning ran under the user's "work without stopping for clarifying questions" directive. Sprint plan was assembled and presented as a single proposal via an `AskUserQuestion` checkpoint (4-option select including 3 alternative themes). User answered "Accept the /sdd:onboard Polish proposal (Recommended)." User can still redirect post-generation by editing `docs/sprint-7.md` before `/sdd:build` runs.

### Execution order decision

**Sequential, no parallel option.** All four items edit `plugins/sdd/skills/onboard/SKILL.md`. Items 1–3 build out the new-profile flow (additive Steps inside Step 2–6). Item 4 attaches to the update flow (Step 1) and the write step (Step 6) with the schema/seeding work that depends on Items 1–3 having defined what goes in the schema. Autonomous mode dispatches subagents one at a time.

Same execution profile as Sprints 5 and 6 (single-file, sequential, autonomous-sequential dispatch).

### Build mode

**Autonomous**, matching priors. Items are spec-mapped: every AC has a clearly-defined target in `onboard/SKILL.md` and a clearly-defined source in `docs/spec.md > Command Skills > /sdd:onboard`. No deepening required mid-build. Mode locked for Sprint 7.

The cached v1 `/sdd:sprint` Step 5 asks the user to pick build mode interactively. Per the no-clarifying-questions directive, this session defaulted to autonomous (matching Sprints 5 and 6) without re-asking. User can redirect by editing `buildMode` in `docs/project-state.json` before `/sdd:build` runs.

### Deepening rounds

None requested. Rationale: every AC has been pre-vetted (sprint planning surfaced no scope ambiguity); the spec already defines `/sdd:onboard` v2 end-to-end (`docs/spec.md > Command Skills > /sdd:onboard`); the work is structurally well-understood (a SKILL.md authoring task with clear acceptance criteria). The mechanical-vs-exploratory ratio favors mechanical. Same call as Sprints 5 and 6.

### Tagging convention

- Items 1, 2, 3, 4 all carry real `[PRD: ...]` tags. No `[unmapped]` items.
- **No `[FB: ...]` tags on Sprint 7 items.** The `/sdd:onboard` Polish epic ACs being implemented (`aacw`–`aadn`) describe sdd-plugin-internal architecture (the onboarding command's mechanics). They are not surfaced feedback items with corresponding rows in `docs/v2-verification.md` — that file's rows track in-scope feedback from the v2 scope's triaged list. The onboard-mechanics ACs do not directly map to specific FB rows.

### New concerns surfaced during planning

- **Item 4 cross-project-patterns read assumes the file's prose is parseable.** Item 4 reads `~/.claude/sdd-cross-project-patterns.md` to surface retro-written preferences. The file is plain markdown without an enforced schema (`/sdd:retro` writes it append-only). Item 4's SKILL.md should describe "surface as labeled cross-project patterns" in a way that doesn't require a strict parser — agent reads the markdown, identifies pattern-like content, displays it with source labels. Documenting here so the build subagent doesn't try to invent a schema.
- **No-overwrite rule for `handoffWarningShown` on update flow.** Item 4's seed step writes `handoffWarningShown: false`, but a downstream command (`/sdd:plan` first-handoff explanation) may have already flipped it to `true`. The Item 4 spec language is explicit: write only when the field is missing. This nuance is important — re-running `/sdd:onboard` should not reset a previously-flipped flag.
- **Sprint 7 is the first dogfood of `/sdd:plan` v2... if reinstalled.** Sprint 6 shipped `/sdd:plan` v2 to source but the cached marketplace is still v1. Until the maintainer reinstalls, every sprint planning session uses cached v1 `/sdd:sprint`. Sprint 7's planning used v1. Sprint 8's planning may also use v1 unless the reinstall happens. The v2 `/sdd:plan` dogfood opportunity sits in the queue waiting for that reinstall — at which point all the new mechanics (prior-sprint notes read, PRD state check, sprint-mode resolution, tag validator, handoff) fire for the first time. Note for future planning sessions: when the plugin reinstalls, the next `/sdd:plan` run is a notable event worth observing closely.

None of these rise to "Open concern" status — each has a documented resolution in the sprint items themselves or is a forward-looking observation. Not adding to `docs/open-concerns.md`.

### Decisions log

| Decision | Choice | Rationale |
|---|---|---|
| Sprint 7 theme | `/sdd:onboard` Polish — full v2 implementation | Unblocks the new `/sdd:plan` v2 mechanics by seeding the profile fields they read; user-visible polish; mechanical character; single-file scope |
| Bundle into one item or split? | Four items (intro/metadata, feedback, plugin/next-step, re-run/seeding) | An 18-AC single item is hard to verify granularly; four logical bands map to natural mini-stories in the PRD |
| Item ordering | 1→2→3→4 | Items 1–3 build the new-profile flow; Item 4 attaches to the update flow + write step and depends on the schema Items 1–3 have built up |
| Include Bug Fix 1? | Defer | Different file, different command, unrelated mechanics; bundling diffuses focus |
| Include `/sdd:polish` re-open? | Defer | Belongs to /polish, not /onboard; cleaner as a small standalone sprint |
| Include Interview Command Behaviors? | Defer | Largest cross-cutting payoff (24 ACs) but touches sdd-guide + 4+ reference files; better as its own large sprint |
| Include `/sdd:discovery` implementation? | Defer | Larger fresh-command implementation; depends on Interview Command Behaviors landing first per AC `aaaj` |
| Execution mode | Sequential | All four items edit the same file; autonomous dispatches sequentially |
| Build mode | Autonomous | Matches priors; items are spec-mapped and mechanical; no clarifying-questions directive defaulted without re-asking |
| Deepening rounds | None | Pre-vetted ACs, fully specified `/sdd:onboard` in spec, structurally well-understood; no exploratory thread |
| `[FB: ...]` tagging on Sprint 7 items | None | `/sdd:onboard` mechanics are sdd-plugin-internal architecture, not surfaced feedback items in v2-verification.md |
| User input | Single AskUserQuestion checkpoint (4 options including 3 alternatives) | Per the "no clarifying questions" directive; user accepted recommended option; can still redirect by editing sprint-7.md before build |
