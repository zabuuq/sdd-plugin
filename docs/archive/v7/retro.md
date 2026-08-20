# Project Retrospective

- **Project:** sdd-plugin — SDD 4.1 flow-tweaks pass (cycle 7)
- **Date of retro:** 2026-08-04

## Lessons

| Lesson | Promoted? | Routed to |
|---|---|---|
| Check `origin/<branch>`, not local `main`, before deciding a PR base — local refs can be arbitrarily stale. | yes | `~/.claude/sdd-cross-project-patterns.md` (global) |
| Partition build issues by file, not by epic, when the loop never merges mid-build — each file owned by exactly one issue. | yes | `/sdd:feedback` → `docs/sdd-feedback.md` (plugin-level) |

Neither lesson's substance already existed in global config, so both were clean to promote (no re-promotion guard triggered). Both stay permanently in `docs/learnings/`.

## Timing

- **Dump-to-ship:** about 3 sessions — discovery brain-dump (2026-07-15) → refine (2026-07-23) → build and ship (2026-08-04).
- Prototype was intentionally skipped this cycle (it's optional, and a text-tweak pass to a markdown-only plugin has nothing to prototype), so there's no dump-to-prototype figure. The north-star dump→prototype-in-a-day metric doesn't apply to a build-only tweak cycle.

## Issue Queue

- **Issues:** 4 created (#18–#21), all closed.
- **PRs:** 4 opened (#22–#25), all merged. Zero awaiting review.
- **Blocked/surfaced:** none. Every checker passed first pass, zero retries. File-disjoint partition held — no cross-PR conflicts.
- **Open items:** none in the queue. Two pre-existing, out-of-scope items were surfaced (not fixed) in PR #25 for a future pass: archive's cycle-reset handoff still emits `/clear`; `AGENTS.md` and `living-documents.md` carry stale v5 sprint/PRD checkbox formats.
