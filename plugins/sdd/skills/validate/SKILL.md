---
name: validate
description: Optional validation pass on docs/plan.md. Offers an adversarial self-critique, gathers validation files from docs/validation/, and reconciles the differences with the user one at a time, bumping the plan to 2.x.
disable-model-invocation: true
---

# /sdd:validate

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, guard rails, and feasibility/viability pushback throughout.

## Optional, User-Initiated

`/sdd:validate` is **optional**. The chain proceeds `refine → prototype` whether or not this command ever runs; nothing downstream requires a `2.x` plan, and no command recommends validate as a required step. It runs only when the user invokes it — typically after `/sdd:refine` finalizes the plan, when the maintainer wants outside eyes (their own adversarial pass, another AI's, or a person's) on `plan.md` before prototyping.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `skills/sdd-guide/references/markers.md` (carry-forward markers out of reconciliation)
3. `skills/sdd-guide/references/deepening-rounds.md` (interview mechanics for the reconciliation walk)
4. `skills/sdd-guide/references/right-sizing.md` (validate re-evaluates `smallProject`)
5. `docs/plan.md` (in full — the reconciliation target)
6. `docs/validation/` (every file, at Beat 2)

## State Updates (immediate)

Update `lastCommand` in `docs/project-state.json` to `"/sdd:validate"` before any other work, per the state-tracking rule in sdd-guide.

## Prerequisites

`docs/plan.md` must exist. If it does not, stop immediately and output: "Run `/sdd:discovery` first." Do not proceed.

Validate works best against a finalized plan (major `≥ 1`). If the plan is still at major `0`, say so and recommend finishing `/sdd:refine` first — but do not block; the user may have a reason.

## smallProject Re-Evaluation (startup)

Per `skills/sdd-guide/references/right-sizing.md`, re-evaluate `smallProject` on startup; flip only on contradictory or first-surfacing evidence, and log a flip in this command's process notes.

## Behavior

Validate runs three beats in order.

### Beat 1: Offer Self-Critique (ask-first)

Offer to run Claude's own adversarial self-critique of `plan.md` — **ask first, never auto-run**. The maintainer may already have enough external sources and not want another Claude pass, but still needs this command to pull them in.

- **On yes:** run the critique — read `plan.md` adversarially (feasibility, internal contradictions, unstated assumptions, missing requirements, architecture risks) — and write the findings as a **validation file into `docs/validation/`** (e.g. `docs/validation/self-critique.md`), uniform with external sources. It gets no special treatment in Beat 3; it is just another file in the folder.
- **On no:** skip straight to Beat 2. No critique runs, nothing is written.

The self-critique does **not** write markers into `plan.md` — its output is the validation file only. Markers enter `plan.md` solely as Beat 3 carry-forwards.

### Beat 2: Gather Validation Files

Scan `docs/validation/`. **Any file present is a validation file** — no naming convention, no manifest, no required format. The folder is a source-agnostic drop-zone: a Claude self-critique, another AI's review, or a person's notes are all treated identically.

Handle both shapes:

- **A full modified copy of the document** — the producer edited a copy of `plan.md`. Extract the proposed changes by diffing it against the live `docs/plan.md`; each difference becomes a reconciliation item.
- **A suggestion list** — free-form findings or recommendations. Each distinct suggestion becomes a reconciliation item.

If `docs/validation/` is missing or empty and the self-critique was declined, there is nothing to reconcile: say so and exit without touching `plan.md` (no version bump, no handoff-worthy change — emit a one-line "nothing to validate against" close instead of the standard handoff).

### Beat 3: Reconciliation Walk

Present the differences between the validations and `plan.md` **one at a time**, in a stable order (by source file, then document order). For each difference:

1. State what the validation says, what `plan.md` says, and where they diverge.
2. Accept a **keep / remove / change** decision: **keep** the plan as-is (reject the suggestion), **remove** what the suggestion says to drop, or **change** the plan per the suggestion (or the user's own variant).
3. Apply the decision to `docs/plan.md` **in place** before moving to the next difference.

One difference per message; interview rules apply (free-form, clarifying questions don't advance the walk).

**Carried-over markers** still live in `plan.md` are walked in this same pass, as reconciliation items in document order, under the resolve rules of `references/markers.md`.

**Nothing is force-decided.** A difference the user can't settle becomes a carry-forward marker in `plan.md` (`[GAP]` or `[ASSUMPTION]` per `markers.md`) rather than a silent drop or a forced ruling.

**Version bump.** After the walk, bump `plan.md` to major `2`: `1.x → 2.0` in place; if already at major `2` (a re-run), bump the minor (`2.0 → 2.1`). Confirm the bump and any carry-forward markers to the user in one line, then proceed to the handoff.

## Process Notes

Per sdd-guide's `## Process Notes` section, append to `process-notes-validate.md` at the project root throughout the run — real-time entries for decisions (each difference's ruling and rationale), pushback, difficult questions, and pivots. Create the file on first append.

## End-of-Command Handoff

Runs after the reconciliation completes. Emit the handoff per the canonical template in `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff`. The `[next-command]` slot is `/sdd:prototype`. Outcome-summary line: `Plan validated at 2.0.` (adjust to the version written). The handoff fires unconditionally at completion.
