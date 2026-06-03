---
name: clarify-gate
description: >
  The single rule for autonomous-vs-ask. Classifies every uncertainty as
  Mechanical (decide silently with a documented assumption) or Material
  (surface to the human, batched and decision-ready). Keeps the run autonomous
  by default and ensures the human is interrupted only when it truly matters —
  and only through the orchestrator. Used by the orchestrator and every
  specialist that hits an open question.
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: README.md
      url: https://github.com/github/spec-kit/blob/main/README.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

# clarify-gate — when to ask the human

The whole point of this agency is to run without a babysitter. This skill is the discipline that makes that safe: it draws a hard line between decisions an agent should just make and decisions a human must make.

## The two classes

### Mechanical — decide it yourself

An uncertainty is **mechanical** when **all** of these hold:

- A reasonable default exists and a competent engineer would pick it without asking.
- Getting it wrong is cheap to reverse (it's inside one slice, not a foundational choice).
- It does not change scope, contradict the constitution, or affect cost, security, privacy, or data integrity.

→ Decide it. **Record the assumption** where the next phase will see it (inline in the spec/plan, or in the slice's notes). Move on. Do not interrupt the human.

### Material — surface to the human

An uncertainty is **material** when **any** of these hold:

- It changes the scope or the shape of the product.
- It contradicts, or isn't covered by, the project constitution.
- It risks cost, security, privacy, data loss, or irreversible external side effects.
- It is genuinely undecidable from the spec, plan, and context — no defensible default exists.
- It requires knowledge only the human/stakeholder has (a business rule, a preference, a credential, an external constraint).

→ Do **not** decide it silently. Route it to the **orchestrator (CEO)**, who is the only agent that talks to the human.

## Routing rule

Specialists never message the user directly. A material question goes **up the chain to the CEO**. The CEO batches all open material questions and asks them in one round, each with a recommended default.

## How to ask (CEO only)

When you do surface questions:

1. **Batch.** One round, all open material questions together. Never drip one at a time.
2. **Be decision-ready.** Each question states the options, your recommended default, and what downstream work changes based on the answer.
3. **Keep moving where you can.** Park the blocked slice and continue any independent work while you wait — do not stall the whole run on one question.

## Default bias

When in doubt between mechanical and material, weigh reversibility and blast radius. Cheap-and-reversible → mechanical (decide, document, proceed). Expensive-or-irreversible → material (ask). Over-asking defeats autonomy; over-deciding risks building the wrong thing. The bias is toward action on small reversible things and toward asking on large irreversible ones.
