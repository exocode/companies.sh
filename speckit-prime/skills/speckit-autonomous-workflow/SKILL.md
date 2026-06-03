---
name: speckit-autonomous-workflow
description: Run the full Spec Kit lifecycle autonomously while preserving command order and escalating only genuine user decisions
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: README.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

# Spec Kit Autonomous Workflow

Use this skill when coordinating a Spec Kit run across agents or orchestration systems.

## Runtime Agnosticism

Do not assume a specific model, provider, router, adapter, coding agent, CLI, or orchestrator. Specify capabilities, artifacts, decisions, and handoffs. The importing environment decides which runtime executes them.

## Spec Kit Structure

Do not invent an alternate artifact layout. Use the active Spec Kit feature directory and local helper script output when available. The usual feature path is `specs/<feature-id>/`, with `.specify/feature.json` used by downstream commands to locate the active feature.

## Hard Invariant

Always preserve this order:

```text
/speckit.constitution -> /speckit.specify -> /speckit.clarify -> /speckit.checklist -> /speckit.plan -> /speckit.tasks -> /speckit.analyze -> /speckit.implement
```

Do not skip a command because the request seems simple. If an artifact already exists, inspect it and decide whether it is current enough to reuse.

## Autonomy Rules

Ask the user only when a decision cannot be inferred safely:

- product intent or target user changes
- legal, compliance, privacy, or security risk acceptance
- credentials, production access, paid services, or destructive operations
- mutually exclusive behavior with no clear default
- stakeholder preference that changes the value proposition

For engineering choices with a safe default, choose conservatively, document the assumption, and continue.

## Run Ledger

Maintain a short run ledger with:

- current phase
- active feature directory
- artifact paths
- script outputs used for path resolution
- assumptions
- user-blocking questions
- refine-loop count
- next action

The ledger is not a substitute for Spec Kit artifacts. It is runtime-neutral resumability glue for an orchestrator.
