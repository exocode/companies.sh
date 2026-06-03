---
name: speckit-slice-refinement
description: Refine broad Spec Kit plans and tasks into smaller dependency-aware vertical slices
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: spec-driven.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

# Spec Kit Slice Refinement

Use this skill after `/speckit.plan` and `/speckit.tasks`, or when QA finds that implementation chunks are too large.

## Slice Standard

A good slice has:

- one observable outcome
- one primary user journey or system capability
- explicit prerequisites
- clear acceptance checks
- bounded files or components when known
- test evidence that can be produced before moving to the next slice

## Plan Refinement

When a plan is too broad, split it before tasks are generated. Prefer vertical slices over layer-by-layer work. A slice may include small changes across model, API, UI, and tests if that is the smallest path to observable behavior.

Flag plans that:

- require a large migration before any behavior can be verified
- mix unrelated workflows
- delay tests until the end
- hide risky integrations behind vague language
- assume parallel work without dependency boundaries

## Task Refinement

When tasks are too broad, split them while preserving traceability to requirements and plan sections.

Each task should include:

- scope
- dependency
- expected artifact or code area
- acceptance check
- whether it can run in parallel

Avoid tasks that only say "build", "wire up", "implement feature", or "finish integration" without a testable boundary.
