---
name: refine
description: Walks the inline markers in docs/plan.md one at a time — assumptions, gaps, concerns — resolving each with the user in place, then finalizes the plan by bumping its version from 0.x to 1.0.
disable-model-invocation: true
---

# /sdd:refine

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, guard rails, and feasibility/viability pushback throughout.

This is the v6 `/sdd:refine` — a clean-break rewrite. No behavior from the old mid-chain refine (unvetted-item processing, living-documents update ordering, loop-to-plan) carries over.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/references/markers.md` (the marker parser contract — this command's core mechanism)
3. `skills/sdd-guide/references/deepening-rounds.md` (interview mechanics for discussion within the walk)
4. `skills/sdd-guide/references/right-sizing.md` (refine re-evaluates `smallProject`)
5. `docs/plan.md` (in full — it is the working surface)

## State Updates (immediate)

Update `lastCommand` in `docs/project-state.json` to `"/sdd:refine"` before any other work, per the state-tracking rule in sdd-guide.

## Prerequisites

`docs/plan.md` must exist. If it does not, stop immediately and output: "Run `/sdd:discovery` first." Do not proceed.

## smallProject Re-Evaluation (startup)

Per `skills/sdd-guide/references/right-sizing.md`, re-evaluate `smallProject` on startup: read the current value, apply the reference's signals against `plan.md`'s content, and flip it only on contradictory or first-surfacing evidence. A confirming re-evaluation is a no-op; a flip is written to `docs/project-state.json` and logged in this command's process notes file.

## Step 1: Marker Walk

Scan `docs/plan.md` for markers using the scan regex from `references/markers.md` and walk them **one at a time, in document order**.

For each marker:

1. **Present it** — quote the marker, its surrounding context (enough of the sentence or section that the user doesn't need to open the file), and what kind of decision it needs. One marker per message; never batch.
2. **Discuss and resolve** — the user settles it (confirms the assumption, fills the gap, rules on the concern). Interview rules apply: free-form, one question at a time, clarifying questions don't advance the walk.
3. **Apply the resolution in place** — replace the marked span with the settled content and **strip the marker, leaving no breadcrumb**. The resolved text reads as if it had always been decided; no "(resolved)" annotations, no changelog residue.

**Re-scan loop.** After each resolution, re-scan the document and continue with the next marker in document order. Repeat until no markers remain except those explicitly left tagged (Step 2). A marker added mid-loop — by an edit during discussion, or a `[GAP]` a prototype run wrote earlier — is caught on a later re-scan; nothing is walked from a stale list. Enumeration is always by fresh scan, never from a remembered roll-up.

**Never auto-remove.** The AI never strips a marker on its own initiative — not for staleness, not because it seems settled by a nearby resolution, not as cleanup. A marker is removed only through explicit user resolution of that marker. No silent closures.

## Step 2: Carry-Forward

Not every marker resolves during refine. When one can't be settled — the user doesn't know yet, the decision depends on something downstream, the discussion stalls without a ruling — **leave it tagged in the document**. It carries forward to `/sdd:prototype` (and beyond) as-is; unresolved markers are a normal end state, not a failure. Nothing is force-decided.

The AI may append a **proposed solution** to an unresolved marker using the `PROPOSED` form from `references/markers.md`:

```
[GAP g2: the plan doesn't say how sessions expire — PROPOSED: default to a 30-day idle window]
```

The proposal rides inside the marker; the marker stays in place. Appending a proposal is never resolution — only the user's explicit decision resolves and strips. When the user later accepts a proposal, that acceptance is the explicit resolution: apply it in place and strip per Step 1's rules.

Before finalizing, tell the user which markers remain tagged so carrying them forward is a choice, not an accident.

## Step 3: Finalize

When the walk ends (no markers remain, or the remaining ones are deliberate carry-forwards), finalize the plan:

- Bump the version major: **`0.x → 1.0`**, edited in the same `**Version:**` line of the same file. If the document is already at major `1` (a re-run), bump the minor instead (`1.0 → 1.1`).
- **No separate "final" file is created** — no `plan-final.md`, no copy, no rename. `docs/plan.md` updated in place is the finalized plan; the version number is the single source of truth for maturity.

Confirm the bump to the user in one line (e.g. "Finalized: `docs/plan.md` is now `1.0`; 2 markers carry forward"), then proceed to the handoff.

## Process Notes

Per sdd-guide's `## Process Notes` section, append to `process-notes-refine.md` at the project root throughout the walk — real-time entries capturing decisions (each marker's resolution and rationale), pushback, difficult questions, and pivots. Create the file on first append. A `smallProject` flip from the startup re-evaluation is logged here.

## End-of-Command Handoff

Runs after finalize completes. Emit the handoff per the canonical template in `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff`. The `[next-command]` slot is `/sdd:prototype`; the handoff may note that `/sdd:validate` is available first (optional — the chain proceeds either way). Outcome-summary line: `Plan finalized at 1.0.` (adjust the version to what was written). The handoff fires unconditionally at completion.
