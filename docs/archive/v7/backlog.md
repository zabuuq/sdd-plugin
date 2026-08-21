# Backlog

Deferred items surfaced during planning. Each entry is fully self-contained so it can be picked up cold months later. In-flight planning docs reference this file by pointer (no inline restatement).

---

### Evaluate Get Shit Done and Superpowers as v2.x/v3 influences
- **What:** Read and evaluate two external projects (Get Shit Done — https://github.com/gsd-build/get-shit-done; Superpowers — https://github.com/obra/superpowers) for ideas that could inform a future SDD pass.
- **Why deferred:** v2's spine is "streamline v1 from feedback." Adding an external-research beat would dilute that. Both projects came from people Jason told about SDD; neither has been opened yet.
- **Trigger to revisit:** When opening v3 scope, or earlier if a v2 user explicitly requests a feature one of them solves.
- **Dependencies:** None.
- **Source:** /sdd:scope — v2 cycle, Phase 1 Beat 3 (external references).

---

### Bidirectional feedback share-back to plugin upstream repo
- **What:** Let `/sdd:feedback` optionally submit a captured note as a GitHub issue against the plugin's upstream repo. Includes:
  - New `feedbackShareUrl` profile field (GitHub repo URL).
  - Per-note prompt at `/feedback` time: "submit to upstream repo?"
  - `gh issue create` integration (auth check during `/onboard`).
  - Project-name redaction toggle for users who don't want project names leaking.
  - Optional `/sdd:feedback-submit` batching command for users who want to queue notes and submit later.
- **Why deferred:** First "new capability" item in v2's scope cycle rather than refinement of existing behavior. v2's audience-shift goal can be met short-term with tech group members emailing/Slacking feedback files manually. Deferring keeps v2 streamlining-focused. If manual transfer proves painful for the tech group, v3 picks it up.
- **Trigger to revisit:** Tech group feedback indicates manual transfer is real friction, OR plugin gains users beyond the tech group, OR v3 cycle opens.
- **Dependencies:** `gh` CLI installed and authed on user's machine. Marketplace path conventions for resolving upstream repo (the user-profile route is preferred — keeps SDD decoupled from Claude Code internals).
- **Source:** /sdd:scope — v2 Round 1 Topic 3.

---

### Recommend auto-update for the SDD marketplace
- **What:** Promote the auto-update note in `/sdd:onboard` from informational ("here's how third-party plugin updates work") to recommendation ("you should turn this on").
- **Why deferred:** Without share-back (above item), auto-update is one-way distribution. Pushing users to flip it on starts feeling like advocacy for an architecture choice that isn't fully wired yet. Information now; recommendation when share-back closes the loop.
- **Trigger to revisit:** Bidirectional share-back ships (item above).
- **Dependencies:** Bidirectional share-back.
- **Source:** /sdd:scope — v2 Round 1 Topic 3 closure.

---

### Pull Caveman's executable machinery into SDD (hook / MCP / scripts)
- **What:** Research whether SDD can adopt Caveman's non-markdown components — the Claude Code auto-activation hook, the `caveman-compress` memory-file compressor, and the `caveman-shrink` MCP middleware (compresses tool descriptions) — rather than only mining their techniques. Determine feasibility, what a clean integration would look like, and what it would cost in install/runtime complexity. v3 deliberately takes only the *techniques* (reimplemented as native markdown in sdd-guide); this item is the heavier "take the machinery too" path.
- **Why deferred:** Adopting the machinery means SDD stops being a pure-markdown, zero-runtime plugin — it grows a hook and/or scripts and an MCP service. That's a posture change too large for v3, whose spine is native token-efficiency with the markdown-only invariant preserved. Worth doing only if native technique-mining proves insufficient and the efficiency payoff justifies the new runtime surface and install steps.
- **Trigger to revisit:** v3 ships and native-only efficiency gains fall short of the Caveman benchmark (~46% input / ~65% output reduction), OR a future cycle accepts SDD taking on a runtime.
- **Dependencies:** Caveman repo (https://github.com/juliusbrussee/caveman) — likely fork/vendor to study and adapt; MCP middleware support in the user's Claude Code setup; settings.json hook plumbing.
- **Source:** /sdd:scope — v3 cycle, Phase 1 Beat 1 — native-vs-machinery Caveman boundary.

---

### Automated third-party AI validation runs in /sdd:validate
- **What:** Extend `/sdd:validate` so Claude can *trigger* external AI validators (ChatGPT/Codex, Gemini, etc.) automatically, not only ingest manually-dropped files. Maintainer's design guidance to preserve:
  - The automated runs live **inside** the `/sdd:validate` command.
  - The front-of-command ask pattern generalizes: just as validate asks "run Claude's adversarial critique? (y/n)", each added external tool gets its own up-front yes/no — "run ChatGPT? run Gemini?" — asked at the front before any run.
  - On each yes, validate fires off a **separate agent** (one for Claude's own critique, one per external tool — one agent per yes). Each agent writes its findings as a validation file into `docs/validation/`, uniform with manual drops.
  - The reconciliation interview then runs over all produced files exactly as it does today.
  - The source list is **extensible** — new tools slot in as additional asks + agents, so the maintainer can add more later.
- **Why deferred:** Maintainer wants v6 finished as-is without the automation; it's a follow-on run. Feasibility of free-tier ChatGPT/Gemini MCP/API access is unknown (was the standing `docs/open-concerns.md` item). Keeping it out lets v6 ship on the load-bearing manual/self-validation file-drop path. The v6 architecture already accommodates it: `docs/validation/` is a source-agnostic drop-zone, so automation is a pure external producer needing no structural change.
- **Trigger to revisit:** After v6 ships, as a dedicated follow-on run; or earlier if free-tier MCP/API access for ChatGPT/Gemini is confirmed usable.
- **Dependencies:** Free-tier (non-paid) MCP or API access to ChatGPT/Codex/Gemini — PRD Non-Goal bars paid models. The v6 `/sdd:validate` command this extends (ask-first flow + source-agnostic `docs/validation/` drop-zone + agent fan-out).
- **Source:** /sdd:spec — v6, validate design — automated external-AI validation round-trip.

---

### GitHub-native automation for PR/branch hygiene
- **What:** Move the PR/branch-hygiene work that `/sdd:resolve-pr` does manually (deleting branches for closed/merged PRs, and surfacing PRs with unresolved feedback) toward GitHub-native automation — e.g. a GitHub Action that auto-deletes head branches on merge, and/or automation that flags/routes PRs carrying review comments. `/sdd:resolve-pr` stays the interactive path; this is the "let GitHub do the routine hygiene" layer on top.
- **Why deferred:** Maintainer's words — "having some automated processes through GitHub will be ideal, but we're not there yet." v6 ships with the manual `/sdd:resolve-pr` command as the load-bearing path; GitHub-native automation is a later refinement once the manual flow is proven.
- **Trigger to revisit:** After v6's build half is exercised on a real project and the manual `/sdd:resolve-pr` flow is validated; or when repo-level GitHub Actions are being set up anyway.
- **Dependencies:** The v6 `/sdd:build` PR/branch-stamping convention (label + branch prefix) that identifies build-created PRs; repo permissions to run Actions.
- **Source:** /sdd:spec — v6, build/resolve-pr design — PR/branch hygiene automation.

---

### Remove the `/clear` handoff from `/sdd:archive`
- **What:** `plugins/sdd/skills/archive/SKILL.md` line 386 still emits the line "Run `/clear`, then `/sdd:discovery` to start the next cycle." as its cycle-reset handoff. Cycle 7 removed the `/clear` nudge from every other command handoff (commit 6bbb240, "Add /sdd:amend, drop /clear and AC checkboxes"), but archive was out of that pass's file-disjoint partition and kept its copy. Rewrite the handoff to point straight at `/sdd:discovery` with no context-management instruction, matching how `/sdd:build` and `/sdd:retro` now end. Note the other `/clear` mentions across the plugin are **not** stale and should be left alone — `pause/SKILL.md`, `checkpoint/SKILL.md`, and `references/pause-resume.md` describe pause/resume mechanics, and `build`, `retro`, and `sdd-guide` explicitly state that *no* `/clear` handoff is emitted. Archive's is the only surviving instruction telling the user to run it.
- **Why deferred:** Surfaced during PR #25 (cycle 7's shared-behavior issue) but out of scope for that issue — the cycle-7 build partitioned issues by file, and `archive/SKILL.md` was owned by no issue in that partition. Fixing it there would have broken the file-disjoint invariant the partition depended on.
- **Trigger to revisit:** Next cycle that touches `plugins/sdd/skills/archive/SKILL.md`, or the next general docs-consistency pass. Cheap and self-contained — a one-command fix whenever a cycle has room.
- **Dependencies:** None. Single file, single handoff block.
- **Source:** /sdd:retro — cycle 7; surfaced out-of-scope in PR #25.

---

### Update `AGENTS.md` and `living-documents.md` off the v5 sprint/PRD model
- **What:** Both files still document the pre-v6 world of sprint files, a separate PRD, and checkbox-tagged acceptance criteria — a structure v6 replaced with the single `docs/plan.md` document and inline markers. Known stale spots:
  - `AGENTS.md` ~line 103 — "ID-based PRD acceptance criteria references," describing `[PRD: a7kp]` tags on sprint items and naming `/sdd:prd` and `/sdd:plan`, neither of which exists in the v6 command set.
  - `AGENTS.md` ~lines 145–168 — the "Sprint item format" and "PRD acceptance criterion format" blocks, plus a tag-conventions list naming `[close-sprint-manifest]` and `[sprint-reopened]` (the latter emitted by `/sdd:polish`, a command v6 dropped). The AC checkbox format `- [ ] `abcd` ...` was explicitly dropped in commit 6bbb240.
  - `AGENTS.md` also still describes `docs/open-concerns.md` as cross-phase continuity carried across `/clear` boundaries.
  - `plugins/sdd/skills/sdd-guide/references/living-documents.md` ~line 92 — the PRD-edit cascade step that opens sprint files and updates `[PRD: ...]` refs, gated on a `[close-sprint-manifest]` block.
  Audit both files end-to-end rather than patching only these lines; the rot is likely broader than the greps that found it. Decide per section whether the v6 equivalent (plan.md, inline markers, `references/markers.md`) replaces it or the section is simply gone.
- **Why deferred:** Same reason as the archive `/clear` item — surfaced in PR #25 but outside cycle 7's file-disjoint issue partition. It's also genuinely larger than a line fix: `AGENTS.md` is the durable agent-facing source of truth, so correcting it means re-deriving what the v6 model actually says, not find-and-replace.
- **Trigger to revisit:** Next cycle that opens `AGENTS.md`, or before onboarding anyone new to the repo — stale agent instructions actively mislead, and `AGENTS.md` is loaded by default.
- **Dependencies:** The v6 plan.md/marker model must be settled (it is — `docs/spec.md` and `references/markers.md` define it). Note `AGENTS.md` points at `plugins/sdd/skills/sdd-guide/references/sprint-tags.md` as the full tag reference and that file no longer exists — the pointer is already dangling, which is a second reason this audit is overdue.
- **Source:** /sdd:retro — cycle 7; surfaced out-of-scope in PR #25.

---
