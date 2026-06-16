# Sprint 1

The whole v3 token-efficiency cycle in one sprint. Items are ordered by the spec's dependency chain: the technique inventory feeds everything, the load map drives the input-footprint trims, then the trims, then verification. End state: a leaner plugin — trimmed reference loads, tightened templates, terser output — that still passes `claude plugin validate .` with no workflow step or behavioral rule lost, plus a recorded dogfood comparison against v2.

Out of scope: adopting Caveman's executable machinery (hook / compressor / MCP middleware) and any behavior change — see `docs/backlog.md`.

## Build Preferences
- **Build mode:** autonomous

## Checklist

### Item 1 — Efficiency technique inventory
- [x] Build `docs/efficiency-techniques.md` — technique inventory, port verdicts, out-of-scope machinery note [PRD: tmca, tmcb, tmcc]
- **PRD ref:** `prd.md > Caveman Technique Mining`
- **Spec ref:** `spec.md > Efficiency Technique Inventory`
- **What to build:** Study the Caveman toolkit and record an inventory of its compression/constraint techniques as a standalone doc. Credit the source in prose only — no source name in the filename or rule labels (per spec naming rule).
- **Acceptance criteria:**
  - [x] `tmca` Inventory lists each technique studied (constraint-skill presentation rules, compression heuristics) with a one-line description of what it does.
  - [x] `tmcb` Each technique marked port-to-markdown or no-port; every no-port entry names why it cannot port (machinery-dependent, etc.).
  - [x] `tmcc` The inventory explicitly records Caveman's hook, `caveman-compress`, and `caveman-shrink` MCP middleware as out-of-scope machinery, not techniques to port.
- **Verification:** Open `docs/efficiency-techniques.md`; confirm every row has a description and a port/no-port verdict, every no-port row has a reason, and the machinery exclusion is present. Confirm no shipped/persisted label uses the "Caveman" name.

---

### Item 2 — Per-command load map
- [x] Build `docs/load-map.md` — per-command reference loads with over-inclusion flags [PRD: lpma, lpmb]
- **PRD ref:** `prd.md > Input-Footprint Reduction > Per-command load map`
- **Spec ref:** `spec.md > Load Map`
- **What to build:** Sweep every `/sdd:*` command `SKILL.md` `Load:` list and record, per command, which sdd-guide reference files it loads at startup, flagging each. This artifact also hosts the rule-inventory audit trail used by Items 4 and 8.
- **Acceptance criteria:**
  - [x] `lpma` Load-map artifact lists, for every `/sdd:*` command, the sdd-guide reference files it loads at startup.
  - [x] `lpmb` Each reference in the map is flagged load-justified or over-included for that command (resolving the per-command over-inclusion open concern).
- **Verification:** Cross-check the map against each command's `SKILL.md` `Load:`/`Read:` list — every loaded reference appears with a flag; spot-check that over-included flags are defensible.

---

### Item 3 — Output Constraints (terseness + anti-sycophancy)
- [x] Add `## Output Constraints` section to sdd-guide; repoint the Tone line [PRD: trsa, trsb, asya, asyb]
- **PRD ref:** `prd.md > Output Verbosity Reduction > Terseness & anti-sycophancy rules`
- **Spec ref:** `spec.md > Output Constraints`
- **What to build:** A new dedicated `## Output Constraints` section in `plugins/sdd/skills/sdd-guide/SKILL.md` holding two named, enumerated rule sets (terseness, anti-sycophancy), derived from the port-to-markdown techniques in Item 1. The existing Tone "no sycophancy" line becomes a one-line pointer so nothing is duplicated.
- **Acceptance criteria:**
  - [x] `trsa` sdd-guide contains an explicit terseness rule set governing response length and over-explanation, identifiable as a named section/rules.
  - [x] `trsb` The terseness rules live in one shared location (sdd-guide), not duplicated per command; commands inherit by loading sdd-guide.
  - [x] `asya` sdd-guide states an anti-sycophancy rule prohibiting praise-padding openers ("great question," "great idea") and equivalent filler.
  - [x] `asyb` The rule enumerates concrete banned patterns, not only a general "be less sycophantic" instruction.
- **Verification:** Read the new section; confirm both rule sets are named and enumerated with concrete banned patterns; confirm the old Tone line is now a pointer with no duplicated rule text.

---

### Item 4 — Reference-stack trim
- [x] Trim/consolidate `references/*.md`, drop over-included loads, preserve every rule by name [PRD: rfsa, rfsb, rfsc]
- **PRD ref:** `prd.md > Input-Footprint Reduction > Reference stack trimmed & consolidated`
- **Spec ref:** `spec.md > Reference Stack Trim`
- **What to build:** Using the load map (Item 2), trim and/or consolidate the reference files and remove over-included references from the command `Load:` lists that don't need them. Run the rule-inventory check: record each file's named behavioral rules before, confirm each present in its new location after. Audit trail lands in `docs/load-map.md`.
- **Acceptance criteria:**
  - [x] `rfsa` Every file in `references/` has a recorded review outcome: trimmed, consolidated, or left-as-is with a reason.
  - [x] `rfsb` At least one reference flagged over-included is no longer loaded by commands that don't need it, verifiable in those commands' `SKILL.md` load lists.
  - [x] `rfsc` No trimmed/consolidated reference drops a behavioral rule: each rule from a removed/merged file is present in its new location and traceable by name.
- **Verification:** Diff `references/` and command `Load:` lists against `docs/archive/v2/`; confirm the rule-inventory audit shows every pre-trim named rule still present post-trim; confirm at least one over-included load was removed.

---

### Item 5 — Template tightening
- [x] Tighten `templates/*.md`; diff vs v2 with no emitted-document section lost [PRD: tpla, tplb]
- **PRD ref:** `prd.md > Input-Footprint Reduction > Verbose templates tightened`
- **Spec ref:** `spec.md > Template Tightening`
- **What to build:** Review each template in `plugins/sdd/skills/sdd-guide/templates/`, cut redundant prose and instructional scaffolding, while keeping every heading the emitted document needs.
- **Acceptance criteria:**
  - [x] `tpla` Each template in `templates/` has a recorded review and removes redundant prose or instructional scaffolding.
  - [x] `tplb` A diff of each revised template against its `docs/archive/v2/` counterpart shows reduced word count with no emitted-document section lost.
- **Verification:** For each template, diff against its v2 counterpart; confirm lower word count and that every output-document section heading survives.

---

### Item 6 — Process-notes leaning
- [x] Condense process-notes guidance; keep four categories and append-only behavior [PRD: pnta, pntb]
- **PRD ref:** `prd.md > Input-Footprint Reduction > Process-notes guidance leaned out`
- **Spec ref:** `spec.md > Process-Notes Leaning`
- **What to build:** Condense the real-time process-notes guidance in sdd-guide and the command `SKILL.md` files, keeping the four capture categories named and preserving the append-only, per-phase behavior verbatim in intent.
- **Acceptance criteria:**
  - [x] `pnta` Process-notes guidance is condensed, and the four capture categories (decisions, pushback, difficult questions, pivots) remain named.
  - [x] `pntb` Append-only, per-phase behavior is preserved: no instruction is added that prunes, edits, or summarizes away historical entries.
- **Verification:** Read the condensed guidance; confirm all four categories are still named and no new instruction permits pruning/editing of historical entries.

---

### Item 7 — Uniform inheritance audit
- [x] Confirm every command loads sdd-guide and none overrides the output rules [PRD: inha]
- **PRD ref:** `prd.md > Output Verbosity Reduction > Verbosity rules applied uniformly`
- **Spec ref:** `spec.md > Output Constraints > Uniform inheritance`
- **What to build:** Sweep every command `SKILL.md`: confirm each loads sdd-guide and carries no instruction that overrides or contradicts the terseness/anti-sycophancy rules from Item 3.
- **Acceptance criteria:**
  - [x] `inha` Every command `SKILL.md` loads sdd-guide, and no command carries instructions that override or contradict the terseness and anti-sycophancy rules.
- **Verification:** Grep each command `SKILL.md` for the sdd-guide load line; read for any tone/verbosity instruction that conflicts with Output Constraints; record the sweep result.

---

### Item 8 — Behavior-preservation sweep
- [x] Compare each command vs v2 (no step removed); full chain produces complete artifacts [PRD: prva, prvb]
- **PRD ref:** `prd.md > Behavior Preservation & Verification > Behavior preserved`
- **Spec ref:** `spec.md > Verification`
- **What to build:** Step-by-step compare each command against its `docs/archive/v2/` form to confirm no ordered workflow step was removed by the trims, and confirm a full chain run still yields complete artifacts. This is the hard line — anything that can't prove preservation gets reverted, not shipped.
- **Acceptance criteria:**
  - [x] `prva` A step-by-step comparison of each command against its `docs/archive/v2/` form shows no ordered workflow step removed.
  - [x] `prvb` A full run of the command chain still produces complete artifacts (scope, PRD, spec, sprint, retro) with all required sections present.
- **Verification:** Per-command ordered-step diff vs v2 recorded; the v3 planning artifacts already on disk (scope, PRD, spec, this sprint) plus a retro check serve as the complete-artifact evidence.

---

### Item 9 — Zero-runtime check
- [x] `claude plugin validate .` passes; no hook/script/MCP in `plugins/sdd/` [PRD: vala, valb]
- **PRD ref:** `prd.md > Behavior Preservation & Verification > Zero-runtime posture verified`
- **Spec ref:** `spec.md > Verification > Zero-runtime check`
- **What to build:** Run plugin validation and confirm the markdown-only posture survived the trims.
- **Acceptance criteria:**
  - [x] `vala` `claude plugin validate .` exits successfully on the trimmed plugin.
  - [x] `valb` `plugins/sdd/` contains no hook, script, or MCP dependency: no executable files and no hook entries in the plugin manifest.
- **Verification:** Run `claude plugin validate .` (exit 0); inspect `plugins/sdd/` for executables and the manifest for hook entries — none present.

---

### Item 10 — Dogfood comparison
- [x] Record v3-vs-v2 input/output reduction against the guide-rails [PRD: dfra, dfrb, dfrc]
- **PRD ref:** `prd.md > Behavior Preservation & Verification > Dogfood comparison`
- **Spec ref:** `spec.md > Verification > Dogfood comparison`
- **What to build:** Treat this v3 planning chain as the dogfood run; compare its input load and output verbosity by inspection against the equivalent `docs/archive/v2/` artifacts and record the result.
- **Acceptance criteria:**
  - [x] `dfra` A dogfood run of the command chain on a real small project is completed and its artifacts retained.
  - [x] `dfrb` The run's input load and output verbosity are compared by inspection against the equivalent `docs/archive/v2/` artifacts, and the comparison is recorded.
  - [x] `dfrc` The recorded comparison states the rough input/output reduction observed and notes whether it lands near the ~23% input / ~32% output guide-rails, with no requirement to hit them.
- **Verification:** A written comparison exists (in process notes or a retained artifact) naming the v3 vs v2 artifacts compared and the rough input/output reduction, with the guide-rail note.

---

## Out of Scope

Deferred items: see `docs/backlog.md`.
