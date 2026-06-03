---
name: speckit-plan
description: >
  Phase 4 of the Spec-Kit pipeline. Produce plan.md and supporting design
  artifacts (research, data-model, contracts, quickstart) — architecture,
  technology, data model, boundaries — every choice traceable to a requirement.
  Invoked via /speckit.plan. Owned by the Solution Architect.
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: templates/commands/plan.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

Run `/speckit.plan` to lock the technical approach. Keep it implementable, traceable to the spec, and inside the constitution — detailed enough for clean task decomposition, no gold-plating. See the canonical command template in the referenced source.
