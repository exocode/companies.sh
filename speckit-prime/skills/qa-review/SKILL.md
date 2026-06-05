---
name: qa-review
description: >
  Post-implementation QA pass for each completed slice. Review implemented code
  against the spec and checklists. Classify every finding by earliest affected
  phase. Loop is bounded — escalate if a slice cannot converge. Owned by the QA
  Reviewer.
---

# QA Review — Slice Review Loop

Use this skill after each implemented slice to verify it meets the spec and checklist before marking it done.

## Trigger

The Implementation Engineer hands you a completed slice (branch, commit, or diff).

## What you do

1. Load the active feature's `spec.md`, `checklists/`, and `tasks.md` from the feature directory.
2. Review the implemented slice against:
   - Every acceptance criterion from the relevant task entry in `tasks.md`
   - Every applicable checklist item in `checklists/`
   - The corresponding requirement in `spec.md`
3. Classify every finding by the earliest phase that must change:
   - **Implementation defect** — the code is wrong or incomplete -> back to the Implementation Engineer
   - **Slice too large or wrong boundary** -> back to the Task Slicer to re-slice
   - **Spec or plan defect** -> up through the CTO to the right owner
4. Emit a verdict: **PASS** or **FAIL + classified findings**.

## Pass criteria

A slice passes when:
- All acceptance criteria from the task entry are satisfied
- No checklist items applicable to this slice are failing
- No spec requirement covered by this slice is violated
- The build and tests are green

## Fail output format

```
SLICE REVIEW: FAIL
Slice: <task-id> — <task-title>

Findings:
[IMPL]  <file>:<line> — <specific defect> — fix: <what to change>
[SLICE] <task-id> — slice boundary issue — re-slice needed: <reason>
[SPEC]  <spec section> — spec defect — route to: <owner>

Earliest affected phase: <implement | tasks | plan | spec>
Downstream commands needing rerun: <list or none>
```

## Loop bound

If the same slice fails review more than 3 times, stop the loop and escalate to the CTO with a summary of all iterations. Do not spin indefinitely.
