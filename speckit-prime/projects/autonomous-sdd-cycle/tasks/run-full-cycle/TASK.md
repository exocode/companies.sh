---
name: Run Full Spec Kit Cycle
description: Execute the complete Spec Kit command sequence with small-slice refinement and QA loopbacks
slug: run-full-cycle
assignee: workflow-conductor
project: autonomous-sdd-cycle
---

Take the user's feature request or repository goal through the complete lifecycle:

```text
/speckit.constitution -> /speckit.specify -> /speckit.clarify -> /speckit.checklist -> /speckit.plan -> /speckit.tasks -> /speckit.analyze -> /speckit.implement
```

Before implementation, ensure the plan and tasks are small enough to verify slice by slice. After implementation, ask the QA Reviewer for a pass/refine decision and loop back only to the earliest affected phase.
