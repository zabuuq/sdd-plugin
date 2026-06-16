# SDD v3 — Technical Specification

## Posture

This spec is a **trim-plan design doc**, following v2's delta-and-resolution posture. It names *what* v3 changes and *why*, and where each change lands. The actual line-edits are left to `/sdd:plan` (sequencing) and `/sdd:build` (execution). "How" detail that outlives the cycle lives in `plugins/sdd/skills/sdd-guide/references/*.md`; this spec points at targets, it does not restate reference content.

v3 changes nothing about plugin behavior. Every section below is an efficiency trim held to one hard line: **no workflow step lost, no behavioral rule dropped, artifacts still complete.**

**Naming rule:** no artifact, file, or rule that ships or persists carries "Caveman" as a label. The source is credited in prose where relevant; deliverables are named by what they do (`docs/efficiency-techniques.md`, not `caveman-inventory.md`; "terseness rules," not "Caveman rules"). PRD epic cross-references cite the PRD heading verbatim — that is a pointer, not a label.

## Stack

Unchanged from v2.

| Layer | Choice | Reference Docs |
|-------|--------|----------------|
| Platform | Claude Code plugin (skills-based) | https://code.claude.com/docs/en/plugins |
| Language | Markdown (SKILL.md + YAML frontmatter) | https://code.claude.com/docs/en/skills |
| Runtime code | None — behavior is markdown instructions | — |
| Distribution | GitHub marketplace repo | https://code.claude.com/docs/en/plugin-marketplaces |
| Validation | `claude plugin validate .` | https://code.claude.com/docs/en/plugins-reference |
| Source studied | juliusbrussee/caveman (MIT, technique reference only) | https://github.com/juliusbrussee/caveman |

No build step, no runtime dependencies, no compiled code. The zero-runtime invariant is itself a v3 acceptance criterion (`valb`).

## Runtime & Deployment

Unchanged from v2. Claude Code CLI/desktop with plugin support; GitHub marketplace distribution. Version bump on ship: `plugins/sdd/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` move together (next bump TBD at release, not in this spec).

## Architecture Overview

v3 has no new components. The "architecture" is the existing per-command load stack, and the work is **subtractive** — trimming what each command pulls in and how much it emits. Two persistent analysis artifacts drive and record the trims; the rest is edits to existing `SKILL.md`, `references/*.md`, and `templates/*.md` files.

```
  docs/efficiency-techniques.md ──┐  (research input: techniques + port verdicts)
                                  ├──► trim edits to plugins/sdd/skills/**
  docs/load-map.md ───────────────┘  (driver: per-command load + over-inclusion flags)
                                          │
                                          ├── references/*.md   (trim / consolidate / leave)
                                          ├── command SKILL.md   (drop over-included loads)
                                          ├── sdd-guide ## Output Constraints  (NEW section)
                                          ├── templates/*.md     (tighten prose)
                                          └── process-notes guidance  (lean, append-only kept)
                                          │
                                          ▼
                          rule-inventory check (audit trail in process notes)
                                          ▼
                          claude plugin validate .  +  dogfood vs docs/archive/v2/
```

### Data Flow — the per-command load order

The only "data flow" v3 touches is what a command loads at startup. This is exactly what `docs/load-map.md` profiles.

```
/sdd:<command> invoked
   │
   ├─► plugins/sdd/CLAUDE.md  ──► loads skills/sdd-guide/SKILL.md   (every command, always)
   │                                  │
   │                                  └─► sdd-guide pulls its own references as needed
   │
   └─► command SKILL.md  ──► "Load:" list  ──► references/*.md  +  templates/*.md
                                  │
                                  ▼
                        ★ over-inclusion lives here ★
              (a reference named in the Load list but not actually
               needed for that command's behavior = the trim target)
```

The load map enumerates the right column for every `/sdd:*` command and flags each reference **load-justified** or **over-included**. Removing an over-included entry from a command's `Load:` list is the highest-leverage input-footprint cut and resolves the standing over-inclusion open concern.

## Efficiency Technique Inventory

Implements `prd.md > Caveman Technique Mining`.

The research artifact that grounds every trim in a studied technique rather than a guess. Lives at `docs/efficiency-techniques.md`. Built first (it is input to everything downstream).

### Artifact: `docs/efficiency-techniques.md`

- One row per technique studied from the source toolkit — presentation/constraint rules and compression heuristics — each with a one-line description of what it does. (`tmca`)
- Each row marked **port-to-markdown** or **no-port**; every no-port row names why it can't port (machinery-dependent, etc.). (`tmcb`)
- A dedicated section records the source's hook, `caveman-compress`, and `caveman-shrink` MCP middleware as **out-of-scope machinery** for v3 — explicitly not techniques to port. (`tmcc`)
- Naming: the file credits the source project in prose; the filename and rule labels do not carry its name.

## Load Map

Implements `prd.md > Input-Footprint Reduction`.

The driver artifact for the input-footprint trims. Lives at `docs/load-map.md`. Built second, from the inventory plus a sweep of every command `SKILL.md` `Load:` list.

### Artifact: `docs/load-map.md`

- For every `/sdd:*` command, lists the sdd-guide reference files it loads at startup. (`lpma`)
- Each reference flagged **load-justified** or **over-included** for that command — this flagging *is* the resolution of the per-command over-inclusion open concern. (`lpmb`)
- Doubles as the worksheet for the reference-stack trim (below) and the home of the rule-inventory audit trail (see Verification).

## Reference Stack Trim

Implements `prd.md > Input-Footprint Reduction`.

### Per-file review outcome

Every file in `plugins/sdd/skills/sdd-guide/references/` gets a recorded outcome: **trimmed**, **consolidated**, or **left-as-is with a reason**. (`rfsa`) The record lives alongside the load map.

### Over-inclusion removal

At least one reference flagged over-included in the load map is removed from the `Load:` list of commands that don't need it — verifiable directly in those commands' `SKILL.md`. (`rfsb`)

### Preservation guard

No trim drops a behavioral rule. Each rule from a removed or merged file is present in its new location and traceable **by name**. (`rfsc`) Enforced by the rule-inventory check (see Verification) — this is the hard line for this section.

## Template Tightening

Implements `prd.md > Input-Footprint Reduction`.

### Per-template review

Each template in `plugins/sdd/skills/sdd-guide/templates/` is reviewed and has redundant prose / instructional scaffolding removed. (`tpla`)

### Diff against v2

Each revised template diffs against its `docs/archive/v2/` counterpart showing reduced word count with **no emitted-document section lost**. (`tplb`) Section-presence is the preservation check here — a template may lose words but not headings the output document needs.

## Process-Notes Leaning

Implements `prd.md > Input-Footprint Reduction`.

### Condense the guidance

Process-notes guidance in sdd-guide and the command `SKILL.md` files is condensed. The four capture categories — **decisions, pushback, difficult questions, pivots** — remain named after the trim. (`pnta`)

### Keep append-only

No instruction is added that prunes, edits, or summarizes away historical entries. The append-only, per-phase behavior is preserved verbatim in intent. (`pntb`) Leaning the *guidance text* must not lean the *retained history*.

## Output Constraints

Implements `prd.md > Output Verbosity Reduction`.

A **new dedicated section** in `plugins/sdd/skills/sdd-guide/SKILL.md` — `## Output Constraints` — that absorbs and sharpens the existing Tone "no sycophancy" line into two named, enumerated rule sets. Lives in sdd-guide only; commands inherit it by loading sdd-guide, and nothing is duplicated per command. The current Tone line becomes a one-line pointer to this section.

### Terseness rules

A named, enumerated rule set governing response length and over-explanation. (`trsa`) Single shared location, not restated per command. (`trsb`)

### Anti-sycophancy rules

A named rule prohibiting praise-padding openers ("great question," "great idea") and equivalent filler. (`asya`) Enumerates **concrete banned patterns**, not just a general "be less sycophantic" instruction. (`asyb`)

### Uniform inheritance

Every command `SKILL.md` loads sdd-guide, and no command carries instructions that override or contradict the terseness/anti-sycophancy rules. (`inha`) Verified as part of the behavior-preservation sweep.

## Verification

Implements `prd.md > Behavior Preservation & Verification`.

### Rule-inventory check

The traceability mechanism for `rfsc` and `prva`. Before trimming any reference or `SKILL.md`, record the named behavioral rules it contains; after, confirm each is present in its new location. The before/after record is the audit trail, kept in `docs/load-map.md` (or the process notes for that sprint). This is how "no rule dropped" is proven rather than asserted.

### Workflow-step comparison

A step-by-step comparison of each command against its `docs/archive/v2/` form shows no ordered workflow step removed. (`prva`) A full chain run still produces complete artifacts (scope, PRD, spec, sprint, retro) with all required sections. (`prvb`)

### Zero-runtime check

`claude plugin validate .` exits successfully on the trimmed plugin. (`vala`) `plugins/sdd/` contains no hook, script, or MCP dependency — no executable files, no hook entries in the manifest. (`valb`)

### Dogfood comparison

The dogfood target is **v3's own planning chain** — this scope/PRD/spec run and what follows — treated as the real small project. (`dfra`) Its input load and output verbosity are compared by inspection against the equivalent `docs/archive/v2/` artifacts, and the comparison is recorded. (`dfrb`) The record states the rough input/output reduction observed and notes whether it lands near the ~23% input / ~32% output guide-rails — no requirement to hit them. (`dfrc`)

## File Structure

v3 adds two persistent docs and one sdd-guide section. Everything else is edits to existing files.

```
docs/
  efficiency-techniques.md   # NEW — technique inventory + port verdicts (tmca-tmcc)
  load-map.md                # NEW — per-command load + over-inclusion flags + rule-inventory audit (lpma-lpmb, rfsa, rfsc)
  spec.md                    # this file
  archive/v2/                # negative reference — dogfood comparison baseline (unchanged)

plugins/sdd/skills/sdd-guide/
  SKILL.md                   # EDIT — add ## Output Constraints; Tone line → pointer; lean process-notes guidance (trsa-asyb, pnta-pntb)
  references/*.md            # EDIT — trim/consolidate/leave per recorded outcome (rfsa-rfsc)
  templates/*.md             # EDIT — tighten prose, no section lost (tpla-tplb)

plugins/sdd/skills/<command>/SKILL.md   # EDIT — drop over-included Load: entries; lean process-notes guidance (rfsb, inha, pnta)
```

No files are deleted by v3 unless a reference is fully consolidated into another (in which case `rfsc` traceability applies).

## Testing Strategy

No automated tests — consistent with the plugin's zero-runtime posture. Verification is structural and behavioral, defined in `## Verification` above:

- **Structural:** `claude plugin validate .` (`vala`), zero-runtime file/manifest check (`valb`).
- **Preservation:** rule-inventory check (`rfsc`), workflow-step comparison (`prva`), template section-presence (`tplb`).
- **Behavioral:** full dogfood chain run producing complete artifacts (`prvb`, `dfra`).
- **Efficiency:** dogfood input/output inspection vs. `docs/archive/v2/` (`dfrb`, `dfrc`) — eyeballed, no token-counting harness (a stated non-goal).

Coverage expectation: every AC has a named verification above; the hard line is preservation — a trim that can't prove its rules survived is reverted, not shipped.

## Key Technical Decisions

| Decision | Why | Tradeoff |
|----------|-----|----------|
| Spec is a trim-plan design doc, not concrete edits | Keeps spec at "what/why"; line-edits belong to plan/build; matches v2 posture | Plan/build carry more decision weight; spec can't be diffed against a finished artifact |
| Analysis artifacts as standalone `docs/` files | Cited as deliverables; must persist for the verification trail; keeps spec lean | Two more living docs to maintain |
| No "Caveman" label on shipped/persisted artifacts | Separates SDD's additions from the source toolkit; clean provenance | Slight indirection — source credited in prose, not in names |
| New `## Output Constraints` section vs. expanding Tone | Satisfies "named, enumerated rule set" (`trsa`); centralizes; Tone stays about voice | One more sdd-guide section; a pointer left behind in Tone |
| Rule-inventory check as the preservation mechanism | Makes "no rule dropped" provable, not asserted (`rfsc`, `prva`) | Adds a record-keeping step to every trim |
| Dogfood = v3's own planning chain | No throwaway work; already a real small project; matches "eyeball vs v2" posture | Comparison is artifact-vs-artifact, not a from-scratch chain run |
| Measurement by inspection, not instrumentation | Stated non-goal; keeps the cycle lightweight | Reduction figures are rough, not exact — accepted by `dfrc` |

## Open Issues

- **Net effect of consolidation on per-command load.** Merging a reference can *raise* the load for commands that loaded only the smaller original. The load map must check that consolidation is a net trim per command, not just globally. → resolve during `/sdd:plan` sequencing / `/sdd:build`.
- **Order of operations between the two artifacts and the trims.** Inventory → load map → trims is the intended order, but the rule-inventory audit spans all three. Exact sprint sequencing is a `/sdd:plan` decision. → resolve at `/sdd:plan`.
- **Whether native-only trims reach the halved benchmark.** Carried over from open-concerns; unresolvable until dogfood time. If trims fall well short, that's the trigger to revisit the backlogged "pull the machinery" item. → resolve at dogfood (`/sdd:build`).

## Out of Scope

Deferred items: see `docs/backlog.md`.
