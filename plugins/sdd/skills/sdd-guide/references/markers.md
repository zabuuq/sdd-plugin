# Markers

**Used by:** `/sdd:discovery` (writes), `/sdd:refine` (resolves), `/sdd:validate` (writes and resolves), `/sdd:prototype` (writes).

This is the single parser contract for the inline markers carried in `docs/plan.md`. Every command that writes or resolves markers follows this file. Do not copy the formats into a command SKILL — say "emit/scan markers per `references/markers.md`" and follow this contract.

## Marker syntax

Three types, inline and in place, machine-findable by a start-of-token scan:

- `[ASSUMPTION a3: <text>]` — a choice the AI made that the user has not confirmed.
- `[GAP g2: <text>]` — something the AI could not resolve and needs input on.
- `[CONCERN c1: <text>]` — a feasibility/viability pushback, kept visible so it survives a clear/compact.

**Optional proposed solution.** The AI may append a proposal inside an unresolved marker, worded so it reads "here's the gap, and here's what I'd do about it," without removing the marker:

```
[GAP g2: the plan doesn't say how sessions expire — PROPOSED: default to a 30-day idle window]
```

Appending a `PROPOSED` clause never counts as resolution. The marker stays until the user resolves it.

## Marker IDs (stateless, round-local)

- A new marker's ID = (max **live** ID of that type currently in the document) + 1, or `1` if none of that type are live.
- IDs are **recycled** across rounds: if a resolution round clears every marker of a type, the next one starts at `1` again. The ID's only job is to find/reference a marker during a resolution pass — it is **not** a permanent citation (contrast AC IDs, which are stable and never recycled).
- A marker that persists (carried-forward, unresolved) keeps its number as long as it stays live; only a type with zero live markers restarts.
- IDs are stable **within** a single resolution walk — no mid-walk renumbering.
- No persisted roll-up list of markers exists anywhere. Enumeration is always by fresh scan — a separate list is a drift trap and a context-loss vector.

## Resolve-loop rules

- Scan regex: `\[(ASSUMPTION|GAP|CONCERN) ([agc]\d+):` — iterate matches in document order.
- Resolve first → replace the marked span in place with settled content → strip the marker, leaving no breadcrumb → re-scan → repeat.
- A marker added mid-loop (including a `prototype` `[GAP]`) is caught on a later re-scan.
- **Hard constraint:** the AI never strips a marker on its own; a marker is removed only through explicit user resolution. No silent closures.
- **Nothing is force-decided at any level, including prototype.** An item that genuinely can't be settled stays tagged and carries forward indefinitely. The AI may append a `PROPOSED` solution but must keep the marker.

## Writers and resolvers

| Command | Role |
|---|---|
| `discovery` | Writer — tags assumptions, gaps, and concerns during auto-draft |
| `refine` | Resolver — the marker-walk scan loop |
| `validate` | Resolver (inside its reconciliation walk) and writer (carry-forward markers out of reconciliation) |
| `prototype` | Writer — `[GAP]` on a page that fails its checker after the bounded retry |

`validate`'s self-critique does **not** write markers into `plan.md` — it writes a validation file into `docs/validation/`. Markers only (re)enter `plan.md` as the carry-forward mechanism for unresolved reconciliation items.
