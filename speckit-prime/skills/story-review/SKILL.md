---
name: story-review
description: >
  Story-level QA pass after all slices of a user story are implemented and have
  already passed slice-level qa-review. Checks the integrated behavior of the
  whole story across slice boundaries. Owned by the QA Reviewer.
---

# Story Review — Integrated User Story QA

Use this skill only after every slice belonging to a user story has already
passed the normal `qa-review` loop.

This is not a replacement for slice-level QA. It is a second, lighter gate that
checks whether the full user story works coherently as one implemented feature.

## Trigger

The CTO dispatches this review when:
- all slices for a user story are complete and slice-QA PASSed, or
- a tightly related issue bundle forms one coherent end-user capability

## What you review

Review the user story as an integrated whole:

1. **Cross-slice behavior**
   - Do the completed slices work together end-to-end?
   - Are there hidden mismatches at boundaries (API, state, events, models)?

2. **Spec fidelity at story level**
   - Does the combined implementation satisfy the user story intent?
   - Is anything technically green but functionally incomplete?

3. **Contract and data consistency**
   - Do contracts, data model, and actual runtime behavior still align when the
     slices are combined?

4. **Regression surface**
   - Did one slice undermine another slice in the same story?
   - Are there docs/quickstart/examples now out of sync with the combined story?

5. **Verification quality**
   - Do the existing tests actually prove the user story works end-to-end?
   - Is an integration test or story-level scenario still missing?

## Output format

### FAIL

```
STORY REVIEW: FAIL
Story: <story-id> — <story-title>

Summary:
  <one paragraph on what fails at integrated story level>

Findings:
[INTEG] <file/area> — <cross-slice mismatch> — fix: <what to change>
[SPEC]  <requirement> — <story intent not fully met> — fix: <what to add/change>
[TEST]  <test gap> — <missing end-to-end or integration coverage> — fix: <what to test>
[DOC]   <doc/quickstart drift> — fix: <what to update>

NEXT ACTION:
  Return owner: CTO
  Assignee: Implementation Engineer
  Fix items:
  1. <concrete fix instruction>
  2. <concrete fix instruction>
  CTO action: create a fix issue for the Implementation Engineer, then re-dispatch this story to QA Reviewer for story re-review.
```

### PASS

```
STORY REVIEW: PASS
Story: <story-id> — <story-title>

Summary:
  <one paragraph confirming the integrated story works as a whole>
Return owner: CTO
CTO action: mark the user story complete or continue to the next story/final checkpoint.
```

## Routing

- FAIL -> back to Implementation Engineer via CTO
- PASS -> CTO may mark the user story complete and continue to the next story or
  final checkpoint
