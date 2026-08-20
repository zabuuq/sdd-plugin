# Partition build issues by file, not by epic, when the loop never merges

**What happened:** The 4.1 plan had 10 epics that overlapped heavily on shared files (sdd-guide, refine, discovery, build, prototype, living-documents). The build loop opens PRs and never merges mid-build (human gate), so every branch is cut from the same base — any two PRs touching the same file collide at merge time regardless of authoring order.

**Lesson:** For a build where PRs stack unmerged, the only conflict-free multi-PR slicing is one where **each file is owned by exactly one issue**. Regroup epics into file-disjoint issues (e.g. build-subsystem / prototype-subsystem / refine+markers / shared-behavior) rather than one-issue-per-epic. Cross-cutting ACs (like removing `/clear` across handoffs) force a "shared-behavior" bucket — accept it. Ground the partition by grepping where each concern actually lives (e.g. refine had no literal `/clear`, so `/clear` removal was sdd-guide-only), not by the plan's impact-map labels alone.

**When it applies:** Any `/sdd:build` (or similar fan-out) over a tightly-coupled markdown/config repo, or any time PRs won't merge until after the whole batch is built.
