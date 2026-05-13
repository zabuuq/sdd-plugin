# Sprint 7

**Theme:** `/sdd:onboard` Polish — full v2 implementation. The current `plugins/sdd/skills/onboard/SKILL.md` is close to v1: Step 5 (communication style) still uses a bulleted-example menu in the question text (violates the v2 open-ended rule), the profile path is never output, the re-run sentence is absent, the `/sdd:feedback` beat is missing, the `feedbackLocalPath` question doesn't exist, the third-party plugin auto-update paragraph is missing, the next-command heads-up names no successor, the update flow doesn't surface retro-written preferences, and the two new v2 profile fields (`handoffWarningShown`, `defaultSprintMode`) are never seeded. This sprint wires all of those in.

**Tangible outcome:** After this sprint, running `/sdd:onboard` produces a fully v2-compliant `~/.claude/sdd-user-profile.json` (every documented field present; new-field defaults seeded), delivers the communication-style question as a single open-ended prompt with no bulleted menu, outputs the profile path and a re-run statement explicitly, covers `/sdd:feedback` in a dedicated beat, asks a skip-friendly `feedbackLocalPath` question, presents a recommendation-free auto-update paragraph, ends with a heads-up naming `/sdd:discovery` as an interview, and on re-run surfaces both originally-onboarded and retro-written preferences with source labels.

**Out of scope:**
- Cross-cutting interview command behaviors that `/sdd:onboard` is supposed to apply once they exist (clarifying-questions-don't-advance, 5/10-question deepening caps, end-of-round recommendations, active prompting, 3-tier context management). These ship in a sdd-guide-focused sprint. `/sdd:onboard` references them in its SKILL.md but does not embed them.
- Bug Fix 1 (`aaaa`–`aaac`, `/sdd:feedback` no-lastCommand-clobber). Different file, unrelated mechanics. Separate sprint.
- `/sdd:polish` re-open mechanism (`aaev`–`aaex`). Different file. Separate sprint.
- `/sdd:discovery` command implementation (`aaah`–`aaaq`). Separate command, separate sprint. `/sdd:onboard` only references `/sdd:discovery` in the end-of-run heads-up.
- Cross-Project Feedback Transfer dual-write logic (`aaff`–`aafr`). Separate sprint. `/sdd:onboard` only writes the `feedbackLocalPath` field; the dual-write consumer (`/sdd:feedback`) is unchanged in this sprint.
- The cross-project-patterns file (`~/.claude/sdd-cross-project-patterns.md`) is not written by this sprint. It's produced by `/sdd:retro` (separate sprint). This sprint only reads from it if present in Item 4.
- Manual PRD-hygiene backfill of historical ACs. Not sprint work.

## Build Preferences
- **Build mode:** autonomous

## Execution order

**Sequential.** All four items edit the same file (`plugins/sdd/skills/onboard/SKILL.md`). Items execute in numbered order. The current SKILL.md has a Steps 1–6 structure; the four items insert/modify steps and add bookend behaviors. Autonomous mode dispatches them sequentially, not in parallel.

## Checklist

### Item 1: Communication-style open prompt + profile path output + re-run statement

- **PRD ref:** `prd.md > /sdd:onboard Polish` (communication style story + profile-path story) `[PRD: aacw, aacx, aacy, aacz]`
- **Spec ref:** `spec.md > Command Skills > /sdd:onboard` (v2 changes — communication-style and profile path bullets)
- **What to build:** Two adjustments to `plugins/sdd/skills/onboard/SKILL.md`:
  1. **Rewrite Step 5 (Communication Style):** Replace the current question (which embeds a bulleted menu of example styles) with a single open-ended prompt — no bulleted examples in the question text. The user's free-form answer writes directly to the `communicationStyle` field of `~/.claude/sdd-user-profile.json`. The `Important Reminders` block already says "Accept any free-form answer for communication style — the examples are suggestions, not constraints"; keep that reminder. The examples can be omitted entirely; if the agent needs to offer examples conversationally during clarification, that's fine, but the question itself is one open prompt.
  2. **Output profile path + re-run sentence:** Add explicit output (in Step 6 Write User Profile, or in a small new "## Closing" / "### Step 7" — author's call) that:
     - Names the literal path `~/.claude/sdd-user-profile.json` at least once during the command's run.
     - States that re-running `/sdd:onboard` updates the same profile file (so the user knows the update flow exists and how to use it).
- **Acceptance criteria:**
  - [ ] `aacw` `/sdd:onboard`'s communication-style question is delivered as a single open-ended prompt with no bulleted examples in the question text.
  - [ ] `aacx` `/sdd:onboard` writes the user's free-form answer directly into the `communicationStyle` field of `~/.claude/sdd-user-profile.json`.
  - [ ] `aacy` `/sdd:onboard` outputs the literal path `~/.claude/sdd-user-profile.json` at least once during its run.
  - [ ] `aacz` `/sdd:onboard` outputs a sentence stating that re-running `/sdd:onboard` updates the same profile file.
- **Verification:**
  - Read the updated SKILL.md and confirm Step 5's question text contains no bulleted-list characters inside the prompt body (a top-line bare prompt only; examples removed from inside the quoted question).
  - Confirm the literal path `~/.claude/sdd-user-profile.json` appears in the SKILL.md as agent-emitted output (not just in code-fenced schema examples).
  - Confirm a "re-running `/sdd:onboard` updates the same profile" sentence appears in the SKILL.md as agent-emitted output.
  - Confirm the `communicationStyle` write target is unchanged (it's already the right field).
- **Status:** [ ] Not started

---

### Item 2: `/sdd:feedback` beat + `feedbackLocalPath` question

- **PRD ref:** `prd.md > /sdd:onboard Polish` (feedback beat story + feedbackLocalPath optional question story) `[PRD: aada, aadb, aadc, aadd, aade, aadf]`
- **Spec ref:** `spec.md > Command Skills > /sdd:onboard` (v2 changes — `/sdd:feedback` beat and `feedbackLocalPath` question bullets)
- **What to build:** Add two new beats to `plugins/sdd/skills/onboard/SKILL.md`, placed after Step 5 (Communication Style) and before Step 6 (Write User Profile). Order: feedback beat first, then `feedbackLocalPath` question, so the question lands while the user is still mentally inside the feedback context.
  1. **Dedicated `/sdd:feedback` beat** (new step, e.g., `### Step 5a` or renumber to `### Step 6`):
     - Covers three things in plain text:
       - What `/sdd:feedback` does (captures a feedback note with context, returns immediately, no chain advancement).
       - When to use it (anytime — independent of the chain — when something the plugin does feels off or could be better).
       - Where notes go: the current project's `docs/sdd-feedback.md`, plus optional forwarding to `<feedbackLocalPath>/docs/sdd-feedback.md` when `feedbackLocalPath` is set.
     - Output text, not a question. No pause for acknowledgment.
  2. **`feedbackLocalPath` question** (new step):
     - Single open-ended question that explains what `feedbackLocalPath` is for: a path to a separate project where `/sdd:feedback` should forward a copy of every feedback note (typical use: a personal plugin-development project that collects feedback from across all projects you use the plugin in).
     - Skip-friendly: explicitly tells the user they can leave the answer blank to skip.
     - Write logic:
       - Non-empty answer → write the value to `feedbackLocalPath` in the profile.
       - Empty answer or skip → omit the `feedbackLocalPath` field from the profile (or write `null` — semantically equivalent). Do NOT re-ask within the same run.
     - One-question-at-a-time per sdd-guide: this is a separate question from communication style, git preference, etc.
- **Acceptance criteria:**
  - [ ] `aada` `/sdd:onboard` includes a dedicated `/sdd:feedback` beat before the command ends.
  - [ ] `aadb` The beat covers what `/sdd:feedback` does, when to use it, and where notes go (the current project's `docs/sdd-feedback.md` plus optional forwarding via `feedbackLocalPath`).
  - [ ] `aadc` `/sdd:onboard` poses a single question for `feedbackLocalPath`.
  - [ ] `aadd` The question text explains what `feedbackLocalPath` is for.
  - [ ] `aade` When the user provides a non-empty answer, `/sdd:onboard` writes that value to the `feedbackLocalPath` field of `~/.claude/sdd-user-profile.json`.
  - [ ] `aadf` When the user provides an empty answer or skips the question, `/sdd:onboard` omits the `feedbackLocalPath` field from the profile or writes `null` to it, and does not re-ask within the same run.
- **Verification:**
  - Read the updated SKILL.md and confirm the `/sdd:feedback` beat appears as plain output text (no question prompt embedded).
  - Confirm the beat covers all three required topics (what / when / where).
  - Confirm `feedbackLocalPath` is asked as a single question with explanatory text, not bundled with another question.
  - Confirm the SKILL.md describes both write branches (non-empty → write value; empty/skip → omit or null), and explicitly forbids re-asking in the same run.
  - Confirm the profile schema example in the SKILL.md (Step 6 / Write User Profile) includes the `feedbackLocalPath` field with appropriate placeholder.
- **Status:** [ ] Not started

---

### Item 3: Plugin auto-update paragraph + next-command heads-up

- **PRD ref:** `prd.md > /sdd:onboard Polish` (auto-update paragraph story + next-command heads-up story) `[PRD: aadg, aadh, aadi, aadj]`
- **Spec ref:** `spec.md > Command Skills > /sdd:onboard` (v2 changes — plugin update mechanics bullet and end-of-command heads-up bullet)
- **What to build:** Add two new pieces to `plugins/sdd/skills/onboard/SKILL.md`. Both run after Item 2's beats but before the final write/confirmation.
  1. **Third-party plugin auto-update paragraph** (new step or beat):
     - Single paragraph covering three points:
       - Third-party plugin update defaults in Claude Code (state what the default is — typically off — using factual language).
       - How to toggle auto-update via `/plugin → Marketplaces`.
       - What `/reload-plugins` does (refreshes loaded plugins from disk; useful after an out-of-band install or local edit).
     - **No recommendation language.** No "you should turn this on," no "we recommend," no "best practice is." Information only. The user makes the call.
     - Output text, not a question.
  2. **End-of-run heads-up naming `/sdd:discovery`:**
     - Replace the current closing line ("tell them to run `/sdd:discovery` to start their first project") with an explicit heads-up that:
       - Names `/sdd:discovery` as the recommended next command.
       - States that `/sdd:discovery` is an interview, not a single-prompt command — so the user knows to expect an interview flow when they run it.
     - This wording is separate from the standard end-of-command handoff (which `/sdd:onboard` emits per sdd-guide's `## End-of-Command Handoff` template). The heads-up is the substantive content of the handoff's outcome-summary line.
- **Acceptance criteria:**
  - [ ] `aadg` `/sdd:onboard` outputs a paragraph covering: third-party plugin update defaults in Claude Code, how to toggle auto-update via `/plugin` → Marketplaces, and what `/reload-plugins` does.
  - [ ] `aadh` The paragraph contains no recommendation language regarding whether to enable auto-update (e.g., no "you should" phrasing about turning auto-update on or off).
  - [ ] `aadi` `/sdd:onboard` ends with a message that names `/sdd:discovery` as the recommended next command.
  - [ ] `aadj` The message states that `/sdd:discovery` is an interview, not a single-prompt command.
- **Verification:**
  - Read the updated SKILL.md and confirm the auto-update paragraph covers all three required topics.
  - Confirm the paragraph contains no "you should," "we recommend," "best practice," or equivalent steering language about whether to enable auto-update.
  - Confirm the closing message names `/sdd:discovery` explicitly.
  - Confirm the closing message describes `/sdd:discovery` as an interview (not a single-prompt command).
- **Status:** [ ] Not started

---

### Item 4: Profile re-surface + new-field seeding

- **PRD ref:** `prd.md > /sdd:onboard Polish` (profile re-surface on re-run story + new-field seeding story) `[PRD: aadk, aadl, aadm, aadn]`
- **Spec ref:** `spec.md > Command Skills > /sdd:onboard` (v2 changes — re-run preference surfacing bullet and new-field seeding bullet; user profile schema)
- **What to build:** Two adjustments to `plugins/sdd/skills/onboard/SKILL.md`. Both attach to existing Step 1 (Check for Existing Profile) and Step 6 (Write User Profile).
  1. **Re-run profile surface with source labels** (Step 1, update flow):
     - Currently the update flow says "Show the current preferences: communication style, git preference, workflow explanation preference." Expand this to read from BOTH:
       - `~/.claude/sdd-user-profile.json` (originally-onboarded preferences — `communicationStyle`, `gitPreference`, `showWorkflowExplanation`, `feedbackLocalPath`, `handoffWarningShown`, `defaultSprintMode`).
       - `~/.claude/sdd-cross-project-patterns.md` (cross-project preferences written by `/sdd:retro` — read the file as plain markdown; surface its high-level content as labeled cross-project patterns).
     - On-screen display labels each preference with its source: "originally-onboarded" vs. "retro-written."
     - When `~/.claude/sdd-cross-project-patterns.md` does not exist (the user has never run `/sdd:retro`), silently surface only the profile preferences. No warning, no empty section.
  2. **Seed new v2 profile fields** (Step 6, write):
     - On profile creation OR update, write:
       - `handoffWarningShown: false`
       - `defaultSprintMode: null` (or omit the field — semantically equivalent per spec).
     - Update the profile schema example in the SKILL.md to include both fields.
     - When the profile already exists and the field is present, do not overwrite (e.g., if `handoffWarningShown: true` has been flipped by a downstream command, leave it). Only write when the field is missing.
- **Acceptance criteria:**
  - [ ] `aadk` When `/sdd:onboard` runs and `~/.claude/sdd-user-profile.json` already exists, it surfaces both originally-onboard-set preferences and `/sdd:retro`-written cross-project preferences.
  - [ ] `aadl` The on-screen display labels each preference with its source (originally-onboarded vs. retro-written).
  - [ ] `aadm` `/sdd:onboard` writes `handoffWarningShown: false` to `~/.claude/sdd-user-profile.json` when creating or updating the profile.
  - [ ] `aadn` `/sdd:onboard` writes `defaultSprintMode: null` to `~/.claude/sdd-user-profile.json` when creating or updating the profile (or omits the field with semantic equivalence to `null`).
- **Verification:**
  - Read the updated SKILL.md and confirm Step 1's update-flow logic reads from both the profile JSON and the cross-project-patterns markdown.
  - Confirm the SKILL.md describes the source-label display ("originally-onboarded" vs. "retro-written") explicitly.
  - Confirm the no-file branch (cross-project-patterns absent) is documented as silent (no warning).
  - Confirm Step 6's write step covers both `handoffWarningShown: false` and `defaultSprintMode: null` (or omits the latter with documented semantic equivalence).
  - Confirm the schema example block in the SKILL.md includes both new fields.
  - Confirm the no-overwrite rule is documented: when the profile already has a value (e.g., `handoffWarningShown: true`), the field is preserved, not reset.
- **Status:** [ ] Not started

---

## Notes

- All four items edit the same file: `plugins/sdd/skills/onboard/SKILL.md`. Sequential execution is mandatory.
- Sprint 7 closes 18 PRD ACs: 4 (Item 1: `aacw`–`aacz`) + 6 (Item 2: `aada`–`aadf`) + 4 (Item 3: `aadg`–`aadj`) + 4 (Item 4: `aadk`–`aadn`).
- `[FB: ...]` tagging: this sprint's items do **not** carry `[FB: ...]` tags. The `/sdd:onboard Polish` epic ACs are sdd-plugin-internal architecture (the onboarding command's mechanics), not surfaced feedback items in `docs/v2-verification.md`. No FB rows correspond directly.
- The wrap-up phase from Sprint 5 fires on this sprint. Since Sprint 7 items carry real `[PRD: ...]` tags, the wrap-up should auto-check 18 PRD ACs in `docs/prd.md`. Since no `[FB: ...]` tags are present, no `docs/v2-verification.md` rows update. The cross-cutting Bug Fix 2 logic doesn't fire (no AC referenced across multiple sprint files in Sprint 7).
- After Sprint 7: the natural next-sprint candidates remain (a) Bug Fix 1 (`aaaa`–`aaac`, `/sdd:feedback` no-lastCommand-clobber); (b) `/sdd:polish` re-open mechanism (`aaev`–`aaex`); (c) Interview Command Behaviors cross-cutting (sdd-guide + reference changes — the biggest payoff sprint, unlocks `/sdd:discovery` per AC `aaaj`); (d) Cross-Project Feedback Transfer (`aaff`–`aafr`); (e) `/sdd:discovery` green-field implementation (`aaah`–`aaaq`); (f) Multi-Session Resume (`/sdd:pause` + `/sdd:unpause`); (g) Automatic Backlog Generation; (h) Process Notes Growth; (i) v1→v2 Migration verification; (j) `/sdd:retro` at Project Close. Choose at the next `/sdd:sprint`.
- The implementation is markdown-only (SKILL.md edits). No code, no runtime. Verification is by SKILL.md re-read against ACs; end-to-end dogfooding fires when the user next re-runs `/sdd:onboard` (which will be after the plugin is reinstalled to pick up v2 source).
- **Plugin-cache caveat:** The cached marketplace at `C:\Users\jcmcc\.claude\plugins\cache\sdd-marketplace\sdd\1.0.0\` is still v1. Until the maintainer reinstalls, the new Sprint 7 source ships to disk but isn't live as a slash command. The v2 dogfood of `/sdd:onboard` Polish happens on reinstall (or by adopting the v2 plugin entry directly).
