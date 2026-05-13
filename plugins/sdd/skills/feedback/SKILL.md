---
name: feedback
description: Quickly flag something the SDD plugin could do better. Captures the note with context and returns immediately.
disable-model-invocation: true
argument-hint: [your feedback note]
---

# /sdd:feedback

Read `skills/sdd-guide/SKILL.md` for shared behavior before executing this command. Follow its tone, interaction rules, and guard rails throughout.

## Loading

On startup, load the following in order:

1. `skills/sdd-guide/SKILL.md` (shared behavior)
2. `docs/project-state.json` — read `currentSprint`, `lastCommand`, and `commandExplanationsShown`
3. `~/.claude/sdd-user-profile.json` — read `feedbackLocalPath` if the file exists. This is the optional cross-project forwarding target (the **root directory of the local plugin clone**, not the full path to the feedback file). If the file does not exist, or the field is absent, `null`, or empty, treat the forwarding target as unset and continue silently.

**Skips:** everything else. Do not load the PRD, spec, sprint files, open concerns, deepening rounds, living documents, or any other context. This command is designed to be instant.

## Prerequisites

- `docs/project-state.json` must exist. If missing: "Run `/sdd:scope` first."

If the prerequisite fails, stop immediately. Do not attempt partial execution.

## State Updates (immediate)

**This command writes nothing to `docs/project-state.json` on startup.** In particular, it does not update `lastCommand`. Preserving `lastCommand` is what lets consecutive `/sdd:feedback` invocations correctly attribute each note to the preceding non-feedback command.

Do NOT read or process `docs/open-concerns.md`. This command skips the open concerns protocol entirely to maintain instant response time.

## First-Run Explanation

Check `docs/project-state.json` for `commandExplanationsShown.feedback`.

**If `feedback` has NOT been shown (or `commandExplanationsShown` does not exist):**

Output the following as plain text. This is not a question — do not wait for acknowledgment. Output it and continue.

---

**What /sdd:feedback does**

Feedback captures a quick note about something the SDD plugin could do better. It saves your note with context (sprint number, last command, timestamp) to `docs/sdd-feedback.md` and returns immediately. These notes are reviewed during `/sdd:retro`.

---

After outputting, set `commandExplanationsShown.feedback` to `true` in `docs/project-state.json`.

**If `feedback` HAS been shown:** Skip the explanation entirely. Do not mention it.

## Behavior

### Step 1: Accept Feedback Note

Read `$ARGUMENTS` for the user's feedback note.

**If `$ARGUMENTS` is empty or not provided**, ask the user a single question:

"What feedback do you have about the SDD plugin?"

Wait for the response. Use that response as the feedback note.

**If `$ARGUMENTS` contains the note**, use it directly. Do not ask any follow-up questions.

### Step 2: Capture Context

Read the following from `docs/project-state.json`:

- **Timestamp:** Generate an ISO-8601 timestamp for the current moment (e.g., `2026-04-09T14:32:00Z`).
- **Current sprint number:** Read from `currentSprint`. If no sprint has been created yet, use `0`.
- **Last command run:** Read from `lastCommand` in `docs/project-state.json`. `/sdd:feedback` does not overwrite this field, so the current value is the preceding non-feedback command — exactly what the note should attribute.
- **Project directory name:** Derive from the current working directory (the final segment of the path).

Also read the forwarding target from `~/.claude/sdd-user-profile.json` (loaded in step 3 of Loading):

- **Forwarding target (`feedbackLocalPath`):** Read the `feedbackLocalPath` field. Treat the target as **set** only if the field is present and its value is a non-empty string. Treat it as **unset** if the profile file is missing, the field is absent, the value is `null`, or the value is an empty string. The set/unset distinction drives Step 3's branching.

### Step 3: Append Entry to sdd-feedback.md

Build the entry once using the template below, then write it to one or two destinations depending on whether `feedbackLocalPath` is set **and** whether the current working directory is inside the configured clone (the inside-clone check in Step 3a determines whether the forwarding write fires).

**Entry template (used for both writes — identical formatting):**

```markdown
### [Timestamp]
- **Sprint:** N
- **After command:** /sdd:[command]
- **Project:** [directory name]
- **Note:** [user's feedback]
```

Where:
- `[Timestamp]` is the ISO-8601 timestamp from Step 2.
- `N` is the current sprint number from Step 2.
- `/sdd:[command]` is the last command run from Step 2.
- `[directory name]` is the project directory name from Step 2.
- `[user's feedback]` is the note from Step 1.

There is one template. There is no separate formatting code path for the forwarding write. Use the same template, the same fields, and the same values for both destinations — the forwarding write is a byte-for-byte mirror of the local write.

**Write 1 — Local write (always fires):**

Append the entry to the current project's `docs/sdd-feedback.md`. If the file does not exist, create it with a top-level heading first:

```markdown
# SDD Feedback
```

Then append the entry. New entries are appended to the end of the file. Do not modify existing entries. This write always happens, regardless of whether `feedbackLocalPath` is set.

**Step 3a — Inside-clone detection (gates Write 2):**

If `feedbackLocalPath` is unset, `null`, or empty, skip this entire sub-step and skip Write 2 (see the unset branch below). The inside-clone check only matters when a forwarding target is configured.

When `feedbackLocalPath` is set (present, non-null, non-empty), determine whether the current working directory is **inside** the configured clone. "Inside" means: cwd is the clone root itself, or cwd is a descendant of the clone root. If cwd is inside, the project's `docs/sdd-feedback.md` IS the clone's `docs/sdd-feedback.md` — a forwarding write would double-write the same note, so Write 2 must be skipped.

This resolution and comparison happens **fresh on every `/sdd:feedback` invocation**. Do not persist normalized paths, cached results, or any inside-clone state across invocations.

Perform the comparison with these seven steps in order. Treat `cwd` as the current working directory and `feedbackLocalPath` as the value read from `~/.claude/sdd-user-profile.json`.

1. **Resolve to absolute.** Resolve both `cwd` and `feedbackLocalPath` to absolute paths. If either is already absolute, leave it; if either is relative, expand it against the appropriate base (cwd is inherently absolute when the agent reads it; `feedbackLocalPath` should be expanded against the user's home directory or treated as already absolute per the user-profile convention).
2. **Resolve symlinks.** Resolve both paths to their real, symlink-followed paths — follow every symlink in each path down to its concrete target. If symlink resolution raises an error or detects a loop on either path, abandon steps 1–6 and use step 7's fallback instead.
3. **Normalize separators.** Replace all backslashes with forward slashes in both paths so the two paths use the same separator regardless of operating system.
4. **Strip trailing slashes.** Remove any trailing `/` from both paths so neither ends in a separator. (Leave a root-only path like `/` or `C:/` as-is — only strip a trailing slash when the path has segments before it.)
5. **Case-fold by OS.** On Windows and macOS (which uses case-insensitive APFS by default), lowercase both paths before comparing. On Linux, preserve case in both paths — Linux filesystems are case-sensitive, so case-folding would produce false matches.
6. **Equality-or-descendant test.** Cwd is **inside** `feedbackLocalPath` if and only if (a) the normalized `cwd` equals the normalized `feedbackLocalPath`, OR (b) the normalized `cwd` starts with the normalized `feedbackLocalPath` followed by a `/`. The trailing-slash requirement in the prefix test prevents false positives like `/home/me/sdd-plugin-fork` matching `/home/me/sdd-plugin`.
7. **Fallback on resolve error or symlink loop.** If step 2 raised an error or detected a symlink loop on either path, skip steps 3–6 and instead compare the un-normalized, literal string values of `cwd` and `feedbackLocalPath`: cwd is "inside" iff the literal `cwd` equals the literal `feedbackLocalPath` OR the literal `cwd` starts with the literal `feedbackLocalPath + "/"`. Use the configured value exactly as it appears in the profile and the cwd exactly as the agent reads it — no resolution, no separator normalization, no case-folding.

The outcome of Step 3a is a single boolean: **cwd is inside the clone** (skip Write 2) or **cwd is outside the clone** (run Write 2). Step 3a never writes anything; it only gates Write 2.

**Write 2 — Forwarding write (gated by `feedbackLocalPath` being set AND cwd being outside the clone):**

Write 2 fires only when both conditions hold: `feedbackLocalPath` is set (present, non-null, non-empty) **and** Step 3a determined cwd is outside the configured clone. When both conditions hold, append the **same entry** — same template, identical formatting — to `<feedbackLocalPath>/docs/sdd-feedback.md`. The command appends `/docs/sdd-feedback.md` to the configured root directory of the local plugin clone; the configured value is the clone root, not the full path to the feedback file. If that file does not exist at the forwarding destination, create it with the same `# SDD Feedback` top-level heading first, then append.

Skip Write 2 entirely in either of these branches:
- **`feedbackLocalPath` is unset, `null`, or empty:** the local write alone is the correct, complete behavior. **Do not emit a warning. Do not prompt the user. Do not mention the absent forwarding configuration anywhere in output.** Staying silent is intentional.
- **`feedbackLocalPath` is set, but Step 3a determined cwd is inside the clone:** Write 1 already wrote into the clone's `docs/sdd-feedback.md`, because the project's feedback file and the clone's feedback file are the same file. A forwarding write would append a duplicate entry. Skip Write 2 silently — do not warn, do not mention the skip in output.

When `feedbackLocalPath` is set and cwd is outside the clone, both Write 1 and Write 2 fire: the local write records the note in the current project, and the forwarding write mirrors it into the clone for later review during `/sdd:retro` on the plugin itself.

**Step 3b — Write ordering and failure handling for Write 2:**

Write 1 fires first. Write 2 fires second. The two writes are sequential and independent in their failure consequences. The user's feedback note is preserved locally even when forwarding fails — that guarantee is the entire point of this ordering.

- **Write 1 (local) is unconditional.** It runs first, before any forwarding attempt. If Write 1 itself fails (the current project's `docs/` is unwritable, disk is full, etc.), surface that error normally — it is a hard error and bubbles up. Write 1's success is not contingent on Write 2 in any way; Write 2 is not attempted until Write 1 has completed.
- **Write 2 (forwarding) is best-effort.** Attempt the forwarding append exactly once per invocation. Do not retry within the same invocation. Do not fall back to an alternate path. A single attempt, then move on.
- **Forwarding failure does not change exit status.** When Write 2 fails for any reason, the `/sdd:feedback` command still completes successfully after Write 1. Do not exit with an error status. Do not abort Step 4's confirmation. The local write succeeded; the command's job is done.
- **Single warning per session on forwarding failure.** When Write 2 fails, emit exactly one warning to the user containing (a) the failure reason as surfaced by the write attempt and (b) the literal instruction `Run /sdd:onboard to update.` (verbatim, including the trailing period). What counts as a forwarding failure: the configured path does not exist, a parent directory along the path is missing, the target is a file when a directory was expected (or vice versa), write permission is denied, or any equivalent write error from the underlying filesystem. The list is illustrative; treat any error raised by the Write 2 attempt as a forwarding failure.
- **Per-session re-warn suppression.** Within a single Claude Code conversation/session, after the first warning fires for a given forwarding target, suppress further warnings for the same forwarding target on subsequent `/sdd:feedback` invocations in that same session. The user is warned once per session, not once per invocation.
- **In-conversation memory is the suppression mechanism.** "Per-session" is enforced entirely by the agent's in-conversation memory: remember within this conversation that the warning already fired for the current forwarding target, and skip emitting it again. There is no persistent state file backing this — no `~/.claude/sdd-feedback-warned-paths.json` or equivalent. Across different sessions (different conversations), the agent has no memory of prior warnings, so the warning correctly re-fires once in each new session until the user fixes the path.

Write 2's failure handling is intentionally minimal: one attempt, one warning at most, no retries, no exit failure, no persistent bookkeeping. The local write always wins; the forwarding write is a courtesy that informs the user when it cannot keep up.

### Step 4: Confirm and Return

Output a brief confirmation. Example:

"Feedback saved to `docs/sdd-feedback.md`."

Do not elaborate. Do not suggest next steps. Do not ask follow-up questions. Return immediately.

## Important Reminders

- **Return immediately.** This command must not interrupt the user's workflow. No follow-up questions, no suggestions, no elaboration after the confirmation.
- **No open concerns processing.** This command skips the open concerns protocol entirely.
- **No process notes.** This command does not write process notes.
- **No interview.** The only question this command ever asks is for the feedback note itself, and only when `$ARGUMENTS` is empty.
- **Never write `lastCommand`.** `/sdd:feedback` does not update `lastCommand` in `docs/project-state.json`. The "After command" field in each note is sourced directly from the current value of `lastCommand`, which is the preceding non-feedback command. Consecutive `/sdd:feedback` runs therefore all attribute to the same preceding command.
- **First-run explanation only on the very first `/sdd:feedback` in the project.** Check `commandExplanationsShown.feedback` — do not show it on subsequent runs.
- **Forwarding-write failures warn once per session, never block the local write.** Write 1 is unconditional and runs first; Write 2 is best-effort, single-attempt, never retried, and never changes exit status. If Write 2 fails, emit one warning (with reason + `Run /sdd:onboard to update.`) per session per forwarding target, suppressing further warnings via in-conversation memory only — no persistent state file.
