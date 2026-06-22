# Open Concerns

## Open

(none)

## Resolved

### Numbered-list / selection-prompt wording
- **Source:** /sdd:spec — v5 spec, Refs Detection & Listing
- **Target:** /sdd:build
- **Status:** resolved
- **Concern:** The exact phrasing of the refs numbered list and the selection prompt is illustrative in the spec. Final wording is a build-time detail, constrained only to a numbered list plus a numeric/keyword reply (no elaborate UI).
- **Resolution:** Resolved at `/sdd:build` (sprint 1, item 1). `### Step A — refs selection` in `plugins/sdd/skills/archive/SKILL.md` adopts the spec's illustrative format verbatim — a numbered list (one relative path per line) plus the prompt `Reply with numbers to archive (e.g. 1,3 or 2-3 or all / none).` — and explicitly flags the exact wording as a build-time authoring choice constrained to a numbered list + numeric/keyword reply, no elaborate UI. Satisfies `rfsa`.
- **Deferral rationale:**

### Leftover empty `docs/refs/` directory after a full selection
- **Source:** /sdd:spec — v5 spec, Refs Move Execution
- **Target:** /sdd:build
- **Status:** resolved
- **Concern:** When every ref is selected, `docs/refs/` is left as an empty directory. Spec's decision is to leave it in place (do not remove the directory). Confirm this is the desired end state at build time.
- **Resolution:** Resolved at `/sdd:build` (sprint 1, item 2). Execute `### 4. Move the selected references` encodes the end state explicitly: "If every candidate was selected, `docs/refs/` may be left empty after the moves. Leave the empty directory in place — this command does not remove the `docs/refs/` directory itself." Consistent with `rfsh`/`rfsi` (the command acts on files, not the directory).
- **Deferral rationale:**

### INDEX reference-files group ordering
- **Source:** /sdd:spec — v5 spec deepening
- **Target:** /sdd:plan
- **Status:** resolved
- **Concern:** Where the `reference files:` bullet sits within the generated `INDEX.md` Contents list. Spec places it after the process-notes group and before the `project-state.json` snapshot bullet; confirm that placement reads cleanly when sequencing the SKILL.md edit. Cosmetic, non-blocking.
- **Resolution:** Resolved at `/sdd:plan`. Placement confirmed: the `reference files:` bullet stays grouped with the other archived-content bullets (planning artifacts / sprint files / process notes) and precedes the `project-state.json` snapshot and cross-cycle living-doc snapshot bullets. This reads cleanly — swept content first, then the snapshot/meta bullets. The single sprint item carries this as part of the INDEX.md enumeration work (`rfsa`-class INDEX behavior); no separate sprint item needed.
- **Deferral rationale:**

### INDEX.md enumeration of swept refs
- **Source:** /sdd:prd
- **Target:** /sdd:spec
- **Status:** resolved
- **Concern:** Whether the auto-generated `docs/archive/v{N}/INDEX.md` should enumerate the refs files swept into `docs/archive/v{N}/refs/`, and at what granularity (flattened relative paths vs. a single "refs/" line).
- **Resolution:** Resolved in `/sdd:spec`. **Full per-file enumeration** — a dedicated "reference files" group in the Contents list, one bullet per swept ref by flattened relative path (`refs/foo.md`, `refs/sub/bar.md`). The group is emitted only when at least one ref was swept. Swept refs also **count toward** the cycle-summary "Artifacts archived: N" total (all-files-archived semantics). See `docs/spec.md > INDEX.md Refs Enumeration`.
- **Deferral rationale:**

### Refs prompt placement vs. the single-confirmation plan gate
- **Source:** /sdd:scope — v5 scope interview
- **Target:** /sdd:spec
- **Status:** resolved
- **Concern:** Whether the per-item refs selection folds into the existing single plan gate or is a separate prompt, without breaking the "one confirmation covers the entire run" contract. The reply-parsing syntax (closely related, also spec-level) was to be settled in the same pass.
- **Resolution:** Resolved in `/sdd:spec` as a **two-step plan gate with one confirmation**. The numbered refs list is shown and the selection collected (Step A) *before* the plan is displayed; the resolved selection folds into the plan as a "Will archive (refs)" group, and the single existing confirmation (Step B) approves the complete plan. The selection reply is a plan-*building* input, not a go/no-go — the contract holds literally. Detection runs in pre-flight (read-only); the nothing-to-archive exit short-circuits before Step A; `docs/refs/` is excluded from "Left in place" in all cases. **Reply-parsing syntax** settled in the same pass: comma-separated numbers, ranges (`2-4`), keywords `all`/`none`, combinations; empty reply = `none`; invalid/out-of-range tokens ignored-and-proceed (safe because the resolved set still passes the final confirmation). See `docs/spec.md > Plan Gate Integration` and `> Selection & Reply Parsing`.
- **Deferral rationale:**

### Refs-only "nothing to archive" interaction
- **Source:** /sdd:scope — v5 scope interview
- **Target:** /sdd:prd
- **Status:** resolved
- **Concern:** The existing "nothing-to-archive exit" triggers on an empty sweep set. If `docs/refs/` is the only thing present (sweep set otherwise empty), it was undefined whether archive should still offer refs selection or take the nothing-to-archive exit.
- **Resolution:** Resolved in `/sdd:prd` (acceptance criteria `bprd`, `bpre`). The empty-sweep-set "nothing to archive" exit wins. A populated `docs/refs/` on its own does **not** count as "something to archive" — when the normal sweep set is empty, the command takes its existing early exit and shows no refs prompt, even if `docs/refs/` has files. Refs selection is strictly additive to a real archive run. (Spec pins the ordering: the exit short-circuits before the refs list — `docs/spec.md > Plan Gate Integration`.)
- **Deferral rationale:**

## Deferred

(none)
