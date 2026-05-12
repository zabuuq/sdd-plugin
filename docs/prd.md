# SDD v2 — Product Requirements

## Problem Statement

v1 of the SDD plugin shipped and worked, but accumulated friction across three real-use projects (Event Planning App, fiver-gigs, sdd-plugin self-use). Two genuine bugs surfaced (`/sdd:feedback` clobbering `lastCommand`; PRD state-tracking gaps in `/sdd:reflect`), and the post-sprint flow consistently felt like three stacked ceremonies (`/build`, `/iterate`, `/reflect`) rather than one cohesive close-out. The audience also expanded: a local tech group is joining as users and feedback sources, so v2 must clear a working-software bar AND its feedback loop must be real and easy to use.

v2 is a streamlining pass driven by that real-use feedback — refinement of what already works, not a feature expansion. Deferred items: see `docs/backlog.md`.

## User Stories

### Bug Fixes

- As a plugin user, I want consecutive `/sdd:feedback` notes to retain the real preceding-command context, so batched feedback accurately attributes each note to the work I was actually doing.
  - [ ] `/sdd:feedback`'s startup sequence contains no step that writes to `lastCommand` in `docs/project-state.json`.
  - [ ] The "After command" field in each feedback note is sourced from the value of `lastCommand` at the moment `/sdd:feedback` is invoked.
  - [ ] When `/sdd:feedback` runs N times consecutively with no other SDD command invoked between runs, every resulting note's "After command" field equals the value of `lastCommand` that was present before the first `/sdd:feedback` invocation.

- As a plugin user, I want completed sprint items to automatically check off their matching PRD acceptance criteria, so my PRD reflects actual project state without manual drift between sprint files and the PRD.
  - [ ] When `/sdd:build`'s wrap-up phase confirms a sprint item is fully complete and no story-split is required, it checks off every PRD acceptance criterion that sprint item references in `docs/prd.md`.
  - [ ] When a sprint item maps to only some of the acceptance criteria of a larger cross-cutting PRD story, only the satisfied criteria are checked; criteria requiring contributions from any sprint item across any sprint file that is still incomplete remain unchecked.
  - [ ] When the final sprint item contributing to a cross-cutting PRD acceptance criterion completes, that criterion gets checked during that sprint's `/sdd:build` wrap-up.
  - [ ] After `/sdd:build`'s wrap-up runs on a sprint, no PRD acceptance criterion remains unchecked while every sprint-item AC across all sprint files that references it is checked.

### /sdd:discovery Command

- As a plugin user starting a new project, I want a dedicated exploration step where I openly discuss what I'm building before any structural decisions, so context is rich before `/scope` draws boundaries.
  - [ ] `/sdd:discovery` exits with a "run `/sdd:onboard` first" message and writes nothing when `~/.claude/sdd-user-profile.json` does not exist.
  - [ ] `/sdd:discovery` runs an interview using the SDD interview pattern: Phase 1 mandatory beats followed by deepening rounds offered via the standard end-of-round recommendation framing.
  - [ ] `/sdd:discovery` applies every cross-cutting interview command behavior defined in the Interview Command Behaviors epic.
  - [ ] `/sdd:discovery` appends to `process-notes-discovery.md` in real time during the interview rather than writing a single end-of-command summary.

- As a plugin user, I want to upload reference files (existing PRDs, notes, diagrams) at the start of a project, so the agent reads them as context for discovery without me re-stating their contents inline.
  - [ ] `/sdd:discovery` has a designated upload location it reads before opening Phase 1 of the interview (exact path/mechanism deferred to `/sdd:spec`).
  - [ ] When the designated upload location contains files, `/sdd:discovery` reads them as context and may reference their content during the interview without the user re-stating that content.
  - [ ] When the designated upload location is empty, `/sdd:discovery` proceeds directly into the Phase 1 interview without prompting the user to upload.

- As a plugin user, I want `/sdd:discovery` to write a `discovery.md` capturing what the project is, so downstream commands and future sessions have grounding without re-reading the full conversation.
  - [ ] `/sdd:discovery` writes `docs/discovery.md` at the end of its interview.
  - [ ] When `docs/discovery.md` already exists at `/sdd:discovery` startup, `/sdd:discovery` requires explicit user confirmation before overwriting it.
  - [ ] Once `docs/scope.md` exists in the project, subsequent commands read `docs/discovery.md` but do not write to it.

- As a plugin user on a small or simple project, I want `/sdd:discovery` to recognize that signal and propagate it downstream, so subsequent interview commands right-size their rounds and questions.
  - [ ] `/sdd:discovery` detects small-project signals during its interview (specific signals and thresholds deferred to `/sdd:spec`).
  - [ ] When small-project signals are detected, `/sdd:discovery` writes `smallProject: true` to `docs/project-state.json`.
  - [ ] When small-project signals are absent, `/sdd:discovery` either omits the `smallProject` field from `docs/project-state.json` or sets it to `false`.
  - [ ] Subsequent interview commands re-evaluate the `smallProject` value on their own startup and may update it in `docs/project-state.json` based on signals surfaced in their own runs.

### Multi-Session Resume

- As a plugin user mid-interview, I want a `/sdd:pause` command that captures the current conversation as a resume file, so I can switch computers or clear context without losing position.
  - [ ] `/sdd:pause` proceeds with its work only when `lastCommand` in `docs/project-state.json` equals one of: `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:refine`, `/sdd:polish`.
  - [ ] When `lastCommand` equals `/sdd:onboard`, `/sdd:build`, `/sdd:retro`, `/sdd:feedback`, or is unset, `/sdd:pause` writes no resume file and exits with a message stating nothing is in-flight to pause.
  - [ ] `/sdd:pause` writes the resume file to `docs/<command>-resume.md`, where `<command>` is the in-flight command name without the `/sdd:` prefix.
  - [ ] The resume file contains these sections, in this order: Status, Setup-already-done, Confirmed decisions, Round status, Pending decision, Files for next-session, Instructions for next-session.
  - [ ] The resume file contains enough captured detail to seed a fresh zero-context model into the same decision state as the paused conversation (specific quality bar deferred to `/sdd:spec`).
  - [ ] When a resume file already exists at the target path, `/sdd:pause` overwrites it rather than appending or refusing.
  - [ ] `/sdd:pause` emits the message "Paused. Run `/sdd:unpause` to resume." as its final output.

- As a plugin user returning from a pause, I want `/sdd:unpause` to figure out which command to resume without me telling it, so I don't have to memorize paths or flags.
  - [ ] `/sdd:unpause` accepts no arguments.
  - [ ] `/sdd:unpause` reads `lastCommand` from `docs/project-state.json` and treats that value as the command to resume.
  - [ ] `/sdd:unpause` locates and reads `docs/<command>-resume.md` (where `<command>` is the `lastCommand` value without the `/sdd:` prefix) before any other interview work.
  - [ ] `/sdd:unpause` reads the source command's `SKILL.md` before resuming the interview.
  - [ ] `/sdd:unpause` resumes the interview at the question or decision captured in the resume file's Pending decision section.
  - [ ] When no resume file exists at the expected path for the current `lastCommand` value, `/sdd:unpause` exits with a message naming the missing file and pointing the user to either `/sdd:pause` or re-running the original command.

- As a plugin user, I want resume files to clean up automatically when the underlying work completes, so stale resume state doesn't accumulate across projects.
  - [ ] When a resumed command's underlying interview completes its document generation step, the resumed command deletes its own resume file at `docs/<command>-resume.md`.
  - [ ] When a command completes its document generation and no resume file exists at the expected path, the command raises no error and continues.
  - [ ] After a resumed command's deletion step runs, no resume file for that command exists in `docs/` until `/sdd:pause` is invoked again.

### Interview Command Behaviors

*Stories in this epic apply to all seven interview commands (`/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:refine`, `/sdd:polish`) unless explicitly scoped to one command.*

- As a plugin user, I want to ask clarifying questions mid-interview without losing my place, so I can think through what's being asked without the agent advancing prematurely.
  - [ ] When the user asks a clarifying question mid-interview, the agent's response addresses only the clarification and does not change the currently-open question.
  - [ ] The agent does not write its own recommendation into the conversation record as the user's answer to the currently-open question.
  - [ ] The interview pointer advances to the next question only after the user provides an explicit answer to the question that was actually asked.

- As a plugin user in a deepening round, I want the round to have predictable bounds, so I can plan my focus and know when to push back on scope.
  - [ ] Each deepening round is composed by the agent with five questions by default.
  - [ ] A deepening round may run up to ten questions without further explanation from the agent.
  - [ ] Past ten questions in a single round, the agent emits a stated reason for needing more questions and obtains explicit user permission before posing the eleventh.

- As a plugin user finishing a deepening round, I want the agent to make a definite recommendation about another round with reasoning, so I'm not left to decide blind whether to continue.
  - [ ] At the end of every deepening round, the agent emits one of: "I think we should do another round, because [reason]" or "I do not think we need another round, because [reason]."
  - [ ] The recommendation includes the agent's reasoning in the same message.
  - [ ] When the recommendation is to continue, the reasoning includes an explicit list of topics the proposed next round would cover.
  - [ ] When the recommendation is to close, the reasoning may include a list of topics a next round would have covered, used to justify why those topics do not warrant a round.

- As a plugin user on a small project, I want interview commands to right-size their behavior based on the `smallProject` hint, so the ceremony fits the work.
  - [ ] Every interview command reads the `smallProject` value from `docs/project-state.json` on startup.
  - [ ] When `smallProject` is `true`, the agent skips Phase 1 beats designated as small-project-skippable (specific beats deferred to `/sdd:spec`).
  - [ ] When `smallProject` is `true`, the agent's end-of-round recommendation defaults to "I do not think we need another round" unless concrete evidence in the just-completed round contradicts that default.
  - [ ] When `smallProject` is `true` and the answers in the most recently completed round are thin (specific thinness signal deferred to `/sdd:spec`), the agent composes the next round with fewer than five questions.
  - [ ] Each interview command may write a new value to `smallProject` in `docs/project-state.json` during its own run based on signals it observes.

- As a plugin user, I want interview commands to actively prompt for additional input throughout, so I don't feel pressured to hold back or skip past a beat that matters to me.
  - [ ] At the end of each Phase 1 beat, the agent emits an explicit invitation for additional input (e.g., "anything else on this?", "did I miss anything?", or equivalent open-ended prompt).
  - [ ] At the end of each deepening-round topic, the agent emits the same explicit invitation.
  - [ ] The agent does not advance to the next beat or topic before the user has had an opportunity to respond to the invitation.
  - [ ] When the user provides additional input in response to the invitation, the agent processes that input as part of the current beat or topic before advancing.

- *(applies to `/sdd:prd` only)* As a plugin user, I want `/sdd:prd` to apply a quality check to every acceptance criterion before writing the PRD, so the file I get out is actually testable.
  - [ ] Before writing `docs/prd.md`, `/sdd:prd` evaluates every acceptance criterion against three checks: specific (describes a concrete behavior), observable (someone can look at the system and determine pass/fail), verifiable (testable without subjective judgment).
  - [ ] When an acceptance criterion fails one or more of the three checks, `/sdd:prd` rewrites it before writing `docs/prd.md`.
  - [ ] The check runs exactly once per `/sdd:prd` run, immediately before document generation.

- *(applies to `/sdd:prd` only)* As a plugin user, I want `/sdd:prd` to flag when my project has crossed the phase-split threshold, so I can decide whether to break it up before things sprawl.
  - [ ] When the in-conversation epic count reaches 10 or more, `/sdd:prd` emits a phase-split recommendation before generating `docs/prd.md`.
  - [ ] The recommendation message explains phase-splitting as breaking the project into smaller pieces, each going through the full SDD workflow independently.
  - [ ] `/sdd:prd` proceeds with PRD generation on user election to keep one phase; the recommendation does not block generation.

- *(applies to `/sdd:prd` only)* As a plugin user, I want `/sdd:prd` to create an Unvetted section in the PRD, so new items surfaced during sprints have a defined landing place before `/sdd:refine` processes them.
  - [ ] `/sdd:prd` writes `docs/prd.md` with an `## Unvetted` section present.
  - [ ] The Unvetted section contains no list items at the moment `/sdd:prd` writes the file.
  - [ ] The Unvetted section contains a marker comment in the file body explaining the section's purpose to future agents and `/sdd:refine`.

### Context Management

*Both stories in this epic apply to all seven interview commands.*

- As a plugin user between deepening rounds, I want a definite recommendation about how to manage context before the next round, so I don't have to guess whether to compact or clear.
  - [ ] At the end of every deepening round, after the standard end-of-round recommendation about whether to continue, the agent emits a separate recommendation choosing one of three tiers: continue, `/compact`, or `/clear`.
  - [ ] The agent recommends the **continue** tier when the round was short and the in-progress artifact already captures the round's value.
  - [ ] The agent recommends the **/compact** tier when the round captured detail in conversation that the artifact did not, but a compacted summary will carry that detail forward usefully.
  - [ ] The agent recommends the **/clear** tier when the round was long or heavy, the artifact is rich and self-contained, and the remaining conversation is dominated by superseded back-and-forth.
  - [ ] The context-management recommendation is delivered with reasoning in the same message and is never phrased as a bare prompt (e.g., "want to compact?" is not allowed).
  - [ ] The plugin does not programmatically invoke `/compact` or `/clear`; the user runs the command after receiving the recommendation.
  - [ ] Recognition signals supporting the recommendation (round length, artifact-vs-conversation coverage, noise ratio) are deferred to `/sdd:spec`.

- As a plugin user moving from one command to the next, I want a default `/clear` handoff so my context is fresh for the next command's work.
  - [ ] At the end of every interview command, the agent emits a handoff message containing both the instruction to run `/clear` and the name of the recommended next command.
  - [ ] The handoff message is emitted unconditionally — no context-weight heuristic causes it to be skipped.
  - [ ] Exact handoff message wording is deferred to `/sdd:spec`.

### /sdd:onboard Polish

- As a plugin user filling out my communication style during onboarding, I want a single open-ended prompt and no example menu, so my answer reflects how I actually want the agent to speak with me rather than the nearest option in a list.
  - [ ] `/sdd:onboard`'s communication-style question is delivered as a single open-ended prompt with no bulleted examples in the question text.
  - [ ] `/sdd:onboard` writes the user's free-form answer directly into the `communicationStyle` field of `~/.claude/sdd-user-profile.json`.

- As a new SDD user, I want `/sdd:onboard` to tell me where my preferences live and how to change them, so I know how to update them without re-onboarding from scratch.
  - [ ] `/sdd:onboard` outputs the literal path `~/.claude/sdd-user-profile.json` at least once during its run.
  - [ ] `/sdd:onboard` outputs a sentence stating that re-running `/sdd:onboard` updates the same profile file.

- As a new SDD user, I want `/sdd:onboard` to cover `/sdd:feedback` before ending, so I know how to log feedback during a project without hunting for instructions later.
  - [ ] `/sdd:onboard` includes a dedicated `/sdd:feedback` beat before the command ends.
  - [ ] The beat covers what `/sdd:feedback` does, when to use it, and where notes go (the current project's `docs/sdd-feedback.md` plus optional forwarding via `feedbackLocalPath`).

- As a new SDD user, I want an optional `feedbackLocalPath` question that I can skip without friction, so I'm not forced to commit to a path I don't have or want yet.
  - [ ] `/sdd:onboard` poses a single question for `feedbackLocalPath`.
  - [ ] The question text explains what `feedbackLocalPath` is for.
  - [ ] When the user provides a non-empty answer, `/sdd:onboard` writes that value to the `feedbackLocalPath` field of `~/.claude/sdd-user-profile.json`.
  - [ ] When the user provides an empty answer or skips the question, `/sdd:onboard` omits the `feedbackLocalPath` field from the profile or writes `null` to it, and does not re-ask within the same run.

- As a new SDD user, I want `/sdd:onboard` to explain third-party plugin auto-update mechanics, so I know how plugin updates work without being pushed to flip a setting.
  - [ ] `/sdd:onboard` outputs a paragraph covering: third-party plugin update defaults in Claude Code, how to toggle auto-update via `/plugin` → Marketplaces, and what `/reload-plugins` does.
  - [ ] The paragraph contains no recommendation language regarding whether to enable auto-update (e.g., no "you should" phrasing about turning auto-update on or off).

- As a new SDD user finishing onboarding, I want a heads-up that `/sdd:discovery` is an interview rather than a one-shot prompt, so I know what to expect when I run it.
  - [ ] `/sdd:onboard` ends with a message that names `/sdd:discovery` as the recommended next command.
  - [ ] The message states that `/sdd:discovery` is an interview, not a single-prompt command.

- As a plugin user re-running `/sdd:onboard`, I want to see both my original onboarding preferences and any cross-project preferences `/sdd:retro` has written, so I have a complete view of what's currently set.
  - [ ] When `/sdd:onboard` runs and `~/.claude/sdd-user-profile.json` already exists, it surfaces both originally-onboard-set preferences and `/sdd:retro`-written cross-project preferences.
  - [ ] The on-screen display labels each preference with its source (originally-onboarded vs. retro-written).

### Sprint Loop

- As a plugin user starting a sprint, I want `/sdd:plan` to read the previous sprint's process notes, so carry-forward items and tech-debt observations from the last `/build` wrap-up are surfaced before I plan.
  - [ ] On startup, `/sdd:plan` reads the file `process-notes-sprint-N.md` with the highest N in the project root, if any such file exists.
  - [ ] `/sdd:plan` surfaces carry-forward items recorded by the previous `/sdd:build` wrap-up.
  - [ ] `/sdd:plan` surfaces quality concerns and tech-debt items recorded by the previous `/sdd:build` wrap-up.
  - [ ] When no `process-notes-sprint-N.md` file exists at startup, `/sdd:plan` proceeds to its next step without emitting a warning.

- As a plugin user starting a sprint, I want `/sdd:plan` to check the PRD's overall state, so I know whether to plan a new sprint, run `/sdd:refine`, or run `/sdd:retro`.
  - [ ] After reading prior process notes, `/sdd:plan` scans `docs/prd.md` and determines whether every acceptance criterion is checked, whether the Unvetted section contains items, and whether unchecked criteria remain.
  - [ ] When every acceptance criterion is checked and the Unvetted section is empty, `/sdd:plan` recommends running `/sdd:retro` and does not proceed to sprint planning.
  - [ ] When the Unvetted section contains items, `/sdd:plan` recommends running `/sdd:refine` and does not proceed to sprint planning until the user acknowledges or overrides.
  - [ ] When neither closing condition holds, `/sdd:plan` proceeds with sprint planning.

- As a plugin user, I want `/sdd:plan` to validate that every sprint item carries an explicit PRD reference (or an explicit unmapped tag), so `/sdd:build`'s wrap-up can deterministically check off PRD criteria without missing references.
  - [ ] Every sprint-item line in `docs/sprint-N.md` written by `/sdd:plan` carries either a PRD acceptance-criterion reference tag (e.g., `[PRD: Epic / Story / AC]` or equivalent — exact syntax deferred to `/sdd:spec`) or an explicit `[unmapped]` tag.
  - [ ] At the end of sprint planning, before finalizing `docs/sprint-N.md`, `/sdd:plan` validates that every sprint-item line in the proposed sprint carries one of the two tag types.
  - [ ] When any sprint-item line lacks both a PRD reference tag and an `[unmapped]` tag, `/sdd:plan` refuses to finalize the sprint file and surfaces every offending line for the user to address.
  - [ ] `/sdd:plan` does not write `docs/sprint-N.md` to disk until validation passes for every sprint-item line.

- As a plugin user finishing `/sdd:plan`, I want a next-command recommendation, so I know what to do next without having to remember the chain.
  - [ ] At the end of `/sdd:plan`'s run, the command emits a recommendation naming the next command (typically `/sdd:build`).
  - [ ] The recommendation includes the cross-cutting between-commands handoff (run `/clear`, then invoke the named next command).

- As a plugin user starting a sprint, I want `/sdd:plan` to remember my preferred sprint mode, so I'm not asked to re-choose it every sprint.
  - [ ] When `~/.claude/sdd-user-profile.json` contains a non-empty `defaultSprintMode` field, `/sdd:plan` uses that value as the sprint mode without asking the user.
  - [ ] When `defaultSprintMode` is unset but `docs/project-state.json` contains a recorded mode from the previous sprint, `/sdd:plan` asks the user whether to reuse that mode and proceeds based on the answer.
  - [ ] When neither `defaultSprintMode` nor a recorded previous-sprint mode is available, `/sdd:plan` asks the user to select a mode.
  - [ ] After mode selection by any path above, `/sdd:plan` offers an "always use this mode" toggle; on user acceptance, the selected mode is written to `defaultSprintMode` in `~/.claude/sdd-user-profile.json`.
  - [ ] `/sdd:plan` writes the selected mode to `docs/project-state.json` for next-sprint reference.

- As a plugin user finishing a sprint's checklist, I want `/sdd:build` to ask whether I'm satisfied or want to iterate, so the wrap-up reflects how I actually feel about the work.
  - [ ] After the final sprint checklist item is marked complete, `/sdd:build` emits the question "Satisfied with this sprint, or items to iterate on?" as the wrap-up entry point.
  - [ ] When `process-notes-sprint-N.md` contains entries written during checklist execution that carry the iteration-candidate marker (exact marker syntax deferred to `/sdd:spec`), `/sdd:build` surfaces those candidates alongside the question.
  - [ ] `/sdd:build` surfaces an iteration candidate at wrap-up only when an entry carrying the iteration-candidate marker exists in `process-notes-sprint-N.md` written before wrap-up began.
  - [ ] When the user answers "satisfied," `/sdd:build` proceeds with close-sprint behavior.
  - [ ] When the user answers "items to iterate," `/sdd:build` recommends running `/sdd:polish` and leaves the sprint file open (no close-sprint actions are performed).
  - [ ] When the user answers "satisfied" and one or more tagged iteration candidates exist in `process-notes-sprint-N.md`, `/sdd:build` asks the user to assign a disposition for each surfaced candidate from the set {discard, capture as tech-debt, actually iterate} before proceeding with close-sprint behavior.
  - [ ] When the user assigns "actually iterate" to any candidate during disposition, `/sdd:build` switches to the "items to iterate" branch and does not perform close-sprint actions.
  - [ ] When the user assigns "capture as tech-debt" to a candidate, `/sdd:build` records that candidate as a tech-debt entry in `process-notes-sprint-N.md` during close-sprint.
  - [ ] When the user assigns "discard" to a candidate, `/sdd:build` performs no further action for that candidate beyond noting the discard decision in `process-notes-sprint-N.md`.

- As a plugin user closing a sprint, I want `/sdd:build` to perform a defined set of close-out actions, so the wrap-up is repeatable and nothing is left implicit.
  - [ ] On close-sprint, `/sdd:build` performs PRD acceptance-criterion checkoff per the Bug Fixes epic Story 2 behavior.
  - [ ] On close-sprint, `/sdd:build` identifies any unfinished items in the sprint checklist, offers story-splitting with explicit user confirmation per item, and updates `docs/prd.md` to reflect each confirmed split.
  - [ ] On close-sprint, `/sdd:build` emits exactly one "anything notable?" prompt covering quality concerns, tech debt, and surprises.
  - [ ] On close-sprint, `/sdd:build` writes `process-notes-sprint-N.md` containing decisions made during the sprint, pushback from either side, surprises, notable items captured in the wrap-up, and dispositions assigned to any iteration candidates.
  - [ ] On close-sprint, after writing the process notes file, `/sdd:build` emits a next-command recommendation based on the PRD state check (continue sprint loop, run `/sdd:refine`, or run `/sdd:retro`).

- As a plugin user who realizes mid-`/polish` that a sprint isn't actually done, I want a re-open escape hatch, so I can correct a premature "satisfied" answer.
  - [ ] When `/sdd:polish` is invoked and the targeted sprint's `process-notes-sprint-N.md` file exists indicating the sprint was closed, the system asks "Re-open the sprint?" before proceeding.
  - [ ] On user confirmation, the sprint is reopened; the specific mechanism for unwinding PRD checkoff and process-notes effects is deferred to `/sdd:spec`.
  - [ ] On user rejection, `/sdd:polish` exits with a message explaining that re-opening is required to proceed.

### /sdd:retro at Project Close

- As a plugin user finishing a project, I want `/sdd:retro` to be recommended only at project close (not every sprint), so I'm not running a retrospective ceremony that doesn't match the project state.
  - [ ] No SDD command recommends running `/sdd:retro` at every sprint boundary.
  - [ ] `/sdd:plan` recommends `/sdd:retro` only when its PRD state check finds every acceptance criterion checked and the Unvetted section empty.
  - [ ] `/sdd:build`'s wrap-up recommends `/sdd:retro` only when its PRD checkoff causes every acceptance criterion in `docs/prd.md` to be checked and the Unvetted section is empty.

- As a plugin user closing a project, I want `/sdd:retro` to capture cross-project patterns I want to carry forward, so my user profile reflects what I learned this project.
  - [ ] `/sdd:retro` includes a final step after its retrospective interview that captures cross-project patterns the user wants to carry forward.
  - [ ] `/sdd:retro` writes captured patterns to `~/.claude/sdd-user-profile.json` or a sibling file in `~/.claude/` (specific file deferred to `/sdd:spec`).
  - [ ] `/sdd:retro` writes a captured pattern only after the user has explicitly confirmed it.
  - [ ] The pattern-capture step fires only after the rest of `/sdd:retro`'s interview has been completed.

### Cross-Project Feedback Transfer

*Convention used by this epic: `feedbackLocalPath` is the **root directory of the local plugin clone**, not the full path to the feedback file. The command appends `/docs/sdd-feedback.md` to it.*

- As a plugin user, I want `/sdd:feedback` to write each note both locally and to my plugin clone (if configured), so feedback gathered while working on real projects reaches the plugin's feedback pile.
  - [ ] `/sdd:feedback` writes each feedback note to the current project's `docs/sdd-feedback.md` file.
  - [ ] When `feedbackLocalPath` is set in `~/.claude/sdd-user-profile.json`, `/sdd:feedback` also writes the note to `<feedbackLocalPath>/docs/sdd-feedback.md`.
  - [ ] Both writes use identical note formatting.
  - [ ] When `feedbackLocalPath` is unset or `null`, `/sdd:feedback` writes only to the current project's `docs/sdd-feedback.md` and emits no warning or prompt.

- As a plugin user working inside my plugin clone, I want `/sdd:feedback` to avoid double-writing the same note, so the feedback pile stays clean.
  - [ ] `/sdd:feedback` compares the current working directory to the configured `feedbackLocalPath` value.
  - [ ] When the current working directory equals `feedbackLocalPath` or is a descendant of it, `/sdd:feedback` performs only the local write — no additional write to `<feedbackLocalPath>/docs/sdd-feedback.md`.
  - [ ] When the current working directory is neither equal to nor a descendant of `feedbackLocalPath`, `/sdd:feedback` performs both the local write and the forwarding write.
  - [ ] Path normalization details (case sensitivity by OS, trailing slashes, symlinks) are deferred to `/sdd:spec`.

- As a plugin user with a misconfigured `feedbackLocalPath`, I want `/sdd:feedback` to never lose my note and to warn me once per session, so I can fix the path without my feedback evaporating.
  - [ ] When `feedbackLocalPath` is set but invalid (path does not exist, write fails, or equivalent), `/sdd:feedback` completes the local write to the current project's `docs/sdd-feedback.md`.
  - [ ] On any forwarding-write failure, `/sdd:feedback` emits a single warning containing the failure reason and the instruction "Run `/sdd:onboard` to update."
  - [ ] `/sdd:feedback` does not retry the forwarding write within the same invocation.
  - [ ] `/sdd:feedback` does not exit with an error status when the forwarding write fails — the command completes after the local write.
  - [ ] When subsequent `/sdd:feedback` invocations within the same session encounter the same forwarding-write failure, no additional warning is emitted.

### Automatic Backlog Generation

- As a plugin user deferring an item during planning, I want the planning command to write a backlog entry only on my explicit confirmation, so the backlog never accumulates surprise entries I didn't sign off on.
  - [ ] When the user defers an item during `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, or `/sdd:plan`, the command emits an explicit confirmation prompt before writing to `docs/backlog.md`.
  - [ ] The confirmation prompt distinguishes "defer to backlog" from "drop."
  - [ ] On user response "defer to backlog" (or equivalent affirmative), the command appends a backlog entry to `docs/backlog.md`.
  - [ ] On any user response other than the affirmative defer-to-backlog answer, the command writes no entry to `docs/backlog.md`.
  - [ ] When an item is dropped rather than deferred, the dropping decision is recorded only in the planning command's process-notes file for the current phase.

- As a plugin user picking up a backlog entry months later, I want every entry to be fully self-contained, so I don't have to dig through old conversations to understand it.
  - [ ] Each backlog entry contains the fields Title, What, Why deferred, Trigger to revisit, Dependencies, and Source, in that order.
  - [ ] The Source field records the command, the session or round, and the thread or topic where the deferral occurred (exact field format deferred to `/sdd:spec`).
  - [ ] Each backlog entry is separated from the next by a horizontal rule (`---`) on its own line.
  - [ ] New entries are appended to `docs/backlog.md` in the order they are written; existing entries are not modified by subsequent commands.

- As a plugin user reading in-flight planning artifacts, I want them to point at the backlog rather than restate deferred items inline, so I have one source of truth and don't have to keep stale lists in sync.
  - [ ] When `docs/backlog.md` exists and contains at least one entry at the moment a planning command generates its artifact, the generated artifact includes a pointer to `docs/backlog.md` in lieu of an inline deferred-items section.
  - [ ] Generated planning artifacts contain no inline "out of scope" or "deferred items" section that restates entries from `docs/backlog.md`.
  - [ ] This rule applies to artifacts generated by `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, and `/sdd:plan`.

### Process Notes Growth

- As a plugin user looking back at a project, I want each planning phase to have its own process-notes file, so the history of each phase is locatable and self-contained.
  - [ ] `/sdd:discovery` writes to `process-notes-discovery.md` in the project root.
  - [ ] `/sdd:scope` writes to `process-notes-scope.md` in the project root.
  - [ ] `/sdd:prd` writes to `process-notes-prd.md` in the project root.
  - [ ] `/sdd:spec` writes to `process-notes-spec.md` in the project root.
  - [ ] `/sdd:plan` and `/sdd:build` write to `process-notes-sprint-N.md` in the project root.
  - [ ] No process-notes file is written to by more than one planning command.

- As a plugin user running `/sdd:retro`, I want it to read process-notes files only as needed, so retro doesn't load the full project history into context at startup.
  - [ ] `/sdd:retro` reads each phase's process-notes file as a separate read during the retro interview, rather than concatenating all process-notes files at startup.
  - [ ] When a process-notes file for a phase the project skipped is absent, `/sdd:retro` proceeds without emitting a warning for that absence.

- As a plugin user working through a long phase, I want process notes to be append-only within the phase, so the history of decisions isn't pruned out from under me.
  - [ ] At end of phase, each phase's process-notes file contains every real-time entry written by the phase's command, in the order written, with no entries modified or removed by the same command after writing.
  - [ ] Mid-phase summarization for multi-session continuity is handled by `/sdd:pause` writing to its resume file, not by edits to the phase's process-notes file.

### v1 → v2 Migration

- As a v1 user opening the v2 migration guide, I want a clear, honest framing of what changed, so I'm not confused by intermediate naming proposals that didn't make it into v2.
  - [ ] The plugin ships a file named `MIGRATION-v1-to-v2.md` at the plugin root.
  - [ ] The opening framing of `MIGRATION-v1-to-v2.md` states: v2 adds `/sdd:discovery` at the start of the planning chain; `/sdd:scope` is narrower (boundary only — exploration moved to `/sdd:discovery`); `/sdd:prd` and `/sdd:spec` are unchanged; `/sdd:sprint` is renamed to `/sdd:plan`; `/sdd:iterate` is renamed to `/sdd:polish`; `/sdd:reflect` is removed; new utilities `/sdd:pause` and `/sdd:unpause`.
  - [ ] The migration guide does not present an intermediate renaming framing in which `/sdd:scope` and `/sdd:prd` were renamed in tandem.

- As a v1 user mid-project, I want a state-aware migration table that tells me exactly what to do based on my current `lastCommand` value, so I'm not left to interpret which v2 command is the right next step.
  - [ ] `MIGRATION-v1-to-v2.md` contains a state-aware migration table keyed on v1 `lastCommand` values.
  - [ ] The table contains a row for every v1 command name that could appear as `lastCommand`, including the now-removed `/sdd:reflect`.
  - [ ] The table contains separate rows for "mid-reflect" and "post-reflect" cases, each naming a v2 command to invoke next.
  - [ ] Each row names the v2 command to invoke next and any one-time action required (e.g., resume-file rename).

- As a v1 user, I want explicit instructions for handling renamed commands and resume files, so I don't have to guess what to do with my in-flight state.
  - [ ] The migration guide documents the rename pairs `/sdd:sprint` → `/sdd:plan` and `/sdd:iterate` → `/sdd:polish`.
  - [ ] The migration guide instructs users to rename v1 resume files to their v2 names when such files exist.
  - [ ] The migration guide includes a statement that `/sdd:scope` is narrower in v2 (exploration moved to `/sdd:discovery`).

- As a v1 user with legacy process notes, I want clear guidance on what stays and what moves, so I don't accidentally delete history or fail to migrate it.
  - [ ] The migration guide states that legacy `process-notes.md` is treated as a read-only historical reference.
  - [ ] The migration guide includes an optional one-time split recommendation for converting legacy `process-notes.md` into per-phase files.
  - [ ] The migration guide states that `docs/archive/v1/` is left untouched by v2.
  - [ ] The index file inside `docs/archive/v1/` contains a one-line note clarifying that the archive contains a pre-rename snapshot of v1 planning artifacts.

- As a v1 user, I want the migration guide to introduce v2's new capabilities, so I know what I'm getting beyond the rename mechanics.
  - [ ] `MIGRATION-v1-to-v2.md` contains a section introducing `/sdd:pause` and `/sdd:unpause`.
  - [ ] `MIGRATION-v1-to-v2.md` contains a section introducing the `feedbackLocalPath` profile field and its purpose.
  - [ ] `MIGRATION-v1-to-v2.md` contains a section stating that `/sdd:discovery` is a genuinely new command (not a rename of any v1 command).

- As a v1 user, I want v2 to ship with no command-name aliases and a fully consistent active material, so I don't have stale references hanging around.
  - [ ] v2 ships with no command alias for `/sdd:sprint`, `/sdd:iterate`, or `/sdd:reflect`.
  - [ ] Every file under `skills/`, `commands/`, the plugin root README, and live prose under `docs/` (excluding `docs/archive/v1/`) uses v2 command names consistently, with no v1 command name appearing except in explicit migration-context references.
  - [ ] `docs/archive/v1/` is left untouched by the v2 migration sweep.
  - [ ] `MIGRATION-v1-to-v2.md` explicitly states that v1 command names are not aliased in v2.

### v2 Release Verification

- As the plugin maintainer, I want `docs/v2-verification.md` to be populated by implementing commands as work ships, so the audit trail builds automatically rather than at release time.
  - [ ] `docs/v2-verification.md` exists in the sdd-plugin project before any v2 implementation sprint begins, seeded with one row per in-scope feedback item from the v2 scope's triaged list.
  - [ ] When a sprint item implementing an in-scope feedback item completes, the implementing command (typically `/sdd:build` during wrap-up) writes or updates a row in `docs/v2-verification.md` for that feedback item.
  - [ ] Each row in `docs/v2-verification.md` maps to exactly one in-scope feedback item from the v2 scope's triaged list.
  - [ ] The exact schema and column set of `docs/v2-verification.md` is deferred to `/sdd:spec`.

- As the plugin maintainer, I want to check off verification rows manually after end-to-end exercise, so the file reflects actual verification rather than checklist theater.
  - [ ] Each row in `docs/v2-verification.md` includes a manually-editable checkable status field.
  - [ ] No SDD command writes a "verified" status into a row in `docs/v2-verification.md`.
  - [ ] No SDD command gates v2 release on the state of `docs/v2-verification.md`.

- As the plugin maintainer, I want the v2 verification approach documented, so the dogfood plan is captured in the project rather than living only in my head.
  - [ ] The sdd-plugin project contains documentation of the v2 verification approach naming the primary dogfood (Jason runs v2 end-to-end on a small website project covering `/sdd:discovery` through `/sdd:retro`).
  - [ ] The documentation names supplementary projects used to cover items that won't fire on the primary dogfood (e.g., cross-machine pause, 10-question cap, per-round `/clear`-and-continue, cross-project pattern capture, longer deepening rounds).
  - [ ] The documentation names `/sdd:pause` and `/sdd:unpause` as items to deliberately exercise even when not strictly needed.
  - [ ] The documentation states that "verified end-to-end at least once" satisfies Pillar 1 of the scope's "done" definition.

- As the plugin maintainer, I want `docs/v2-verification.md` to be sdd-plugin-specific and to persist after release, so it serves as a historical record rather than churning out of user projects.
  - [ ] `docs/v2-verification.md` is created only in the sdd-plugin project; no SDD command creates the file in any other project.
  - [ ] The implementing-command write behavior described above fires only when `docs/v2-verification.md` already exists in the running project's `docs/` directory.
  - [ ] After v2 ships, no SDD command deletes, archives, or removes `docs/v2-verification.md`.
  - [ ] Future plugin versions (v3 and beyond) follow the same out-of-pattern allowance: `docs/<version>-verification.md` is sdd-plugin-specific and persists.

## Unvetted

<!-- Items added during sprints that have not yet been refined. /sdd:refine processes these before future sprints. Do not remove this section. -->

## What We're Building

v2 of the SDD plugin — a streamlining pass on v1 driven by real-use feedback from three projects (Event Planning App, fiver-gigs, sdd-plugin self-use). The v2 command chain is `/sdd:onboard → /sdd:discovery → /sdd:scope → /sdd:prd → /sdd:spec → /sdd:plan → /sdd:build → /sdd:polish → /sdd:refine → /sdd:retro`, with `/sdd:pause`, `/sdd:unpause`, and `/sdd:feedback` available anytime.

Substantively v2 adds a genuinely new `/sdd:discovery` command at the start of the planning chain (exploration with file uploads, which v1 implicitly mixed into `/sdd:scope`); a `/sdd:pause` and `/sdd:unpause` utility pair for multi-session resume; a set of cross-cutting interview-command behaviors that apply consistently across all seven interview commands (clarifying questions never advance the interview, 5-suggested-10-hard-cap questions per round, definite end-of-round recommendations with topic lists, right-size detection that propagates from `/sdd:discovery` downstream, active prompting for additional user input); a three-tier between-rounds context-management recommendation model (continue / `/compact` / `/clear`); a default-`/clear` handoff between commands; per-phase process-notes files; automatic backlog generation gated on user confirmation; a `/sdd:retro` that fires only at project close and captures cross-project patterns to the user profile; cross-project feedback transfer via a `feedbackLocalPath` profile field; and a `docs/v2-verification.md` audit-trail artifact in the sdd-plugin project for tracking that every in-scope feedback item shipped and was verified end-to-end.

Mechanically v2 also fixes two v1 bugs (`/sdd:feedback` clobbering `lastCommand`, and PRD state-tracking missing checkoff for non-split sprint items), absorbs `/sdd:reflect`'s responsibilities into `/sdd:build`'s wrap-up and `/sdd:retro`'s project-close step, renames `/sdd:sprint` → `/sdd:plan` and `/sdd:iterate` → `/sdd:polish`, narrows `/sdd:scope` to boundary-drawing only, and ships a migration guide (`MIGRATION-v1-to-v2.md`) covering the rename pairs, a state-aware migration table keyed on v1 `lastCommand`, and resume-file rename rules.

## What We'd Add With More Time

Deferred items: see `docs/backlog.md`.

## Non-Goals

- New SDD capabilities beyond the refinement scope. v2 is a streamlining pass; new features that don't directly resolve v1 feedback friction are out.
- Feedback share-back from user projects to the plugin's upstream GitHub repo (deferred to v3).
- Recommending auto-update of the SDD marketplace in `/sdd:onboard` (informational paragraph only; promotion to recommendation deferred to v3 when share-back closes the loop).
- Cross-project pattern capture in the middle of a project. Pattern capture fires only at `/sdd:retro` (project close), not at sprint boundaries.
- Within-phase compaction of process-notes files. Files are append-only within a phase; `/sdd:pause` covers in-flight summarization needs.
- Programmatic invocation of `/compact` or `/clear` by SDD commands. The plugin recommends; the user runs.
- Fabrication of iteration candidates by `/sdd:build` at wrap-up. Candidates are surfaced only when an explicit marker exists in `process-notes-sprint-N.md` written before wrap-up began.
- Hard internal gating of `/sdd:retro` on PRD completion state. Recommendation-timing is enforced; running `/sdd:retro` on an incomplete project remains possible (e.g., for paused or abandoned projects).
