---
name: speckit-implement
description: >
  Phase 7 of the Spec-Kit pipeline. Execute the refined slices in dependency
  order — small, coherent, reversible commits to each slice's done-condition.
  Always followed by the qa-review loop. Invoked via /speckit.implement. Owned
  by the Implementation Engineer.
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: templates/commands/implement.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

Run `/speckit.implement` to build the slices. One vertical slice at a time, to its done-condition, running the project's tests after each. Never mark a slice done on a red build. Each completed slice goes to the `qa-review` loop. See the canonical command template in the referenced source.
