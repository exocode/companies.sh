---
name: speckit-analyze
description: >
  Phase 6 of the Spec-Kit pipeline. Delegates to the native /speckit.analyze
  command installed in the project. Never copies or re-implements SpecKit logic.
---

# speckit-analyze — Native Command Delegation

This skill does not contain SpecKit logic. It delegates to the native command
that SpecKit installed in the project.

## What you do

Invoke the native `/speckit.analyze` command that SpecKit installed in this
project. The command lives at:

- `.kilocode/workflows/speckit.analyze.md` (KiloCode)
- `.claude/commands/speckit.analyze.md` (Claude Code)
- or the equivalent for whatever agent runtime is active

This command produces the cross-artifact consistency check.

If the native command is not found, tell the CTO (who tells the CEO) that
SpecKit has not been initialized in this project and suggest running:

```
uvx --from git+https://github.com/github/spec-kit.git specify init .
```

Do not reproduce or approximate SpecKit's analyze logic yourself. The native
command is the single source of truth and stays up to date automatically.
