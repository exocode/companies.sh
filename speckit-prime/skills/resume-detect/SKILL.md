---
name: resume-detect
description: >
  Pre-flight scan — always the first step. Detect which Spec-Kit artifacts
  already exist, resolve the active feature directory, and return the earliest
  incomplete phase so the pipeline resumes correctly instead of restarting.
---

# Resume Detect — Pre-Flight Scan

Always run this skill first, before any other pipeline phase. It prevents restarting work that already exists.

## What you do

1. **Resolve FEATURE_DIR** in this order:
   - Explicit `SPECIFY_FEATURE_DIRECTORY` env or argument
   - `.specify/feature.json` -> `feature_directory` field
   - Run `.specify/scripts/bash/check-prerequisites.sh --json` if it exists
   - Scan `specs/` for directories matching `NNN-*` or timestamp pattern, pick the highest-numbered one
   - If ambiguous, ask the CEO (who asks the human)

2. **Scan for existing artifacts** in `FEATURE_DIR`:
   - `.specify/memory/constitution.md` -> constitution done
   - `spec.md` -> specify done
   - `spec.md` with no `[NEEDS CLARIFICATION]` markers -> clarify done
   - `checklists/` directory with files -> checklist done
   - `plan.md` -> plan done
   - `tasks.md` -> tasks done
   - `tasks.md` with all tasks as small vertical slices -> refine-slices done
   - analyze output or no blocking findings -> analyze done
   - implementation evidence (commits, test results) -> implement in progress or done

3. **Return a resume ledger**:
```
FEATURE_DIR: <resolved path>
ENTRY_PHASE: <0-7a>
EXISTING_ARTIFACTS: <list>
STALE_WARNINGS: <list of any artifacts that may be out of sync>
NEXT_ACTION: <what the CEO should dispatch next>
```

## Stale artifact detection

Flag an artifact as potentially stale if:
- `plan.md` is older than `spec.md` (spec was updated after planning)
- `tasks.md` is older than `plan.md`
- Implementation is ahead of the analyze pass

Do not automatically discard stale artifacts — report them and let the CEO decide whether to rerun the affected phase.
