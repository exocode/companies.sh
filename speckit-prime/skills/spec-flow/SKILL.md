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
Phase 0   constitution    -> CEO runs: speckit-constitution → spec-critic → [human gate if FAIL]
Phase 1   specify         -> CEO dispatches to: Spec Analyst
Phase 1a  clarify         -> Spec Analyst runs automatically after specify (no separate dispatch)
Phase 1b  spec-critic     -> Spec Analyst runs autonomously after clarify
Phase 1c  clarify-challenger -> CEO dispatches to: Clarify Challenger (mandatory, before human gate)
Phase 1c+ human gate      -> CEO presents combined SPEC ANALYST HANDBACK + CLARIFY CHALLENGER REPORT
Phase 1d  spec-review     -> CEO dispatches to: Spec Reviewer (independent gate, blocks plan)
Phase 1e  tech-brief      -> CEO runs tech-brief skill: presents tech proposals, waits for human input
Phase 2   checklist       -> CEO dispatches to: CTO -> QA Reviewer
Phase 3   plan            -> CEO dispatches to: CTO -> Solution Architect (with PLAN ARGUMENTS)
Phase 4   tasks           -> CEO dispatches to: CTO -> Task Slicer
Phase 4a  refine-slices   -> CTO -> Task Slicer (immediately after tasks)
Phase 4b  artifact-review -> CTO -> QA Reviewer (cross-artifact consistency, hard gate)
Phase 5   analyze         -> CEO dispatches to: CTO -> QA Reviewer
Phase 6   implement       -> CEO dispatches to: CTO -> Implementation Engineer (slice by slice)
Phase 6a  qa-review loop  -> CTO -> QA Reviewer (after each slice, mandatory)
```

**Human gate pattern (all phases marked [human gate]):**
Set issue to `in_review` → post decision summary comment → human sees
Approve/Reject buttons in UI → Approve wakes CEO via handoff → CEO advances.

**Hard gate:** Phase 1e (tech-brief) requires explicit human input before
Phase 2 (checklist) can start. The CEO never invents a tech stack.

## Advancement rule

Before advancing to the next phase, the CEO verifies the expected artifact
exists and is coherent:

| Phase completed  | Artifact to verify                              | Git checkpoint |
|------------------|-------------------------------------------------|----------------|
| constitution     | `.specify/memory/constitution.md`               | ① signal commit |
| specify          | `specs/<feature-id>/spec.md`                    | — (not yet) |
| clarify          | `spec.md` has no open `[NEEDS CLARIFICATION]`   | — (not yet) |
| spec-review      | APPROVED or APPROVED WITH FIXES verdict         | ② signal commit |
| checklist        | `specs/<feature-id>/checklists/` files exist    | — |
| plan             | `specs/<feature-id>/plan.md`                    | — |
| tasks            | `specs/<feature-id>/tasks.md`                   | — |
| refine-slices    | all tasks in `tasks.md` are small vertical slices | — |
| artifact-review  | APPROVED or APPROVED WITH FIXES verdict         | — |
| analyze          | consistency report with no blocking findings    | ③ signal commit |
| implement/slice  | each slice passes QA review                     | ④ signal commit per slice |

**Git checkpoint signals:** At each marked phase, the CEO emits a
`GIT CHECKPOINT` message to the human with the exact files to stage and
a suggested commit message. The human commits — or confirms they want the
agent to commit. Nothing is committed automatically without this signal.

## CEO rules

- NEVER write spec.md, plan.md, tasks.md, or code directly — dispatch every time
- NEVER call /speckit.specify, /speckit.plan, /speckit.tasks, /speckit.implement,
  /speckit.checklist, /speckit.analyze yourself — these belong to specialists
- NEVER review code yourself — that is the QA Reviewer's exclusive job
- ALWAYS run resume-detect first to find the entry phase
- ALWAYS batch human questions — never interrupt for mechanical choices
- IF a specialist is stuck, accept the blocker, make a documented assumption, or surface to the human
- IF you feel tempted to "help" by writing a spec or a line of code — stop and dispatch instead

The CEO's value is in routing, sequencing, and gating — not in execution.
An agent that bypasses the org chart is not faster; it is broken.
