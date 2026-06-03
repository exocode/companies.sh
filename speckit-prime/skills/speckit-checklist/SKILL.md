---
name: speckit-checklist
description: >
  Phase 3 of the Spec-Kit pipeline. Generate the quality checklists the spec and
  plan must satisfy — the acceptance gates the QA Reviewer later reviews against.
  Invoked via /speckit.checklist. Owned by the QA Reviewer.
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: templates/commands/checklist.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

Run `/speckit.checklist` to produce the quality gates up front, before planning. These checklists become the fixed acceptance criteria the `qa-review` loop checks each implemented slice against. See the canonical command template in the referenced source.
