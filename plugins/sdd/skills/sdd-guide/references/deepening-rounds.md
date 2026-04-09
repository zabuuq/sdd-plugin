# Deepening Rounds

**Used by:** `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:sprint`

This reference defines the two-phase interview pattern used by planning and sprint-planning commands.

## Phase 1 — Mandatory Questions

Each command defines a set of mandatory questions — the minimum needed to produce a meaningful document. These are the foundation; without them the output would be incomplete or based on assumptions.

**Rules for Phase 1:**
- Ask questions one at a time. Wait for the user's response before asking the next.
- All questions are open-ended. Never present multiple-choice options.
- Stay adaptive. If the user's answer to one question naturally covers a later question, skip it. If their response opens an important thread not in the script, follow it.
- The goal is a complete understanding, not a completed question list.

## Phase 2 — Deepening Rounds

After all mandatory questions have been answered (or covered), transition to the deepening phase.

**Transition prompt:** Offer the user a clear choice:

> "I've got enough to generate your [document name]. Want another round to sharpen things, or ready to proceed?"

If the user chooses to proceed, generate the document. If they want another round, run a deepening round.

**Each deepening round:**
- Generate 4-5 targeted questions based on everything discussed so far.
- Questions should target:
  - **Edge cases** — scenarios the mandatory answers didn't address.
  - **Ambiguities** — places where the user's intent could be read multiple ways.
  - **Thin areas** — topics the mandatory answers touched but didn't develop.
  - **Hidden assumptions** — things the user might not realize they're assuming.
  - **Unexplored angles** — perspectives the mandatory questions didn't cover (technical constraints, user experience implications, failure modes, scale concerns).
- Push for refinement and polish, not just coverage.
- Ask these questions one at a time, same rules as Phase 1.

**After each deepening round:** Offer the same choice again. The user can do as many rounds as they want. There is no cap.

**Process notes:** Log the number of deepening rounds completed and what each round surfaced. This goes into the appropriate process notes file (see sdd-guide core for which file).
