---
name: spec-flow
description: >
  Full pipeline orchestration map for the CEO. Defines the canonical phase
  order, which agent owns each phase, and what artifact must land before
  advancing. The CEO drives this flow and never executes specialist work
  directly.
---

# Spec Flow — Pipeline Orchestration Map

This skill defines the canonical pipeline the CEO drives. Each phase has exactly one owner. The CEO dispatches, verifies the artifact, then advances. The CEO never executes the work.

## Pipeline Order

```
Phase 0  constitution   -> CEO dispatches to: self (CEO uses /speckit.constitution)
Phase 1  specify        -> CEO dispatches to: Spec Analyst
Phase 2  clarify        -> CEO dispatches to: Spec Analyst
Phase 3  checklist      -> CEO dispatches to: CTO -> QA Reviewer
Phase 4  plan           -> CEO dispatches to: CTO -> Solution Architect
Phase 5  tasks          -> CEO dispatches to: CTO -> Task Slicer
Phase 5a refine-slices  -> CEO dispatches to: CTO -> Task Slicer (immediately after tasks)
Phase 6  analyze        -> CEO dispatches to: CTO -> QA Reviewer
Phase 7  implement      -> CEO dispatches to: CTO -> Implementation Engineer (slice by slice)
Phase 7a qa-review loop -> CEO dispatches to: CTO -> QA Reviewer (after each slice)
```

## Advancement rule

Before advancing to the next phase, the CEO verifies the expected artifact exists and is coherent:

| Phase completed  | Artifact to verify                              |
|------------------|-------------------------------------------------|
| constitution     | `.specify/memory/constitution.md`               |
| specify          | `specs/<feature-id>/spec.md`                    |
| clarify          | `spec.md` has no open `[NEEDS CLARIFICATION]`   |
| checklist        | `specs/<feature-id>/checklists/` files exist    |
| plan             | `specs/<feature-id>/plan.md`                    |
| tasks            | `specs/<feature-id>/tasks.md`                   |
| refine-slices    | all tasks in `tasks.md` are small vertical slices |
| analyze          | consistency report with no blocking findings    |
| implement        | each slice passes QA review                     |

## CEO rules

- NEVER write spec.md, plan.md, tasks.md, or code directly
- NEVER call /speckit.specify, /speckit.plan, /speckit.implement yourself — dispatch
- ALWAYS run resume-detect first to find the entry phase
- ALWAYS batch human questions — never interrupt for mechanical choices
- IF a specialist is stuck, accept the blocker, make a documented assumption, or surface to the human
