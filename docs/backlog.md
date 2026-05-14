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
