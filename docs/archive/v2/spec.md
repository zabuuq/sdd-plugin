# SDD Plugin v2 — Technical Specification

## Posture

This spec is a **delta-and-resolution document**, not a self-contained re-statement of the plugin. v1's spec (935 lines, archived at `docs/archive/v1/spec.md`) partially duplicated content that lives in the plugin's `references/*.md` files, and that duplication became a maintenance liability — the spec was both a planning artifact AND a runtime artifact, and updates had to happen twice.

v2 spec covers:
1. What v2 changes vs. v1.
2. Resolutions of every open concern targeted at `/sdd:spec`.
3. Concrete decisions for every PRD acceptance criterion that defers to spec.

Anything that's a "how" — parser rules, loading order, schemas, the exact text of behavioral guidance — lives in a `references/*.md` file inside `plugins/sdd/skills/sdd-guide/references/`. This spec names the reference file and explains "what + why"; the reference file owns "how."

The runtime artifact (the plugin code itself) is the single source of truth.

## Stack

Unchanged from v1.

| Layer | Choice | Reference Docs |
|-------|--------|----------------|
| Platform | Claude Code plugin (skills-based architecture) | https://code.claude.com/docs/en/plugins |
| Language | Markdown (SKILL.md files with YAML frontmatter) | https://code.claude.com/docs/en/skills |
| Runtime code | None — behavior defined through skill instructions and templates | — |
| Distribution | GitHub marketplace repo | https://code.claude.com/docs/en/plugin-marketplaces |
| Plugin reference | — | https://code.claude.com/docs/en/plugins-reference |

No build step, no runtime dependencies, no compiled code.

## Runtime & Deployment

Unchanged from v1.

- **Runtime:** Claude Code CLI or desktop app with plugin support.
- **Deployment:** GitHub repository structured as a plugin marketplace.
- **Installation:** `/plugin marketplace add <github-user>/sdd-plugin` → `/plugin install sdd@<marketplace-name>`.
- **Local development:** `claude --plugin-dir ./plugins/sdd`.
- **Environment:** Claude Code with plugin support, `git` CLI available.
- **No API keys, no external services, no hosting infrastructure.**

Version bump: `plugins/sdd/.claude-plugin/plugin.json` → `"version": "2.0.0"`. Marketplace catalog `version` bumped accordingly.

## Architecture Overview

v2 command chain:

```
/sdd:onboard ──→ /sdd:discovery ──→ /sdd:scope ──→ /sdd:prd ──→ /sdd:spec ──→ /sdd:plan ──→ /sdd:build ──→ /sdd:polish ──→ /sdd:refine ──→ /sdd:retro
   (one-time)       (NEW)          (narrower)                                  (renamed         (wrap-up      (renamed       (unchanged)   (project
                                                                              from /sprint)    absorbed)    from /iterate)                  close only)

Anytime:  /sdd:pause (NEW)    /sdd:unpause (NEW)    /sdd:feedback (cross-project transfer)
```

The chain enforces position via prerequisites. Each interview command ends with an explicit handoff message instructing `/clear` plus the next command name (see [Cross-Cutting: End-of-Command Handoff](#end-of-command-handoff)).

### Data Flow

```
                  ~/.claude/sdd-user-profile.json (global, cross-project)
                  ~/.claude/sdd-cross-project-patterns.md (NEW, /retro-written)
                          ▲                    ▲
                          │ written/updated    │ written
                          │                    │
/sdd:onboard ─────────────┘                    │
                                               │
/sdd:discovery ──→ docs/discovery.md           │
                   docs/refs/ (read)           │
                                               │
/sdd:scope ────────→ docs/scope.md             │
                     project-state.json        │
                     (smallProject auth)       │
                                               │
/sdd:prd ──────────→ docs/prd.md (AC IDs)      │
                                               │
/sdd:spec ─────────→ docs/spec.md              │
                     CLAUDE.md, AGENTS.md      │
                     docs/refs/ (read)         │
                                               │
/sdd:plan ─────────→ docs/sprint-N.md          │
                     (with [PRD: ...]          │
                      [FB: ...] [unmapped]     │
                      tags)                    │
                                               │
/sdd:build ────────→ code artifacts            │
                     sprint-N.md checks        │
                     process-notes-sprint-N.md │
                     ([iteration-candidate],   │
                      [close-sprint-manifest]) │
                     docs/prd.md AC checkoff   │
                     docs/v2-verification.md   │
                       (sdd-plugin only)       │
                                               │
/sdd:polish ───────→ iteration items to        │
                     sprint-N.md               │
                     [sprint-reopened] marker  │
                                               │
/sdd:refine ───────→ docs/prd.md (unvetted →   │
                       vetted, new AC IDs)     │
                     docs/spec.md              │
                     open sprint-N.md refs     │
                     AGENTS.md, CLAUDE.md      │
                                               │
/sdd:retro ────────→ docs/retro.md ────────────┘ (cross-project patterns)

Cross-cutting:
  /sdd:pause ─────→ docs/<command>-resume.md  (then user /clears + /unpauses)
  /sdd:unpause ──→ reads <command>-resume.md, resumes the underlying command
  /sdd:feedback ─→ docs/sdd-feedback.md (local)
                   + <feedbackLocalPath>/docs/sdd-feedback.md (forwarded)
  All commands → docs/open-concerns.md (read/resolve/append; /feedback excepted)
  All commands → docs/project-state.json (read/update lastCommand; /feedback excepted in v2)
  Planning commands → process-notes-<phase>.md (per-phase, append-only)
  Sprint commands → process-notes-sprint-N.md (append-only)
  All planning commands + /plan → docs/backlog.md (on user-confirmed deferral)
```

## File Structure

```
sdd-plugin/                                       # marketplace repo root
├── .claude-plugin/
│   └── marketplace.json                          # version bumped to 2.0.0
├── README.md
└── plugins/
    └── sdd/
        ├── .claude-plugin/
        │   └── plugin.json                       # version: 2.0.0
        ├── CLAUDE.md                             # plugin-level (loads sdd-guide)
        ├── MIGRATION-v1-to-v2.md                 # NEW: state-aware migration guide
        └── skills/
            ├── sdd-guide/
            │   ├── SKILL.md                      # tone, rules, chain, handoff template
            │   ├── references/
            │   │   ├── deepening-rounds.md       # UPDATED: 5/10 caps, definite recs
            │   │   ├── context-loading.md        # UPDATED: discovery.md, refs/, verification
            │   │   ├── living-documents.md       # UPDATED: v2 update ordering
            │   │   ├── open-concerns.md          # unchanged
            │   │   ├── right-sizing.md           # NEW: smallProject + skippable beats
            │   │   ├── pause-resume.md           # NEW: schema + distillation quality bar
            │   │   ├── sprint-tags.md            # NEW: all v2 tag formats and parsers
            │   │   └── context-management.md     # NEW: three-tier between-rounds heuristic
            │   └── templates/
            │       ├── scope-template.md         # UPDATED: narrower (boundary only)
            │       ├── prd-template.md           # UPDATED: AC IDs, Unvetted seeded
            │       ├── spec-template.md          # unchanged
            │       ├── sprint-template.md        # UPDATED: tag-based items
            │       ├── retro-template.md         # UPDATED: project-close only
            │       ├── discovery-template.md     # NEW
            │       └── open-concerns-template.md # unchanged
            ├── onboard/SKILL.md                  # UPDATED (7 polish items)
            ├── discovery/SKILL.md                # NEW
            ├── scope/SKILL.md                    # UPDATED (narrower; smallProject authority)
            ├── prd/SKILL.md                      # UPDATED (AC IDs, quality check, phase-split flag)
            ├── spec/SKILL.md                     # UPDATED (lean v2 posture)
            ├── plan/SKILL.md                     # RENAMED from sprint/
            ├── build/SKILL.md                    # UPDATED (wrap-up, manifest)
            ├── polish/SKILL.md                   # RENAMED from iterate/
            ├── refine/SKILL.md                   # UPDATED (v2 update ordering)
            ├── retro/SKILL.md                    # UPDATED (project-close only)
            ├── pause/SKILL.md                    # NEW
            ├── unpause/SKILL.md                  # NEW
            └── feedback/SKILL.md                 # UPDATED (no lastCommand write; forwarding)
```

**Removed from v1:**
- `plugins/sdd/skills/sprint/` (renamed to `plan/`)
- `plugins/sdd/skills/iterate/` (renamed to `polish/`)
- `plugins/sdd/skills/reflect/` (work absorbed into `build/` wrap-up + `retro/`)
- `plugins/sdd/skills/sdd-guide/templates/reflect-template.md`

---

## Behavioral Layer: sdd-guide

Implements `prd.md > Interview Command Behaviors` and `prd.md > Context Management`.

### sdd-guide Core (SKILL.md)

Implements `prd.md > Interview Command Behaviors`.

Holds the shared behavior every command loads. v1 content carries over (tone, interaction rules, guard rails, command chain, command explanations, process notes, state tracking, open concerns).

v2 additions:
- **Tone enforcement** unchanged from v1 (read `communicationStyle` from `~/.claude/sdd-user-profile.json`; profile value is authoritative).
- **Clarifying questions never advance the interview.** Mid-interview clarifying questions get answered without changing the open question and without the agent recording its own recommendation as the user's answer. Applies to every interview command.
- **5-suggested / 10-hard-cap questions per deepening round.** Past 10, the agent emits a stated reason and obtains explicit user permission before posing the eleventh.
- **Definite end-of-round recommendation with topic preview.** Phrased as "I think we should do another round, because [reason; list of topics]" or "I do not think we need another round, because [reason; topics a next round would have covered]." Never a blank prompt.
- **End-of-command handoff template** (see [Cross-Cutting: End-of-Command Handoff](#end-of-command-handoff)) lives inline here because it applies to every interview command.

### Reference: deepening-rounds.md (updated)

Implements `prd.md > Interview Command Behaviors`.

Defines the two-phase interview pattern. v2 updates:
- 5/10 question caps locked into Phase 2 round composition.
- Definite end-of-round recommendation framing locked into transition between rounds.
- Topic preview required when recommending continuation.
- Reasoning required when recommending closure (may list topics a hypothetical next round would have covered).

### Reference: context-loading.md (updated)

Defines per-command file-loading rules to prevent context window exhaustion. v2 additions:
- `/sdd:discovery` loads `docs/refs/` if non-empty.
- `/sdd:spec` loads `docs/refs/` at startup.
- `/sdd:plan` reads the most recent `process-notes-sprint-N.md` to surface carry-forward items.
- `/sdd:build` re-entry adds `[close-sprint-manifest]` lookup logic when re-opening.
- `/sdd:retro` reads `docs/sdd-cross-project-patterns.md` for prior pattern context.
- `/sdd:onboard` reads both `~/.claude/sdd-user-profile.json` and `~/.claude/sdd-cross-project-patterns.md` when surfacing existing preferences.
- 200-line threshold rule unchanged.

### Reference: living-documents.md (updated)

Defines the protocol for updating PRD/spec/CLAUDE.md/AGENTS.md/sprint files. v2 updates:
- **Update ordering for `/sdd:refine`** (revised): PRD → spec → open sprint files (if affected) → AGENTS.md → CLAUDE.md. Each step user-confirmed before proceeding.
- **`docs/v2-verification.md` is NOT in the living-documents chain** — it's a manual audit trail.
- **`docs/backlog.md` is NOT in the living-documents chain** — it's append-only deferral log written during planning, never edited by /refine.
- **Default stance unchanged**: resist changes to scope/PRD/spec; new items go to backlog or the Unvetted section.
- **Re-scoping trigger unchanged**: accumulated changes that fundamentally shift the project surface a re-scoping recommendation.
- **PRD health monitoring updated**:
  - 10+ epics → `/sdd:prd` emits phase-split recommendation (does not block generation).
  - 5+ unvetted items → flag scope creep.

### Reference: open-concerns.md (unchanged)

Cross-phase concern tracking. v1 mechanism carries over without change.

### Reference: right-sizing.md (NEW)

Implements `prd.md > Interview Command Behaviors > small project` story plus the spec-time deferrals about signals, thresholds, and skippable beats.

**Content owned by this file:**
- The `smallProject` field semantics (set by `/sdd:scope`, re-evaluated by subsequent commands).
- Signal list for the heuristic judgment (one primary feature, no external users, no multi-component architecture, no third-party integrations, brief reference uploads, concise opening framing).
- Per-command skippable Phase 1 beats when `smallProject: true`.
- Thinness signal for sub-5-question rounds (short answers, deflections, agent's natural framing toward closure).
- Interaction with the 5/10 question cap (cap stays; default count drops).

**Detection placement:**
- `/sdd:discovery` does **not** detect or write `smallProject`. (This is a deliberate v2 revision; see [PRD Edits Required](#prd-edits-required).)
- `/sdd:scope` writes the authoritative initial value at end of its Phase 1.
- Every subsequent interview command reads it on startup; may flip on contradictory evidence (typically toward `false`).

### Reference: pause-resume.md (NEW)

Implements `prd.md > Multi-Session Resume` plus the spec-time deferral about distillation quality bar. Also serves the `/clear` branch of the three-tier between-rounds model.

**Content owned by this file:**
- Resume-file location convention: `docs/<command>-resume.md`.
- Required sections in order: Status, Setup-already-done, Confirmed decisions, Round status, Pending decision, Files for next-session, Instructions for next-session.
- Per-section quality bar (described in detail; see [Cross-Cutting: Pause/Resume Quality Bar](#pause-resume-quality-bar)).
- Distill-not-dump anti-patterns: no verbatim conversation, no agent reasoning trail, no re-explanation of plugin behavior, cross-reference don't restate, length target 1–3 pages.
- Cleanup: resumed command deletes its own resume file when the underlying interview completes.
- Per-round summary writes use this same schema and location convention.

### Reference: sprint-tags.md (NEW)

Implements `prd.md > Bug Fixes > PRD state-tracking`, `prd.md > Sprint Loop`, `prd.md > v2 Release Verification`, and `prd.md > /sdd:polish re-open`.

**Content owned by this file** — every tag format and parser anchor used in v2 sprint and process-notes workflows:

- `[PRD: id1, id2]` and `[unmapped]` — sprint-item PRD reference tags. See [PRD Acceptance Criteria IDs](#prd-acceptance-criteria-ids) and [Sprint-Item Tags](#sprint-item-tags).
- `[FB: FB-NNN]` — sprint-item feedback reference tag. See [Sprint-Item Tags](#sprint-item-tags).
- `[iteration-candidate]` — in-flight process-notes marker. See [Iteration-Candidate Markers](#iteration-candidate-markers).
- `[close-sprint-manifest]` / `[/close-sprint-manifest]` — block anchors for the structured close-sprint record. See [Close-Sprint Manifest](#close-sprint-manifest).
- `[sprint-reopened]` — process-notes entry marker emitted on `/sdd:polish` re-open. See [Close-Sprint Manifest](#close-sprint-manifest).

**Parser conventions:** all tags begin with `[`, end with `]`, and use a consistent prefix pattern (literal name, optional colon, optional payload). Multi-value payloads are comma-separated. The file specifies the exact regex for each.

### Reference: context-management.md (NEW)

Implements `prd.md > Context Management > between rounds` plus the spec-time deferral about the three-tier recognition heuristic.

**Content owned by this file:**
- Three-tier model: continue / `/compact` / `/clear`.
- Per-tier recognition signals (round size, artifact-conversation gap, superseded noise, token-usage approximation).
- Tier-mapping rules.
- Failure-mode mitigation (AI self-assessment is fallible; user override upward always allowed; downgrade override carries a brief context-weight warning).
- Between-commands handoff cross-references the template in `sdd-guide/SKILL.md` (it's not duplicated here).
- `/clear` branch consolidates with `pause-resume.md` — agent writes a resume file before emitting the `/clear` recommendation.

### Templates

Located at `plugins/sdd/skills/sdd-guide/templates/`. v2 changes:

- **discovery-template.md** (NEW) — output schema for `docs/discovery.md`.
- **scope-template.md** (UPDATED) — narrower; exploration sections removed, boundary sections retained.
- **prd-template.md** (UPDATED) — AC entries written with inline 4-char ID in backticks; Unvetted section seeded with a marker comment explaining the section's purpose.
- **sprint-template.md** (UPDATED) — sprint items as single checkbox lines carrying tags.
- **retro-template.md** (UPDATED) — project-close-only structure; cross-project pattern capture step appended.
- **reflect-template.md** (REMOVED) — `/sdd:reflect` no longer exists.

---

## Command Skills

For each command: lists what changed, cross-references the PRD epic it implements, and points at relevant reference files. v1 content carries forward unless explicitly noted.

### /sdd:onboard

Implements `prd.md > /sdd:onboard Polish`.

**v2 changes** (per PRD ACs):
- `communicationStyle` question delivered as a single open-ended prompt; no bulleted example menu in the question text.
- Outputs the literal path `~/.claude/sdd-user-profile.json` at least once during its run.
- States that re-running `/sdd:onboard` updates the same profile file.
- Includes a `/sdd:feedback` beat covering what the command does, when to use it, and where notes go (current project + optional `feedbackLocalPath` forwarding).
- Includes a `feedbackLocalPath` question; skip-friendly. Empty answer → field omitted or `null`; non-empty → written to profile.
- Outputs a paragraph covering third-party plugin auto-update mechanics (toggle via `/plugin → Marketplaces`, behavior of `/reload-plugins`). Information only — no "you should" recommendation language.
- Ends with a heads-up naming `/sdd:discovery` as the next command and stating it is an interview, not a one-shot.
- On re-run with an existing profile, surfaces both originally-onboarded preferences AND patterns from `~/.claude/sdd-cross-project-patterns.md`, each labeled with source.
- Seeds new profile fields: `handoffWarningShown: false` (flipped true after first interview-command handoff), `defaultSprintMode` (left unset; populated by `/sdd:plan`'s toggle).

**User profile schema** (`~/.claude/sdd-user-profile.json`):
```json
{
  "version": 1,
  "createdAt": "ISO-8601",
  "updatedAt": "ISO-8601",
  "communicationStyle": "string — free-form user style",
  "gitPreference": true,
  "showWorkflowExplanation": true,
  "feedbackLocalPath": "string or null",
  "handoffWarningShown": false,
  "defaultSprintMode": null
}
```

### /sdd:discovery (NEW)

Implements `prd.md > /sdd:discovery Command`.

**Job:** open exploration before structural decisions. "What is this?"

**Loads:** sdd-guide core, deepening-rounds, user profile, `docs/refs/` (if non-empty), `discovery-template.md`.

**Prerequisites:** `~/.claude/sdd-user-profile.json` must exist. Missing → exit with "Run `/sdd:onboard` first."

**Behavior summary:**
- If `docs/refs/` is non-empty at startup, emit a one-line acknowledgment: `Read N files from docs/refs/: [filename1, ...]. I'll reference them as we go.` No content summaries.
- Phase 1 interview covering the idea, problem, audience, approach, references; uses cross-cutting interview command behaviors from sdd-guide.
- Phase 2 deepening rounds per `deepening-rounds.md`.
- Writes `docs/discovery.md` at the end; requires explicit confirmation before overwriting an existing file.
- Once `docs/scope.md` exists, subsequent commands read but never write `docs/discovery.md`.
- Appends to `process-notes-discovery.md` in real time during the interview.
- Does NOT detect or write `smallProject`. (Detection is `/sdd:scope`'s job; see [PRD Edits Required](#prd-edits-required).)
- Ends with the [End-of-Command Handoff](#end-of-command-handoff) naming `/sdd:scope`.

### /sdd:scope

Implements `prd.md > Interview Command Behaviors > small project` (authoritative detection).

**v2 changes:**
- Job narrowed: boundary-drawing only. Exploration moved to `/sdd:discovery`.
- Authoritative `smallProject` detection at end of Phase 1, using the signal list in `right-sizing.md`. Writes the field to `docs/project-state.json`.
- Reads `docs/discovery.md` as required input. Missing discovery file → recommend running `/sdd:discovery` first.
- Skippable Phase 1 beats when `smallProject` was tentatively flagged by `/sdd:discovery` (per `right-sizing.md`): project-split consideration; multi-user/team deepening.
- Applies all cross-cutting interview command behaviors.
- Ends with handoff naming `/sdd:prd`.

**Note:** /sdd:scope does not write a soft draft from `/sdd:discovery`. The scope/discovery boundary is clean — `/sdd:discovery` owns `docs/discovery.md`, `/sdd:scope` owns `docs/scope.md`. (Earlier design floated a pre-draft handoff; dropped during the PRD interview.)

### /sdd:prd

Implements `prd.md > Interview Command Behaviors > PRD-specific stories`.

**v2 changes:**
- AC line format: `- [ ] \`abcd\` AC text...` where `abcd` is a 4-char lowercase alphabetic ID assigned by `/sdd:prd` at write time. IDs are checked for collision against the existing PRD before assignment.
- Pre-write quality check: every AC evaluated against three checks — specific, observable, verifiable. Failing ACs rewritten before the file is generated. Check runs exactly once per `/sdd:prd` run.
- Phase-split flag: when in-conversation epic count reaches 10+, recommend phase-splitting before generating the file. Recommendation does not block; the user may elect to keep one phase.
- Generates the file with an `## Unvetted` section seeded with a marker HTML comment explaining its purpose to future agents and `/sdd:refine`. Section is empty at write time.
- Applies cross-cutting interview command behaviors.
- Ends with handoff naming `/sdd:spec`.

**One-time backfill for this project:** the existing `docs/prd.md` in this project needs IDs added. Done as part of the sprint that implements `/sdd:prd`'s ID generation.

### /sdd:spec

Implements `prd.md > Interview Command Behaviors` and the lean-spec posture.

**v2 changes:**
- Reads `docs/refs/` at startup as architectural reference context.
- Spec output follows the **lean delta-and-resolution posture** described at the top of this file. Spec text covers "what + why"; "how" content lives in `references/*.md` files in the plugin.
- Spec output cross-references PRD epics for every component section ("Implements `prd.md > [Epic]`").
- Spec output uses granular `###` subsections — these are addresses for `/sdd:plan` to cite when writing sprint items.
- Stack table includes a reference docs URL for every tool and library.
- Generates or merges CLAUDE.md and AGENTS.md per the v2 update ordering in `living-documents.md`.
- Applies cross-cutting interview command behaviors.
- Ends with handoff naming `/sdd:plan`.

### /sdd:plan (RENAMED from /sprint)

Implements `prd.md > Sprint Loop > /sdd:plan stories`.

**v2 changes:**
- Reads the most recent `process-notes-sprint-N.md` (highest N) at startup if any exist. Surfaces carry-forward items, quality concerns, and tech-debt observations from the previous sprint's `/sdd:build` wrap-up. Missing file → proceed without warning.
- **PRD state check** (after reading prior notes):
  - Scans `docs/prd.md` for AC checkoff status and Unvetted section content.
  - **Priority: Unvetted wins.** If Unvetted contains items, recommend `/sdd:refine` and do not proceed to sprint planning until the user acknowledges or overrides.
  - Only after Unvetted is empty: evaluate AC completion. All checked → recommend `/sdd:retro`, do not proceed. Otherwise → proceed with sprint planning.
- **Sprint-mode memory:**
  - If `defaultSprintMode` is set in user profile, use it without asking.
  - Else if `docs/project-state.json` has a recorded mode from the previous sprint, ask whether to reuse it.
  - Else ask the user to pick a mode.
  - After selection, offer an "always use this mode" toggle that writes to `defaultSprintMode` on user acceptance.
  - Mode is also written to `docs/project-state.json` for next-sprint reference.
- **Sprint-item tag validation:** every sprint-item line must carry either a `[PRD: ...]` tag or an explicit `[unmapped]` tag. The validator parses every checkbox bullet in the proposed `docs/sprint-N.md`. If any line lacks both, the validator surfaces every offending line and `/sdd:plan` refuses to write the file. See [Sprint-Item Tags](#sprint-item-tags) and `references/sprint-tags.md`.
- When `docs/v2-verification.md` exists (sdd-plugin project only): also prompt the user for `[FB: ...]` tags on sprint items implementing in-scope feedback items.
- Applies cross-cutting interview command behaviors. Skippable beat: sprint-mode confirmation when last mode is persisted.
- Ends with handoff naming `/sdd:build` (plus the cross-cutting `/clear` handoff).

### /sdd:build

Implements `prd.md > Bug Fixes > PRD state-tracking`, `prd.md > Sprint Loop > /sdd:build stories`, and `prd.md > v2 Release Verification` write behavior.

**v2 changes:**
- **Execution-phase additions:**
  - When the agent observes a real iteration-candidate during checklist execution, writes a `[iteration-candidate]` marker entry to `process-notes-sprint-N.md`. See [Iteration-Candidate Markers](#iteration-candidate-markers).
  - When working a sprint item that carries a `[FB: FB-NNN]` tag, no immediate action — the row update happens at close-sprint.
- **Wrap-up phase** (fires when the final sprint checklist item is marked complete):
  1. Emits the literal question: `Satisfied with this sprint, or items to iterate on?`
  2. If `process-notes-sprint-N.md` contains `[iteration-candidate]` entries written *before* wrap-up began, surface them alongside the question.
  3. On "items to iterate" → recommend `/sdd:polish`. Sprint stays open; no close-sprint actions.
  4. On "satisfied" with tagged candidates present → ask the user to assign a disposition for each: `discard` / `capture as tech-debt` / `actually iterate`. Any `actually iterate` switches the wrap-up to the "items to iterate" branch.
  5. On "satisfied" with no remaining iterate-now candidates: **close-sprint actions** (in order):
     - **PRD AC checkoff** per `prd.md > Bug Fixes > Story 2`: every PRD AC referenced by closed sprint items checks off. Cross-cutting ACs check off only when all contributing sprint items across all sprint files have closed. Implementation parses each closed item's `[PRD: id1, id2]` tag and updates `docs/prd.md` accordingly.
     - **Story splits** for unfinished items: prompt the user per-unfinished item; on confirmation, move unfinished ACs to a new story in `docs/prd.md`. New AC IDs assigned for any new ACs introduced by the split.
     - **"Anything notable?" prompt** — one prompt covering quality concerns, tech debt, and surprises. User responses logged to process notes.
     - **v2-verification row updates** (sdd-plugin only): for each closed sprint item carrying `[FB: ...]`, write/append to the corresponding row's `Shipped (sprint/item)` column in `docs/v2-verification.md`. Notes column may receive auto-text; Verified column is never written by the agent.
     - **Write `process-notes-sprint-N.md`**: decisions made during the sprint, pushback, surprises, notable items, dispositions assigned to candidates, plus the structured `[close-sprint-manifest]` block (see [Close-Sprint Manifest](#close-sprint-manifest)).
     - **Next-command recommendation** based on PRD state: continue sprint loop, run `/sdd:refine`, or run `/sdd:retro`.
- **Error handling — stop-revert-revise** (v2 pinning of v1 carry-over open concern):
  - "Last clean state" = the most recent user commit on the current branch.
  - On stop-revert-revise, when `gitPreference: true`: agent proposes `git reset --hard HEAD` after warning about uncommitted changes; waits for explicit go-ahead.
  - When `gitPreference: false`: agent provides a manual rollback summary (files changed + what to revert to where); user handles manually.
  - `/sdd:build` does NOT auto-commit. Auto-checkpointing is feature expansion outside v2's scope; if wanted, defer to a future `/sdd:refine` pass.
- **Re-entry after `/clear` (step-by-step mode):** same v1 sequence — CLAUDE.md → AGENTS.md → current sprint file → next unchecked item → spec subsection + PRD story for that item → open-concerns.md → project-state.json.

### /sdd:polish (RENAMED from /iterate)

Implements `prd.md > Sprint Loop > /sdd:polish stories`.

**v2 changes:**
- Renamed from `/sdd:iterate`.
- **Re-open escape hatch** when the targeted sprint's `process-notes-sprint-N.md` contains a `[close-sprint-manifest]` (indicating the sprint was closed):
  - Asks "Re-open the sprint?" before proceeding.
  - On confirmation: surfaces the manifest's checked-off PRD ACs; asks "Which (if any) should be re-opened?"; un-checks selected ACs in `docs/prd.md`; reverses story splits per the manifest's split mapping for selected ACs; appends a `[sprint-reopened]` marker entry to process notes.
  - On rejection: exits with a message explaining that re-opening is required to proceed.
  - See [Close-Sprint Manifest](#close-sprint-manifest) for the exact mechanism and edge cases.
- Iteration items append to current `docs/sprint-N.md` under `## Iteration N` headers, same as v1.

### /sdd:refine

Implements `prd.md > Interview Command Behaviors` cross-cutting plus its v1 carry-over job.

**v2 changes:**
- **Update ordering** (revised): PRD → spec → open sprint files (if affected) → AGENTS.md → CLAUDE.md. Each step user-confirmed before proceeding.
- **Sprint-file ref updates:** when /refine edits an AC that's referenced in `[PRD: ...]` tags in open sprint files (no `[close-sprint-manifest]` yet), offer to update refs in lockstep. Closed sprint files are not touched.
- **AC editing semantics** (see `living-documents.md`):
  - Pure reword (same intent) → check survives; user confirms intent didn't shift.
  - Semantic change → un-check the AC; surface any work shipped against the old wording as an iteration candidate (logged to the current open sprint's process notes if one exists, or flagged to the user).
  - Split/merge → new ACs get fresh IDs and start unchecked; old IDs removed.
- **`docs/v2-verification.md` is NOT touched** by /refine. User hand-edits the Notes column for refine-related context.
- **`docs/backlog.md` is NOT touched** by /refine. Backlog is append-only deferral log written by planning commands.
- Applies cross-cutting interview command behaviors.

### /sdd:retro

Implements `prd.md > /sdd:retro at Project Close`.

**v2 changes:**
- **Recommendation timing:** fires only at project close. `/sdd:plan` and `/sdd:build` recommend `/sdd:retro` only when all PRD ACs are checked AND the Unvetted section is empty. Running `/sdd:retro` on an incomplete project is not blocked (paused/abandoned projects may still want a retro), but it is not recommended.
- **Cross-project pattern capture step** (appended to the retro interview):
  - Fires only after the rest of the retro interview is complete.
  - Captures patterns the user wants to carry forward.
  - Writes confirmed patterns to `~/.claude/sdd-cross-project-patterns.md` (markdown, append-only). Entry format:
    ```
    - YYYY-MM-DD (project-name): User's chosen phrasing of the pattern.
    ```
  - Patterns are written only after explicit per-pattern user confirmation.
- Reads `process-notes-*.md` files selectively (one phase at a time) rather than concatenating all at startup, to avoid loading the full project history.
- Missing per-phase process-notes files for skipped phases produce no warning.

### /sdd:pause (NEW)

Implements `prd.md > Multi-Session Resume > pause stories`.

**Job:** distill the in-flight command's conversation into a resume file.

**Prerequisites:**
- `docs/project-state.json` must exist with `lastCommand` set to one of: `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:refine`, `/sdd:polish`.
- For other `lastCommand` values (`/sdd:onboard`, `/sdd:build`, `/sdd:retro`, `/sdd:feedback`, unset): exit with "nothing in-flight to pause" message; no file written.

**Behavior:**
- Writes `docs/<command>-resume.md` per the schema in `references/pause-resume.md`.
- Overwrites an existing resume file at the target path.
- Final output: literal message `Paused. Run /sdd:unpause to resume.`

See [Pause/Resume Quality Bar](#pause-resume-quality-bar) for the distillation requirements.

### /sdd:unpause (NEW)

Implements `prd.md > Multi-Session Resume > unpause stories`.

**Job:** zero-argument resume of whatever was paused.

**Prerequisites:**
- `docs/project-state.json` must exist.
- A resume file matching the current `lastCommand` value must exist at `docs/<command>-resume.md` (where `<command>` is `lastCommand` without the `/sdd:` prefix).

**Behavior:**
- Accepts no arguments.
- Reads `lastCommand` from `docs/project-state.json` to determine which command to resume.
- Loads `docs/<command>-resume.md` and the source command's `SKILL.md` before any interview work.
- Resumes at the question or decision captured in the resume file's `Pending decision` section.
- Missing resume file → exit with a message naming the file and pointing to `/sdd:pause` or re-running the original command.

**Resume-file cleanup:** when the resumed command's underlying interview completes its document generation step, the resumed command deletes its own resume file at `docs/<command>-resume.md`. Missing-file case raises no error; the command continues.

### /sdd:feedback

Implements `prd.md > Bug Fixes > /sdd:feedback no lastCommand write` and `prd.md > Cross-Project Feedback Transfer`.

**v2 changes:**
- **Startup sequence contains no step that writes to `lastCommand`.** Consecutive `/sdd:feedback` invocations correctly attribute each note to the preceding non-feedback command.
- **Cross-project forwarding:**
  - If `feedbackLocalPath` is set in `~/.claude/sdd-user-profile.json` AND the current working directory is neither equal to nor a descendant of `feedbackLocalPath` (after normalization, see below), write the same note to `<feedbackLocalPath>/docs/sdd-feedback.md` as well.
  - Inside-clone detection skips the forwarding write to prevent double-writes when the user is working in the plugin clone itself.
- **Path normalization for inside-clone detection:**
  1. Resolve both paths to absolute.
  2. Resolve symlinks to real paths.
  3. Normalize separators to forward slashes.
  4. Strip trailing slashes.
  5. Case-fold on Windows and macOS (default APFS); preserve case on Linux.
  6. cwd is "inside" `feedbackLocalPath` iff cwd equals `feedbackLocalPath` OR cwd starts with `feedbackLocalPath + "/"`.
  7. Symlink-loop or resolve errors: fall back to literal string comparison.
- **Failure handling:** forwarding-write failures (path nonexistent, write fails, target is a file not a directory) emit a single warning per session containing the reason and the instruction `Run /sdd:onboard to update.` No retry. No exit-with-error. The local write to current-project `docs/sdd-feedback.md` always completes.
- Note format unchanged from v1; both writes use identical formatting.

---

## Cross-Cutting Mechanisms

### PRD Acceptance Criteria IDs

Implements `prd.md > Bug Fixes > PRD state-tracking` parser-driver.

- **Format:** 4-character lowercase alphabetic IDs (`abcd`) — pattern `[a-z]{4}`. ~457K possible IDs.
- **Assignment:** `/sdd:prd` and `/sdd:refine` generate IDs at write time, checking for collision against existing PRD IDs before settling.
- **AC line format:**
  ```
  - [ ] `abcd` Acceptance criterion text continues here.
  ```
  ID is inline in backticks at the start of the AC text.
- **Stability:** IDs do not renumber. Reordering ACs within a story does not change IDs. Removing an AC removes its ID. Splitting an AC into two results in two new IDs.
- **Backfill:** This project's existing `docs/prd.md` needs IDs added. The implementation sprint that ships `/sdd:prd`'s ID-generation will include backfilling the existing PRD as one of its tasks.

### Sprint-Item Tags

Implements `prd.md > Bug Fixes > PRD state-tracking` validation and `prd.md > v2 Release Verification`.

Sprint items in `docs/sprint-N.md` are single checkbox lines. Each line carries one tag (or two if `[FB: ...]` applies):

```
- [ ] Build /sdd:pause command with resume-file write [PRD: mxqr, pvtb]
- [ ] Implement /sdd:plan validation parser [PRD: znyg] [FB: FB-002]
- [ ] Refactor shared logging helpers [unmapped]
```

- **`[PRD: id1, id2, ...]`** — comma-separated PRD AC IDs. One tag per item; multiple IDs allowed for items satisfying multiple ACs.
- **`[unmapped]`** — explicit tag for items not mapped to any PRD AC.
- **`[FB: FB-NNN, FB-MMM]`** — feedback reference tag for sprint items implementing in-scope feedback items. Mirrors PRD-tag comma format. Optional. Only used in sdd-plugin project (when `docs/v2-verification.md` exists).

**Validation:** `/sdd:plan` parses every checkbox bullet in the proposed sprint file. Every line must have a `[PRD: ...]` tag OR an `[unmapped]` tag. Failure surfaces every offending line and refuses to write the file.

**Parser regex** (lives in `sprint-tags.md`):
- `[PRD: ...]`: `\[PRD:\s*[a-z]{4}(?:\s*,\s*[a-z]{4})*\s*\]`
- `[unmapped]`: literal `[unmapped]`
- `[FB: ...]`: `\[FB:\s*FB-\d{3}(?:\s*,\s*FB-\d{3})*\s*\]`

### Iteration-Candidate Markers

Implements `prd.md > Sprint Loop > /sdd:build wrap-up iteration candidates`.

In-flight marker written by `/sdd:build` to `process-notes-sprint-N.md` during checklist execution when the agent observes a real iteration candidate. Wrap-up parses entries for this marker.

**Format:**
```
[iteration-candidate] During build of /sdd:pause, the resume-file structure
felt too rigid for cases where multiple confirmed decisions are pending —
worth revisiting whether the schema should support a stack. (item: mxqr)
```

- **Required:** literal `[iteration-candidate]` at start of line + free-prose description.
- **Optional:** inline sprint-item reference like `(item: mxqr)` for the reader's benefit. Not parser-enforced.
- **No severity field.** Disposition (discard / tech-debt / iterate) is wrap-up territory.
- **Single class.** Tech-debt, quality concerns, and surprises surface at wrap-up's "anything notable?" prompt, not via in-flight markers.
- **Fabrication prohibited.** Per PRD: agent writes the marker only when a real candidate is observed during execution.
- **Multi-line:** entries continue until the next blank line or next marker.

**Parser regex** (lives in `sprint-tags.md`): start-of-line `\[iteration-candidate\]`, match continues until blank line or next start-of-line tag.

### Close-Sprint Manifest

Implements `prd.md > Sprint Loop > /sdd:build wrap-up close-sprint` and `prd.md > Sprint Loop > /sdd:polish re-open`.

Structured block written by `/sdd:build` close-sprint to `process-notes-sprint-N.md`. Records what happened at close in a form `/sdd:polish` re-open can deterministically reverse.

**Format:**
```
[close-sprint-manifest] timestamp: 2026-05-12T15:45:00Z
- PRD ACs checked: mxqr, pvtb, znyg
- Story splits: <original-story-anchor> → <new-story-anchor>: <ac-ids>; ...
- Iteration candidate dispositions: <candidate-summary>: discard / tech-debt / iterate; ...
- Tech-debt entries: <count> (informational)
- v2-verification rows updated: FB-005, FB-012 (informational)
[/close-sprint-manifest]
```

**Re-open behavior** (`/sdd:polish`):
1. Read most recent manifest from `process-notes-sprint-N.md`.
2. Emit literal `Re-open the sprint?` prompt.
3. On confirmation: list manifest's checked-off PRD ACs; ask "Which (if any) should be re-opened?"
4. For each selected AC: un-check in PRD; reverse split (if part of a manifest split mapping); if split-off story has no remaining ACs after reverse, delete the empty split-off story.
5. ACs added by `/sdd:refine` to the split-off story since close → not touched by reverse (they stay).
6. ACs deleted by `/sdd:refine` since close → informational warning, skip, no block.
7. Append a `[sprint-reopened]` marker to process notes:
   ```
   [sprint-reopened] timestamp: ...; un-checked: mxqr; splits-undone: <original> → <new>
   ```
8. Proceed with `/sdd:polish` iteration flow.
9. On rejection: exit with re-open-required message.

**What re-open does NOT reverse:**
- Iteration-candidate dispositions (user already decided; re-litigation is manual).
- Tech-debt entries (append-only history).
- `docs/v2-verification.md` "Shipped" rows (implementation did ship; verification is manual anyway).

**Multiple cycles:** the most recent manifest is authoritative. Earlier manifests stay as history but don't drive re-open logic.

### Right-Sizing Heuristic

Implements `prd.md > Interview Command Behaviors > small project`.

`smallProject` field in `docs/project-state.json` carries a tentative-or-authoritative judgment about project scale. See `references/right-sizing.md` for signals, skippable beats per command, and the thinness rule.

**Detection placement:**
- `/sdd:scope` — **authoritative initial determination** at end of Phase 1.
- Every subsequent interview command — re-evaluates on startup; may flip on contradictory or first-surfacing evidence.

**Note on PRD edit:** Initial PRD assigns detection to `/sdd:discovery`. v2 spec relocates detection to `/sdd:scope` to keep `/sdd:discovery` focused on open exploration. See [PRD Edits Required](#prd-edits-required).

### Three-Tier Between-Rounds Context Management

Implements `prd.md > Context Management > between rounds`. Owned by `references/context-management.md`.

At the end of every deepening round, after the round-continuation recommendation, the agent emits a separate recommendation choosing one of three tiers: **continue / `/compact` / `/clear`**.

**Signals the agent considers:**
- Round size (questions, answer length).
- Artifact-conversation gap (does the in-progress artifact alone catch a fresh agent up?).
- Superseded noise (false starts, walked-back recommendations).
- Token-usage approximation (round word count, file reads, tool calls).

**Tier mapping:**
- **continue** — short round, artifact captures the value.
- **/compact** — substantive round whose conversation carries detail the artifact doesn't, but compaction will preserve the carry-forward.
- **/clear** — long/heavy round, artifact is rich and self-contained, conversation dominated by superseded noise.

**Failure-mode mitigation:**
- AI self-assessment of context weight is fallible.
- Recommendation is always definite (PRD-required).
- User may always override upward (heavier tier than recommended).
- User downward overrides carry a brief context-weight warning from the agent before complying.

**`/clear` branch consolidation:** when the agent recommends `/clear`, it first writes a resume file (using `/sdd:pause`'s schema — see [Pause/Resume Quality Bar](#pause-resume-quality-bar)) and instructs the user to `/clear` then `/sdd:unpause`.

### End-of-Command Handoff

Implements `prd.md > Context Management > between commands`. Template lives inline in `sdd-guide/SKILL.md` core.

Every interview command emits a handoff message unconditionally at completion.

**Standard form (every interview command, every time):**
```
[One-line outcome summary, e.g., "Spec generated."]

Run `/clear`, then `/sdd:[next-command]` to continue.
```

**First-handoff explanation (prepended once per user, then never again):**
```
Heads-up: `/clear` wipes this conversation window so the next command starts fresh.
Your work is safe in `docs/` and process-notes files — only the chat scrollback gets
cleared. This handoff fires after every interview command from now on.

[standard form follows]
```

Tracked via `handoffWarningShown` in `~/.claude/sdd-user-profile.json` (seeded false by `/sdd:onboard`, flipped true by the first interview command that emits a handoff). Cross-project, one-time-per-user.

**Next-command map:**

| Outgoing | Next |
|---|---|
| /sdd:onboard | /sdd:discovery |
| /sdd:discovery | /sdd:scope |
| /sdd:scope | /sdd:prd |
| /sdd:prd | /sdd:spec |
| /sdd:spec | /sdd:plan |
| /sdd:plan | /sdd:build |
| /sdd:refine | /sdd:plan |
| /sdd:polish | /sdd:build |

**Out-of-pattern commands:**
- `/sdd:build` — not in the interview-command set. Next-command recommendation comes from wrap-up state check (/plan, /refine, /polish, or /retro).
- `/sdd:retro` — terminal. Emits "Project closed." plus a brief pointer to cross-project pattern capture. No `/clear` instruction.
- `/sdd:pause` — emits PRD-required `Paused. Run /sdd:unpause to resume.` Overrides normal handoff.
- `/sdd:unpause` — no separate handoff; the resumed command emits its normal handoff at completion.
- `/sdd:feedback` — emits no handoff (instant-return per PRD).

### Pause/Resume Quality Bar

Implements `prd.md > Multi-Session Resume > pause distillation`. Owned by `references/pause-resume.md`.

Resume file at `docs/<command>-resume.md`. Required sections in order:

| Section | Quality bar |
|---|---|
| **Status** | 1–2 sentences. Which command is in flight + where in the flow. |
| **Setup-already-done** | Bullets of completed actions, each self-contained. |
| **Confirmed decisions** | The meat. Substantive locked decisions with brief rationale + cross-references to source artifacts. |
| **Round status** | Phase + round number + last completed beat or question. |
| **Pending decision** | The specific open question (not a topic gesture). Literal question + what's been weighed + the options on the table. |
| **Files for next-session** | Paths the resumed agent must read before resuming. |
| **Instructions for next-session** | Direct address to the resumed agent. "You are resuming `/sdd:[command]` at Beat N. Confirm decisions in this file with the user, then continue with…" |

**Distill-not-dump rules:**
- No verbatim back-and-forth — only outcomes.
- No agent reasoning trail — only conclusions.
- No re-explanation of plugin behavior — point at source `SKILL.md`.
- Cross-reference artifacts; don't restate them.
- **Length target: 1–3 pages of markdown.** If significantly longer, distillation has failed; the agent compresses before writing.

Both `/sdd:pause` and the three-tier `/clear` branch use the same schema.

### Backlog Entry Format

Implements `prd.md > Automatic Backlog Generation`.

Each entry in `docs/backlog.md`:

```markdown
### [Item title]
- **Title:** Same as the heading.
- **What:** Description of the item.
- **Why deferred:** Reason it's not in the current project's scope.
- **Trigger to revisit:** What event or condition should bring this back into consideration.
- **Dependencies:** What it depends on (other backlog items, future work, external factors).
- **Source:** /sdd:[command] — [phase/round identifier] — [topic]

---
```

**Source field examples:**
- `Source: /sdd:scope — Phase 1, beat "audience" — local tech group as contributors vs consumers`
- `Source: /sdd:prd — Round 2 deepening — story-split threshold edge cases`
- `Source: /sdd:spec — Phase 1, beat "v2-verification.md schema" — grouping rows by epic`

**Format conventions:**
- Fields in the exact order shown.
- Entries separated by `---` on its own line.
- Entries appended in write order; existing entries are not modified by subsequent commands.
- When `docs/backlog.md` has at least one entry, planning artifacts (`scope.md`, `prd.md`, `spec.md`, `sprint-N.md`) include a pointer to `docs/backlog.md` in lieu of an inline deferred-items section.

**Write trigger:** any of `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan` emits an explicit defer-to-backlog confirmation prompt before writing. The prompt distinguishes "defer to backlog" from "drop." Only an affirmative defer answer produces an entry. Dropped items are recorded only in the current phase's process-notes file.

### File Uploads (docs/refs/)

Implements `prd.md > /sdd:discovery Command > file uploads`. Spec-time decision: which commands read.

Shared reference directory `docs/refs/` within the project's `docs/`. User manages the directory between commands; no interactive upload UI.

**Readers:**
- **`/sdd:discovery`** (PRD-required) — read before Phase 1. If non-empty, agent emits a one-line acknowledgment naming files (no content summaries).
- **`/sdd:spec`** — read at startup as architectural reference context. Primary beneficiary: tech reference docs, integration specs, prior architecture, security/compliance constraints.

**Non-readers:** `/sdd:scope`, `/sdd:prd`, `/sdd:plan`, `/sdd:refine`, `/sdd:polish`, `/sdd:retro`, `/sdd:build`, `/sdd:pause`, `/sdd:unpause`, `/sdd:feedback`, `/sdd:onboard` do not read `docs/refs/`.

**Empty-directory behavior:** directory absent or empty → reader proceeds without prompting.

**Lifecycle:** mutable across project lifecycle. No lock semantics.

---

## Plugin-Project-Specific Artifacts

### docs/v2-verification.md

Implements `prd.md > v2 Release Verification`.

Sdd-plugin-specific audit-trail artifact. Persists after v2 ships. Future plugin versions ship `docs/<version>-verification.md` per the same pattern.

**Format:** markdown table.

```markdown
| ID | Source | Summary | Shipped (sprint/item) | Verified | Notes |
|----|--------|---------|-----------------------|----------|-------|
| FB-001 | Event Planning App | /sdd:feedback clobbers lastCommand on consecutive runs | | `[ ]` | |
| FB-002 | fiver-gigs | PRD state-tracking missing for non-split items | | `[ ]` | |
| ... | | | | | |
```

**Columns:**
- **ID:** `FB-NNN`, three-digit-padded, sequential. Stable. Assigned at seeding time. Never renumbered.
- **Source:** project of origin (Event Planning App, fiver-gigs, sdd-plugin self-use).
- **Summary:** one-line description of the feedback item.
- **Shipped (sprint/item):** auto-written by `/sdd:build` wrap-up on close. Format: `sprint N, item M`. Multiple values appended for cross-cutting items: `sprint 2 item 3; sprint 3 item 1`.
- **Verified:** manual-only checkbox `[ ]` / `[x]`. **No SDD command writes a checked verified box.**
- **Notes:** free text. `/sdd:build` wrap-up may append brief auto-notes; user may edit freely. `/sdd:retro` does not touch.

**Write triggers:**
- **Seeding:** `/sdd:spec` finalization creates the file (this project only) and seeds rows with FB-IDs assigned to each in-scope feedback item from the v2 scope's triaged list.
- **"Shipped" updates:** `/sdd:build` wrap-up on close-sprint, for each closed item carrying `[FB: ...]`.
- **"Notes" updates:** `/sdd:build` wrap-up (auto-append); user (manual).
- **"Verified" updates:** user only.

**Activation gate:** the file's presence in the project IS the activation flag. `/sdd:plan` prompts for `[FB: ...]` tags only when the file exists. `/sdd:build` wrap-up updates rows only when the file exists. No SDD command creates the file in any project other than sdd-plugin.

**Lifetime:** no SDD command deletes, archives, or removes `docs/v2-verification.md` after v2 ships. The file is a historical record.

---

## Migration

### MIGRATION-v1-to-v2.md

Implements `prd.md > v1 → v2 Migration`.

Ships at `plugins/sdd/MIGRATION-v1-to-v2.md`.

**Opening framing** (PRD-mandated):
> v2 adds `/sdd:discovery` at the start of the planning chain. `/sdd:scope` is narrower (boundary only — exploration moved to `/sdd:discovery`). `/sdd:prd` and `/sdd:spec` are unchanged. `/sdd:sprint` is renamed to `/sdd:plan`. `/sdd:iterate` is renamed to `/sdd:polish`. `/sdd:reflect` is removed (work absorbed into `/sdd:build` wrap-up and `/sdd:retro`). New utilities: `/sdd:pause` and `/sdd:unpause`.

The intermediate-shuffle framing (in which `/scope` and `/prd` were renamed in tandem before `/prd` was reinstated) is NOT presented. It nets to nothing useful for end users.

**State-aware migration table** keyed on v1 `lastCommand`, with mid-flight vs completed columns:

| v1 `lastCommand` | If mid-flight | If completed |
|---|---|---|
| `/sdd:onboard` | n/a (single-session) | `/sdd:discovery` |
| `/sdd:scope` | `/sdd:scope` (continue; v2 scope is narrower — exploration belongs in `/sdd:discovery` if you want it captured) | `/sdd:prd` |
| `/sdd:prd` | `/sdd:prd` (continue) | `/sdd:spec` |
| `/sdd:spec` | `/sdd:spec` (continue) | `/sdd:plan` |
| `/sdd:sprint` | `/sdd:plan` (renamed; `sprint-N.md` preserved) | `/sdd:build` |
| `/sdd:build` | `/sdd:build` (continue; v2 wrap-up fires on final item check) | `/sdd:plan` |
| `/sdd:iterate` | `/sdd:polish` (renamed) | `/sdd:build` (resume sprint with appended items) |
| `/sdd:reflect` | `/sdd:build` (v2 wrap-up absorbed reflect's work — re-enter `/build`) | `/sdd:plan` (sprint closed; plan next sprint, or run `/sdd:retro` if at project close) |
| `/sdd:refine` | `/sdd:refine` (continue) | `/sdd:plan` |
| `/sdd:retro` | `/sdd:retro` if at project close in v2's sense (all PRD ACs checked + Unvetted empty); otherwise abandon — v2 doesn't fire `/retro` per-sprint | Project complete OR `/sdd:plan` if per-sprint use in v1 (project still has work) |
| `/sdd:feedback` | n/a | Re-orient: real state before `/feedback` is lost in v1 due to the lastCommand bug — check sprint file presence or just enter `/sdd:plan` |

### Resume-File Renames

| v1 resume file | v2 resume file |
|---|---|
| `sprint-resume.md` | `plan-resume.md` |
| `iterate-resume.md` | `polish-resume.md` |
| `reflect-resume.md` | (no v2 equivalent — review the notes, then enter `/sdd:build` to use its wrap-up) |

### Other Migration Content (PRD-required)

- **No command-name aliases in v2.** Migration guide explicitly states this.
- **`process-notes.md` (legacy)** treated as a read-only historical reference. Migration guide includes an optional one-time split recommendation for converting legacy `process-notes.md` into per-phase files.
- **`docs/archive/v1/`** untouched by v2. Migration guide states this. Index file inside the archive contains a one-line "pre-rename snapshot" note.
- **`feedbackLocalPath` introduction:** dedicated section explaining the field and its purpose.
- **`/sdd:pause` / `/sdd:unpause` introduction:** dedicated section explaining the new utilities.
- **`/sdd:discovery` introduction:** dedicated section stating it is a genuinely new command (not a rename).

### Atomic Rename Sweep

Full sweep on active material at migration time: `skills/`, `commands/`, `sdd-guide`, README, all live prose under `docs/` (excluding `docs/archive/v1/`). Every reference to v1 command names updated to v2 names, except in explicit migration-context references. No aliases shipped.

sdd-plugin self-migration runs the standard guide. Manual archival of v1 planning artifacts under `docs/archive/v1/` was already completed before v2 scoping began.

---

## Testing Strategy

The plugin is pure markdown with no runtime code, so traditional automated testing doesn't apply. v2 testing mirrors v1.

### Unit Tests

Not applicable. No runtime code.

### Integration Tests (Manual / Structural)

- **Plugin validation:** `claude plugin validate .` or `/plugin validate .` — checks plugin.json, marketplace.json, SKILL.md frontmatter, directory structure.
- **Cross-document consistency:** every spec section that cites a PRD epic resolves to an actual epic in `docs/prd.md`. Every `[PRD: ID]` in a sprint file resolves to an actual AC ID in `docs/prd.md`. Tooling for this lives in the dogfood, not in the plugin.
- **Tag parsing:** the regexes in `references/sprint-tags.md` are exercised by `/sdd:plan` validation and `/sdd:build` wrap-up. Misformatted tags are caught by validation, which is the test signal.

### End-to-End Testing

Per `prd.md > v2 Release Verification`:
- **Primary dogfood:** Jason runs v2 end-to-end on a small website project covering `/sdd:discovery` through `/sdd:retro`.
- **Coverage for items that won't fire on a small build:** other in-progress projects in varying completion states exercise cross-machine pause, the 10-question cap, per-round `/clear`-and-continue, cross-project pattern capture, and longer deepening rounds.
- **Deliberate exercise of `/sdd:pause` and `/sdd:unpause`** even when not strictly needed.
- **Verification tracking:** `docs/v2-verification.md` populated by implementing commands and manually checked off by Jason as items are exercised end-to-end.

### Context Budget Testing

`/sdd:build` re-entry on large projects must not trigger Claude Code's auto-compaction. Tested by running v2 on a multi-sprint project and monitoring for auto-compaction signals.

---

## PRD Edits Required

These v2 spec decisions revise PRD acceptance criteria. The PRD update happens during spec finalization, before any v2 implementation sprint begins, per `living-documents.md`'s update ordering for `/sdd:refine` (extended here to a special spec-time edit because the v2 spec phase is allowed to revise the v2 PRD).

1. **Small-project detection relocates from `/sdd:discovery` to `/sdd:scope`.**
   - **Remove from `/sdd:discovery` Command epic:**
     - "When small-project signals are detected, `/sdd:discovery` writes `smallProject: true` to `docs/project-state.json`."
     - "When small-project signals are absent, `/sdd:discovery` either omits the `smallProject` field from `docs/project-state.json` or sets it to `false`."
     - "/sdd:discovery detects small-project signals during its interview..."
   - **Add to `/sdd:scope` (within Interview Command Behaviors small-project story or as a new story under a `/sdd:scope` epic):**
     - `/sdd:scope` detects small-project signals during Phase 1.
     - `/sdd:scope` writes the authoritative `smallProject` value to `docs/project-state.json` at end of Phase 1.
     - Signals listed in `references/right-sizing.md`.

2. **Add `defaultSprintMode` and `handoffWarningShown` to the user profile** schema in the `/sdd:onboard` Polish epic stories that reference profile fields.

3. **Add `/sdd:retro` cross-project pattern file** as `~/.claude/sdd-cross-project-patterns.md` (vs the deferred-to-spec PRD AC "sibling file").

The PRD edit ships alongside this spec, before any v2 implementation sprint begins.

---

## Key Technical Decisions

| Decision | Why | Tradeoff |
|----------|-----|----------|
| Lean delta-and-resolution spec | v1 spec duplicated runtime content, creating maintenance liability. Compress to "what + why"; let `references/*.md` own "how." | Spec readers must follow cross-references to see full behavior. Mitigated by reference files being short and well-named. |
| 4-char alphabetic AC IDs, opaque | Stable across `/sdd:refine` reorders and rewords. No positional brittleness. | Sprint files unreadable without loading PRD context. Acceptable because `/sdd:plan` and `/sdd:build` always load relevant PRD sections anyway. |
| Single-class `[iteration-candidate]` marker | Quality/tech-debt/surprises caught by wrap-up's "anything notable?" prompt. One marker class avoids surface-area sprawl. | In-flight tech-debt observations don't get structured surface — rely on user to mention at wrap-up. Acceptable given the explicit catch-all prompt. |
| `/sdd:scope` owns `smallProject` detection (not `/sdd:discovery`) | `/sdd:discovery` is open exploration; adding detection expands its scope. `/sdd:scope` draws the boundary, which IS the smallness determination. | Revises a PRD AC. Mitigated by explicit "PRD Edits Required" section. |
| Per-round summary IS the pause resume file | Consolidates two artifacts with the same purpose. One schema, one parser. | When the three-tier model recommends `/clear`, the agent writes a resume file before recommending — extra step but small. |
| Manifest-based, user-driven re-open | Deterministic state for `/sdd:polish` to reverse. Surgical un-check rather than blanket revert. | Adds bulk to process notes. Worth it because re-open needs to know what to undo. |
| No auto-checkpoint in `/sdd:build` | v2 is a streamlining pass, not feature expansion. Clean-state = last user commit. | Loses some safety vs auto-checkpoint. Acceptable; can be added in a future `/sdd:refine` pass. |
| `docs/refs/` read by `/sdd:discovery` and `/sdd:spec` only | Two natural consumers; other commands derive from upstream artifacts. Keeps complexity low. | Late-arriving references don't influence `/sdd:scope`. If they substantively change shape, that's a re-scope event, not a `/scope` rerun. |
| Cross-project patterns in a separate markdown file | Patterns are free-form guidance; markdown fits better than JSON fields. Keeps profile clean. | Two files for `/sdd:onboard` to load on display. Trivial. |

---

## Open Issues

Carry-forward concerns surfaced or not fully resolved during this spec phase.

- **MIT-distribution soft acceptance criteria** — several PRD ACs rely on personal judgment (e.g., "enough context to pick this up cold months later" for backlog entries). Fine for self-use, weaker for outside contributors. **Defer to `/sdd:refine`** when items with judgment-based criteria are next refined — anchor them with example entries or anti-examples.

- **One-time PRD AC ID backfill** for this project's existing `docs/prd.md`. Listed as a task in the v2 sprint that implements `/sdd:prd`'s ID generation.

- **The PRD-edit step for `smallProject` relocation** is itself a one-shot operation that needs to happen before any v2 implementation sprint reads the PRD with the expectation that `/sdd:scope` detects. Tracked in [PRD Edits Required](#prd-edits-required).
