# Process Notes — Scope (v5 cycle)

Real-time, append-only. Four categories: decisions, pushback, difficult questions, pivots.

## Decisions

- **Cycle idea:** extend `/sdd:archive` to handle `docs/refs/`, which it currently ignores entirely (not on the sweep allow-list, not part of the carry-forward trio — it falls into "left in place" and is never read).
- **Refs disposition = per-run, per-item selection.** When `/sdd:archive` runs and `docs/refs/` has contents, it presents a numbered list of the items and asks the user which to include. Chosen over (a) always-sweep into `archive/v{N}/refs/`, (b) snapshot-like-the-trio, (c) whole-directory yes/no.
- **Selection UX:** numbered list; user replies with the numbers to keep (or checks them off). Explicitly kept simple at user's request.
- **Selected items** → swept (moved) into `docs/archive/v{N}/refs/`. **Unselected items** → left live in `docs/refs/` for the next cycle. No flagging/deletion/reporting of unselected items.
- **Empty/absent `docs/refs/`** → nothing happens; archive behaves exactly as it does today (no prompt, no note).
- **Audience:** anyone using the SDD plugin. **Solo** maintainer work. Dogfooded against this repo.
- **smallProject = true.** Signals: one primary feature, single markdown SKILL file, no new integrations (git/gh already present), no refs uploads, concise framing, explicit "keep it simple." None strongly contradicted.

## Pushback

- (none yet)

## Difficult questions

- Initial ambiguity in the user's phrasing: "check any reference directory and what items should be included" — resolved as a typo for "...and **ask** what items should be included." This flipped the design from a whole-directory sweep/skip to per-item selection.

## Pivots

- (none)

## Wrap-up

- **Deepening rounds completed:** 0. Recommended closure at the Phase 1 → Phase 2 transition (small project, feature fully pinned, user said "keep it simple"); user accepted.
- **Project split:** no — single-command enhancement, one sprint.
- **Title:** "Archive Refs Selection (SDD v5)" — confirmed by user.
- **Open concerns logged:** 2 (refs prompt placement vs. plan gate → /sdd:spec; refs-only "nothing to archive" interaction → /sdd:prd).
- No new backlog deferrals this session.
