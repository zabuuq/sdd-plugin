<!-- docs/open-concerns.md — running list of unresolved questions across SDD commands. See plugins/sdd/skills/sdd-guide/references/open-concerns.md. -->

# Open Concerns

## Open

_(none)_

## Resolved

### Free-tier ChatGPT / Gemini automation capabilities
- **Raised by:** /sdd:scope — v6 Round 1, Topic 2 (research/validate step). Refined /sdd:prd — v6 Round 1, Q4 (validation split into `/sdd:validate`).
- **Concern:** Whether Claude could orchestrate an automated round-trip handing `plan.md` to ChatGPT/Codex/Gemini and pulling findings back into `docs/validation/`, and whether free tiers expose usable MCP/API access for it.
- **Resolved by:** /sdd:spec — v6 validate design. The `/sdd:validate` architecture makes automation **orthogonal**: `docs/validation/` is a source-agnostic drop-zone and validate treats every file uniformly regardless of producer (Claude self-critique, another AI, a person). Claude's own self-validation is itself written as a file into that folder. So any future automated round-trip is just another writer to the same folder — no structural change needed. The load-bearing path (manual + self-validation file-drop) is complete in v6. The automation feature itself is captured as a backlog item ("Automated third-party AI validation runs in /sdd:validate") with the maintainer's design guidance, deferred to a follow-on run per explicit maintainer decision. Nothing is left unspecified in the v6 design.
