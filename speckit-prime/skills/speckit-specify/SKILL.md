---
name: speckit-specify
description: >
  Phase 1 of the Spec-Kit pipeline. Turn a feature idea into spec.md — user
  scenarios, functional requirements, success criteria — as testable outcomes,
  not implementation detail. Invoked via /speckit.specify. Owned by the Spec
  Analyst.
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: templates/commands/specify.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

Run `/speckit.specify` to produce `spec.md` from the feature idea. Write requirements as observable, testable outcomes; leave implementation choices to the plan phase. See the canonical command template in the referenced source.
