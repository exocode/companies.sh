---
name: speckit-implement
description: >
  Phase 7 of the Spec-Kit pipeline. Delegates to the native /speckit.implement
  command installed in the project. Never copies or re-implements SpecKit logic.
---

# speckit-implement — Native Command Delegation

This skill does not contain SpecKit logic. It delegates to the native command
that SpecKit installed in the project.

## What you do

Invoke the native `/speckit.implement` command that SpecKit installed in this
project. The command lives at:

- `.kilocode/workflows/speckit.implement.md` (KiloCode)
- `.claude/commands/speckit.implement.md` (Claude Code)
- or the equivalent for whatever agent runtime is active

This command produces the slice implementation.

If the native command is not found, tell the CTO (who tells the CEO) that
SpecKit has not been initialized in this project and suggest running:

```
uvx --from git+https://github.com/github/spec-kit.git specify init .
```

Do not reproduce or approximate SpecKit's implement logic yourself. The native
command is the single source of truth and stays up to date automatically.
