# Process Notes — SDD v2

v1 process notes archived to `docs/archive/v1/process-notes.md`.

## /sdd:scope

- **Cycle context:** v2 scoping driven by feedback collected in v1 across three projects (Event Planning App, fiver-gigs, sdd-plugin self-use). Feedback pile lives at `docs/sdd-feedback.md` and serves as the de facto seed for this scope phase. v1 planning artifacts archived to `docs/archive/v1/`.
- **Open concerns carried in:** Four open items from v1 retro/scope flagged at startup: clean-state definition for `/sdd:build` revert, post-sprint command ceremony heaviness, spec/runtime duplication, and soft acceptance criteria for MIT distribution. The post-sprint ceremony concern explicitly targets this command — it should get resolved or deferred during this v2 scoping pass.

### Beat 1 — The idea
- **Captured:** v2 is a streamlining pass of v1 driven by real-use feedback. Not a feature expansion; refinement of what already works based on friction surfaced across actual project usage.
- **User framing was tight and unprompted** — single sentence, no probing needed. Spine of v2 is clear from the start.

### Beat 2 — Who it's for
- **Captured:** Jason + a local tech group he is a member of and plans to actively hand the plugin to.
- **Audience shift from v1:** v1 was "Jason + MIT-licensed permissive sharing (others *could* adopt it)." v2 actively pursues an audience of peer developers.
- **Follow-up surfaced a critical bar-setting decision:** when asked whether the tech group would use it on real projects vs. kick-the-tires for feedback, user answered "probably both." Implication captured for downstream beats — v2 has to clear a working-software bar AND its feedback loop has to be real, since invited users are a feedback source.

### Session pause
- **Paused mid-Beat 3** when user needed to switch computers. Resume artifact written to `docs/scope-resume.md` using the schema proposed in feedback note `2026-04-24T00:05:00Z` (status / confirmed / pending / instructions-for-next-session). Convenient dogfooding of the v2 multi-session-resume feature request.
- **Setup work captured in resume doc** so the next-session agent does not redo archival, state reset, or scaffolding.
