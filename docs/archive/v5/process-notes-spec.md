# Process Notes — /sdd:spec (v5: archive refs selection)

## Decisions

- **Refs selection vs. plan gate → two-step gate (option b).** Show the numbered refs list first, collect the user's selection, then fold the resolved selection into the normal archive plan as a "Will archive (refs): …" group and take the **single** confirmation on the complete plan. Two prompts, but the confirmation contract ("one confirmation covers the entire run") is preserved — the selection reply is a list-building step, not a go/no-go confirmation. The go/no-go remains singular and final, on the full plan.
  - Consequence: the empty-sweep-set "nothing to archive" exit (`bprd`/`bpre`) must short-circuit **before** the refs list is shown, so a populated `docs/refs/` on an otherwise-empty run never triggers a prompt.

- **Reply-parsing syntax.** Accept comma-separated numbers (`1,3,5`), ranges (`2-4`), and keywords (`all` / `none`). Forms combine (`1,3-5,8`).
  - Empty reply or `none` → archive no refs; every ref left live. (Same outcome as selecting nothing.)
  - Forgiveness: **ignore and proceed** — invalid/out-of-range tokens are silently dropped, valid selections honored. Safe because the resolved selection is shown in the plan's "Will archive (refs)" group and still passes the single final confirmation before any file moves.
  - `all` → select every item (any other tokens in the same reply are ignored).

- **INDEX.md enumeration of swept refs (open concern, resolved).** Full per-file enumeration — a dedicated "reference files" group in the Contents list, one bullet per swept ref by flattened relative path (`refs/foo.md`, `refs/sub/bar.md`). Granularity = flattened relative paths, not a single `refs/` line.
- **Artifacts-archived count.** Swept refs count toward the cycle-summary "Artifacts archived: N" total (all-files-archived semantics), not a separate count.

## Deepening round 1 (3 questions)

- **What counts as a ref file.** Ignore dotfiles / hidden files (names beginning with `.`, e.g. `.gitkeep`, `.DS_Store`) — they are not counted, not listed, not moved. Consequence: a `docs/refs/` containing only a `.gitkeep` counts as empty → no prompt (consistent with `bprb`). Symlinks are treated as files (listed and moved like any other entry).

- **"Left in place" report interaction.** `docs/refs/` is **never** shown in the plan gate's "Left in place" set — in all cases: feature engaged, refs empty/absent, or partial selection (some refs left live). The refs directory is fully owned by the refs-handling path and is excluded from the generic left-in-place computation outright. This avoids double-surfacing and satisfies `rfsi` (unselected refs get no reporting beyond absence from the swept set).

- **Git staging of refs moves.** Refs moves ride the existing tight-staging discipline (no `git add -A`). Stage explicitly: the new `docs/archive/v{N}/refs/...` adds (covered by staging the `docs/archive/v{N}/` tree) and the `docs/refs/...` deletions of selected files (outside that tree → staged explicitly by path). Unselected refs are untouched working-tree files and are never staged.

Round 1 surfaced no new surprises — all three edges resolved cleanly into the existing archive mechanics. No further round needed.

## Spec specifics

- **Stack / deployment / DB / auth / frontend / testing-framework beats: all N/A or established.** Markdown-only, zero-runtime plugin extending one existing command. `smallProject: true` confirmed at startup (one feature, solo maintainer, no architecture, no integrations) — no flip, no write, standard skippable spec beats moot anyway.
- **Spec shape:** command-*extension* design doc, modeled on the v4 `/sdd:archive` new-command spec house style. Self-contained to `plugins/sdd/skills/archive/SKILL.md`; no `project-state.json` schema change, no new reference file (refs handling is command-specific, not cross-cutting — so it stays in the command SKILL, not `references/`).
- **Deepening rounds:** 1 round, 3 questions (ref-file definition incl. dotfiles/symlinks; "Left in place" interaction; git staging). All resolved into existing mechanics; closed per smallProject default.
- **Author-resolved (no user input needed), recorded as spec Open Issues → routed to /plan or /build:** INDEX reference-files group ordering within Contents; exact numbered-list wording; leftover empty `docs/refs/` directory after a full selection (spec leaves it in place).
- **Cross-doc updates:** CLAUDE.md and AGENTS.md merged — cycle pointers refreshed v4→v5, v4 demoted to prior-cycle, new "two-step plan gate preserves single-confirmation contract" architecture decision captured in AGENTS.md. Existing content preserved.
- **No PRD divergence** this cycle — unlike v4 (allow-list vs deny-list), the v5 spec implements the PRD ACs as written; nothing routed to /sdd:refine for a PRD edit.

