# Learning Hackathon submission: draft answers

Draft answers for the Devpost Learning Hackathon: Spec Driven Development submission form. Review, edit, and approve before passing to Claude Cowork to fill the form. Character counts are noted where the form imposes a limit.

Fields marked **[NEEDS YOUR INPUT]** require something only Jason knows: hours spent, file uploads, dropdown choices. Fields marked **[PICK ONE]** offer alternatives. Choose the one you like best and delete the others.

---

## Page 2: project overview

### Project name (60 char limit)

**[PICK ONE]**

- `Spec-Driven Development Plugin` (30)
- `Spec-Driven Development for Claude Code` (39)
- `SDD Plugin: Spec-Driven Development for Claude Code` (52)

Recommendation: the third option. It's specific about both what it is and what it runs on.

### Elevator pitch (200 char limit)

**[PICK ONE]**

- `A Claude Code plugin that turns spec-driven development into a guided workflow. Structured interviews from scoping through retrospective, producing planning artifacts at every stage.` (180)
- `A Claude Code plugin that interviews you through scope, PRD, spec, sprint planning, build, and retrospective. Turns ad-hoc AI prompting into a repeatable, artifact-producing workflow.` (185)
- `Claude Code plugin for spec-driven development. Structured interviews from scope through retrospective, with surgical context loading and persistent artifacts that survive a /clear.` (181)

Recommendation: the third option. It names the two technically interesting ideas (surgical context loading, persistent artifacts surviving `/clear`) instead of listing phases.

---

## Page 3: project details

### About the project (Project story)

## Inspiration

After 30 years of building software, I have a mental model of what good planning looks like, and watching AI coding agents skip past it has been frustrating. People jump straight to prompting without structured planning, which leads to vague instructions, scope creep, and builds that drift from intent. Every new chat session starts from zero. Context evaporates, decisions get re-litigated, and △ feedback from earlier sessions disappears into the void.

I had been using my own manual workflow for a while: ask the AI to generate a list of planning questions, then answer them one at a time. It worked, but the questions went stale by the time I finished them because ideas evolve as you talk. Then I came across Devpost's hackathon-in-a-plugin curriculum and recognized exactly the structure I had been hand-rolling. So I decided to use my Devpost hackathon spot to build a more general version of that workflow as its own Claude Code plugin, and use the workflow to build the workflow.

## What it does

The SDD Plugin turns spec-driven development into a guided, repeatable Claude Code workflow. Eleven slash commands organized into three phases:

- **Planning phase:** `/sdd:onboard` (one-time per machine; captures user profile and communication preferences), `/sdd:scope`, `/sdd:prd`, `/sdd:spec`. Each runs a one-question-at-a-time interview with optional deepening rounds, producing a markdown artifact and updating CLAUDE.md and AGENTS.md so the AI has authoritative operating instructions.
- **Sprint loop:** `/sdd:sprint` pulls a logical batch of work from the PRD into a buildable sprint with verification steps. `/sdd:build` executes it (step-by-step or autonomous mode). `/sdd:iterate` adds polish work mid-sprint. `/sdd:reflect` does the sprint retrospective, splits partially-completed stories, and recommends the next move. `/sdd:refine` runs unvetted PRD additions through a compressed planning interview.
- **Project close:** `/sdd:retro` synthesizes a project-level retrospective across all sprints. `/sdd:feedback` runs anytime, capturing plugin improvement notes with auto-context (timestamp, sprint, last command, project) without interrupting flow.

The result is markdown artifacts at every stage: scope, PRD, spec, sprint files, CLAUDE.md, AGENTS.md, process notes, reflection, retrospective. All version-controlled in the project directory and read fresh by the AI each session.

## How we built it

I built the plugin as a pure markdown skills architecture. No runtime code, no build step, no dependencies. All behavior lives in SKILL.md files with YAML frontmatter, plus reference files and templates. I adapted it from the hackathon-in-a-plugin curriculum's pattern but stripped the hackathon-specific framing (no learner profiles, no time-boxing to 3-4 hours, no submission artifacts) and rebuilt around a project-agnostic workflow with sprints, retrospectives, and a backlog.

The build itself ran through the SDD process recursively. I ran `/scope`, `/prd`, `/spec`, and `/checklist` on the plugin idea, generating the planning documents that are part of this submission. Then `/build` worked through 16 checklist items in autonomous mode, with verification checkpoints every 3-4 items and a commit per item. Distribution went to a marketplace repo at `github.com/zabuuq/sdd-plugin` so anyone can install it with `/plugin marketplace add zabuuq/sdd-plugin`.

## Challenges we ran into

- **Auto-compaction on a parallel project.** While running another long build with Claude Code, I hit context-window auto-compaction mid-stream. That pain became architecture: `/sdd:build` loads only the specific spec subsection and PRD story for the current checklist item, never the full documents. This is the single most important technical decision in the plugin, and it came out of failure, not foresight.
- **Lost △ feedback across sessions.** While working on `/spec`, I went back to old `/scope` and `/prd` session outputs and discovered that flagged concerns had evaporated with the conversations. Nothing carried them forward. That gap produced `docs/open-concerns.md`: a dedicated file every command reads on startup, resolves what it can (including implicit resolutions, marked explicitly), defers what it can't with rationale, and appends new concerns from its own work.
- **PRD session pause.** The PRD interview ran long enough that I had to pause partway through and resume in a later session. That itself became a useful test of whether the planning artifacts could carry context across a true session break. They could, but it forced me to think harder about state files and resumption.
- **GitHub API push froze.** When publishing the distribution repo, pushing 27 files via the GitHub API hung. I pivoted to git CLI, which worked immediately. Captured in process notes as a small but useful adaptation.
- **Post-sprint ceremony felt too heavy.** During dogfooding on three downstream projects, the post-sprint cluster (`/sdd:reflect` + `/sdd:refine` + `/sdd:retro`) started to feel like overhead: too much structured introspection for sprints where the honest answer was "it went fine." I'm now using the plugin on itself to consolidate these into a single more flexible command.

## Accomplishments that we're proud of

- **All 16 checklist items shipped, validated on three projects.** The plugin isn't a demo. One downstream project is complete, two are still in-flight, and the workflow has held up across all three.
- **Surgical context loading works.** Across multi-sprint dogfooding I haven't hit auto-compaction once. Exactly the failure mode that motivated the design.
- **The self-improvement loop is real.** I sketched it during `/scope`: the plugin captures feedback during use, and later you take that feedback to the plugin's own repo and run the SDD process on the plugin itself. It's now running. I'm using the plugin to redesign the plugin.
- **Zero scope creep.** The PRD's "What we'd add with more time" list (GitHub integration, Jira/Linear, multi-agent, automated improvement) stayed there. Discipline I credit entirely to the planning process imposing it.
- **Open source under MIT, shipped to a public marketplace.** Anyone can install it.

## What we learned

**Context lives in files, not in chat history.** The mechanisms that earned their keep in real use were all about persisting context across `/clear` boundaries: surgical loading, the open-concerns file, `lastCommand` tracking, CLAUDE.md as architectural summary. Once you internalize this, a whole class of "the AI forgot" failures stops happening.

**Adaptive interviewing beats pre-built question lists.** With my old workflow, by the time I finished a stale question list, ideas had evolved past it. Generating questions one at a time, responsive to what's already been said, keeps the conversation fresh and surfaces things upfront question-batching never could.

**Friction at boundaries gets noticed; friction in-flight gets noticed because it's not there.** The post-sprint retrospective ceremony felt heavy because it interrupted flow. `/sdd:feedback` felt right because it captures notes without interrupting anything. The cleanest proof that `/feedback` works: I used it to flag the heaviness of the other commands.

**Building the tool forced me to discover parts of the methodology, rather than only encoding what I already knew.** The sprint concept emerged during `/prd`. The open-concerns mechanism emerged during `/spec`. The self-improvement loop emerged during `/scope`. Spec-driven development isn't a fixed practice you apply. It's a thinking discipline that evolves as you push on it.

## What's next for SDD Plugin: Spec-Driven Development for Claude Code

- **Post-sprint command consolidation.** Collapse `/sdd:reflect`, `/sdd:refine`, and `/sdd:retro` into a single more flexible command that scales effort to what the sprint surfaced. Already in flight, using the plugin on itself.
- **GitHub integration as an optional tracking layer.** Issues, milestones, labels, and project boards alongside (or replacing) markdown sprint files. Held out of v1 deliberately, but a natural next step.
- **Jira and Linear integration.** Same concept, different platforms. Would need an abstraction layer over the tracking backend.
- **Project templates.** Pre-seeded scope, PRD, and spec for common project types (web app, API, CLI tool) to accelerate the planning phases when the shape is well-known.
- **Multi-agent support.** AGENTS.md is generated now, but dedicated config files for Gemini, Codex, and Copilot would broaden where the plugin is useful.
- **Sharing with the community.** MIT licensed, public marketplace, and I'm planning to share with friends, colleagues, and back to the Devpost hackathon community where the inspiration came from.

### Built with

```
markdown
yaml
claude-code
plugin-skills
git
github
```

(No traditional stack. The plugin is pure markdown skills with no runtime code, no build step, and no dependencies. The "stack" is the Claude Code plugin system itself.)

### "Try it out" links

- **Distribution repo (install this):** `https://github.com/zabuuq/sdd-plugin`
- **Development repo (planning artifacts, process notes, reflection):** `https://github.com/zabuuq/devpost-hackathon-sdd-plugin` **[NEEDS YOUR INPUT: confirm exact repo URL]**
- **Install command:**
  ```
  /plugin marketplace add zabuuq/sdd-plugin
  /plugin install sdd@sdd-marketplace
  ```

### Project media

- **Image gallery: [NEEDS YOUR INPUT].** Optional. Could be a screenshot of `/sdd:scope` running in Claude Code, the docs/ folder structure, or the marketplace listing. Skip if you'd rather not.
- **Video demo link: [NEEDS YOUR INPUT].** Optional. If you record a quick walkthrough of running `/sdd:onboard` then `/sdd:scope` on a sample project, link it here. YouTube, Vimeo, Vidyard, or Youku.

---

## Page 4: additional info

### Upload a file (optional)

**[NEEDS YOUR INPUT].** Suggestion: attach `docs/reflection.md`, or zip the `docs/` folder so reviewers can see the full artifact set. 25MB max.

### How far through the process did you get?

**[NEEDS YOUR INPUT: pick from the dropdown]**

Likely answer: **"Completed all phases"** or whatever the equivalent option is. You ran `/onboard`, `/scope`, `/prd`, `/spec`, `/checklist`, `/build` (16/16 items), `/iterate` (1 cycle for distribution setup), and `/reflect`.

### How many hours did the full experience take?

**[NEEDS YOUR INPUT].** Estimate based on session timestamps in process-notes.md. The planning phases ran across April 7–9. The build phase ran through commits dated 1eb3a81 through a929303. Distribution setup was a separate session. A rough range from the artifact dates: **20–40 hours** spread across multiple sessions. You'll have a better number than I do.

### Upload your files to file storage software and share them here (e.g. Google Drive)

**[NEEDS YOUR INPUT].** Optional. If you want reviewers to see the source artifacts beyond what's in the GitHub repos, share a Google Drive folder with `docs/`, `process-notes.md`, and `docs/reflection.md`.

### How would you rate the overall experience?

**[NEEDS YOUR INPUT: pick from the dropdown]**

Suggested rating: high. The process worked. You finished, you shipped, and you're using the result. If you have a concern, rate it honestly.

### Which parts of the process were most valuable to you?

The mechanisms that fight context loss across `/clear` boundaries: surgical context loading for `/build`, the `open-concerns.md` file for cross-phase continuity, and `/feedback` for in-flight capture without interrupting flow. These weren't ceremonial. They earned their keep in dogfooding on three downstream projects.

The structured interview discipline was also valuable. My old workflow involved working with the AI to build a list of questions and then answering them one at a time. The questions would go stale by the time I got through them because ideas change and new things get discovered along the way. Adaptive interviewing with deepening rounds keeps the questions fresh. They respond to what's already been surfaced instead of being locked in upfront.

### Where did you get stuck or what felt like a waste of time?

The post-sprint retrospective ceremony felt too heavy in real use. `/sdd:reflect` plus `/sdd:refine` plus `/sdd:retro` is too much structured introspection for a workflow you're running yourself. After several sprints where the honest answer was "the sprint went fine," the same retrospective questions started feeling like overhead. I'm now using the plugin itself to consolidate these into a single more flexible command, which is the self-improvement loop the plugin was designed to enable. So the friction itself became useful signal.

The PRD session also had to be paused partway through and resumed later. Having a session-state file to pick up from helped, but it's a noticeable cost of long planning interviews.

### Did this experience change how you approached building with a coding agent?

Yes. The biggest shift: **context lives in files, not in chat history.** Persisting plans, decisions, and concerns in the project directory, and treating CLAUDE.md and AGENTS.md as living operating instructions the AI reads fresh each session, eliminates a whole class of "the AI forgot" failures. Combined with `/clear` discipline between phases, this is the antidote to chatbot amnesia and context rot.

The other shift was around when to interview vs. when to prompt. For anything that requires real planning, anything where bad early decisions compound, flipped interaction (the AI asks you questions) is dramatically better than one-shot prompting. For tactical work inside a well-specified plan, direct prompting is fine. The trick is knowing which mode you're in.

### How likely are you to use this kind of workflow in your day to day work? (255 char limit)

Already running it on three of my own projects: one complete, two in flight. Continued use is certain. Open source under MIT, and I'm sharing it with friends and colleagues. (173)

### Would you recommend this to a colleague?

**[NEEDS YOUR INPUT: likely yes; checkbox or radio]**

### What would you change about this experience? (255 char limit)

Write acceptance criteria for the experience of running a tool repeatedly, not single-run correctness. A "takes under two minutes when nothing notable happened" criterion would have caught the post-sprint ceremony-heaviness three projects earlier. (247)

---

## Notes for Claude Cowork

- The form has four pages: Manage team (page 1, not shown), Project overview (page 2), Project details (page 3), Additional info (page 4), then Submit.
- Character limits are strict on page 2. The chosen option will fit, but verify after pasting.
- Several fields on page 4 are dropdowns or checkboxes the user must select manually. Anywhere you see **[NEEDS YOUR INPUT]** or **[PICK ONE]**, surface that to the user before submitting.
- Don't submit the form without explicit user approval on the final page.
