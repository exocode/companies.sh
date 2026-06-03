---
name: refine-slices
description: >
  Refinement pass that runs after speckit.tasks. Decomposes broad, multi-step
  tasks into the smallest independently implementable, testable, and reversible
  vertical slices — without reordering the plan or dropping Spec-Kit's
  dependency and parallelization markers. Use whenever tasks.md is too coarse
  for tight implement→review cycles.
---

# refine-slices — make the slices small

Spec-Kit's `tasks` output is correct but frequently too coarse: a single task may bundle several layers and several outcomes. Coarse tasks produce large diffs that are hard to implement cleanly, hard to review, and hard to reverse. This pass cuts them down.

## The target: a vertical slice

A good slice is:

- **Vertical** — it delivers one observable outcome end-to-end (the thin slice through the layers it needs), not a horizontal "do all the database work" chunk.
- **Independently implementable** — buildable on its own, given its declared dependencies.
- **Independently testable** — it has a single, explicit done-condition you can check.
- **Independently reversible** — small enough to back out as one coherent change.

## Split heuristics

Split a task when any of these is true:

1. **The "and" test.** Its goal contains "and" joining two outcomes → split into one slice per outcome.
2. **Multi-layer, no single outcome.** It touches several layers (UI + API + DB) without one observable result → split until each slice has one observable result.
3. **No crisp done-condition.** You can't write a one-line "done when…" → it's too big or too vague; split or sharpen.
4. **Reviewer can't hold it in one pass.** If the QA Reviewer couldn't review it against the checklist in a single tight pass, split it.
5. **Mixed reversibility.** A reversible change bundled with an irreversible one → separate them so the risky part is isolated and gated.

## What you must preserve

- **Order and dependencies.** You are subdividing tasks, not re-planning. Keep Spec-Kit's sequencing and dependency edges; a child slice inherits its parent's dependencies and adds intra-parent ordering.
- **Parallelization markers.** Keep Spec-Kit's parallel/independent markers; propagate them to the slices that are still genuinely parallel.
- **Traceability.** Every slice links to its parent task and to the spec requirement it serves. No orphan slices, no scope added that isn't in the plan.

## Output

A rewritten `tasks.md` where each item is a small vertical slice with: an explicit done-condition, a dependency link, a parent-task link, and a requirement link. Sized so one implement→review cycle handles it cleanly.

## Don't over-split

A slice that is already small, single-outcome, and crisply done-conditioned is left alone. The goal is reviewable and reversible, not atomic-to-the-point-of-bureaucracy. Splitting a 5-minute task into five 1-minute tasks adds coordination cost without benefit — stop when each slice passes the heuristics above.
