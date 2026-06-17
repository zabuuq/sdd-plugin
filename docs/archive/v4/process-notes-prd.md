# Process Notes — /sdd:prd (v4 cycle, /sdd:archive)

## Decisions

- **Confirmation gate.** `/sdd:archive` shows a plan (files to move, branch name, PR) and gets one user confirmation before any file move or git operation. Rationale: the sweep is destructive and hard to reverse; the "no precondition guard" framing in scope governs *when* it can run, not *whether* it pauses for confirmation. The two compose — runnable anytime, but confirms before mutating.
- **Decline default.** A "no" at the confirmation gate aborts cleanly — nothing moved, no branch, no PR. Stated as default, not interviewed.
- **Archive-dir collision → next free number.** Target version defaults to the cycle number in `project-state.json`; if `docs/archive/v{N}/` already exists, archive advances to the next free `v{N}` and uses that (rather than refusing, merging, or overwriting). Observable behavior; cycle-number *ownership/increment* remains a spec open concern.
- **INDEX.md auto-generated.** Archive composes `INDEX.md` automatically from observable data (cycle number, swept artifact list, dates, last command) — no prompt for a user-authored description. Keeps the command to a single interaction (the confirmation gate).
- **Git-failure recovery prompt.** When the file archive succeeds but a git step fails (repo present but `gh` missing, push rejected, branch exists, no remote), archive reports the specific failure and asks the user for confirmation on how to proceed — it does NOT silently treat git as best-effort, nor hard-fail the whole command. The file archive already stands at that point; the prompt is a recovery decision (retry / finish without git / etc.). This is a conditional second interaction, fired only on git failure. Partially informs the spec "git step shape" open concern (observable behavior now fixed; branch-naming and PR-confirm mechanics still spec-owned).
- **Empty-state detection.** When invoked with nothing to archive (only the live carry-forward trio present, no cycle artifacts), archive detects this and reports it without making changes — no empty `v{N}/` dir, no branch, no PR.

## Deepening rounds

- Phase 1 walkthrough completed in 5 crisp directive answers (confirmation gate, collision→next-free-number, auto INDEX.md, git-failure recovery prompt, empty-state detection). Thinness signal fired (short answers + remaining threads are all spec-owned "how"); smallProject=true → recommended close after Phase 1.
- 0 deepening rounds run. User accepted the close recommendation and elected to generate.
- Context recommendation: continue (light session, decisions captured here). No /compact or /clear.

## Phase-splitting

- 6 epics (Run Control & Safety, Artifact Sweep, Living-Doc Preservation, Archive Index, Directory Reset, Git Integration). Below the 10-epic phase-split threshold → no recommendation fired. Single PRD.

## Generation

- docs/prd.md written. Pre-write AC quality check passed on all ACs as drafted (specific/observable/verifiable); notable: living-doc snapshot AC pinned to "byte-identical at archive time, before reset" to make it verifiable; directory-reset ACs written as observable downstream behavior (fresh /sdd:scope, valid JSON) rather than pinning the exact field-reset map, which is a spec open concern.
