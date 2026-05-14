# Deepening Rounds

**Used by:** `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:refine`

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

**Transition into Phase 2.** Once Phase 1 has finished, the agent emits the structured end-of-round recommendation (defined below under **End-of-round recommendation**) before running any deepening rounds and before generating the document. The recommendation fires at every round-decision point — including the Phase 1 → Phase 2 transition, even though no deepening round has run yet. A bare prompt ("want another round?" / "ready to proceed?") is **not allowed** at this transition; the agent takes a position with reasoning, exactly as it would at end-of-round.

If the user accepts the close-recommendation, the agent generates the document. If the user accepts (or overrides to) the continue-recommendation, the agent runs a deepening round. The user can elect as many rounds as they want — the structured recommendation fires before every one.

**Each deepening round:**
- Compose **5 questions by default**, based on everything discussed so far.
- A round **may run up to 10 questions** without further explanation. The agent extends from the default 5 to as many as 10 only when the material genuinely warrants it (a thread opened mid-round, an answer surfaced a new ambiguity, etc.).
- **Past 10 questions, stop.** Before posing the eleventh question in a round, the agent must emit a stated reason and obtain explicit user permission to continue. Example: "We've covered ten questions this round and [specific area] still feels unresolved because [reason]. Want me to keep going, or close the round here?" Do not pose question eleven until the user has said yes.
- Questions should target:
  - **Edge cases** — scenarios the mandatory answers didn't address.
  - **Ambiguities** — places where the user's intent could be read multiple ways.
  - **Thin areas** — topics the mandatory answers touched but didn't develop.
  - **Hidden assumptions** — things the user might not realize they're assuming.
  - **Unexplored angles** — perspectives the mandatory questions didn't cover (technical constraints, user experience implications, failure modes, scale concerns).
- Push for refinement and polish, not just coverage.
- Ask these questions one at a time, same rules as Phase 1.

The 5/10 caps are a **ceiling**, not a target. They protect the user from over-interviewing. Right-sizing (next paragraph) only moves the **default** down for small projects; it never raises the ceiling. See `references/right-sizing.md > ## Interaction with the question cap` for the canonical statement of how the floor and ceiling compose.

**Right-sized round size (smallProject + thinness signal).** When `smallProject: true` (per `references/right-sizing.md`) AND the thinness signal fires on the just-completed round or transition, the agent composes the next round with **fewer than 5 questions, typically 2-3**, instead of the default 4-5. The thinness signal and its "two of three observations" rule are defined in `references/right-sizing.md > ## The thinness signal and sub-5-question rounds` — do not restate them here.

When the shortened round fires, the agent names it plainly in the transition message: "This is feeling well-covered. Want a quick 2-3 question pass to close gaps, or are you ready to proceed?" (Close paraphrase is fine; the user-facing intent is "well-covered" + "quick 2-3 question pass.") The user retains the right to decline the round entirely, exactly as in standard cadence — right-sizing only changes what the agent proposes by default, not the user's options.

The 5/10 caps are unchanged by right-sizing (see preceding paragraph and `references/right-sizing.md > ## Interaction with the question cap`).

**End-of-round recommendation (definite framing).** After each deepening round, the agent does **not** ask a bare open prompt. The agent emits one of two literal recommendations:

> "I think we should do another round, because [reason]."

or

> "I do not think we need another round, because [reason]."

A bare prompt like "want to do another round?" is **not allowed**. The agent must take a position and justify it.

- **When recommending continuation:** a **topic preview is required**. The recommendation must include an explicit list of the topics the proposed next round would cover, so the user can judge whether those topics are worth a round.
- **When recommending closure:** **reasoning is required**. The agent may list topics a hypothetical next round would have covered, and uses them to justify why those topics do not warrant a round (already covered implicitly, low risk, deferrable to refinement, etc.).

**Right-sized default direction (smallProject).** When `smallProject: true` (per `references/right-sizing.md`), the end-of-round recommendation **defaults to the close form** — "I do not think we need another round, because [reason; topics a next round would have covered]" — UNLESS concrete evidence in the just-completed round contradicts that default. Concrete contradicting evidence means something specific surfaced — a genuine ambiguity, an unresolved edge case, a hidden assumption the user reacted to — not a vague sense that "we could probably go deeper." The recommendation **form is unchanged** by this default shift: it remains definite, with reasoning, with the same topic-preview discipline (closure-reasoning lists topics a next round would have covered and explains why they do not warrant one; continuation-reasoning previews topics). A bare prompt is still not allowed. Only the default direction shifts — from "depends on round content" to "close, unless evidence contradicts."

The user always has the final say. They can accept the recommendation, override it (do another round when closure was recommended, or close when continuation was recommended), or redirect the proposed topics.

**Process notes:** Log the number of deepening rounds completed and what each round surfaced. This goes into the appropriate process notes file (see sdd-guide core for which file).
