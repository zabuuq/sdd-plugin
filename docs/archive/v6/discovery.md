# SDD v6 — Discovery

A brainstorm-first replacement for the SDD front end: brain-dump in, three drafted docs and a client-ready prototype out, aimed at getting more projects actually finished.

## The Idea

v6 replaces the current `/sdd:discovery → /sdd:scope → /sdd:prd → /sdd:spec` linear front end with a single brainstorm-first flow. It is a complete top-down replacement, not a fast-lane bolted alongside the existing chain.

The flow:

1. **Brain-dump** — the maintainer talks (recordings → transcripts) or writes a long doc.
2. **Auto-draft** — the AI gleans the signal and drafts **scope + PRD + spec together in one pass**, covering the *whole* project (large projects still get split into pieces later, but the three docs describe the entire project, not a piece).
3. **Interview** — the AI interviews the maintainer *about the drafts* — reacting to something on the page, not extracting from a blank one.
4. **Research** — the AI validates the three docs.
5. **One-shot prototype** — a fast prototype of "what the AI thinks you'd want," built as plain HTML/CSS/JS. High-fidelity clickable or low-fidelity screenshots; the AI may ask which.
6. **Refine** — the maintainer reacts to the prototype; the three docs get refined against it.
7. **Issues as backlog** — tasks become GitHub issues that *are* the backlog (no separate backlog file), so other agents (Copilot, Codex) can be pulled in via the repo. Planning pulls items into medium-sized sprints, with a loop that picks items off one at a time.
8. **Streamlined retro** — replays the lessons captured during the project instead of asking "did it go well?" cold.

## Why It Matters

Optimize for **throughput of finished projects**, not per-step speed. The current chain hasn't worked as well as it looks: "as much as it's been working, it hasn't been working — I've only finished a few projects using this process as it is." The linear front end is what stalls completion, not just what slows it down.

The tax today:

- **The upfront doc slog** — roughly "8 hours / 2 days a week" assembling documents. The root cause is structural: **four separate interviews** (discovery, scope, PRD, spec), each with its own 5–20 question stack. That split created two compounding failures — **repetition** across commands, and **cross-phase loss**, where a spec-flavored thought raised during scope got deferred and then lost in the shuffle before its command arrived. Combining scope + PRD + spec into one drafted pass attacks both: fewer total questions, and cross-cutting points captured while fresh.
- **Hollow retros** — the current retro surfaces nothing real ("yeah, everything was great").
- **Questions answerable only in development** — some things can't be settled until you're building, so front-loading them wastes effort.

## Audience

Solo — "this plug-in is pretty much going to be for me." Build it the way the maintainer wants to work; others are optional later. No backward-compatibility obligation, which is what makes the clean-break replacement acceptable.

## Prior Art and Inspiration

- **The conference talk's six-step SDD flow** (slides in `docs/refs/`: high-level goals → initial requirements → independent multi-AI research → consolidated spec → phased execution plan → coding sprints; plus a "Prompting Strategies That Worked" slide). This **sparked** v6 but is not a spec — "the talk sparked it, but it's my own flow now." No part of the six-step model is load-bearing on its own. The deliberate divergences aren't exceptions to a model being followed; they're the flow being itself:
  - **Medium-sized sprints**, not the talk's "small sprints." Sprints shouldn't be the whole project, but they don't need to be tiny.
  - **Whole-project scope/PRD/spec**, not per-piece documents.

## Constraints

- **Plain web tech for the prototype** — HTML/CSS/JS, for simplicity.
- **Solo, no backward-compat** — the old chain can be replaced outright.
- **External agents/skills/memory are optional, not load-bearing.** v6 stands on its own and merely cooperates with the maintainer's existing template system. Posture: assume installed; if something's missing, note it or **fail quietly and continue**. v6 must function without them.
- **Sacred parts that must survive the rewrite (closed list, exactly two):** the **interview**, and **not losing accumulated context**. The user confirmed "that covers it."

## Starting Context

`docs/refs/` was non-empty at command start and every file was read as interview context:

- Two slide photos — the six-step SDD flow and "Prompting Strategies That Worked."
- Two brain-dump transcripts — `Jun 24 at 8-30 PM.txt` and `Jun 24 at 8-52 PM.txt` (the maintainer enacting his own proposed flow: "I'm going to try to do a lot of recording like this... and use that as my brainstorming"). The matching `.m4a` audio isn't machine-readable; the transcripts are the readable form. These answered most of Phase 1 in bulk.

**How v6 defines success** (surfaced during discovery, carried forward for `/sdd:scope`):

- **Concrete:** get from discovery to a **high-fidelity prototype in a single day**, at least once. Never achieved with the current flow; not expected every time, but landing it once proves the front end is fast enough. It doubles as a whole-front-end stress test.
- **Felt:** the front end didn't drain the maintainer, and he still had momentum when he hit code — the opposite of arriving at the build already spent.

**The prototype's nature** (important for downstream design): not a throwaway mirror, not a code seed — a **durable reference artifact**. It's compared against the three docs, it's shown to **clients and investors** (so it must be solid and presentable, and it grows on the maintainer's feedback), and it guides the final product. But its **code is disposable**: building the real product off the prototype's code "will probably lead to disaster." The final product starts **fresh**, using the prototype as a north star, not a foundation.

**The lessons-as-they-happen mechanism** (nature captured here; structure is `/sdd:scope`'s job): capture lessons the moment they surface — during doc creation and during each item's build — and feed them back so mistakes don't repeat. What counts as a lesson, mostly **project-level**:

- **Lost-context / redundant-question events** — a later step re-asks something already answered (has happened multiple times).
- **Repeated roadblocks** — the user or AI hits a wall, solves it, then hits the same wall again; the solution must be recorded.
- **Course corrections** — the AI goes one direction, the user says "no, different direction."
- **Reusable designs, code practices, or preferences.**

Project-level lessons live with the project and are worth repeating within it. Global lessons (true across projects, e.g. "I always use repos") should be recordable at a global level too, and higher-level *plugin* lessons flow to `/sdd:feedback`. These captured lessons are the **retro's raw material**: the retro replays them for confirmation, then asks if there's anything else; if not, it ends.

## Open Questions

- **Global vs. project-level lesson storage.** The maintainer wants project-level capture as the main target but also wants some lessons recordable at a global level. Where the boundary sits and how global lessons get written is unresolved — a signal for `/sdd:scope`.
- **Research validation structure.** The "AI researches to validate the three docs" beat is named but not shaped (how much, against what, how results feed back).
- **GitHub-issues-as-backlog bounding.** Issues replacing the local backlog is decided; the mechanics (what a task-to-issue conversion includes, how the sprint-pull loop works) are scope/spec territory.

## Deferred Topics

Deferred items: see `docs/backlog.md`.
