# SDD v3 â€” Product Requirements

## Problem Statement

Running the SDD plugin costs more tokens than it should. Across v2, the per-command load stack (sdd-guide plus its reference files), the document templates, and the real-time process-notes guidance all grew â€” so every command burns input tokens on its own machinery before doing any work. Agent responses also carry verbosity overhead: sycophancy padding and over-explanation that spend output tokens without adding value.

v3 makes the plugin **cheaper to run** without changing what it does. It trims the plugin's own input footprint and tightens the agent's output, mining proven techniques from the Caveman project and reimplementing them as native markdown. Behavior, accuracy, and decision history are preserved, and the plugin stays zero-runtime pure markdown â€” no hook, no scripts, no MCP middleware. The audience is unchanged from v2: Jason and the local tech group who run SDD on their own projects.

## User Stories

### Caveman Technique Mining

- As the SDD maintainer, I want Caveman's compression and constraint techniques extracted into a documented inventory so that v3's trims are grounded in a proven reference instead of ad-hoc guesses.
  - [x] An inventory artifact lists each Caveman technique studied (constraint-skill presentation rules, compression heuristics) with a one-line description of what it does. `tmca`
  - [x] Each listed technique is marked port-to-markdown or no-port, and every no-port entry names the reason it cannot port (machinery-dependent, etc.). `tmcb`
  - [x] The inventory explicitly records Caveman's hook, `caveman-compress`, and `caveman-shrink` MCP middleware as out-of-scope machinery for v3, not techniques to port. `tmcc`

### Input-Footprint Reduction

- As the SDD maintainer, I want a per-command load map so that I can see which reference files each command actually loads at startup and which are over-included.
  - [x] A load-map artifact lists, for every `/sdd:*` command, the sdd-guide reference files it loads at startup. `lpma`
  - [x] Each reference in the map is flagged load-justified or over-included for that command, resolving the open concern about per-command over-inclusion. `lpmb`

- As the SDD maintainer, I want the reference stack trimmed and consolidated so that the per-command load carries less redundant text.
  - [x] Every file in `references/` has a recorded review outcome: trimmed, consolidated, or left-as-is with a reason. `rfsa`
  - [x] At least one reference flagged over-included in the load map is no longer loaded by commands that do not need it, verifiable in those commands' `SKILL.md` load lists. `rfsb`
  - [x] No trimmed or consolidated reference drops a behavioral rule: each rule from a removed or merged file is present in its new location and traceable by name. `rfsc`

- As the SDD maintainer, I want verbose templates tightened so that every emitted document costs fewer input tokens to produce.
  - [x] Each template in `templates/` has a recorded review and removes redundant prose or instructional scaffolding. `tpla`
  - [x] A diff of each revised template against its `docs/archive/v2/` counterpart shows reduced word count with no emitted-document section lost. `tplb`

- As the SDD maintainer, I want real-time process-notes guidance leaned out so that the guidance itself stops bloating the load without losing decision history.
  - [x] Process-notes guidance in sdd-guide and the command `SKILL.md` files is condensed, and the four capture categories (decisions, pushback, difficult questions, pivots) remain named. `pnta`
  - [x] The append-only, per-phase process-notes behavior is preserved: no instruction is added that prunes, edits, or summarizes away historical entries. `pntb`

### Output Verbosity Reduction

- As an SDD user, I want terseness rules baked into sdd-guide so that every command produces shorter, denser responses without per-command tuning.
  - [x] sdd-guide contains an explicit terseness rule set governing response length and over-explanation, identifiable as a named section or rules. `trsa`
  - [x] The terseness rules live in one shared location (sdd-guide) and are not duplicated per command; commands inherit them by loading sdd-guide. `trsb`

- As an SDD user, I want explicit anti-sycophancy rules so that responses drop praise-padding and filler.
  - [x] sdd-guide states an anti-sycophancy rule that prohibits praise-padding openers (e.g. "great question," "great idea") and equivalent filler. `asya`
  - [x] The rule enumerates concrete banned patterns rather than only a general instruction to be less sycophantic. `asyb`

- As an SDD user, I want the verbosity rules applied uniformly so that no command silently opts out.
  - [x] Every command `SKILL.md` loads sdd-guide, and no command carries instructions that override or contradict the terseness and anti-sycophancy rules. `inha`

### Behavior Preservation & Verification

- As the SDD maintainer, I want behavior preserved so that the efficiency pass never silently drops a workflow step or breaks an artifact.
  - [x] A step-by-step comparison of each command against its `docs/archive/v2/` form shows no ordered workflow step removed. `prva`
  - [x] A full run of the command chain still produces complete artifacts (scope, PRD, spec, sprint, retro) with all required sections present. `prvb`

- As the SDD maintainer, I want the zero-runtime markdown posture verified so that the efficiency work introduces no runtime surface.
  - [x] `claude plugin validate .` exits successfully on the trimmed plugin. `vala`
  - [x] `plugins/sdd/` contains no hook, script, or MCP dependency: no executable files and no hook entries in the plugin manifest. `valb`

- As the SDD maintainer, I want a dogfood comparison so that the efficiency gain is confirmed by inspection against v2.
  - [x] A dogfood run of the command chain on a real small project is completed and its artifacts retained. `dfra`
  - [x] The dogfood run's input load and output verbosity are compared by inspection against the equivalent `docs/archive/v2/` artifacts, and the comparison is recorded. `dfrb`
  - [x] The recorded comparison states the rough input and output reduction observed and notes whether it lands near the ~23% input / ~32% output guide-rails, with no requirement to hit them exactly. `dfrc`

## Unvetted

<!-- New items that surface during sprints and have not yet been refined land here. They are not yet approved requirements. /sdd:refine processes this section before future sprints, promoting items into approved stories or deferring them. Leave this section and this marker in place across PRD rewrites even when its contents change. -->

## What We're Building

A token-efficiency revision of the SDD plugin. The plugin's commands, workflow, and output artifacts stay the same; what changes is cost. v3 mines the Caveman project for compression and presentation-constraint techniques, reimplements the portable ones as native markdown inside sdd-guide, and uses them to trim the per-command reference load, tighten verbose templates, lean out process-notes guidance, and bake terseness plus anti-sycophancy rules into the shared guide so every command inherits terser output. The whole pass is held to a hard line: no workflow step lost, artifacts still complete, decision history intact, and the plugin still passing `claude plugin validate .` as zero-runtime pure markdown. Success is judged by inspection against the archived v2 artifacts, with rough ~23% input / ~32% output reductions as sanity-check guide-rails rather than contractual gates.

## What We'd Add With More Time

Deferred items: see `docs/backlog.md`.

## Non-Goals

- Adopting Caveman's executable machinery (the auto-activation hook, `caveman-compress`, the `caveman-shrink` MCP middleware). v3 mines techniques only and stays markdown-only. (Backlogged.)
- Building a token-counting or instrumentation harness. Measurement is by eyeball against `docs/archive/v2/`.
- Changing plugin behavior, the command set, or any workflow step. v3 is efficiency only.
- Evaluating Get Shit Done or Superpowers as influences. (Backlogged.)
- Any audience shift. The users are the same as v2.
