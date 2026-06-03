---
name: speckit-resume-run
description: Resume a Spec Kit workflow from existing artifacts by detecting the entrypoint, validating upstream context, repairing gaps, and continuing at the next valid command
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

# Spec Kit Resume Run

Use this skill when a Spec Kit project already has artifacts and the workflow should continue instead of starting over.

This skill is runtime-neutral. It must work whether artifact discovery and command execution are handled by a local CLI, hosted orchestrator, coding agent, or any compatible LLM-backed system.

## Resume Protocol

Follow this sequence:

```text
detect artifacts -> classify entrypoint -> validate upstream chain -> repair gaps -> resume at next valid command
```

Never assume the latest-looking artifact is valid. A `tasks.md` file is not enough if it no longer traces to the current plan or spec.

## Artifact Detection

Look for the project-local Spec Kit artifact chain:

- `.specify/feature.json`
- `.specify/memory/constitution.md`
- `specs/<feature-id>/spec.md`
- `specs/<feature-id>/checklists/`
- `specs/<feature-id>/plan.md`
- `specs/<feature-id>/research.md`
- `specs/<feature-id>/data-model.md`
- `specs/<feature-id>/contracts/`
- `specs/<feature-id>/quickstart.md`
- `specs/<feature-id>/tasks.md`
- analyze output or unresolved findings
- implementation branch, diff, tests, or verification evidence

Record artifact paths, timestamps when available, script JSON outputs, and any visible feature identifier. If multiple feature directories exist, prefer `.specify/feature.json` or `SPECIFY_FEATURE_DIRECTORY` over branch-name inference. Ask only if no safe match exists.

## Entrypoint Classification

Use the deepest valid artifact as the tentative entrypoint:

- only constitution is valid: continue with `/speckit.specify`
- spec is valid but unclear: continue with `/speckit.clarify`
- clarify/checklist are valid: continue with `/speckit.plan`
- plan is valid: run slice refinement, then continue with `/speckit.tasks`
- tasks are valid: run task refinement, then continue with `/speckit.analyze`
- analysis is valid and findings are resolved: continue with `/speckit.implement`
- implementation exists: validate the full artifact chain, then QA or refine the smallest affected slice

## Upstream Validation

Before resuming, check:

- freshness: artifact names, feature identifiers, and assumptions match the current request
- traceability: tasks trace to plan sections and requirements
- completeness: required prior artifacts exist or can be reconstructed safely
- consistency: no known analyze findings contradict the next command
- slice size: plan and tasks are small enough for focused implementation
- script compatibility: local `.specify/scripts/{bash,powershell}/` helpers exist or a reasonable fallback is documented

## Gap Repair

Repair gaps at the earliest affected phase:

- missing or stale spec: run or repair `/speckit.specify`
- unanswered product decision: run `/speckit.clarify`
- weak acceptance criteria: run `/speckit.checklist`
- stale or broad plan: refine or rerun `/speckit.plan`
- broad or untraceable tasks: refine or rerun `/speckit.tasks`
- missing consistency evidence: run `/speckit.analyze`

Only rerun downstream commands when their inputs changed.

## User Escalation

Ask the user only when artifact selection or gap repair changes product intent, accepts risk, requires credentials, or chooses between incompatible existing artifact chains.
