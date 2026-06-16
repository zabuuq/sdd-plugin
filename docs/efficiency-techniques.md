# Efficiency Technique Inventory

This is the research artifact that grounds v3's trims in a studied technique rather
than an ad-hoc guess. It is built first because it is input to everything downstream:
the Output Constraints work (terseness + anti-sycophancy) derives from the
**port-to-markdown** rows here, and the reference/template/process-notes trims lean on
the same inventory.

The techniques below were studied directly from the **Caveman** toolkit
(https://github.com/juliusbrussee/caveman, MIT) — a token-efficiency toolkit for Claude
Code. Its repo and constraint skill were fetched and read for this inventory. Caveman
splits into two kinds of value: a presentation-layer **constraint skill** (terseness,
anti-sycophancy, output-discipline rules) and a set of **executable machinery** (an
auto-activation hook, a memory-file compressor, an MCP middleware). Only the constraint
skill is pure presentation discipline that can be expressed as markdown instructions, so
only it ports into v3.

**Naming note:** the source is credited here in prose. None of the technique labels
below are source-branded — they are descriptive names for the underlying discipline, so
that the rules ported into sdd-guide stand on their own.

**Benchmark rationale.** Caveman's full reported figures — roughly **46% input** cut
(memory-file compressor) and **~65% output** cut (constraint skill across 10 tasks) —
lean partly on its executable machinery (the compressor and the MCP middleware), which
v3 deliberately excludes to preserve the zero-runtime, pure-markdown posture. v3 mines
only the native-portable techniques, so it targets roughly **half** the benchmark as a
sanity-check guide-rail: about **~23% input** and **~32% output**. These are guide-rails,
not contractual gates.

## Technique inventory

One row per technique studied. Verdict is **port-to-markdown** (becomes a native
sdd-guide rule) or **no-port** (with the reason it cannot port). For each portable row,
the description names the native-markdown rule it becomes so downstream items can act on
it directly.

| # | Technique | What it does | Verdict |
|---|-----------|--------------|---------|
| 1 | Anti-sycophancy / no-pleasantries rule | Drops pleasantries and praise padding ("great question," "you're absolutely right"); answer leads with substance. | **port-to-markdown** → sdd-guide Output Constraint: no opening praise, acknowledgement, or sign-off filler; respond with the answer. |
| 2 | Filler-word removal | Drops low-content qualifiers — "just / really / basically / actually / simply" — and hedging. | **port-to-markdown** → Output Constraint: ban listed filler/hedge words from responses. |
| 3 | Sentence-fragment delivery | Permits noun phrases / fragments and the "[thing] [action] [reason]. [next step]." pattern instead of full prose, omitting conjunctions where context carries. | **port-to-markdown** → Output Constraint: fragments allowed; prefer terse phrasing over full-sentence prose in conversational replies. |
| 4 | No tool-call / process narration | Forbids narrating tool calls and step-by-step "now I will…" play-by-play. | **port-to-markdown** → Output Constraint: no narration of tool calls or internal steps; report results, not intentions. |
| 5 | No decorative formatting | Bans decorative tables and emoji used for ornament rather than information. | **port-to-markdown** → Output Constraint: no emoji; tables/structure only when they carry information. (Aligns with existing AGENTS.md no-emoji rule.) |
| 6 | Shortest-decisive-quote for logs/errors | Don't dump long raw logs or error output unasked; quote the single shortest decisive line. | **port-to-markdown** → Output Constraint: excerpt the decisive line from logs/output instead of pasting full dumps. |
| 7 | Single-answer discipline (no recap) | Forbids producing a normal answer plus a terse recap; emit the answer once. | **port-to-markdown** → Output Constraint: one pass at the answer; no restate-then-summarize duplication. |
| 8 | Code / path / command / URL preservation | All executable content — code blocks, file paths, CLI commands, API names, exact error strings, URLs — is kept byte-for-byte; terseness is stylistic only. | **port-to-markdown** → Output Constraint guard-rail: terseness never edits code, paths, commands, identifiers, or URLs; preserve verbatim. (This is the correctness guard that lets the other rules be safe.) |
| 9 | Acronym discipline | Well-known tech acronyms (DB/API/HTTP) are fine; never invent new abbreviations the reader must decode. | **port-to-markdown** → Output Constraint: standard acronyms OK, no invented shorthand. |
| 10 | Style-only / no-translation | Compression is stylistic terseness, not summarization that drops substance and not translation of the user's language. | **port-to-markdown** → framing note for the Output Constraints: trim verbosity, preserve all substance and the user's language. |
| 11 | Always-on persistence framing | Constraint stays active every response rather than decaying after several turns; only an explicit user opt-out disables it. | **port-to-markdown (partial)** → expressed natively by living in sdd-guide, which loads on every `/sdd:*` command, so the discipline is inherently always-on. No flag-file mechanism is ported (see machinery below). |
| 12 | Safety-clarity override | Terseness yields to clarity for security warnings, irreversible actions, or genuine ambiguity. | **port-to-markdown** → Output Constraint exception: be explicit (not terse) for warnings, destructive/irreversible actions, and real ambiguity. |
| 13 | Tiered terseness levels (lite / full / ultra / wenyan) | A user-selectable dial: lite = drop filler only; full = fragments + dropped articles; ultra = telegraphic with causal arrows; wenyan = Classical-Chinese variant. | **no-port** — machinery-shaped UX (per-session `/caveman <level>` command + mode state). v3 has no mode toggle and ships a single fixed terseness profile (≈ the "lite/full" middle); the multi-level dial is out of scope. |
| 14 | Memory-file compression heuristic | Rewrites persistent memory files (e.g. CLAUDE.md) into a denser form, preserving code/URLs, for a permanent input cut (~46% reported). | **no-port as automation** — the *heuristic* (denser phrasing, preserve code/URLs) informs the manual reference/template/process-notes trims, but the `caveman-compress` runtime that performs it is machinery (see below). v3 applies the heuristic by hand, not via a compressor. |

## Out-of-scope machinery

These three Caveman components are **executable machinery, not techniques to port**. Each
would require runtime that breaks v3's zero-runtime, pure-markdown posture, so they are
deliberately excluded (backlogged for possible future research, per scope).

| Component | What it is | Why excluded |
|-----------|-----------|--------------|
| Auto-activation hook | A Claude Code session hook that writes a flag file so caveman mode self-activates at message one without a per-session command. | Requires a runtime hook + flag-file I/O. Breaks zero-runtime markdown posture. v3 gets always-on behavior for free by putting the rules in sdd-guide, which loads on every command — no hook needed. |
| `caveman-compress` | A memory-file compressor sub-skill/command that rewrites files like CLAUDE.md to cut input tokens permanently. | An executable compression step over files. Breaks zero-runtime posture. v3 applies the *compression heuristic* (technique #14) manually to references/templates/process-notes instead of running a compressor. |
| `caveman-shrink` MCP middleware | An npm-published MCP middleware that wraps MCP servers and compresses tool-description schemas before the agent sees them. | Requires an MCP server + Node middleware layer. Breaks zero-runtime, no-MCP posture. The SDD plugin ships no MCP server, so there is nothing for it to wrap. |
