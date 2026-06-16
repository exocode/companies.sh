---
name: human-gate
description: >
  Standardised human approval gate for CEO and CTO. Posts a decision summary
  as a comment, sets the issue to in_review, and waits for human Approve or
  Reject. Use at every point where a human decision is required before the
  pipeline can advance.
---

# Human Gate

Use this skill whenever you need explicit human approval before advancing
to the next pipeline phase.

## When to use

- CEO: after SPEC ANALYST HANDBACK + CLARIFY CHALLENGER REPORT, after analyze
  results, after TECH BRIEF, at every GIT CHECKPOINT
- CTO: after checklist is produced, after plan is produced, after tasks +
  refine-slices, after analyze, after each QA PASS on a slice

## Steps — execute in exact order, no skipping

### Step 1 — Post a decision summary comment

Write a comment on the current issue containing:

```
## [Phase name] — Human Gate

**What was produced:** <one sentence>

**Key findings / decisions needed:**
<bullet list — keep it short, max 5 items>

**Recommendation:** <your recommended action>

**To proceed:** Approve this issue. To request changes: Reject and add a comment.
```

### Step 2 — Set status to `in_review`

Immediately after posting the comment:

```
PATCH /api/issues/{currentIssueId}
{ "status": "in_review" }
```

Do NOT skip this step. Without `in_review`, the human sees no Approve/Reject
buttons and cannot signal their decision back to you.

### Step 3 — Stop and wait

Do not dispatch the next phase. Do not close the issue. Do not add more
comments. Stop here.

Paperclip will wake you automatically via `finish_successful_run_handoff`
when the human acts.

### Step 4 — On wake: read the outcome

- **Approved**: set status back to `in_progress`, then dispatch the next phase.
- **Rejected**: read the human's comment for feedback, apply it, loop back
  to the relevant phase or escalate if the feedback requires a product decision.

## Hard rules

- Never advance past a gate without completing Steps 1 and 2.
- Never set `in_review` without a summary comment — the human needs context.
- Never use `in_review` while waiting for a child agent issue — that is
  `in_progress` + a comment. `in_review` is exclusively for human decisions.
