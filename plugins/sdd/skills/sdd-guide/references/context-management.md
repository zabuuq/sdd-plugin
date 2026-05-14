# Context Management — Three-Tier Between-Rounds Model

**Used by:** `/sdd:discovery`, `/sdd:scope`, `/sdd:prd`, `/sdd:spec`, `/sdd:plan`, `/sdd:refine` — every interview command at the end of each deepening round.

This reference is the canonical home for the v2 three-tier between-rounds context-management model. At the end of every deepening round, after the round-continuation recommendation defined in `references/deepening-rounds.md`, the agent emits a **separate** recommendation choosing one of three context-management tiers: **continue**, **`/compact`**, or **`/clear`**. The two recommendations are independent — the user may want another round and still need a `/compact` between them, or be ready to proceed and still benefit from a `/clear`-then-handoff before the next command starts.

The recommendation is always definite. It is never a bare prompt like "what would you like to do?" — it is a named tier plus the agent's reasoning, delivered in the same message. The user accepts, overrides upward, or overrides downward (see Failure-mode mitigation below).

## The three tiers

### Tier 1 — continue

**What it means:** Keep going in the same session with no context action. The next round (or the next command) runs against the conversation as-is.

**Triggers (all must hold):**
- The round just finished was short — roughly 2–4 questions or fewer.
- The in-progress artifact (scope.md, prd.md, spec.md, sprint-N.md, etc.) already captures the substantive value of the round; a fresh agent reading the artifact alone would not be missing anything load-bearing.
- No significant superseded back-and-forth piled up during the round (no major walked-back decisions, no long exploratory tangents that landed elsewhere).
- The session as a whole still feels light — no accumulated drift, no signs of context pressure.

**Recommendation message form:**

> Recommendation: continue. The round was short and `<artifact>` already captures what we settled. No reason to compact or clear yet.

### Tier 2 — `/compact`

**What it means:** Recommend the user run Claude Code's `/compact` command. Compaction preserves a summary of the conversation as carry-forward context while reclaiming budget. The agent does NOT run `/compact` itself (see Failure-mode mitigation).

**Triggers (the round captured detail the artifact alone won't carry):**
- The round was substantive — roughly 5+ questions, or long answers, or both — and developed nuance that hasn't yet been written into the artifact.
- There IS an artifact-conversation gap: a fresh agent reading the artifact alone would miss material context (rationale behind a decision, an edge case the user raised but the artifact records only as a one-liner, a deferred choice with weighed options).
- Superseded noise exists but isn't dominant — some walked-back paths, but the productive content outweighs them.
- Total session weight is climbing but not yet heavy enough to justify a full reset.

**Recommendation message form:**

> Recommendation: `/compact`. This round developed `<one-line reason>` that `<artifact>` doesn't fully capture yet. A compacted summary will carry that detail forward without keeping the whole back-and-forth in active context. Run `/compact` when ready; I'll pick up after.

### Tier 3 — `/clear`

**What it means:** Recommend the user run Claude Code's `/clear` command to reset the session. Because `/clear` discards conversation state entirely, the agent **first writes a resume file** using the `pause-resume.md` schema so the next session can pick up cleanly via `/sdd:unpause`. The agent does NOT run `/clear` itself.

**Triggers (all of these typically hold together):**
- The round was long or heavy — many questions, long answers, multiple file reads, several tool calls, or all of the above.
- The artifact is rich and self-contained — a fresh agent reading the artifact (plus the resume file the agent is about to write) has everything needed to continue.
- The remaining conversation is dominated by superseded back-and-forth — false starts, walked-back recommendations, exploratory branches that landed elsewhere — that would only dilute the next round's context.
- Token-usage proxies (cumulative word count, number of file reads, tool calls, length of the running message log) suggest the session is genuinely heavy. The agent does not have an exact count; it judges from rough signals.

**Recommendation message form (note the integrated handoff):**

> Recommendation: `/clear`. The round was heavy, `<artifact>` is now rich and self-contained, and most of what's left in conversation is superseded back-and-forth that won't help the next round. I've written `docs/<command>-resume.md` so we can pick up cleanly. Run `/clear`, then `/sdd:unpause`.

The resume-file write happens **before** the recommendation message is sent — by the time the user sees the recommendation, the file is already on disk. See "/clear branch and pause-resume" below.

## Recognition signals

The agent judges the tier from rough proxies, not an exact accounting. Approximate is correct; pretending to a precision the agent doesn't have is wrong.

### Round size

- **Low:** 1–3 questions, brief answers (a sentence or two each), one round.
- **High:** 5+ questions, long answers (paragraph-length), or multiple rounds completed in the same session.

### Artifact-conversation gap

- **Low (small gap):** The artifact reflects everything that was decided. The conversation is mostly the path to those decisions, and the path won't matter to a fresh agent.
- **High (large gap):** The conversation contains rationale, weighed options, or deferred concerns that the artifact records thinly or not at all. A fresh agent reading the artifact alone would have to re-derive what was already worked out.

### Superseded noise

- **Low:** Most of the round's content remains directionally relevant. Few walked-back recommendations, few abandoned branches.
- **High:** Significant portions of the conversation no longer reflect current direction — false starts, revised positions, exploratory tangents that ended elsewhere. The noise-to-signal ratio is climbing.

### Token-usage approximation

The agent does not have an exact token count. It judges from rough proxies:

- Cumulative word count of the conversation.
- Number of files read this session.
- Number of tool calls executed.
- Length of the running message log.

- **Low:** Short session overall, few file reads, minimal tool use.
- **High:** Long running message log, many file reads (especially repeated reads of large files), heavy tool use, or a sense that the session has been going for a while.

## Tier-mapping rules

The signals compose into a tier choice:

| Round size | Artifact-conversation gap | Superseded noise | Token usage | Tier |
|---|---|---|---|---|
| Low | Low | Low | Low | **continue** |
| High | High | Low–medium | Low–medium | **`/compact`** |
| High | Low (artifact captures it) | High | High | **`/clear`** |

Mixed cases resolve by emphasis:

- If the artifact captures the value and the conversation is heavy with superseded noise → lean **`/clear`**.
- If the artifact has a real gap and the round was substantive → lean **`/compact`**.
- If the round was light and nothing accumulated → **continue**, even if the session has been going a while; weight alone is not enough to force action when the round was thin.

When two tiers feel equally defensible, prefer the lighter one. The user can always override upward.

## Failure-mode mitigation

**AI self-assessment of context weight is fallible.** The agent cannot see its own token count, can't precisely measure the artifact-conversation gap, and may misjudge superseded noise. The mitigation pattern below is designed around that fallibility.

- **The plugin does NOT programmatically invoke `/compact` or `/clear`.** Both are user-run commands. The agent recommends; the user executes (or doesn't).
- **The recommendation is always definite.** Even when uncertain, the agent commits to a named tier plus reasoning, in the same message. Bare prompts ("what would you like to do?") are not acceptable.
- **User may override upward at any time, no explanation required.** If the agent recommended `continue` and the user wants `/compact` or `/clear`, the agent complies without pushback. Upward overrides are always safe — they cost only a fresh start, not lost state.
- **User downward overrides carry a brief context-weight warning.** If the agent recommended `/clear` and the user says "let's just continue," the agent complies, but first delivers a one-sentence warning: "Continuing — note context is on the heavy side, so flag it if responses start to feel slower or less precise." The warning is brief, single-pass, not repeated.
- **The recommendation is delivered with reasoning in the same message; never a bare prompt.** "Recommendation: `/compact`. Reason: ..." is the form. "What should we do about context?" is not.

## Between-commands handoff

When an interview command finishes and the chain moves to the next command (e.g., `/sdd:scope` → `/sdd:prd`), there is a separate handoff template defined in `plugins/sdd/skills/sdd-guide/SKILL.md`. That handoff may itself recommend a `/compact` or `/clear` before the next command runs. The criteria are the same as the between-rounds tiers documented here; the only difference is the trigger point (end-of-command rather than end-of-round). The handoff template is not duplicated in this reference.

## `/clear` branch and pause-resume

The `/clear` tier consolidates with `/sdd:pause` and `/sdd:unpause`. Before the agent emits a `/clear` recommendation, it writes a resume file using the schema, location, and quality bar defined in `references/pause-resume.md`. The resume-file location is `docs/<command>-resume.md`, where `<command>` is the in-flight command name.

This is the same file format, same quality bar, and same cleanup rule as a manual `/sdd:pause`. The only difference is the trigger — automatic, on `/clear` recommendation, rather than user-initiated. After `/clear`, the user runs `/sdd:unpause`, which reads the resume file and resumes the in-flight command.

The agent never recommends `/clear` without first writing the resume file. If for some reason the resume-file write fails, the agent downgrades the recommendation to `/compact` and notes the failure, rather than recommending `/clear` with no handoff.

## Examples

### Example: `/compact` recommendation

End of a substantive PRD deepening round 2. The user asked good follow-ups about a deferred concern (third-party auth scope), the agent walked through three options, the user picked one. The artifact records the chosen option as a one-liner but not the rationale or the rejected options.

> That covers the deepening round. Recommendation for context: `/compact`. This round developed the auth-scope decision with rationale and two rejected options that `docs/prd.md` only captures as a one-liner. A compacted summary will carry the why forward without keeping the whole exchange in active context. Run `/compact` when ready, then we'll start the next round.

### Example: `/clear` with pause-resume handoff

End of a long spec deepening round 4. Five rounds total, many file reads, several walked-back recommendations earlier in the session that no longer reflect direction. The spec is now in good shape. The next round (or the user's choice to proceed) will benefit more from a fresh session than from compacting the noise.

The agent first writes `docs/spec-resume.md` (per `references/pause-resume.md`), then sends:

> That covers the round. Recommendation for context: `/clear`. The session is heavy — five rounds, multiple file reads, and most of what's still in conversation is superseded back-and-forth from earlier rounds that no longer reflects where we landed. `docs/spec.md` is rich and self-contained at this point, so a fresh start will be cleaner than a compaction. I've written `docs/spec-resume.md` so we can pick up exactly here. Run `/clear`, then `/sdd:unpause`.
