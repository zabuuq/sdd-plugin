---
name: spec
description: Technical specification designed for the project. Architecture, data models, tech stack, file structure. Detailed enough that any agent could build from it without asking questions.
disable-model-invocation: true
---

# /sdd:spec

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

Read `skills/sdd-guide/references/deepening-rounds.md` for the two-phase interview pattern used in this command.

Read `skills/sdd-guide/references/context-management.md` for the three-tier between-rounds context recommendation (continue / `/compact` / `/clear`) emitted alongside the end-of-round content recommendation.

Read `skills/sdd-guide/references/backlog.md` for the defer-to-backlog vs drop confirmation prompt, the 6-field entry schema, and the bootstrap rule for `docs/backlog.md`.

## Loading

- Load: `docs/scope.md`, `docs/prd.md` (read both in full)
- Load: `docs/open-concerns.md` (if it exists)
- Load: `docs/project-state.json` (if it exists)
- Load: `~/.claude/sdd-user-profile.json` (for communication style and experience context)
- Load: `skills/sdd-guide/references/backlog.md` (defer-to-backlog write trigger and entry format)

## Prerequisites

Both `docs/scope.md` AND `docs/prd.md` must exist. If either is missing:

- Missing `docs/scope.md` → tell the user to run `/sdd:scope` first.
- Missing `docs/prd.md` → tell the user to run `/sdd:prd` first.
- Missing both → tell the user to run `/sdd:scope` first, then `/sdd:prd`.

Do not proceed if prerequisites are not met.

## Behavior

### Step 1: Update State

Update `lastCommand` in `docs/project-state.json` to `"/sdd:spec"`. Create the file if it does not exist.

### Step 1a: Read and Re-Evaluate `smallProject`

After the `lastCommand` update, read the top-level `smallProject` value from `docs/project-state.json`. If the key is absent or `null`, treat it as "no judgment yet" — standard cadence applies. See `skills/sdd-guide/references/right-sizing.md > ## The smallProject field` for what the value means and how it shapes downstream behavior.

Then re-evaluate per `skills/sdd-guide/references/right-sizing.md > ## The smallProject field > Authoring lifecycle`. Apply the signals from that reference's `## Signals for the heuristic judgment` section against the material this command has access to at startup (`docs/scope.md` and `docs/prd.md` already loaded, plus `docs/open-concerns.md` if present). Do not restate the signal list or rule of thumb here — the reference is canonical.

Outcomes:

- **Re-evaluation confirms the prior value:** no-op. Do not write `docs/project-state.json`. Do not append to process notes.
- **Re-evaluation flips the value (`true` → `false` or `false` → `true`):** write the new value to `docs/project-state.json` under the top-level `smallProject` key, and append a one-line entry to `process-notes-spec.md` at the project root noting the flip direction and a brief rationale (which signal or signals tipped the judgment). If `process-notes-spec.md` does not yet exist, create it on this append.

Bias toward humility — when in doubt, leave the value alone and proceed with standard cadence.

### Step 2: First-Run Explanation

Check `commandExplanationsShown.spec` in `docs/project-state.json`. If it is not `true`, output the following as plain text (not a question — do not wait for acknowledgment), then set `commandExplanationsShown.spec` to `true`:

---

**/sdd:spec** designs the technical architecture for your project. We'll work through your stack, deployment, file structure, data flow, and testing strategy — producing a spec document detailed enough that any developer or AI agent could build from it without asking clarifying questions.

The spec maps directly to your PRD epics. Every architectural component gets its own section with granular subsections, so later commands can load exactly the context they need for each piece of work.

We'll also generate CLAUDE.md and AGENTS.md — instruction files that give AI agents the architectural context they need to build correctly.

---

### Step 3: Open Concerns

Read `docs/open-concerns.md`. Follow the open concerns protocol from sdd-guide:

1. Read all entries.
2. Resolve what you can — especially concerns targeted at `/sdd:spec`. Mark indirect resolutions with an explanation.
3. Defer what you cannot resolve with rationale.
4. Note which open concerns targeted at `/sdd:spec` need to be addressed during this session. Weave them into the interview naturally — do not present them as a separate checklist.

### Step 4: Tech Preferences Interview (Phase 1 — Mandatory Questions)

This is the mandatory question phase from the deepening rounds pattern. Ask questions one at a time, free-form only. Adapt the depth and vocabulary to the user's experience level (inferred from their profile and prior responses).

**Small-project right-sizing (conditional, governs the Phase 1 mandatory beats in Steps 4–7 and 11).** Consult the `smallProject` value read and re-evaluated at startup (Step 1a above).

- **When `smallProject` is `true`:** the Phase 1 beats designated in `skills/sdd-guide/references/right-sizing.md > ## Skippable Phase 1 beats per command > ### \`/sdd:spec\`` default to skipped. The designated beats may map into any of the mandatory-beat steps below (Steps 4, 5, 7, and 11 in particular). Do not restate the beat list here — the reference is canonical.
- **When `smallProject` is `false`, `null`, or absent:** run every Phase 1 mandatory beat as normal.

These beats are **skippable, not required-skipped**. If the user explicitly raises a designated topic (e.g., names a security or observability concern) or earlier material leaves a designated beat genuinely open, ask about it anyway. Per the reference, open-concerns capture remains in full regardless of `smallProject`.

**Mandatory topics to cover** (adapt phrasing and order to the conversation flow — skip any topic the user already addressed in scope or PRD):

1. **Stack preferences** — What language, framework, and runtime do they want to use? Do they have existing experience or strong preferences? Any constraints (e.g., must deploy to a specific platform, must use a specific database)?

2. **Existing codebase** — Is this greenfield or are they building onto something? If existing code, what's the current stack and what constraints does it impose?

3. **Data storage** — What kind of data does the app manage? What are the access patterns? Do they have a database preference or should you recommend one based on their needs?

4. **Authentication and authorization** — Does the app need user accounts? What auth approach fits? Are there role-based access requirements?

5. **Third-party integrations** — Any external APIs, services, or libraries that are known requirements? Payment processing, email, file storage, etc.?

6. **Frontend approach** — If there's a UI: SPA vs. SSR vs. static? Component library preferences? Styling approach?

### Step 5: Deployment Discussion

Ask about deployment as a dedicated topic:

- Where will this run? (Cloud provider, self-hosted, serverless, edge, etc.)
- What does their deployment pipeline look like? (CI/CD, manual, etc.)
- Environment management — how do they handle staging vs. production?
- Any infrastructure constraints or requirements?

### Step 6: Research Current Documentation

If WebSearch is available, search for current documentation on the proposed tools, libraries, and APIs. Collect reference URLs for:

- Official docs for every framework and library in the proposed stack
- API documentation for third-party integrations
- Migration guides or upgrade notes if using specific versions

These URLs go into the Stack table in the spec. They are what `/sdd:build` agents will reference during implementation.

If WebSearch is not available, ask the user to provide or confirm documentation URLs for the key tools in their stack.

### Step 7: Propose Architecture

Propose the architecture section by section, mapped to PRD epics. For each architectural component:

- Name the PRD epic it implements (cross-reference as "Implements `prd.md > [Epic Name]`")
- Describe the component's responsibilities
- Define its interfaces and contracts with other components
- Specify data shapes, API signatures, or schema definitions

Present each section to the user for feedback before moving to the next. Do not dump the entire architecture at once.

**Critical: granular subsections.** Every architectural component gets its own `##` heading. Within each component, create `###` subsections for specific pieces (e.g., `## Frontend` > `### Search Component`, `## API` > `### GET /api/items`, `## Data Model` > `### Users Table`). These subsection headings become the addresses that `/sdd:plan` uses to link checklist items to their spec context, and that `/sdd:build` uses to load only the relevant spec section. If subsections are too coarse, build agents will load too much irrelevant context or miss critical details.

### Step 8: File Structure

Build the file structure tree together with the user:

- Propose an initial file tree based on the architecture discussion
- Annotate key files with brief descriptions
- Walk through it with the user — they may want to reorganize, rename, or add/remove files
- The final tree should account for every component discussed in the architecture

### Step 9: Data Flow Walkthrough

Walk through the data flow for the most important data in the application:

- Identify the primary data entity (or entities) from the PRD
- Trace how that data moves through the system: user action → frontend → API → database → response
- Draw ASCII diagrams showing the flow
- Cover the happy path and at least one important error/edge case path
- If the app has multiple critical data flows, walk through each one

### Step 10: Testing Strategy

Define the testing strategy:

- **Unit tests** — what gets unit tested, which framework, what's the approach
- **Integration tests** — API routes, database operations, component integration
- **End-to-end tests** — which critical user flows need e2e coverage, which framework (Playwright, Cypress, etc.)
- **Coverage expectations** — target percentage, what must be covered vs. what's nice to have

Ask the user about their testing preferences and experience level. Adapt recommendations accordingly — don't prescribe heavyweight testing for a solo developer's side project, and don't under-specify for a production system.

### Step 11: Multi-Service Architecture (Conditional)

If the architecture involves multiple services (microservices, separate frontend/backend deployments, background workers, etc.):

- Define service boundaries clearly
- Specify communication patterns (REST, gRPC, message queues, events, etc.)
- Document which service owns which data
- Define the contract between services

Skip this step for monolithic architectures.

### Step 11a: Deferrals

When a topic surfaces during the spec interview that the user explicitly wants to push off — something that doesn't belong in `Open Issues` (which stays in `docs/spec.md` for the spec author's attention) and isn't being folded into any architectural section — route the decision through the defer-to-backlog vs drop prompt defined in `skills/sdd-guide/references/backlog.md > ## Write trigger`. An affirmative defer answer appends an entry to `docs/backlog.md` per the schema in that reference; a drop answer (or any non-affirmative response) writes no entry and records the drop in `process-notes-spec.md` only.

### Step 12: Deepening Rounds (Phase 2)

Follow the deepening rounds protocol defined in `skills/sdd-guide/references/deepening-rounds.md` — including the Phase 1 → Phase 2 transition, the per-round question count (5 default, up to 10 with explicit reason+permission past the cap), and the structured end-of-round content recommendation (continue-with-topic-preview or close-with-reasoning). Do not emit a bare transition prompt; the reference defines the recommendation wording.

After the end-of-round content recommendation fires, emit the three-tier between-rounds context recommendation per `skills/sdd-guide/references/context-management.md`. Order is fixed: content recommendation first, then context recommendation, in the same end-of-round message group. The two are separate and independent.

Deepening questions for the spec should target edge cases, ambiguities, thin areas, hidden assumptions, and unexplored angles in the architecture. Ask them one at a time per the reference.

### Step 13: Generate `docs/spec.md`

Generate `docs/spec.md` using the template at `skills/sdd-guide/templates/spec-template.md`.

**Requirements:**

- Fill in every section of the template based on the interview discussion.
- The Stack table must include a reference docs URL for every tool and library. These URLs are what `/sdd:build` agents will look up during implementation.
- Every component section must open with a PRD epic cross-reference: "Implements `prd.md > [Epic Name]`"
- Granular subsections (`###`) under every component (`##`) — these are critical for `/sdd:plan` and `/sdd:build` context loading.
- The file structure tree must be complete and annotated.
- Data flow diagrams as ASCII art.
- Key Technical Decisions table filled in with every significant choice, its rationale, and its tradeoff.
- Open Issues section populated with any unresolved technical questions. Open Issues is NOT a deferred-items section — entries here are in-spec unresolved questions for the spec author's attention. Items the user chose to defer rather than carry forward live in `docs/backlog.md`, surfaced via the conditional pointer below.
- `## Out of Scope` — conditional backlog pointer. Before writing the artifact, check `docs/backlog.md` against the heading-scan rule in `skills/sdd-guide/references/backlog.md > ## Parser note` — if the file exists and has at least one entry by that rule, emit the section heading and the literal line `Deferred items: see \`docs/backlog.md\`.`. Otherwise omit both the heading and the body. Do not inline-list deferred items here; the backlog is the single source of truth. Do not restate the parser rule inline; the reference is canonical.

Present the generated spec to the user for review. Incorporate feedback before finalizing.

### Step 14: Generate or Merge CLAUDE.md

**If `CLAUDE.md` exists at the project root:**
- Read the existing file in full.
- Preserve all relevant existing content (custom instructions, project-specific rules the user added).
- Merge in spec-derived instructions: architectural summary, key conventions, file structure overview.
- Do not blindly overwrite — treat the existing file as authoritative for anything not directly contradicted by the spec.

**If `CLAUDE.md` does not exist:**
- Generate a fresh file.

**CLAUDE.md content requirements:**
- Concise architectural summary — enough for a Claude agent to re-orient on the project without reading the full spec.
- Key conventions and patterns from the spec (naming, file organization, error handling approach).
- Explicit instruction: "Read AGENTS.md for full project context and coding conventions."
- Does NOT reference sprint files — CLAUDE.md is stable across sprints.
- Kept concise. This is a quick-orientation document, not a second spec.

### Step 15: Generate or Merge AGENTS.md

**Same merge/generate logic as CLAUDE.md** — if it exists, read and merge; if not, generate fresh. Preserve existing content when merging.

**AGENTS.md content requirements:**
- Agent-agnostic content — not Claude-specific. Any AI agent or human developer should benefit from this file.
- Project context: what the project is, who it's for, what problem it solves.
- Architecture decisions: the "why" behind major technical choices (pull from Key Technical Decisions in the spec).
- Coding conventions: naming, file structure patterns, import conventions, error handling, logging.
- Development workflow: how to run, test, and deploy.
- This is a living document — it will be updated as the project evolves.

### Step 16: Append to Process Notes

Append to `process-notes-spec.md` in the project root, per sdd-guide's `## Process Notes` (four categories, real-time, append-only). Decisions here are the architectural ones and the discussion that led to them. Also capture spec specifics: stack choices debated and why the final choice won; number of deepening rounds and what each surfaced; concerns or doubts that arose.

If `process-notes-spec.md` does not yet exist at the project root, create it on the first append.

### Step 17: Update Open Concerns

Update `docs/open-concerns.md`:

- Move any concerns resolved during this session to the Resolved section with resolution explanations.
- Add new concerns that arose during spec writing (especially items from the Open Issues section of the spec).
- Defer concerns with rationale where appropriate.

### Step 18: Update State

Update `lastCommand` in `docs/project-state.json` to `"/sdd:spec"` (confirming completion). Ensure `commandExplanationsShown.spec` is `true`.

### Step 19: Delete docs/spec-resume.md

After `docs/spec.md` has been written (per Step 13 above), delete `docs/spec-resume.md` if it exists. A missing file is not an error — continue silently. Per `skills/sdd-guide/references/pause-resume.md > ## Cleanup`, the resumed command owns this deletion; `/sdd:pause` is the only writer.

### Step 20: End-of-Command Handoff

Emit the handoff per `## End-of-Command Handoff` below.

## End-of-Command Handoff

Runs as the final step after Step 19 (process notes, open concerns, state update, any CLAUDE.md / AGENTS.md writes, and the resume-file cleanup have all completed).

Emit the handoff per the canonical template in `skills/sdd-guide/SKILL.md > ## End-of-Command Handoff`. That template defines the two-line standard form, the first-handoff explanation paragraph (prepended exactly once per user), and the `handoffWarningShown` tracking convention in `~/.claude/sdd-user-profile.json`. Do not restate that mechanism here.

### Next-command target

The `[next-command]` slot in the template is always `/sdd:plan` for `/sdd:spec`.

### Outcome-summary line

Use a one-line outcome summary in the form `Spec generated.` Substitute project-specific phrasing only if it preserves the one-line, declarative shape (e.g., `Spec generated. CLAUDE.md and AGENTS.md updated.`).

### Unconditional emission

The handoff fires unconditionally at completion. No context-weight heuristic, deepening-rounds outcome, or multi-service-architecture branch causes it to be skipped.

## Important Reminders

- **One question at a time.** Never ask the user multiple questions in a single message.
- **Free-form questions only.** No multiple-choice options for interview questions.
- **Granular subsections are critical.** Every `##` component needs `###` subsections. These are how `/sdd:plan` references specific spec sections and how `/sdd:build` does surgical context loading. Coarse sections break the downstream workflow.
- **Cross-reference PRD epics.** Every component section cites which PRD epic it implements.
- **Include reference doc URLs.** Every tool and library in the Stack table needs a link to its current docs. `/sdd:build` agents use these.
- **CLAUDE.md merge logic.** Never blindly overwrite an existing CLAUDE.md. Read it, preserve relevant content, merge spec-derived instructions.
- **AGENTS.md merge logic.** Same principle — preserve and merge, do not replace.
- **Adaptive flow.** Skip topics the user already covered. Follow important threads that weren't in the script. The goal is a complete, buildable spec — not a completed checklist.
