---
name: speckit-clarify
description: >
  Phase 2 of the Spec-Kit pipeline. Resolve every [NEEDS CLARIFICATION] marker
  and underspecified requirement in the spec. In this agency, mechanical
  ambiguities are defaulted-with-assumption and only material ones are routed to
  the human via clarify-gate. Invoked via /speckit.clarify. Owned by the Spec
  Analyst.
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: templates/commands/clarify.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

Run `/speckit.clarify` to drive open questions out of `spec.md`. Pair it with the `clarify-gate` skill: decide mechanical questions yourself (record the assumption), route material ones up to the CEO. The spec is clarified when no material ambiguity remains. See the canonical command template in the referenced source.
