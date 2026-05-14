# Sprint Tags

**Used by:** `/sdd:plan`, `/sdd:build`, `/sdd:polish`, `/sdd:refine`, `/sdd:prd`

Canonical home for every tag format, marker, and parser regex used in v2 sprint files (`docs/sprint-N.md`) and process-notes files (`process-notes-sprint-N.md`, plus the phase process notes for PRD AC IDs). If a tag's regex is here, this is the source of truth; do not redefine it in command SKILL files. If a command needs to recognize a new tag, add it here first.

## Parser conventions

- All tags open with `[` and close with `]`.
- The text immediately after `[` is a literal tag name (e.g. `PRD`, `FB`, `unmapped`, `iteration-candidate`, `close-sprint-manifest`, `sprint-reopened`).
- Tags with a payload use the form `[name: payload]` — colon then optional whitespace then payload.
- Multi-value payloads are comma-separated; whitespace around commas is tolerated.
- Tags that act as block anchors come in pairs: `[name]` open, `[/name]` close.
- Process-notes markers (e.g. `[iteration-candidate]`, `[sprint-reopened]`) are anchored to the start of a line. Sprint-item tags may appear anywhere within a checkbox bullet line, though the canonical placement is at the end of the line.
- Regexes below are written in PCRE / JavaScript flavor. They are deliberately anchored or scoped so a parser can use them either to test a single line or to scan a file. Where line-anchoring matters it is called out explicitly.

## PRD Acceptance Criteria IDs

**Purpose.** Each PRD acceptance criterion carries a stable short ID. Sprint items reference these IDs via the `[PRD: ...]` tag (below) to prove that every implementation line is tied to an accepted requirement. IDs are assigned at write time by `/sdd:prd` and `/sdd:refine`, with a collision check against existing IDs in `docs/prd.md` before settling.

**Format.** Four lowercase ASCII letters. ~457K possible IDs; collisions are detected by exact match against the existing PRD.

**Example AC line.**

```
- [ ] `mxqr` The /sdd:pause command writes a resume file containing the current command, the in-progress question, and any confirmed decisions.
```

The ID sits in backticks immediately after the checkbox, before the AC prose. Stability rules:

- Reordering ACs within a story does not change IDs.
- Removing an AC removes its ID; the ID is gone, not recycled.
- Splitting an AC into two yields two new IDs (the original ID is retired with the original AC).

**Regex (the ID itself, four lowercase letters):**

```
[a-z]{4}
```

**Regex (a full AC line, capturing the ID in group 1):**

```
^- \[[ x]\] `([a-z]{4})` .+$
```

Group 1 is the AC ID. The checkbox class `[ x]` accepts unchecked or checked ACs.

**Regex (any inline reference to an AC ID, e.g. in a sprint item or process note):**

```
`([a-z]{4})`
```

Note that this also matches any four-letter lowercase string in backticks. Use the contextually narrower regexes below when scanning sprint items.

## `[PRD: ...]` sprint-item tag

**Purpose.** Marks a sprint checkbox item as implementing one or more PRD acceptance criteria. One tag per item; the payload may carry multiple IDs when a single item satisfies multiple ACs. Required on every sprint checkbox unless `[unmapped]` is used instead.

**Example.**

```
- [ ] Build /sdd:pause command with resume-file write [PRD: mxqr, pvtb]
```

**Regex (matches the tag anywhere on a line):**

```
\[PRD:\s*[a-z]{4}(?:\s*,\s*[a-z]{4})*\s*\]
```

**Regex (with capture of the comma-separated payload in group 1):**

```
\[PRD:\s*([a-z]{4}(?:\s*,\s*[a-z]{4})*)\s*\]
```

Group 1 yields the inner payload; split on `/\s*,\s*/` to obtain individual AC IDs. The outer regex enforces that every payload token is a valid AC ID format.

**Parser semantics.**

- An item with a `[PRD: ...]` tag is considered "mapped" for the purposes of `/sdd:plan` validation.
- An item may carry a `[PRD: ...]` tag and a `[FB: ...]` tag (see below); both are independently legal.
- An item must not carry both `[PRD: ...]` and `[unmapped]`.

## `[unmapped]` sprint-item tag

**Purpose.** Explicit marker for sprint items that intentionally do not map to any PRD AC (e.g. refactors, infrastructure, plumbing that supports later mapped items). Forces the maintainer to acknowledge the gap rather than accepting silent absence of a tag.

**Example.**

```
- [ ] Refactor shared logging helpers [unmapped]
```

**Regex (literal match, anywhere on a line):**

```
\[unmapped\]
```

**Parser semantics.**

- An item with `[unmapped]` satisfies `/sdd:plan` validation in the same way as a `[PRD: ...]` item — both count as "tagged."
- An item must not carry both `[unmapped]` and `[PRD: ...]`. (`/sdd:plan` does not enforce this combination explicitly, but the result is logically incoherent; flag it as a validation warning if encountered.)
- `[unmapped]` and `[FB: ...]` may co-exist (e.g. an in-scope feedback item that is not itself a PRD AC).

## `[FB: ...]` sprint-item tag (sdd-plugin only)

**Purpose.** Feedback reference for sprint items implementing an in-scope feedback item. Used only in the sdd-plugin project — specifically when `docs/v2-verification.md` exists. Other projects ignore this tag. `/sdd:build` wrap-up uses this tag to update verification rows.

**Example.**

```
- [ ] Implement /sdd:plan validation parser [PRD: znyg] [FB: FB-002]
- [ ] Tighten resume-file schema for stacked decisions [FB: FB-005, FB-012]
```

**Regex (matches the tag anywhere on a line):**

```
\[FB:\s*FB-\d{3}(?:\s*,\s*FB-\d{3})*\s*\]
```

**Regex (with capture of the comma-separated payload in group 1):**

```
\[FB:\s*(FB-\d{3}(?:\s*,\s*FB-\d{3})*)\s*\]
```

Group 1 yields the inner payload; split on `/\s*,\s*/` for individual IDs. The numeric component is three digits, zero-padded (`FB-005`, not `FB-5`).

**Parser semantics.**

- `[FB: ...]` is optional. Its presence or absence does not affect `/sdd:plan` mapping validation.
- `[FB: ...]` may co-exist with either `[PRD: ...]` or `[unmapped]`.
- `/sdd:build` close-sprint scans every closed (checked) sprint item for `[FB: ...]` tags and writes one row per ID to `docs/v2-verification.md`.

## `[iteration-candidate]` process-notes marker

**Purpose.** In-flight marker written by `/sdd:build` to `process-notes-sprint-N.md` during checklist execution when the agent observes something that may warrant follow-up iteration. Close-sprint wrap-up scans for these markers and prompts the maintainer for a disposition (discard, tech-debt, iterate).

**Format.** Literal `[iteration-candidate]` at the start of a line, followed by free-prose description on the same line and optionally subsequent lines. The entry continues until the next blank line or the next start-of-line tag. An inline `(item: abcd)` reference may follow the prose for the reader's benefit; it is not parser-enforced and does not alter regex matching.

**Example.**

```
[iteration-candidate] During build of /sdd:pause, the resume-file structure
felt too rigid for cases where multiple confirmed decisions are pending —
worth revisiting whether the schema should support a stack. (item: mxqr)
```

**Regex (matches the marker at start of line; use with multiline mode):**

```
^\[iteration-candidate\]
```

**Regex (matches the marker plus the rest of its first line, group 1 captures the inline prose):**

```
^\[iteration-candidate\][ \t]+(.+)$
```

**Regex (matches the full multi-line entry; PCRE multiline + dotall):**

```
^\[iteration-candidate\][ \t]+[\s\S]*?(?=\n\s*\n|\n\[|\z)
```

The entry ends at the first of: (a) a blank line, (b) the next start-of-line tag, or (c) end of file.

**Parser semantics.**

- No severity field. The single class is "candidate observed during build"; disposition is decided at wrap-up, not in-flight.
- Optional `(item: abcd)` is informational. The regex `\(item:\s*([a-z]{4})\)` extracts the AC ID if present.
- Fabrication prohibited. Agents write the marker only when a real candidate is observed during execution. Wrap-up trusts the file content as written; it does not back-fill candidates.
- Tech-debt and other observations surface at the wrap-up "anything notable?" prompt, not via this marker.

## `[close-sprint-manifest]` / `[/close-sprint-manifest]` block

**Purpose.** Structured block written by `/sdd:build` close-sprint to `process-notes-sprint-N.md`. Captures the canonical record of what happened at sprint close: PRD ACs checked, story-split mappings, iteration-candidate dispositions, tech-debt entries, and v2-verification rows updated. `/sdd:polish` re-open reads this block when undoing a sprint close.

**Format.** Paired anchors. The opening anchor `[close-sprint-manifest]` carries a timestamp on the same line. The closing anchor `[/close-sprint-manifest]` is on its own line.

**Example.**

```
[close-sprint-manifest] timestamp: 2026-05-12T15:45:00Z
- PRD ACs checked: mxqr, pvtb, znyg
- Story splits: <original-story-anchor> → <new-story-anchor>: <ac-ids>; ...
- Iteration candidate dispositions: <candidate-summary>: discard / tech-debt / iterate; ...
- Tech-debt entries: <count> (informational)
- v2-verification rows updated: FB-005, FB-012 (informational)
[/close-sprint-manifest]
```

**Regex (matches the opening anchor, group 1 captures the timestamp):**

```
^\[close-sprint-manifest\][ \t]+timestamp:\s*(\S+)\s*$
```

**Regex (matches the closing anchor):**

```
^\[/close-sprint-manifest\]\s*$
```

**Regex (matches the entire block from open to close; PCRE multiline + dotall, group 1 captures body):**

```
^\[close-sprint-manifest\][ \t]+timestamp:[^\n]*\n([\s\S]*?)^\[/close-sprint-manifest\]\s*$
```

**Parser semantics.**

- Exactly one manifest block is written per sprint close. A second `/sdd:build` close on the same sprint number is a re-open scenario, not a duplicate manifest.
- Body lines inside the block use plain markdown list items. There is no inner tag grammar; the body is human-readable summary plus comma-separated ID lists.
- `/sdd:polish` re-open reads the most recent manifest block to identify which ACs to un-check and which story splits to undo.
- Manifest body comma lists for AC IDs follow the same `[a-z]{4}` format; for feedback IDs they follow the `FB-\d{3}` format.

## `[sprint-reopened]` process-notes marker

**Purpose.** Emitted by `/sdd:polish` re-open after appending undo information to `process-notes-sprint-N.md`. Records what was un-checked and which story splits were undone, so that a subsequent close-sprint operates on a clean known state.

**Example.**

```
[sprint-reopened] timestamp: 2026-05-13T09:12:00Z; un-checked: mxqr; splits-undone: <original> → <new>
```

**Regex (matches the marker at start of line, group 1 captures the timestamp, group 2 captures the rest of the payload):**

```
^\[sprint-reopened\][ \t]+timestamp:\s*(\S+);\s*(.+)$
```

**Regex (looser; matches the marker anchor only, for scanning):**

```
^\[sprint-reopened\]
```

**Parser semantics.**

- One marker is written per re-open event. Multiple re-opens leave multiple markers in chronological order.
- The payload after the timestamp is a semicolon-separated list of fields. Field order is currently `un-checked: <ac-ids>` then `splits-undone: <description>`; more fields may be added later, so parsers should split on `;` and key-match rather than assuming positional order.
- `/sdd:build` close-sprint that follows a re-open writes a fresh `[close-sprint-manifest]` block; the prior manifest is left in place as history.

## Validation summary

`/sdd:plan` enforces, for each checkbox bullet in the proposed sprint file:

- The line matches `^- \[[ x]\] ` (standard checkbox bullet), and
- The line contains at least one of `\[PRD:\s*[a-z]{4}(?:\s*,\s*[a-z]{4})*\s*\]` or `\[unmapped\]`.

Failure mode: surface every offending line with line number and refuse to write the file. `/sdd:plan` does not auto-tag and does not infer mappings; the maintainer fixes the file and re-runs.

`/sdd:build` close-sprint scans the closed sprint file for `[FB: ...]` tags on checked items and writes one verification row per FB ID to `docs/v2-verification.md` (sdd-plugin only). It scans `process-notes-sprint-N.md` for `[iteration-candidate]` entries and prompts for dispositions. It writes exactly one `[close-sprint-manifest]` block on completion.

`/sdd:polish` re-open scans `process-notes-sprint-N.md` for the most recent `[close-sprint-manifest]` block, reverses its effects against `docs/prd.md` and the sprint file, and appends a `[sprint-reopened]` marker.
