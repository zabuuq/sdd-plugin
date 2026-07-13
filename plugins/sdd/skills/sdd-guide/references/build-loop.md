# Build-Loop Skeleton

**Used by:** `/sdd:build` (with git machinery), `/sdd:prototype` (without git machinery).

The vendored loop skeleton, owned by the plugin — not the personal `~/.claude/skills/build-loop`. Both consuming commands load this file and apply their specialization column. The skeleton is the shared contract; the specializations are the only permitted divergence.

## Skeleton

Every item (a GitHub issue for `build`, a page for `prototype`) runs the same five-stage loop:

1. **Plan** — read the item's source material (`plan.md` section, issue body, navigation path) and lay out the concrete steps before touching anything.
2. **Make** — execute the plan.
3. **Separate checker** — a distinct agent (never the maker) verifies the result against the item's acceptance criteria or checker rules.
4. **Bounded retry (≤2)** — on checker failure, the maker gets at most two retry rounds against the checker's findings. Past two, the item is failed — what happens next is specialization-specific.
5. **Compound** — capture lessons: anything non-obvious learned during the item is written to `docs/learnings/` before moving on.

## Specializations

| | `build` (durable) | `prototype` (disposable) |
|---|---|---|
| Isolation | git worktree per item; agents fan out across issues in parallel | none — pages build in place under `prototype/` |
| Output | one branch + one PR per issue | pages in `prototype/` |
| On twice-failed item | **stop and surface** to the maintainer — no guessing past a failure | write a `[GAP: …]` marker into `plan.md` (per `references/markers.md`) and **continue** with the remaining pages |
| PR/branch stamping | `sdd` label on the PR + branch prefix `sdd/<issue>-<slug>`, so `/sdd:resolve-pr` recognizes its own | n/a |

## Shared rules

- **The checker is never the maker.** The separate-checker stage is a different agent with fresh context; a maker self-review does not count.
- **No decisions mid-loop.** An under-specified item is surfaced, not guessed. Required decisions are expected to already live in `plan.md` (and, for `build`, in the issue).
- **Never auto-merge.** For `build`, human PR review is the only gate; the loop opens PRs and stops there.
