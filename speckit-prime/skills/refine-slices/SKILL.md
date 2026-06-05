---
name: refine-slices
description: >
  Slice refinement pass. Decompose every task that is too broad into the
  smallest vertical slice that is independently implementable, testable, and
  reversible. Run immediately after /speckit.tasks. Owned by the Task Slicer
  and CTO.
---

# Refine Slices

Use this skill immediately after `/speckit.tasks` generates `tasks.md`, and whenever a QA review routes a slice back for re-slicing.

## What a good slice looks like

- One coherent change with a single observable outcome
- Independently implementable (no hidden dependencies on unbuilt work)
- Independently testable (has a clear done-condition that can be verified)
- Independently reversible (can be rolled back without breaking other slices)
- If the task goal contains the word "and" -> split it
- If the task touches many layers (data model + API + UI + tests) without a single observable outcome -> split it
- Target: an implementation agent can build it and a reviewer can review it in one tight pass

## Refinement process

1. Read `tasks.md` and identify every task that violates the slice criteria above.
2. For each over-broad task, decompose it into the smallest set of vertical slices that together achieve the original goal.
3. Assign each new slice:
   - A clear done-condition (testable boundary)
   - Explicit prerequisites (which slices must be done first)
   - Parallel-safe marker if it can run concurrently with other slices
   - File or module scope when known
4. Preserve the original task IDs as parent references. New slices get IDs like `<parent-id>.1`, `<parent-id>.2`.
5. Preserve Spec-Kit's task ordering and dependency chain — you are subdividing, not reordering.
6. Keep each refined slice traceable to its parent task and to the spec requirement it serves.

## What you produce

A refined `tasks.md` where every item is a small vertical slice with:
- An explicit done-condition
- A dependency link to prerequisites
- A parent-task reference for traceability
- Parallel-safe or sequential marker
