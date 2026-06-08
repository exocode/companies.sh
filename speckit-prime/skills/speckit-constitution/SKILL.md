---
name: speckit-constitution
description: >
  Phase 0 of the Spec-Kit pipeline. Delegates to the native /speckit.constitution
  command installed in the project. Never copies or re-implements SpecKit logic.
---

# speckit-constitution — Native Command Delegation

This skill does not contain SpecKit logic. It delegates to the native command
that SpecKit installed in the project.

## What you do

Invoke the native `/speckit.constitution` command that SpecKit installed in this
project. The command lives at:

- `.kilocode/workflows/speckit.constitution.md` (KiloCode)
- `.claude/commands/speckit.constitution.md` (Claude Code)
- or the equivalent for whatever agent runtime is active

This command produces the project constitution.

If the native command is not found, tell the CTO (who tells the CEO) that
SpecKit has not been initialized in this project and suggest running:

```
uvx --from git+https://github.com/github/spec-kit.git specify init .
```

Do not reproduce or approximate SpecKit's constitution logic yourself. The native
command is the single source of truth and stays up to date automatically.
