# Check origin/<branch>, not local main, before deciding a PR base

**What happened:** Local `main` was 43 commits behind `origin/main`. Reading only local refs, it looked like 41 commits of v6 work were unmerged and `main` needed a giant catch-up PR. A `gh pr create` for the catch-up failed with "No commits between main and archive-v6" — because `origin/main` already had everything (merged earlier via PR #17). The whole catch-up premise was a stale-local-ref artifact.

**Lesson:** Before branching build work or proposing a base-branch fix, `git fetch` and compare against **`origin/<branch>`**, not the local tracking branch. `git rev-list --count origin/main..<branch>` and the reverse tell the truth; local `main..` can be arbitrarily stale. One fetch would have skipped a wrong AskUserQuestion and a redundant branch push.

**When it applies:** Any time repo topology drives a decision (PR base, catch-up, "is this merged?") — resolve it against the remote, not local refs.
