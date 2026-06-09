---
name: speckit-implement
description: >
  Phase 6 of the Spec-Kit pipeline. Delegates to the native /speckit.implement
  command installed in the project. Scope-limits context loading to reduce
  token usage. Never copies or re-implements SpecKit logic.
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

Pass the assigned slice ID as the argument so the command can scope its work.

## Context loading — scope to the slice

Before invoking the native command, apply these loading rules to minimise
context size and token usage:

**tasks.md** — do not load the full file. Extract only:
- The task entry matching your assigned slice ID
- Its direct dependency tasks (prerequisites listed in that entry)
- The phase header for orientation

If you are not given a specific slice ID, load the first unchecked `[ ]`
task only.

**contracts/** — load only the contract files explicitly referenced in your
assigned task entry. If none are referenced, skip contracts entirely.

**constitution.md** — do not re-read. Your agent instructions already encode
the constitution constraints. Only load the file if you need to quote a
specific clause that is disputed in the current issue.

**plan.md, data-model.md, research.md** — load on demand if a specific
question arises during implementation. Do not pre-load all planning docs
at session start.

## Token discipline

- Read all files you will need for a fix *before* writing any of them.
  Do not re-read files already in your context.
- If you have made more than 35 tool calls: stop, commit what is done,
  comment progress, let the harness retry the remainder.
- Do not mark tasks `[x]` yourself — the CTO does this after QA PASS.
  Report your completed slice ID clearly so the CTO can apply the mark.

## If the native command is not found

Tell the CTO that SpecKit has not been initialized in this project and
suggest running:

```
uvx --from git+https://github.com/github/spec-kit.git specify init .
```

Do not reproduce or approximate SpecKit's implement logic yourself.
