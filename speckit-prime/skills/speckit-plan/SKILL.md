---
name: speckit-plan
description: >
  Phase 4 of the Spec-Kit pipeline. Delegates to the native /speckit.plan
  command installed in the project. Never copies or re-implements SpecKit logic.
---

# speckit-plan — Native Command Delegation

This skill does not contain SpecKit logic. It delegates to the native command
that SpecKit installed in the project.

## What you do

Invoke the native `/speckit.plan` command that SpecKit installed in this
project. The command lives at:

- `.kilocode/workflows/speckit.plan.md` (KiloCode)
- `.claude/commands/speckit.plan.md` (Claude Code)
- or the equivalent for whatever agent runtime is active

This command produces the technical implementation plan.

If the native command is not found, tell the CTO (who tells the CEO) that
SpecKit has not been initialized in this project and suggest running:

```
uvx --from git+https://github.com/github/spec-kit.git specify init .
```

Do not reproduce or approximate SpecKit's plan logic yourself. The native
command is the single source of truth and stays up to date automatically.
