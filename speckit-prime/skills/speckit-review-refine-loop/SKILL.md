---
name: speckit-review-refine-loop
description: Add bounded QA and review loops to Spec Kit runs by routing findings to the earliest affected phase
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: docs/reference/workflows.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

# Spec Kit Review Refine Loop

Use this skill during `/speckit.analyze`, before `/speckit.implement`, and after implementation.

## Review Decision

Return one of:

- pass
- pass with notes
- refine required
- user decision required

Every refine finding must include:

- earliest affected phase
- artifact section
- problem
- required change
- downstream commands to rerun

## Earliest Phase Routing

Route findings to the earliest phase that can fix the cause:

- `/speckit.clarify` for missing product decisions
- `/speckit.checklist` for weak acceptance criteria
- `/speckit.plan` for architecture, dependency, or risk problems
- `/speckit.tasks` for task size, order, or missing work
- `/speckit.implement` for code, test, or evidence defects

Do not patch implementation symptoms when the defect belongs in the spec, checklist, plan, or task structure.

## Loop Bounds

Keep loops small:

- refine only the affected slice
- preserve stable task identifiers where possible
- rerun downstream commands only when their inputs changed
- escalate after repeated failure on the same decision

The goal is autonomous progress, not endless replanning.
