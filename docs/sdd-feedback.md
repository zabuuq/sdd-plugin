# SDD Feedback

### 2026-04-10T00:00:00Z
- **Sprint:** 0
- **After command:** /sdd:onboard
- **Project:** Event Planning App
- **Note:** Onboarding — Feels lacking, needs review. Consider what additional elements or steps could be added to improve it.

### 2026-04-10T00:05:00Z
- **Sprint:** 0
- **After command:** /sdd:scope
- **Project:** Event Planning App
- **Note:** Encourage more user input — Plugin should prompt/encourage users to add more information freely, rather than feeling like they need to skip things or hold back.

### 2026-04-10T00:10:00Z
- **Sprint:** 0
- **After command:** /sdd:scope
- **Project:** Event Planning App
- **Note:** File uploads during /scope command — Explore whether the plugin should suggest users can upload files during scope, and provide a designated place to do so. Those files would then be scanned/used for the scope analysis.

### 2026-04-10T00:15:00Z
- **Sprint:** 0
- **After command:** /sdd:scope
- **Project:** Event Planning App
- **Note:** File uploads elsewhere — Same question applies: would file upload functionality be useful at other points later in the workflow too?

### 2026-04-10T00:20:00Z
- **Sprint:** 0
- **After command:** /sdd:feedback
- **Project:** Event Planning App
- **Note:** /sdd:feedback clobbers lastCommand — Each feedback call overwrites lastCommand in project-state.json to /sdd:feedback, so back-to-back feedback entries lose the real "after command" context. Consider not updating lastCommand when the previous value was already /sdd:feedback, so consecutive feedback notes preserve the original command context. (User acknowledged that batching feedback contributes to the issue, but it's still worth addressing.)

### 2026-04-10T00:25:00Z
- **Sprint:** 0
- **After command:** /sdd:scope
- **Project:** Event Planning App
- **Note:** /discovery command — Entertain the idea of either: (a) replacing /scope with a /discovery command, or (b) adding /discovery as a separate command that lives alongside /scope in the process. Worth thinking through how they'd relate to each other.

### 2026-04-11T00:00:00Z
- **Sprint:** 0
- **After command:** /sdd:scope
- **Project:** Event Planning App
- **Note:** Deepening-round prompts should preview topics — When any SDD skill asks "Do you want another round?", it should also suggest what items/thin spots could be discussed in that next round. /sdd:scope did this once in this session and it was much more enlightening than a bare yes/no prompt. Apply this pattern to deepening-round offers at all levels (scope, prd, spec, sprint).

### 2026-04-11T00:05:00Z
- **Sprint:** 0
- **After command:** /sdd:feedback
- **Project:** Event Planning App
- **Note:** Context management for extra deepening rounds — Consider what options exist for clearing or compacting session memory when extra rounds run during a phase. If users run 5, 10, 15 rounds of deepening, the session could accumulate too much context. Explore strategies like mid-phase summarization, discarding superseded answers, or offering a "compact now" checkpoint between rounds. (Note: the "after command" for this entry is /sdd:feedback because the previous entry was also /sdd:feedback — this is the clobbering issue already flagged in an earlier feedback note; the actual command in play was /sdd:scope.)

### 2026-04-21T23:55:22Z
- **Sprint:** 1
- **After command:** /sdd:reflect
- **Project:** fiver-gigs
- **Note:** Reflect on a simple sprint feels like a bit much. Consider recognizing short sprints and opening with a single encompassing "was everything okay?" question that can be answered and moved past, instead of running through multiple sub-questions. More involved sprints should still get the deeper reflection, but smaller ones shouldn't.

### 2026-04-22T00:45:00Z
- **Sprint:** 2
- **After command:** /sdd:reflect
- **Project:** fiver-gigs
- **Note:** The first and second sprint asked me what mode (autonomous mode or not). It might be better if it asks if I would like to use the same mode as the previous sprint and maybe an option to set it as the mode always used.

### 2026-04-22T01:05:00Z
- **Sprint:** 2
- **After command:** /sdd:reflect
- **Project:** fiver-gigs
- **Note:** /sdd:reflect has a gap in PRD state tracking. Step 4 (Partial Story Splitting) checks off PRD criteria only for the completed portion of split stories. Step 5 (PRD State Check) then scans the PRD for "every story across all epics checked off" to decide the next-step recommendation (refine / sprint / retro). But nothing in reflect checks off the PRD for fully-complete sprint items (the non-split case), so the PRD will always look incomplete as long as sprints deliver stories whole. This caused drift across Sprint 1 and Sprint 2 in this project — sprint files showed [x] Complete while the PRD still showed [ ] for the same criteria. Suggested fix: in Step 3 (Product Check), after confirming a sprint item is fully complete (no split needed), automatically check off the matching acceptance criteria in docs/prd.md. Handle the case where a sprint item maps to only some of the criteria of a larger cross-cutting PRD story (e.g., "each sample folder contains populated credits.md" — true only after all samples ship) so those criteria stay unchecked until actually satisfied.

### 2026-04-24T00:00:00Z
- **Sprint:** 0
- **After command:** /sdd:scope
- **Project:** Event Planning App
- **Note:** Automatic backlog generation during planning — Consider having SDD planning skills (/sdd:scope, /sdd:prd, /sdd:spec, /sdd:sprint) automatically maintain a `docs/backlog.md` as deferred items surface, instead of leaving them only in scope-in-progress / future-concept notes that disappear when scope-in-progress is archived. In this session the user explicitly asked "can we add these to a backlog so we don't forget them later?" mid-scope — and on inspection, the deferred items were scattered across the in-progress doc's "Future-concept notes" section and across individual "Confirmed decisions" sub-bullets, with no single source of truth. We then created `docs/backlog.md` manually. This should be a built-in behavior: any time a planning skill defers an item, it gets written to `docs/backlog.md` with full context (what it is, why deferred, when it might come back, dependencies) so future planning sessions can pick up cold. Bonus: the scope/PRD/spec docs can then reference the backlog rather than duplicating "out of scope" lists, keeping one source of truth.

### 2026-04-24T00:05:00Z
- **Sprint:** 0
- **After command:** /sdd:scope
- **Project:** Event Planning App
- **Note:** First-class multi-session resume support — The /sdd:scope skill currently only auto-detects `docs/seed.md` as starting context. Multi-session scope assembly (we've spanned 3+ sessions on this project) requires a custom in-progress doc (`docs/scope-in-progress.md`) and the user has to know to ask the agent to read it. The agent doesn't natively look for it. Consider standardizing this — e.g., a `docs/scope-resume.md` (or similar) that the skill specifically detects in its startup sequence (alongside seed.md), with a documented schema for status / confirmed decisions / pending topics / instructions-for-next-session. Would also help with /sdd:prd, /sdd:spec, /sdd:sprint — any planning command that might span sessions. Today this works only because we treat the in-progress doc as a "soft contract" with the next session's agent; it'd be much more reliable as a first-class skill behavior. Bonus consideration: a "save and resume later" command (`/sdd:pause`?) that explicitly snapshots the in-progress state and tells the user how to come back.

### 2026-05-01T14:56:41Z
- **Sprint:** 1
- **After command:** /sdd:reflect
- **Project:** sdd-plugin
- **Note:** Consider renaming the /scope step to /discovery and the /prd step to /scope.

### 2026-05-01T15:30:00Z
- **Sprint:** 1
- **After command:** /sdd:feedback
- **Project:** sdd-plugin
- **Note:** create a process with /feedback to transfer feedback from one project to the plugin project for use. Should be runable from either project.

### 2026-05-04T00:00:00Z
- **Sprint:** 0
- **After command:** /sdd:scope
- **Project:** sdd-plugin
- **Note:** Clarifying questions must not advance the interview. Across multiple projects and steps (scope, prd, spec, etc.), the agent has a recurring tendency, when the user asks a clarifying question mid-interview, to (a) provide the clarification, (b) substitute its own recommendation as if it were the user's answer, and (c) advance to the next question. The user's clarifying question is NOT an answer to the current question. Plugin behavior should require the agent to: answer the clarification only, hold position on the current question, never substitute its own recommendation as the user's answer, and only advance the interview pointer on an explicit user answer to the question that was asked. Applies to all interview commands (discovery, scope, spec, plan, refine, polish).

### 2026-05-11T00:00:00Z
- **Sprint:** 0
- **After command:** /sdd:scope
- **Project:** sdd-plugin
- **Note:** Right-size detection for small projects. Interview commands (/discovery, /scope, /prd, /spec, /plan) must recognize when a project is small/simple and not go overboard with rounds, questions, or ceremony. Concretely: detect small-project signals during /discovery (and re-evaluate at each subsequent step) and propagate a "small project" hint downstream; when set, lower round-count expectations, fire fewer Phase 1 beats, lean harder toward a definite "I do not think we need another round, because..." closure, and pick fewer than the suggested 5 questions per round when answers are obviously thin. Motivation: v2 chain is gaining a /prd step; chain length must not punish small projects. This generalizes the existing 2026-04-21 feedback about fast-path /sdd:reflect for simple sprints to every interview command in the chain. Detection signals and exact thresholds deferred to spec time — the right shape is a small heuristic, not a numeric rule.

### 2026-05-12T15:30:00Z
- **Sprint:** 0
- **After command:** /sdd:spec
- **Project:** sdd-plugin
- **Note:** Add a command for setting up the environment (maybe /sdd:sdd). When run the plugin creates the docs directory and the directory for placing reference documents (used for /sdd:discovery). If the environment is already set up, it lets the user know and asks if the user wants anything cleared.

### 2026-05-12T15:31:00Z
- **Sprint:** 0
- **After command:** /sdd:spec
- **Project:** sdd-plugin
- **Note:** Add a --help modifier to all commands. When passed, the plugin explains the command and where the command falls in the normal path of commands.

### 2026-05-12T15:32:00Z
- **Sprint:** 0
- **After command:** /sdd:spec
- **Project:** sdd-plugin
- **Note:** Add a /sdd:backlog tag to manually add something to the backlog.

### 2026-05-12T15:45:00Z
- **Sprint:** 0
- **After command:** /sdd:spec
- **Project:** sdd-plugin
- **Note:** Add an optional /sdd:viability command that can be run anytime. Stress-tests the idea — solves a real problem, audience is real, approach is sound. Distinct from /sdd:discovery (descriptive/exploratory) so the tone problem of mixing supportive exploration with evaluative critique stays separated.

### 2026-06-22T00:00:00Z
- **Sprint:** 1
- **After command:** /sdd:retro
- **Project:** sdd-plugin
- **Note:** /sdd:build should auto-defer to project conventions. In the v5 cycle, the autonomous-build SKILL.md instructs subagents to commit when done, but this repo's AGENTS.md forbids auto-commit during /sdd:build ("Clean state = last user commit"). The conflict was resolved correctly (subagents did not commit) only because the maintainer knew AGENTS.md overrides the built-in behavior. The plugin should detect and defer to project-convention files (e.g. AGENTS.md commit discipline) automatically, rather than relying on maintainer knowledge of the override. Caused no friction this cycle but is latent risk for any future autonomous build in a repo with its own commit/convention rules. Surfaced during /sdd:retro for v5.
