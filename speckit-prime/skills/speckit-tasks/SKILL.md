---
name: speckit-tasks
description: >
  Phase 5 of the Spec-Kit pipeline. Break the plan into tasks.md with ordering,
  dependencies, and parallelization markers. In this agency it is always
  followed by the refine-slices pass to cut tasks down to small vertical slices.
  Invoked via /speckit.tasks. Owned by the Task Slicer.
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: templates/commands/tasks.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

Run `/speckit.tasks` to generate `tasks.md` from the plan, then immediately run the `refine-slices` skill — Spec-Kit's tasks are often too coarse for tight implement→review cycles. See the canonical command template in the referenced source.
