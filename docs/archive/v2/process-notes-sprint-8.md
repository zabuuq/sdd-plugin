# Sprint 8 — Process Notes

## Planning Session — 2026-05-13

### Context

Sprint 8 planning kicked off immediately after Sprint 7's wrap-up, same calendar day. Sprint 7 closed `/sdd:onboard` Polish (18 ACs: `aacw`–`aadn`); the `/sdd:onboard` SKILL.md now seeds `feedbackLocalPath` (Item 2 of Sprint 7), `handoffWarningShown`, and `defaultSprintMode` (Item 4), surfaces re-run preferences with source labels, and carries the `/sdd:discovery` heads-up. The natural follow-on is to wire `/sdd:feedback` v2 to consume the just-seeded `feedbackLocalPath` field.

Planning session ran under the user's `work without stopping for clarifying questions` directive — no `AskUserQuestion` checkpoints fired. Proposal was assembled and committed directly to `docs/sprint-8.md`. The user can redirect by editing the sprint file before `/sdd:build` runs.

### Why /sdd:feedback v2 (CPFT + Bug Fix 1) next

Six reasons this sprint beats the alternatives:

1. **Direct continuation of Sprint 7.** Sprint 7 seeded `feedbackLocalPath`. Without Sprint 8's `/sdd:feedback` consumer, that field sits in the profile with no reader. Sequential dependency.

2. **Audience-facing differentiator.** Per AGENTS.md, v2 must clear two bars — "working software" AND "feedback loop must be real and easy to use." CPFT delivers the second. The local tech group joining as feedback sources gets a functional pipeline: feedback they file from within their own projects forwards to the maintainer's plugin clone automatically.

3. **Single file, well-bounded.** All 16 ACs live in `plugins/sdd/skills/feedback/SKILL.md`. No cross-file edits, no sdd-guide changes, no plugin manifest changes. Matches the Sprint 5/6/7 single-file pattern.

4. **First sprint with `[FB: ...]` tags.** Sprints 1–7 had no FB-tagged items (Sprints 1–4 predate the tag mechanism; Sprints 5–7 implemented internal plugin mechanics not on the FB-list). Sprint 8 closes four FB rows in `docs/v2-verification.md`: FB-001 (Bug Fix 1), FB-030 (dual-write), FB-031 (inside-clone detection), FB-032 (failure handling). First real exercise of Sprint 5's `[FB:]` row-update logic.

5. **Bug Fix 1 is verification-only.** The no-`lastCommand`-write behavior already ships in `feedback/SKILL.md` (lines 27–29 and 117) — written during early v2 development before Bug Fix 2's auto-checkoff mechanism existed, so the PRD ACs `aaaa`–`aaac` are unchecked despite the work having shipped. Sprint 8's Item 1 closes the gap with a verify-and-checkoff pass. Cheap, honest win.

6. **Mechanical-vs-exploratory: mechanical.** Every AC names specific writes, conditions, error paths. The spec (`docs/spec.md > Command Skills > /sdd:feedback`) documents the cross-project forwarding, the 6+1-step path normalization, and the failure-handling semantics in full. No design ambiguity at planning time. Matches Sprints 5/6/7 character.

### Sprint candidate analysis

Considered four candidate themes:

| Candidate | Size | Risk | Notes | Verdict |
|---|---|---|---|---|
| CPFT + Bug Fix 1 (chosen) | 16 ACs (3 + 13) | Low | Single file, mechanical, builds on Sprint 7, first FB-tag sprint, closes historical-AC gap | **Selected** |
| Bug Fix 1 + `/sdd:polish` re-open | 6 ACs (3 + 3) | Low | Two files, two unrelated commands, smaller payoff than CPFT | Defer |
| Interview Command Behaviors + Context Management | 34 ACs (24 + 10) | High | Largest sprint of the project; touches sdd-guide core + 4+ reference files; biggest single payoff (unblocks `/sdd:discovery`) | Defer — deserves its own focused sprint |
| Multi-Session Resume | 17 ACs | Medium | Two new commands + cleanup; natural successor after CPFT lands the feedback loop fully | Defer one sprint |

**Why CPFT wins over Interview Command Behaviors despite the latter's bigger payoff:** Interview Command Behaviors is the highest-leverage sprint in the project (24 ACs cross-cutting, unblocks `/sdd:discovery`), but its scope spans 5+ files and the risk of mid-sprint splits is non-trivial. Building it after CPFT lets Sprint 8 be a fast, well-bounded win that closes the v2 feedback loop; Sprint 9 (or 10) can then focus the full sprint's attention on Interview Command Behaviors without the additional surface area of `/sdd:feedback` changes also in flight.

**Why not bundle `/sdd:polish` re-open into Sprint 8:** `/sdd:polish` re-open is 3 ACs in `polish/SKILL.md` (different file). Bundling diffuses Sprint 8's focus and breaks the single-file pattern. Cleaner as a small standalone sprint or paired with Interview Command Behaviors later.

### PRD scan

- 13 epics in `docs/prd.md`, 206 ACs total.
- **Checked ACs (post-Sprint-7):** 58 (21 from Sprint 5 + 19 from Sprint 6 + 18 from Sprint 7). Sprint 5's wrap-up also checked `aaad`–`aaag` (Bug Fix 2 self-implementing ACs), `aahk`, and `aahv` (v2-verification row update behavior + sdd-plugin gate), bringing the actual checked count higher — exact count not load-bearing for planning.
- **Unchecked across remaining epics:** Bug Fix 1 (3 — Sprint 8 closes); /sdd:discovery (10); Multi-Session Resume (17); Interview Command Behaviors (24); Context Management (10); /sdd:polish re-open (3); /sdd:retro at Project Close (5); Cross-Project Feedback Transfer (13 — Sprint 8 closes); Automatic Backlog Generation (9); Process Notes Growth (10); v1→v2 Migration (13); v2 Release Verification (~9 still open).
- **Unvetted: empty** (line 363 of prd.md, unchanged).
- **Open concerns:** 1 Open item (`Soft acceptance criteria for MIT distribution`), targets `/sdd:refine`. Not in this sprint's scope. No action.
- **Historical-AC backfill gap:** Sprints 1–4 closed work pre-Bug-Fix-2; many ACs remain unchecked. Sprint 8 Item 1 closes the Bug Fix 1 subset (`aaaa`–`aaac`); the broader gap remains maintainer PRD-hygiene.

### Open concerns processed

Read `docs/open-concerns.md`. Single Open item targets `/sdd:refine`. Not in this sprint's scope. No update made.

### FB-tag mapping

First sprint with `[FB: ...]` tags. Mapping from the v2-verification.md seed:

| Item | PRD ACs | FB tag | v2-verification row Source |
|---|---|---|---|
| Item 1 (Bug Fix 1) | `aaaa`, `aaab`, `aaac` | `FB-001` | Event Planning App |
| Item 2 (dual-write) | `aaff`, `aafg`, `aafh`, `aafi` | `FB-030` | sdd-plugin self-use |
| Item 3 (inside-clone detection) | `aafj`, `aafk`, `aafl`, `aafm` | `FB-031` | sdd-plugin self-use |
| Item 4 (failure handling) | `aafn`, `aafo`, `aafp`, `aafq`, `aafr` | `FB-032` | sdd-plugin self-use |

Sprint 7's failure to tag FB-017 through FB-023 (which correspond directly to Sprint 7's `/sdd:onboard` Polish items) is acknowledged as a Sprint 7 planning miss. It could be retroactively corrected via `/sdd:polish` re-open or PRD-hygiene maintenance, but is not Sprint 8 work.

### Sprint item decomposition

Four items, mapped to the natural layering of the spec's `/sdd:feedback` bullets:

- **Item 1: Bug Fix 1 verification + PRD checkoff** (3 ACs, FB-001). Verification-only pass — re-read `feedback/SKILL.md` and confirm the no-`lastCommand`-write behavior is intact at lines 27–29 and 117. No SKILL.md edits expected. Bug Fix 2's auto-checkoff handles the PRD update.

- **Item 2: Cross-project dual-write + unset-path no-warn** (4 ACs, FB-030). Adds the forwarding write to Step 3 (Append Entry) of `feedback/SKILL.md`. Reads `feedbackLocalPath` from `~/.claude/sdd-user-profile.json`. Writes to both locations with identical formatting when the field is set; silently writes only locally when unset.

- **Item 3: Inside-clone detection + 6-step path normalization** (4 ACs, FB-031). Gates the forwarding write on the cwd-vs-`feedbackLocalPath` comparison. Documents all six normalization sub-steps (absolute → realpath → forward-slash → strip-trailing-slash → case-fold-by-OS → equality-or-descendant test) plus the 7th symlink-loop-fallback step.

- **Item 4: Failure handling — single warn per session, never lose local note** (5 ACs, FB-032). Wraps the forwarding write in unconditional-local + warn-once-per-session + no-retry + no-exit-with-error semantics. In-conversation memory enforces per-session warning suppression; no persistent state file.

### Out of scope (explicit)

- Cross-cutting interview command behaviors. `/sdd:feedback` doesn't run an interview.
- `/sdd:polish` re-open mechanism (`aaev`–`aaex`). Different file.
- `/sdd:discovery` command implementation (`aaah`–`aaaq`). Blocked by Interview Command Behaviors.
- Multi-Session Resume (`aaar`–`aabg`). Larger separate sprint.
- Interview Command Behaviors + Context Management (`aabh`–`aacv`). Largest cross-cutting sprint.
- `feedbackLocalPath` collection during `/sdd:onboard` — Sprint 7's work, already shipped.
- Cross-project-patterns file write — `/sdd:retro` owns that file.
- A persistent warning-state file (`~/.claude/sdd-feedback-warned-paths.json` or equivalent). Spec says per-session, not per-machine. In-conversation memory is the enforcement.
- Manual PRD-hygiene backfill of historical ACs from Sprints 1–4 beyond `aaaa`–`aaac`.

### Execution order decision

**Sequential, no parallel option.** All four items edit `plugins/sdd/skills/feedback/SKILL.md`. Items 2, 3, 4 stack on the same Step 3 (Append Entry): Item 2 adds the dual-write infrastructure; Item 3 gates the forwarding write on inside-clone detection; Item 4 wraps the forwarding write in failure handling. Each later item builds on the prior item's edits.

Item 1 is verification-only and could technically run in parallel with Items 2–4 (no edits to the SKILL.md), but autonomous mode dispatches subagents sequentially in this plugin, so the ordering is moot. Item 1 is positioned first to close the historical-AC gap before the substantive new work begins.

Same execution profile as Sprints 5/6/7 (single-file, sequential, autonomous-sequential dispatch).

### Build mode

**Autonomous**, matching priors. Items are spec-mapped: every AC has a clearly-defined target in `feedback/SKILL.md` and a clearly-defined source in `docs/spec.md > /sdd:feedback`. No deepening required mid-build. Mode locked for Sprint 8.

The cached v1 `/sdd:sprint` Step 5 asks the user to pick build mode interactively. Per the no-clarifying-questions directive, this session defaulted to autonomous (matching Sprints 5–7) without re-asking. User can redirect by editing `buildMode` in `docs/project-state.json` before `/sdd:build` runs.

### Deepening rounds

None requested. Same call as Sprints 5–7: pre-vetted ACs, fully specified `/sdd:feedback` in spec, structurally well-understood single-file work, mechanical character. The 4-way item decomposition was the only design call, and it follows the natural layering of the spec's bullets (Bug Fix 1 → dual-write → inside-clone → failure handling).

### Tagging convention

- Items 1, 2, 3, 4 all carry real `[PRD: ...]` tags. No `[unmapped]` items.
- All four items carry `[FB: ...]` tags — **first sprint with FB tags**. FB-001, FB-030, FB-031, FB-032 each map to exactly one Sprint 8 item.
- The Sprint 5 close-sprint wrap-up logic (PRD checkoff + v2-verification row updates) will fire for the first time with both kinds of tag present. This is a notable verification event for the v2 build itself.

### New concerns surfaced during planning

- **Bug Fix 1 already shipped, ACs unchecked.** Item 1's verification-only character is the right call — re-writing already-working logic would be wasteful — but it raises a broader PRD-hygiene question: how many other ACs across the PRD are silently satisfied by Sprint 1–4 work that predates Bug Fix 2? A systematic re-audit would surface them, but it's out of scope for Sprint 8. Flag for a future maintainer PRD-hygiene pass.
- **Per-session warning enforcement relies on agent memory.** Item 4's per-session warn-suppression has no persistent backing — it's enforced by the in-conversation-context-window memory of the agent. If a conversation is `/clear`-ed or compacted, the warning state resets. The spec says "per session" without further qualification; the most natural read is per-conversation. The SKILL.md should be explicit so a future maintainer doesn't misread the spec and add a persistent state file.
- **Sprint 7 FB-tag gap unresolved.** Sprint 7 items (`/sdd:onboard` Polish) map directly to FB-017 through FB-023 but were planned and shipped without `[FB:]` tags. Sprint 8's process notes call this out; the gap is a candidate for `/sdd:polish` re-open or PRD-hygiene maintenance, not Sprint 8 work.
- **Plugin reinstall opportunity persists.** Sprint 8 planning ran on cached v1 `/sdd:sprint`. The v2 source for `/sdd:plan` exists from Sprint 6 but isn't live until reinstall. The first `/sdd:plan` v2 dogfood remains the most notable verification event in the near-term queue.

None of these rise to "Open concern" status — each has a documented resolution in the sprint items themselves or is a forward-looking observation. Not adding to `docs/open-concerns.md`.

### Decisions log

| Decision | Choice | Rationale |
|---|---|---|
| Sprint 8 theme | `/sdd:feedback` v2 — Bug Fix 1 + Cross-Project Feedback Transfer | Continues Sprint 7 (consumes `feedbackLocalPath`); single file; mechanical; first FB-tag sprint |
| Item 1 character | Verification + PRD checkoff (no SKILL.md edits) | Bug Fix 1 already shipped pre-Bug-Fix-2; verifying and checking off closes the historical-AC gap honestly |
| Item decomposition (Items 2–4) | Dual-write → inside-clone detection → failure handling | Natural layering of the spec's `/sdd:feedback` bullets; each later item depends on the prior item's structure |
| Bundle Bug Fix 1 with CPFT? | Yes | Same file; Bug Fix 1 is logically a CPFT precondition (back-to-back forwards would misattribute the same way back-to-back local writes already do) |
| Include `/sdd:polish` re-open? | Defer | Different file, different command; bundling diffuses focus |
| Include Interview Command Behaviors? | Defer | Largest cross-cutting payoff (24 ACs) — deserves its own focused sprint to manage scope risk |
| Include Multi-Session Resume? | Defer one sprint | Natural successor after CPFT lands the feedback loop |
| Execution mode | Sequential | All four items edit the same file (or, for Item 1, no edits); autonomous dispatches sequentially |
| Build mode | Autonomous | Matches Sprints 5–7; items are spec-mapped and mechanical; no-clarifying-questions directive defaulted without re-asking |
| Deepening rounds | None | Pre-vetted ACs, fully specified `/sdd:feedback` in spec, structurally well-understood; no exploratory thread |
| `[FB: ...]` tagging | Yes — all four items | First sprint where ACs map to entries on the v2-verification.md FB-list; tagging exercises Sprint 5's row-update logic for the first time |
| Per-session warning enforcement | In-conversation agent memory, not persistent state file | Spec says per-session; persistent state would be out of scope and conflict with the plugin's no-runtime-code posture |
| User input | None — no `AskUserQuestion` checkpoint | Per the no-clarifying-questions directive; proposal committed directly to sprint-8.md; user can redirect by editing the file before `/sdd:build` |

---

## Build Session — 2026-05-13

### Context

`/sdd:build` invoked under the no-clarifying-questions directive. Autonomous mode (locked at planning). Build proceeded through all four items without checkpoint pauses. The autonomous-mode "every 3-4 items" checkpoint resolves to the end of the sprint with only 4 items, so the final summary replaces a mid-sprint review.

`docs/open-concerns.md` does not exist in this project — skipped the open-concerns step (every command's startup protocol; the file simply isn't present here).

### Item 1 — Bug Fix 1 verification + PRD checkoff

Verification-only pass, executed by the orchestrator (no subagent dispatched). Walked the current `plugins/sdd/skills/feedback/SKILL.md`:

- **`aaaa`** satisfied by line 30: *"This command writes nothing to `docs/project-state.json` on startup. In particular, it does not update `lastCommand`."*
- **`aaab`** satisfied by lines 74 + 90 + 98: Step 2 reads `lastCommand`; Step 3 template binds `**After command:** /sdd:[command]` to that read value with no transformation or fallback.
- **`aaac`** satisfied by line 161 (then line 138 after later items shifted the file): the "consecutive runs all attribute to the same preceding command" guarantee is documented in the Important Reminders block.

No SKILL.md edits. PRD ACs checked off and Item 1 status flipped to `[x]`. As predicted in the sprint plan, this was a verify-and-checkoff close of the historical-AC gap.

### Item 2 — Cross-project dual-write + unset-path no-warn

Dispatched to a general-purpose subagent with the full item spec, the loaded PRD story, the spec subsection, and an explicit instruction set (do NOT commit; forward-reference Items 3 & 4; preserve existing Step 3 behavior). Subagent applied targeted edits and reported back in the required structure.

Result:
- Added a new bullet to the Loading section reading `~/.claude/sdd-user-profile.json` for `feedbackLocalPath` (with the "treat as unset" rules for missing file / absent / `null` / empty).
- Extended Step 2 (Capture Context) with the forwarding-target read.
- Restructured Step 3 into one shared template + Write 1 (local, always fires) + Write 2 (forwarding, fires when `feedbackLocalPath` is set).
- Added a "Forward references" block flagging Items 3 and 4 as the layers still to come.

Verified against all four ACs (`aaff`, `aafg`, `aafh`, `aafi`). No deviations from the plan.

### Item 3 — Inside-clone detection + 6-step path normalization

Dispatched to a second subagent with the explicit 7-step normalization from the spec, the gating rule (inside cwd → skip Write 2; outside cwd → both writes), and a reminder to write literal agent-actionable phrasing (not `path.realpath()` pseudocode).

Result:
- Added Step 3a (Inside-clone detection) between Write 1 and Write 2 with all seven normalization sub-steps in spec order: absolute → realpath → forward-slash → strip-trailing-slash → case-fold-by-OS (Windows + macOS lowercase; Linux preserve) → equality-or-descendant test → literal-string fallback on symlink-loop / resolve error.
- The descendant-prefix test correctly uses `feedbackLocalPath + "/"` to prevent false matches like `/home/me/sdd-plugin-fork` matching `/home/me/sdd-plugin`.
- Re-gated Write 2 to fire only when `feedbackLocalPath` is set AND cwd is outside the clone. Both skip branches (unset config; inside-clone) documented as silent.
- Replaced Item 2's stale forward reference with present-tense prose; preserved Item 4's forward reference.

Verified against all four ACs (`aafj`, `aafk`, `aafl`, `aafm`). The `aafm` deferral closed by the SKILL.md implementing the spec's resolution. No deviations.

### Item 4 — Failure handling, single-warn-per-session

Dispatched to a third subagent with the failure-mode list (path nonexistent, parent missing, target type mismatch, permission denied, equivalent), the literal warning phrase requirement (`Run /sdd:onboard to update.` verbatim including the trailing period), and the in-conversation-memory enforcement model (no persistent state file).

Result:
- Added Step 3b (Write ordering and failure handling for Write 2) documenting: Write 1 unconditional + first; Write 2 best-effort single-attempt second; no retry; no fallback path; no exit-with-error on Write 2 failure; one warning per session per forwarding target with reason + literal onboard-instruction phrase; in-conversation memory as the suppression mechanism with explicit "no persistent state file" disclaimer.
- Failure scenarios enumerated illustratively (path missing, parent dir missing, target type mismatch, permission denied, "or any equivalent write error") matching the spec's permissive language.
- Replaced Item 3's forward reference with the present-tense failure-handling description.
- Added a reinforcing bullet to the Important Reminders block summarizing the rules for future-agent readers.

Verified against all five ACs (`aafn`, `aafo`, `aafp`, `aafq`, `aafr`). No deviations.

### Observations during build

- **Subagent dispatch worked cleanly.** Each subagent received a self-contained prompt with the spec, PRD, what-to-build, scoping rules, and verification checklist. All three reported back in the required structure (files changed, what was added, AC-by-AC verification, deviations) under 250 words. No mid-item escalations or stop-and-ask events.
- **No iteration candidates surfaced.** The work was mechanical layering on top of an already-coherent Step 3. The 6+1 path-normalization steps and the per-session warning model were spec-resolved, leaving no design ambiguity at build time.
- **No tech-debt entries.** Same mechanical character — no compromises taken, no shortcuts that warrant later cleanup.
- **No story splits.** Every AC closed cleanly; no Sprint 8 item satisfied only part of its referenced ACs.
- **First successful `[FB: ...]` row-update.** Sprint 5's `/sdd:build` close-sprint logic populates `docs/v2-verification.md` rows when sprint items carrying `[FB:]` tags close. Sprint 8 is the first sprint with FB-tagged items, so this is the first real exercise of that logic. Rows FB-001, FB-030, FB-031, FB-032 all updated with `sprint 8, item N`. Verified column stays manual `[ ]`.
- **No deviation from the spec.** The implemented behavior in `feedback/SKILL.md` matches the spec's `/sdd:feedback` section bullet-for-bullet (the v2 changes block, the 7-step path normalization, the failure-handling rules).

[close-sprint-manifest] timestamp: 2026-05-13T17:30:00Z
- PRD ACs checked: aaaa, aaab, aaac, aaff, aafg, aafh, aafi, aafj, aafk, aafl, aafm, aafn, aafo, aafp, aafq, aafr
- Story splits: none
- Iteration candidate dispositions: none (no candidates surfaced)
- Tech-debt entries: 0
- v2-verification rows updated: FB-001 (sprint 8, item 1), FB-030 (sprint 8, item 2), FB-031 (sprint 8, item 3), FB-032 (sprint 8, item 4)
[/close-sprint-manifest]

### Sprint 8 close

16 of 16 PRD ACs closed. 4 of 4 items closed. 4 of 4 FB-list rows shipped (verification still manual-only on each). `plugins/sdd/skills/feedback/SKILL.md` v2 is feature-complete: dual-write to current project + forwarded clone; inside-clone detection prevents double-write; per-session warning on forwarding failure preserves the local note. The new mechanics ship to disk but are not live as a slash command until the maintainer reinstalls the plugin cache — same caveat as Sprints 5/6/7.

Recommended next sprint (for the next `/sdd:plan` or cached `/sdd:sprint`): Interview Command Behaviors + Context Management (`aabh`–`aacv`, ~34 ACs, biggest cross-cutting payoff, unblocks `/sdd:discovery` per `aaaj`). Alternative smaller choices: `/sdd:polish` re-open mechanism (3 ACs); Multi-Session Resume (17 ACs, two new commands); Automatic Backlog Generation (9 ACs cross-cutting).
