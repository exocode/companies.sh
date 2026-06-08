---
name: speckit-specify
description: >
  Phase 1 of the Spec-Kit pipeline. Delegates to the native /speckit.specify
  command installed in the project. Never copies or re-implements SpecKit logic.
---

# speckit-specify — Native Command Delegation

This skill does not contain SpecKit logic. It delegates to the native command
that SpecKit installed in the project.

## What you do

Invoke the native `/speckit.specify` command that SpecKit installed in this
project. The command lives at:

- `.kilocode/workflows/speckit.specify.md` (KiloCode)
- `.claude/commands/speckit.specify.md` (Claude Code)
- or the equivalent for whatever agent runtime is active

Pass the feature description as the argument.

If the native command is not found, tell the CEO that SpecKit has not been
initialized in this project and suggest running:

```
uvx --from git+https://github.com/github/spec-kit.git specify init .
```

Do not reproduce or approximate SpecKit's specify logic yourself. The native
command is the single source of truth and stays up to date automatically.
