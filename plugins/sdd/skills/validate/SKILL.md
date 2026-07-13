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

Validate runs three beats in order (defined below).

## Process Notes

Per sdd-guide's `## Process Notes` section, append to `process-notes-validate.md` at the project root throughout the run — real-time entries for decisions (each difference's ruling and rationale), pushback, difficult questions, and pivots. Create the file on first append.

## End-of-Command Handoff

Runs after the reconciliation completes. Emit the handoff per the canonical template in `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff`. The `[next-command]` slot is `/sdd:prototype`. Outcome-summary line: `Plan validated at 2.0.` (adjust to the version written). The handoff fires unconditionally at completion.
