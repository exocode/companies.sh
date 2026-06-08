---
name: speckit-clarify
description: >
  Phase 2 of the Spec-Kit pipeline. Delegates to the native /speckit.clarify
  command installed in the project. Never copies or re-implements SpecKit logic.
  In interactive mode the Spec Analyst pauses after clarify and presents all
  open questions to the human via the CEO before continuing.
---

# speckit-clarify — Native Command Delegation + Human Gate

This skill does not contain SpecKit logic. It delegates to the native command
and adds a mandatory human review gate (Option B interactive mode).

## What you do

1. Invoke the native `/speckit.clarify` command that SpecKit installed in this
   project. The command lives at:
   - `.kilocode/workflows/speckit.clarify.md` (KiloCode)
   - `.claude/commands/speckit.clarify.md` (Claude Code)
   - or the equivalent for whatever agent runtime is active

2. **Human gate — mandatory after every clarify run:**
   After the native command completes, do NOT hand back to the CEO silently.
   Instead, compile all questions and answers from the clarify session and
   route them to the CEO with this exact format:

   ```
   CLARIFY SUMMARY — human review required before advancing

   The following questions were resolved during /speckit.clarify.
   Please confirm, override, or add context for any of these:

   Q1: <question> → resolved as: <answer> [source: auto-resolved / user]
   Q2: ...

   Any unresolved or overridden answers will be written back to spec.md
   before the pipeline continues to checklist and planning.

   Reply "confirmed" to advance, or provide corrections.
   ```

3. Wait for the CEO to relay the human's response before marking clarify done.
   If the human overrides an answer, update spec.md accordingly.
   Only after human confirmation does clarify count as complete.

If the native command is not found, tell the CEO that SpecKit has not been
initialized and suggest `uvx --from git+https://github.com/github/spec-kit.git specify init .`

Do not reproduce or approximate SpecKit's clarify logic yourself.
