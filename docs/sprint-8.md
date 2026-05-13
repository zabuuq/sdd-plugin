# Sprint 8

**Theme:** `/sdd:feedback` v2 — Bug Fix 1 (verification + PRD checkoff) + Cross-Project Feedback Transfer (new mechanics). Bug Fix 1's no-`lastCommand`-write behavior already shipped in `plugins/sdd/skills/feedback/SKILL.md` (lines 27–29 + line 117) but its PRD acceptance criteria (`aaaa`–`aaac`) sit unchecked because the work pre-dated Bug Fix 2's automatic checkoff mechanism. Sprint 8 closes that PRD-hygiene gap and stacks the substantive new CPFT work on top: dual-write to current project + `feedbackLocalPath`-forwarded copy, inside-clone detection with 6-step path normalization, and failure handling that warns once per session without losing the local note.

**Tangible outcome:** After this sprint, `plugins/sdd/skills/feedback/SKILL.md` v2 is feature-complete. Running `/sdd:feedback` in a project that has `feedbackLocalPath` configured writes the note to both the current project's `docs/sdd-feedback.md` AND the configured local plugin clone's `docs/sdd-feedback.md` (with single-write-only-the-local-copy when the user is working inside the clone). Misconfigured paths surface a single per-session warning without dropping the local note. Consecutive `/sdd:feedback` runs (Bug Fix 1) attribute each note to the actual preceding non-feedback command. The PRD checkoff covers 16 ACs (`aaaa`–`aaac` from Bug Fix 1; `aaff`–`aafr` from Cross-Project Feedback Transfer). `docs/v2-verification.md` gets its first row updates: FB-001 (Bug Fix 1), FB-030 (dual-write), FB-031 (inside-clone detection), FB-032 (failure handling) all gain `Shipped (sprint/item)` values.

**Out of scope:**
- Cross-cutting interview command behaviors (clarifying-questions-don't-advance, 5/10 caps, end-of-round recommendations, active prompting, 3-tier context management). `/sdd:feedback` doesn't run an interview; these don't apply.
- `/sdd:polish` re-open mechanism (`aaev`–`aaex`). Different file (`polish/SKILL.md`), different mechanics. Separate sprint.
- `/sdd:discovery` command implementation (`aaah`–`aaaq`). Blocked by Interview Command Behaviors per AC `aaaj`.
- Multi-Session Resume (`aaar`–`aabg`). Larger standalone sprint.
- Interview Command Behaviors + Context Management (`aabh`–`aacv`). Largest cross-cutting sprint, deserves its own focused pass.
- `feedbackLocalPath` collection during `/sdd:onboard` — Sprint 7's work, already shipped (Item 2). Sprint 8 only consumes the field.
- Cross-project-patterns file write — `/sdd:retro`'s responsibility, not `/sdd:feedback`. Different sprint.
- `~/.claude/sdd-feedback-warned-paths.json` or equivalent persistent warning-state file — the spec calls for **per-session** warning behavior (single warning per session, not per machine). Implementation MAY use an in-memory flag in the agent's session state OR a documented convention; no separate persistent state file is required.
- Manual PRD-hygiene backfill of historical ACs from Sprints 1–4 beyond `aaaa`–`aaac`. Item 1's checkoff closes the Bug Fix 1 sub-gap; the rest of the gap remains maintainer hygiene.

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** All four items edit the same file (`plugins/sdd/skills/feedback/SKILL.md`). Item 1 is verification-only (current file already satisfies the ACs; PRD checkoff is the deliverable). Items 2–4 stack the new CPFT mechanics: dual-write infrastructure (Item 2) → inside-clone detection that gates the forwarding write (Item 3) → failure handling that wraps the forwarding write in a try/catch-equivalent with single-warn-per-session semantics (Item 4). Each later item depends on the prior item's structure. Autonomous mode dispatches subagents one at a time.

## Checklist

### Item 1: Bug Fix 1 verification + PRD checkoff `[PRD: aaaa, aaab, aaac]` `[FB: FB-001]`

- **PRD ref:** `prd.md > Bug Fixes` (consecutive `/sdd:feedback` notes retain real preceding-command context story) `[PRD: aaaa, aaab, aaac]`
- **Spec ref:** `spec.md > Command Skills > /sdd:feedback` (v2 changes — startup sequence contains no step that writes to `lastCommand`)
- **What to build:** Verification pass — no new code. The current `plugins/sdd/skills/feedback/SKILL.md` already implements Bug Fix 1:
  - Line 27–29: "This command writes nothing to `docs/project-state.json` on startup. In particular, it does not update `lastCommand`."
  - Line 73: "Last command run: Read from `lastCommand` in `docs/project-state.json`. `/sdd:feedback` does not overwrite this field, so the current value is the preceding non-feedback command — exactly what the note should attribute."
  - Line 117: "Never write `lastCommand`. … The 'After command' field in each note is sourced directly from the current value of `lastCommand`, which is the preceding non-feedback command. Consecutive `/sdd:feedback` runs therefore all attribute to the same preceding command."

  Re-read the file, confirm each of `aaaa`/`aaab`/`aaac` is satisfied by the existing text, and check off the PRD ACs. If verification surfaces any gap (e.g., a startup step that does write `lastCommand` that the reviewer missed), fix it in the same item — but the expectation is no edits beyond the PRD checkoff that Bug Fix 2's auto-checkoff will perform at close-sprint.
- **Acceptance criteria:**
  - [x] `aaaa` `/sdd:feedback`'s startup sequence contains no step that writes to `lastCommand` in `docs/project-state.json`.
  - [x] `aaab` The "After command" field in each feedback note is sourced from the value of `lastCommand` at the moment `/sdd:feedback` is invoked.
  - [x] `aaac` When `/sdd:feedback` runs N times consecutively with no other SDD command invoked between runs, every resulting note's "After command" field equals the value of `lastCommand` that was present before the first `/sdd:feedback` invocation.
- **Verification:**
  - Re-read `plugins/sdd/skills/feedback/SKILL.md`. Walk every step that touches `docs/project-state.json` (currently: Step 2 reads `currentSprint` and `lastCommand`; the State Updates section explicitly forbids the write). Confirm no write to `lastCommand` exists.
  - Confirm Step 3's note template binds "After command" to the `lastCommand` value read in Step 2 (no transformation, no fallback that would mask a back-to-back invocation).
  - Confirm the Important Reminders block reinforces the no-write rule for any future agent editing the file.
  - If all three checks pass, no SKILL.md edits are needed — the close-sprint PRD-checkoff alone closes these three ACs.
- **Status:** [x] Complete

---

### Item 2: Cross-project dual-write + unset-path no-warn `[PRD: aaff, aafg, aafh, aafi]` `[FB: FB-030]`

- **PRD ref:** `prd.md > Cross-Project Feedback Transfer` (dual-write story — write each note both locally and to plugin clone) `[PRD: aaff, aafg, aafh, aafi]`
- **Spec ref:** `spec.md > Command Skills > /sdd:feedback` (v2 changes — cross-project forwarding bullet; the dual-write semantics without yet wiring inside-clone detection — Item 3 covers that)
- **What to build:** Extend `plugins/sdd/skills/feedback/SKILL.md`'s Step 3 (Append Entry) to perform **two** writes when the configured forwarding target is set:
  1. **Local write** (existing, unchanged): append the entry to the current project's `docs/sdd-feedback.md` with the exact format already documented. This write always happens.
  2. **Forwarding write** (new): if `feedbackLocalPath` is set in `~/.claude/sdd-user-profile.json` AND a non-null/non-empty value, also append the same entry to `<feedbackLocalPath>/docs/sdd-feedback.md`.

  Both writes use **identical formatting** — the same template, same fields, same values. The forwarding write is a byte-for-byte mirror of the local write to its forwarding destination.

  When `feedbackLocalPath` is unset, `null`, or empty, only the local write happens. The command emits **no warning, no prompt, no mention** of the absent forwarding configuration — staying silent is intentional (mirrors the spec's "no warning or prompt" rule).

  Add a new beat or update the existing Step 3 (Append Entry to sdd-feedback.md) to document both writes. Add a profile-read step (or extend Step 2 Capture Context) to read `feedbackLocalPath` from `~/.claude/sdd-user-profile.json`.

  **Do not yet implement inside-clone detection** — that's Item 3. For Item 2, when `feedbackLocalPath` is set, the forwarding write fires unconditionally. Item 3 will gate the forwarding write on the inside-clone check.

  **Do not yet implement failure handling** — that's Item 4. For Item 2, the forwarding write is assumed to succeed (or the underlying file-write tool's error surfaces); Item 4 wraps it in single-warn-per-session error semantics.
- **Acceptance criteria:**
  - [x] `aaff` `/sdd:feedback` writes each feedback note to the current project's `docs/sdd-feedback.md` file.
  - [x] `aafg` When `feedbackLocalPath` is set in `~/.claude/sdd-user-profile.json`, `/sdd:feedback` also writes the note to `<feedbackLocalPath>/docs/sdd-feedback.md`.
  - [x] `aafh` Both writes use identical note formatting.
  - [x] `aafi` When `feedbackLocalPath` is unset or `null`, `/sdd:feedback` writes only to the current project's `docs/sdd-feedback.md` and emits no warning or prompt.
- **Verification:**
  - Re-read the updated SKILL.md and confirm a step reads `~/.claude/sdd-user-profile.json` and extracts `feedbackLocalPath`.
  - Confirm the local write always fires (its current Step 3 behavior is preserved unchanged).
  - Confirm the forwarding write is described as appending the same entry to `<feedbackLocalPath>/docs/sdd-feedback.md` when the field is set.
  - Confirm the same template is used for both writes — no separate formatting code path. (Phrasing like "use the same template / identical formatting" must appear in the SKILL.md.)
  - Confirm the unset/null branch is documented as silent — no warning, no prompt, no mention. (Phrasing like "no warning, no prompt" must appear in the SKILL.md.)
  - Confirm the SKILL.md notes that inside-clone detection (Item 3) and failure handling (Item 4) layer on top of this dual-write infrastructure — forward references are acceptable since later items in the same sprint will fill them in.
- **Status:** [x] Complete

---

### Item 3: Inside-clone detection + 6-step path normalization `[PRD: aafj, aafk, aafl, aafm]` `[FB: FB-031]`

- **PRD ref:** `prd.md > Cross-Project Feedback Transfer` (inside-clone double-write avoidance story) `[PRD: aafj, aafk, aafl, aafm]`
- **Spec ref:** `spec.md > Command Skills > /sdd:feedback` (v2 changes — path normalization for inside-clone detection bullet, all 7 numbered steps including the symlink-loop fallback)
- **What to build:** Gate Item 2's forwarding write on an **inside-clone detection** that prevents double-writing when the user is working inside the plugin clone itself. Add a step (between the profile read and the forwarding write) that:
  1. Resolves both the current working directory and `feedbackLocalPath` to absolute paths.
  2. Resolves symlinks to real paths.
  3. Normalizes path separators to forward slashes.
  4. Strips trailing slashes.
  5. Case-folds on Windows and macOS (default APFS); preserves case on Linux.
  6. Tests whether cwd equals `feedbackLocalPath` OR cwd starts with `feedbackLocalPath + "/"` (the descendant check). If yes, cwd is **inside** the clone.

  Plus the spec's 7th step:
  7. On symlink-loop or resolve error, fall back to literal string comparison of the un-normalized paths.

  When the check returns "inside the clone": the forwarding write is **skipped**. The local write (which IS the local plugin clone's `docs/sdd-feedback.md` in this case) is the single canonical write — no double-write occurs.

  When the check returns "outside the clone": the forwarding write proceeds as Item 2 specified.

  Document all 6+1 normalization steps explicitly in the SKILL.md so the runtime agent has clear instructions. The agent is the executor — these aren't code paths in a runtime, they're an instruction set for the agent to follow on each `/sdd:feedback` invocation. Use literal phrasing the agent can act on (e.g., "resolve cwd to its absolute, symlink-followed real path" not "call `path.realpath()`").

  No state is persisted between `/sdd:feedback` invocations for path normalization purposes — the resolution and comparison happen fresh on each call.
- **Acceptance criteria:**
  - [x] `aafj` `/sdd:feedback` compares the current working directory to the configured `feedbackLocalPath` value.
  - [x] `aafk` When the current working directory equals `feedbackLocalPath` or is a descendant of it, `/sdd:feedback` performs only the local write — no additional write to `<feedbackLocalPath>/docs/sdd-feedback.md`.
  - [x] `aafl` When the current working directory is neither equal to nor a descendant of `feedbackLocalPath`, `/sdd:feedback` performs both the local write and the forwarding write.
  - [x] `aafm` Path normalization details (case sensitivity by OS, trailing slashes, symlinks) are deferred to `/sdd:spec`.
- **Verification:**
  - Re-read the updated SKILL.md and confirm a comparison step exists that takes cwd and `feedbackLocalPath` as inputs.
  - Confirm all six normalization sub-steps are documented in the SKILL.md in the spec's order (absolute → realpath → forward-slash → strip-trailing-slash → case-fold-by-OS → equality-or-descendant test).
  - Confirm the OS-dependent case-folding rule is described: Windows + macOS case-fold; Linux preserve case.
  - Confirm the symlink-loop / resolve-error fallback (literal string comparison) is documented.
  - Confirm the "inside cwd → skip forwarding write" branch and the "outside cwd → do both writes" branch are both explicitly described.
  - Confirm `aafm` (the AC deferring normalization details to `/sdd:spec`) is satisfied by the SKILL.md actually implementing the spec's normalization rules — the spec resolved this deferral with the 7-step normalization in `spec.md > Command Skills > /sdd:feedback`, so the implementation referencing the spec's resolution closes the AC.
- **Status:** [x] Complete

---

### Item 4: Failure handling — single warn per session, never lose the local note `[PRD: aafn, aafo, aafp, aafq, aafr]` `[FB: FB-032]`

- **PRD ref:** `prd.md > Cross-Project Feedback Transfer` (misconfigured-path graceful-degrade story) `[PRD: aafn, aafo, aafp, aafq, aafr]`
- **Spec ref:** `spec.md > Command Skills > /sdd:feedback` (v2 changes — failure handling bullet: single warning per session, no retry, no exit-with-error, local write always completes)
- **What to build:** Wrap Item 3's forwarding write in failure-handling semantics that ensure the user **never loses a note** and **never sees the same warning twice** within a session. Add the following to `plugins/sdd/skills/feedback/SKILL.md`:
  1. **Local write is unconditional.** Even when the forwarding write fails (whatever the failure mode), the local write to the current project's `docs/sdd-feedback.md` must always complete. Document this as the ordering rule: local write fires first; if it fails, that's a hard error and bubbles up. The forwarding write fires second; its failure does not affect the local write or the command's exit status.
  2. **Single warning per session on forwarding failure.** When the forwarding write fails (path nonexistent, parent directory missing, write permission denied, target is a file not a directory, or any other write error), the agent emits **one warning** describing the failure reason and the instruction `Run /sdd:onboard to update.` (literal phrasing per spec).
  3. **No retry within the same invocation.** A single forwarding-write attempt — no exponential backoff, no immediate retry, no fallback path.
  4. **No exit-with-error.** The command's exit status is success after the local write completes, regardless of the forwarding write's result. The user's note is preserved locally; the warning informs them about the forwarding gap.
  5. **No re-warn within the same session.** Subsequent `/sdd:feedback` invocations within the same conversation/session that hit the same forwarding-write failure emit **no additional warning**. The user is told once.

  Document the per-session warning behavior. Since this plugin has no runtime state store between `/sdd:feedback` invocations across sessions, "per-session" is enforced by the agent's in-conversation memory: within a single conversation/session, the agent remembers it has warned and suppresses subsequent warnings. Across sessions (different conversations), the warning re-fires once.

  The SKILL.md should make the "per-session" semantics explicit — within a single Claude Code conversation, the agent tracks whether it has already warned and skips re-warning. No separate persistent state file. No `~/.claude/sdd-feedback-warned-paths.json` or equivalent — out of scope per the sprint header.
- **Acceptance criteria:**
  - [x] `aafn` When `feedbackLocalPath` is set but invalid (path does not exist, write fails, or equivalent), `/sdd:feedback` completes the local write to the current project's `docs/sdd-feedback.md`.
  - [x] `aafo` On any forwarding-write failure, `/sdd:feedback` emits a single warning containing the failure reason and the instruction "Run `/sdd:onboard` to update."
  - [x] `aafp` `/sdd:feedback` does not retry the forwarding write within the same invocation.
  - [x] `aafq` `/sdd:feedback` does not exit with an error status when the forwarding write fails — the command completes after the local write.
  - [x] `aafr` When subsequent `/sdd:feedback` invocations within the same session encounter the same forwarding-write failure, no additional warning is emitted.
- **Verification:**
  - Re-read the updated SKILL.md and confirm the ordering rule is documented: local write first, forwarding write second, local write's success is unconditional.
  - Confirm the warning message wording includes the literal `Run /sdd:onboard to update.` phrasing (the spec specifies this exact text).
  - Confirm the "no retry within same invocation" rule is documented.
  - Confirm the "no exit-with-error on forwarding failure" rule is documented.
  - Confirm the per-session re-warn suppression is documented and the in-conversation-memory enforcement is explained (no persistent state file).
  - Confirm the SKILL.md describes what a "forwarding failure" covers — at minimum: path nonexistent, write permission denied, target is a file not a directory. The spec language ("forwarding-write failures … or equivalent") is permissive; the implementation should mirror that without enumerating every possible OS error.
- **Status:** [x] Complete

---

## Notes

- All four items edit the same file: `plugins/sdd/skills/feedback/SKILL.md`. Sequential execution is mandatory.
- Sprint 8 closes 16 PRD ACs: 3 (Item 1: `aaaa`–`aaac`) + 4 (Item 2: `aaff`–`aafi`) + 4 (Item 3: `aafj`–`aafm`) + 5 (Item 4: `aafn`–`aafr`).
- Sprint 8 is the **first sprint with `[FB: ...]` tags**. Sprint 5's `/sdd:build` wrap-up will fire row updates in `docs/v2-verification.md` for FB-001 (Item 1), FB-030 (Item 2), FB-031 (Item 3), FB-032 (Item 4). Each row's `Shipped (sprint/item)` column gets populated with `sprint 8, item N`. The `Verified` column stays manual-only.
- **Item 1 is verification-only.** No SKILL.md edits expected; the close-sprint Bug Fix 2 auto-checkoff handles the PRD update. This is honest dogfooding — the v2 plugin's verification artifact records that the work shipped (in an earlier sprint) AND that its PRD ACs are now properly tagged-and-checked.
- **Items 2, 3, 4 stack on the same Step 3 (Append Entry) of `feedback/SKILL.md`.** Item 2 introduces the forwarding-write infrastructure; Item 3 gates it on inside-clone detection; Item 4 wraps it in failure handling. Each subagent should build on the prior item's edits — not rewrite Step 3 wholesale each time.
- **The cached marketplace plugin is still v1**, same caveat as Sprints 5/6/7. The new `feedback/SKILL.md` v2 mechanics ship to disk but aren't live as a slash command until the maintainer reinstalls. The first real dogfood of CPFT fires on reinstall + a `/sdd:feedback` invocation with `feedbackLocalPath` set in the user profile (which Sprint 7 just made possible to write).
- **First `/sdd:plan` v2 dogfood is still pending.** Sprint 8 planning used cached v1 `/sdd:sprint` for the same reason. Whenever the maintainer reinstalls, the next `/sdd:plan` invocation is the first dogfood of Sprint 6's `/sdd:plan` v2 — a notable event worth observing for Sprint 9 planning.
- **After Sprint 8: natural next-sprint candidates remain** (a) `/sdd:polish` re-open mechanism (`aaev`–`aaex`, 3 ACs, small); (b) Interview Command Behaviors cross-cutting (`aabh`–`aacl`, 24 ACs, biggest payoff — unblocks `/sdd:discovery` per AC `aaaj`); (c) Context Management three-tier (`aacm`–`aacv`, 10 ACs, natural pair with Interview Command Behaviors); (d) Multi-Session Resume (`aaar`–`aabg`, 17 ACs, two new commands); (e) Automatic Backlog Generation (`aafs`–`aagd`, 9 ACs cross-cutting); (f) Process Notes Growth (`aage`–`aagn`, 10 ACs cross-cutting); (g) v1→v2 Migration (`aago`–`aahi`, 13 ACs, single file MIGRATION-v1-to-v2.md); (h) v2 Release Verification (`aahj`–`aahx`, 11 ACs, mix of file presence + documentation). Choose at the next `/sdd:plan` (or cached `/sdd:sprint`).
- **PRD AC ID conventions check:** Sprint 8 uses 16 IDs across the `aa**` namespace. None collide with Sprint 7's `aacw`–`aadn` (which are checked off). The PRD's Unvetted section remains empty — no refinement needed before Sprint 8 build.
- **No deepening rounds requested.** Same rationale as Sprints 5/6/7: mechanical character, fully-specified spec section (`spec.md > /sdd:feedback`), structurally well-understood single-file work. The 4-way item decomposition was the only design call, and it follows the natural layering of the spec bullets.
