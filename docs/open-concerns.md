<!-- docs/open-concerns.md — running list of unresolved questions across SDD commands. See plugins/sdd/skills/sdd-guide/references/open-concerns.md. -->

# Open Concerns

## Open

### context-loading.md has no v6 disposition
- **Raised by:** /sdd:build — v6 sprint 1, item 3/7 (foundations group).
- **Concern:** `references/context-loading.md` is absent from the spec's v6 file-structure listing, but no sprint item deletes or rewrites it. Its content is heavily v5 (per-command load table naming `scope`/`prd`/`plan`/`polish`, sprint-file re-entry order). Left untouched during the foundations group. Needs a decision at item 28's reference sweep: delete it, or rewrite its load table for the v6 command set.

## Resolved

### Free-tier ChatGPT / Gemini automation capabilities
- **Raised by:** /sdd:scope — v6 Round 1, Topic 2 (research/validate step). Refined /sdd:prd — v6 Round 1, Q4 (validation split into `/sdd:validate`).
- **Concern:** Whether Claude could orchestrate an automated round-trip handing `plan.md` to ChatGPT/Codex/Gemini and pulling findings back into `docs/validation/`, and whether free tiers expose usable MCP/API access for it.
- **Resolved by:** /sdd:spec — v6 validate design. The `/sdd:validate` architecture makes automation **orthogonal**: `docs/validation/` is a source-agnostic drop-zone and validate treats every file uniformly regardless of producer (Claude self-critique, another AI, a person). Claude's own self-validation is itself written as a file into that folder. So any future automated round-trip is just another writer to the same folder — no structural change needed. The load-bearing path (manual + self-validation file-drop) is complete in v6. The automation feature itself is captured as a backlog item ("Automated third-party AI validation runs in /sdd:validate") with the maintainer's design guidance, deferred to a follow-on run per explicit maintainer decision. Nothing is left unspecified in the v6 design.
