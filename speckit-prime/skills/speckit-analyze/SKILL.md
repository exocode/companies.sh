---
name: speckit-analyze
description: >
  Phase 6 of the Spec-Kit pipeline. Cross-artifact consistency check across
  spec ↔ plan ↔ tasks — catch contradictions, gaps, and requirements with no
  implementing task before any code is written. Invoked via /speckit.analyze.
  Owned by the QA Reviewer.
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: templates/commands/analyze.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

Run `/speckit.analyze` after slice refinement and before implementation. Inconsistencies route back to the offending artifact's owner via the CTO, then re-flow forward. Do not start `implement` on a failing analyze. See the canonical command template in the referenced source.
