---
name: speckit-constitution
description: >
  Phase 0 of the Spec-Kit pipeline. Establish or load the project constitution —
  the governing principles every later phase is held to. Invoked via the
  runtime's /speckit.constitution slash command. Owned by the CEO.
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: templates/commands/constitution.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

Run `/speckit.constitution` to create or update the project constitution. This is the first phase of `spec-flow` and runs once per project. If a constitution already exists, load it instead of overwriting it, and hold every downstream phase accountable to it. See the canonical command template in the referenced source.
