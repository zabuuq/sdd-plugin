# Backlog

**Used by:** `/sdd:discovery`, `/sdd:refine`, `/sdd:validate`

Canonical home for every "how" detail of the backlog mechanism. The backlog is an append-only deferral log written during planning. If a writing command needs to add an entry, route through this file's `## Write trigger` and use this file's `## Entry format`; do not restate the prompt text or the entry schema inline in command SKILL files.

## Purpose

`docs/backlog.md` records items that surfaced during planning and that the user explicitly chose to defer rather than fold into the current project's scope, PRD, spec, or sprint. It is the long-lived holding place for ideas that are worth remembering but not worth doing now. The file is written only by the five planning commands listed above and is **never edited by `/sdd:refine`** — see `references/living-documents.md > ## Exclusions from the Living-Documents Chain`. Entries are append-only across the life of the project; no command in the SDD chain modifies, reorders, or removes existing entries.

## Entry format

Each entry in `docs/backlog.md` uses the schema below. The 6 fields appear in fixed order. Each entry is preceded by a `### [Item title]` heading whose title text matches the `Title` field. Entries are separated by `---` on its own line.

```markdown
### [Item title]
- **Title:** Same as the heading.
- **What:** Description of the item.
- **Why deferred:** Reason it's not in the current project's scope.
- **Trigger to revisit:** What event or condition should bring this back into consideration.
- **Dependencies:** What it depends on (other backlog items, future work, external factors).
- **Source:** /sdd:[command] — [phase/round identifier] — [topic]

---
```

**Format conventions:**

- Fields in the exact order shown — Title, What, Why deferred, Trigger to revisit, Dependencies, Source. Do not reorder.
- The `### [Item title]` heading immediately precedes the entry; the heading text matches the `Title` field verbatim.
- Field bullets use the literal markers `- **Title:**`, `- **What:**`, `- **Why deferred:**`, `- **Trigger to revisit:**`, `- **Dependencies:**`, `- **Source:**` — these are the parser anchors.
- Entries are separated by `---` on its own line.

## Source field format

The `Source` field records where the deferral originated. Format:

```
/sdd:[command] — [phase/round identifier] — [topic]
```

`[command]` is the planning command that wrote the entry. `[phase/round identifier]` names the structural moment in that command's flow (e.g., Phase 1 beat, deepening Round N, a specific spec phase). `[topic]` is a short prose handle on the thread or subject the deferral arose from.

Examples (verbatim):

- `Source: /sdd:discovery — pre-draft interview, question 3 — local tech group as contributors vs consumers`
- `Source: /sdd:refine — marker walk, [GAP g2] — story-split threshold edge cases`
- `Source: /sdd:validate — reconciliation, difference 4 — grouping requirements by epic`

## Write trigger

A backlog entry is only ever written via an explicit user-facing confirmation prompt. There is no implicit or silent path that writes to `docs/backlog.md`.

When a planning command surfaces an item the user signals they want to push off — out of scope for the current project, out of scope for the current PRD round, deferred rather than folded into `docs/plan.md`, etc. — the command emits a prompt that explicitly distinguishes the two outcomes:

- **Defer to backlog** — write an entry to `docs/backlog.md` using the `## Entry format` above.
- **Drop** — write no entry. Record the drop decision (item summary and the fact that the user chose to drop rather than defer) in the current phase's process-notes file only.

The prompt fires every time such an item surfaces. The user's answer gates the write:

- An affirmative "defer to backlog" answer (or recognizable equivalent — "defer," "yes defer," "add to backlog," etc.) produces a backlog entry.
- Any other answer — "drop," "no," silence, "skip it," "forget it," or anything not affirmatively choosing the defer outcome — writes no entry to `docs/backlog.md`. The drop decision is recorded only in the phase's process-notes file.

Recommended prompt shape (paraphrasable, not a fixed string):

> This isn't going into the current plan. Defer to backlog (write an entry to `docs/backlog.md`) or drop (record the decision in process notes only)?

The prompt names both options explicitly. Do not collapse it into a single yes/no question.

## Writers

The following commands write to `docs/backlog.md` via the write trigger above:

- `/sdd:discovery`
- `/sdd:refine`
- `/sdd:validate`

Every other SDD command is a **non-writer**: `/sdd:onboard`, `/sdd:prototype`, `/sdd:build`, `/sdd:retro`, `/sdd:checkpoint`, `/sdd:resolve-pr`, `/sdd:pause`, `/sdd:unpause`, `/sdd:feedback`, `/sdd:archive`. Build agents in particular never act on backlog entries (see `skills/build/SKILL.md`). The backlog is append-only during planning and is never reshaped by post-planning commands.

## Append order and modification rule

New entries are appended to the end of `docs/backlog.md` in the order they are written. Subsequent commands do not modify, reorder, or remove existing entries. An entry, once written, stays in place verbatim until the user (outside the SDD chain) chooses to edit or remove it manually.

This append-only stance is what lets the file act as a long-lived deferral log across many planning cycles without entries silently shifting or vanishing.

## Bootstrap

If `docs/backlog.md` does not exist when the first deferral fires, the writing command creates it. The created file begins with a one-line HTML-style marker comment naming the file's purpose, followed by a blank line, then the first entry:

```markdown
<!-- docs/backlog.md — append-only deferral log written by SDD planning commands. See plugins/sdd/skills/sdd-guide/references/backlog.md. -->

### [First item title]
- **Title:** ...
```

The exact wording of the marker comment is flexible; what matters is that the file opens with an HTML comment naming the file's purpose so a future reader (human or agent) lands on the right context immediately.

## Parser note

Backlog entries are detected by an `^### ` heading scan against `docs/backlog.md`. "The file has at least one entry" means at least one line in the file matches the regex `^### ` (start-of-line heading at the level-3 depth). The pointer-mechanism in `docs/plan.md` (its `## Non-Goals` section includes a pointer to `docs/backlog.md` in lieu of an inline deferred-items list when this check passes) uses exactly this rule.
