---
name: qa-review
description: >
  The bounded QA/review→refine loop that runs after speckit.implement. Reviews
  each implemented slice against the spec and the up-front checklists,
  classifies every defect (implementation / slice / spec-plan), routes it to
  the right owner, and re-reviews — with a hard per-slice iteration budget so it
  converges instead of spinning. Escalates unconvergeable slices to the
  orchestrator. Use after each slice is implemented.
---

# qa-review — review, refine, converge

This is the loop that turns "Spec-Kit ran" into "the thing is actually right." It reviews each implemented slice and drives fixes back through the pipeline — but bounded, so an autonomous run can't get stuck.

## What you review against

Two fixed references, never vibes:

1. **The spec** (`spec.md`) — does the slice deliver the requirement it traces to?
2. **The checklists** (`checklists/*`, from phase 3) — does it pass the quality gates established up front?

Plus the project constitution as the always-on backstop.

## The loop

For each implemented slice:

```
review slice ── pass ──▶ close slice
     │
   fail
     │
  classify defect ──▶ route to owner ──▶ owner fixes ──▶ re-review
     │                                                      │
     └──────────────── iteration budget ◀───────────────────┘
```

### Classify every defect

| Class | Meaning | Routes to |
|-------|---------|-----------|
| **Implementation** | code is wrong/incomplete but the slice is right | Implementation Engineer (re-implement) |
| **Slice** | the slice is oversized, mis-scoped, or not vertical | Task Slicer (re-slice via `refine-slices`) |
| **Spec / plan** | the requirement or design itself is wrong/missing | up through the CTO to the Spec Analyst or Solution Architect |

A spec/plan defect is the expensive one — catching it here still beats shipping it. Route it back to the owning phase, let it re-flow forward, don't patch around it in code.

## The bound — this is what makes it safe

- **Default budget: 3 review iterations per slice.**
- Each failed review spends one iteration.
- When the budget is exhausted and the slice still fails, **stop**. Do not re-review again. Escalate the slice to the CTO (→ CEO) with the full failure history: what failed, what was tried, the suspected root cause, and a recommendation.
- Escalation is a clean stop, not a crash. The rest of the run's independent slices continue.

This bound is deliberate: an unbounded review→refine loop is the classic way an autonomous pipeline burns budget forever. Converge or escalate.

## Verdict per slice

- **Pass** — meets the spec and its checklist; close it.
- **Fail (classified, routed)** — defect class + owner + concrete reproduction/criterion.
- **Escalated** — budget spent; handed up with history.

## Done

The phase is done when every slice is Pass or Escalated. Escalated slices become material questions for the human (via the CEO). A run with zero escalations completed fully autonomously — the goal.
