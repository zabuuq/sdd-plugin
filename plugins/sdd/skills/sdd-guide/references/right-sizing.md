# Right-Sizing

**Used by:** `/sdd:scope` (writes), `/sdd:prd`, `/sdd:spec`, `/sdd:plan` (re-evaluate)

This reference is the canonical home for the v2 right-sizing heuristic. It defines what a "small project" means in the SDD workflow, which command owns the judgment, how subsequent commands revisit it, and how the judgment shortens interviews without sacrificing the document's usefulness.

The goal is simple: when the project is genuinely small, the interview shouldn't feel like a TPS-report drill. When the project is larger or genuinely uncertain, the full interview cadence stays in force. Right-sizing is a quality-of-experience lever, not an excuse to skip rigor.

## The `smallProject` field

`smallProject` lives in `docs/project-state.json` (see `references/living-documents.md` for the project-state schema). It carries one of three values:

- `true` — the project is judged small. Interview commands may trim Phase 1 beats and may default to shorter deepening rounds (see below).
- `false` — the project is judged normal-or-larger. Standard interview cadence applies.
- `null` / absent — no judgment has been made yet. Standard cadence applies. This is the state before `/sdd:scope` finishes Phase 1.

### Authoring lifecycle

- `/sdd:discovery` does **not** detect or write `smallProject`. Discovery is open exploration; a scale judgment at that stage would be premature and would also bias the very framing discovery is meant to surface. This is a deliberate v2 relocation; the initial PRD assigned detection to discovery, and v2 moved it. See `spec.md > PRD Edits Required` for the rationale trail.
- `/sdd:scope` writes the **authoritative initial value** at the end of its Phase 1, after the mandatory scoping questions have been answered. Scope is the first command that has enough signal to make a defensible call, and it is the natural place to commit one.
- `/sdd:prd`, `/sdd:spec`, and `/sdd:plan` **re-evaluate on startup**. Each reads the current value, applies the signals below against the material it now has access to, and may flip the value if contradictory or first-surfacing evidence appears. A re-evaluation that confirms the prior value is a no-op; a flip is logged in the relevant process notes file.

Re-evaluation is one-directional in practice but not by rule. A command can flip `true` to `false` if new complexity surfaces (a third-party integration mentioned in PRD that scope missed, a multi-component architecture that emerges in spec). It can also flip `false` to `true` if a project that initially looked larger turns out, on closer inspection, to be one feature wearing a costume. The bias should be toward humility — when in doubt, leave the value alone and proceed with standard cadence.

## Signals for the heuristic judgment

A project is a candidate for `smallProject: true` when several of the following are present. None of these is individually sufficient; the judgment is holistic.

- **One primary feature.** The user is building one thing, not a system of cooperating things. A todo app, a single-purpose CLI, a one-page calculator, a single Slack command. If the user describes "the feature" rather than "the features," that's a signal.
- **No external users.** The artifact is for the user themselves, a small group they're directly part of, or an internal tool with a known and stable audience. Public-facing products with strangers as users almost always pull in concerns (auth, abuse, accessibility, support) that push past small.
- **No multi-component architecture.** No separate frontend and backend services, no worker queue, no separate admin tool, no microservices. A single deployable, or a tightly-coupled pair (e.g., a static site plus one serverless function) still counts as small.
- **No third-party integrations.** No payment processors, no OAuth providers beyond a single sign-in, no external APIs that the project depends on for its core value. One incidental dependency (e.g., a font CDN) does not disqualify; a core integration does.
- **Brief or absent reference uploads.** During `/sdd:discovery`, the user uploaded little or nothing. A user with a 40-page existing PRD, a competitor analysis, and three architecture diagrams is almost certainly not building a small project.
- **Concise opening framing.** The user's opening description of the project is short and specific. Long, hedged, multi-paragraph framings — especially ones that name multiple user types or several distinct workflows — point away from small.

A reasonable rule of thumb: four or more of these present, with none of them strongly contradicted, justifies `smallProject: true`. Three or fewer, or any single strong contradiction (e.g., the user explicitly names external paying customers), justifies `false`.

## Skippable Phase 1 beats per command

When `smallProject: true`, each interview command may trim its Phase 1 mandatory questions. The cap defined in `references/deepening-rounds.md` and the 5/10 question structure still apply (see "Interaction with the question cap" below); what changes is which beats are mandatory inside Phase 1.

The lists below are skippable, not required-skipped. A command should still ask a beat if the user's earlier answers leave it genuinely open.

### `/sdd:scope`

When the signals already point clearly to small by the time scope reaches its later mandatory questions, scope may skip:

- The "who are the users beyond yourself" beat (already answered: nobody, or a known small group).
- The "what's out of scope" beat as a separate question — fold it into a closing summary instead.
- The "what does success look like in six months" beat — small projects don't need a six-month horizon.

### `/sdd:prd`

When `smallProject: true`:

- Skip the user-segmentation beat. One audience, no segmentation.
- Skip the "non-goals" beat as a standalone question; fold non-goals into the goals beat or into a brief closing.
- Skip the "competitive landscape / differentiators" beat. Small projects rarely have competitors in any meaningful sense.
- Acceptance-criteria generation remains in full — small projects still need testable ACs.

### `/sdd:spec`

When `smallProject: true`:

- Skip the architecture-overview beat — there's nothing multi-component to overview.
- Skip the "cross-cutting concerns" beat (observability, security posture, accessibility) unless the user has raised one explicitly. Small private projects don't carry these as standing concerns.
- Skip the deployment-topology beat. A single deployable doesn't need a topology section.
- Open-concerns capture (see `references/open-concerns.md`) remains in full.

### `/sdd:plan`

When `smallProject: true`:

- Skip the "how do we sequence multiple sprints" beat. Small projects often complete in one or two sprints.
- Skip the dependency-graph beat. With one component and no integrations, dependencies are linear.
- Sprint-item generation and PRD tag-back remain in full.

## The thinness signal and sub-5-question rounds

The standard deepening-round size is 4-5 questions (`references/deepening-rounds.md`). When `smallProject: true`, the agent watches for a **thinness signal** during the round in progress and during transitions between rounds. When the signal fires, the next round defaults to 2-3 questions instead of 4-5.

The thinness signal is composed of three observations:

- **Short answers.** The user is replying in one or two sentences, not paragraphs. They're answering the question and stopping.
- **Deflections.** The user is saying things like "I don't think that applies," "we don't need that," "skip it," or "you tell me." Not hostile — just signaling there isn't material to develop.
- **Agent's natural framing toward closure.** When the agent goes to compose the next question, it finds itself reaching. The remaining angles feel pro-forma rather than genuinely productive. (This is a judgment the agent makes on itself; it is a real signal and should be honored.)

**Two or more of these three observations**, present in the same round or at the transition between rounds, triggers the shortened round. One alone is not enough — short answers from a terse user are normal; a single deflection might just mean one question landed wrong.

When the shortened round fires, the agent says so plainly at the transition: "This is feeling well-covered. Want a quick 2-3 question pass to close gaps, or are you ready to proceed?" The user can still decline the round entirely, the same as in standard cadence.

## Interaction with the question cap

The 5/10 question cap from `references/deepening-rounds.md` is **unchanged** by right-sizing. The cap is an upper bound; right-sizing moves the **default** down when warranted. Specifically:

- Standard cadence: 4-5 questions per round, cap of 5 in Phase 1 and 10 across deepening rounds (per the cap policy in `references/deepening-rounds.md`).
- Right-sized cadence (`smallProject: true` plus thinness signal): default drops to 2-3 questions per round. Caps stay where they are. A user who wants more rounds can always have them.

The cap is a ceiling protecting the user from over-interviewing. Right-sizing is a floor adjustment that keeps small-project interviews from feeling padded. The two mechanisms compose: a small project might run 2-3 questions per round for two rounds and finish in six total questions, well under the cap. A small project where the user wants to dig in can still run full 4-5 question rounds up to the cap. The user is always in charge of "another round?"; right-sizing only affects what the agent proposes by default.
