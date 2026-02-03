# Quick Task 002: Add Heredoc Avoidance Instruction to GSD Personas

**Completed:** 2026-02-03
**Status:** Done

## Objective

Add a special instruction to every GSD persona (subagent) telling them to avoid executing code via heredocs and instead write throwaway Python scripts to `/tmp/cc/` with random names.

## Changes Made

Added `<execution_notes>` section to all 11 GSD persona files in `agents/`:

| File | Lines Added |
|------|-------------|
| gsd-codebase-mapper.md | 17 |
| gsd-debugger.md | 17 |
| gsd-executor.md | 17 |
| gsd-integration-checker.md | 17 |
| gsd-phase-researcher.md | 17 |
| gsd-plan-checker.md | 17 |
| gsd-planner.md | 17 |
| gsd-project-researcher.md | 17 |
| gsd-research-synthesizer.md | 17 |
| gsd-roadmapper.md | 17 |
| gsd-verifier.md | 17 |

## Instruction Content

```markdown
<execution_notes>
**Avoid heredocs for code execution.** Write throwaway Python scripts to `/tmp/cc/` using the Write tool, then execute. Use `mktemp /tmp/cc/XXXXXX.py` or `/tmp/cc/script_$(date +%s%N).py` for unique names.
</execution_notes>
```

## Verification

```bash
grep -l "/tmp/cc/" agents/gsd-*.md | wc -l
# Result: 11 (all persona files contain the instruction)
```

## Rationale

- Heredoc execution can cause parsing issues in bash
- Writing to `/tmp/cc/` provides cleaner error messages with file references
- Using `$(date +%s%N)` ensures unique filenames to avoid collisions
- The instruction uses Write tool for content, bash only for path generation and execution
