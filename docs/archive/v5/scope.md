# Archive Refs Selection (SDD v5) — Scope

## Idea

Extend the `/sdd:archive` command so it handles the `docs/refs/` reference directory, which it currently ignores entirely. Today `docs/refs/` is neither on the archive sweep allow-list nor part of the carry-forward trio, so it silently falls into "left in place" and is never read or moved.

The change: when `/sdd:archive` runs and `docs/refs/` has contents, the command detects it, presents a **numbered list** of the items, and **asks** the user which items to include in the archive. The user replies with the numbers they want to keep (or checks them off). Selected items are swept into the archive; unselected items stay live for the next cycle.

## Who It's For

Anyone using the SDD plugin. This is internal dev tooling for the plugin itself, dogfooded against this repository. Solo maintainer work.

## Inspiration & References

- The existing `/sdd:archive` **plan gate**, specifically its "Left in place" surfacing. The new refs prompt extends that same "show the user what's happening to each file" model, but adds a per-item include/skip decision rather than a passive report.

## Goals

- `/sdd:archive` no longer silently ignores `docs/refs/`.
- When refs has contents, the user gets a per-item choice over what is archived, driven by a simple numbered list.
- The selection mechanism stays simple — a numbered list and a numeric reply, no elaborate UI or configuration.
- No regression to any existing archive behavior (sweep allow-list, trio snapshot, `INDEX.md`, reset, git steps) when refs is empty or absent.

## What "Done" Looks Like

When `/sdd:archive` runs:

- **`docs/refs/` has contents:** the command presents a numbered list of the items and asks which to include. The user selects by number. **Selected items** are swept (moved) into `docs/archive/v{N}/refs/` alongside the cycle's other frozen artifacts. **Unselected items** are left live in `docs/refs/` for the next cycle — nothing else happens to them (no flagging, deletion, or reporting).
- **`docs/refs/` is empty or absent:** nothing happens. `/sdd:archive` behaves exactly as it does today — no prompt, no note.
- Everything else about `/sdd:archive` is unchanged.

## What's Explicitly Cut

Deferred items: see `docs/backlog.md`.

## Loose Implementation Notes

These are early hunches, not commitments — to be settled in `/sdd:prd` and `/sdd:spec`:

- Reply parsing format (e.g. `1,3,5`, ranges, `all`/`none`) is a spec-level detail.
- Behavior for nested subdirectories inside `docs/refs/` is unspecified at scope level.
- Where the refs prompt sits relative to the existing single-confirmation plan gate (one combined gate vs. a separate prompt) is a spec decision.
- Whether the generated `INDEX.md` should enumerate the swept refs is open.
- The edge case where refs is the *only* thing present (interaction with the existing "nothing to archive" exit) needs a defined answer.
