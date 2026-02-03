---
quick: 002
type: execute
files_modified:
  - agents/gsd-codebase-mapper.md
  - agents/gsd-integration-checker.md
  - agents/gsd-roadmapper.md
  - agents/gsd-debugger.md
  - agents/gsd-executor.md
  - agents/gsd-phase-researcher.md
  - agents/gsd-plan-checker.md
  - agents/gsd-planner.md
  - agents/gsd-project-researcher.md
  - agents/gsd-research-synthesizer.md
  - agents/gsd-verifier.md
autonomous: true

must_haves:
  truths:
    - "All 11 GSD persona files contain heredoc avoidance instruction"
    - "Instruction clearly explains to write throwaway Python to /tmp/cc/"
    - "Instruction includes example command"
  artifacts:
    - path: "agents/gsd-*.md"
      provides: "Updated persona files with execution notes"
      contains: "/tmp/cc/"
---

<objective>
Add a special instruction to every GSD persona (subagent) telling them to avoid executing code via heredocs and instead write throwaway Python scripts to /tmp/cc/ with random names.

Purpose: Prevents heredoc-related issues in bash execution and provides cleaner code execution pattern.
Output: All 11 gsd-*.md files in agents/ updated with new instruction section.
</objective>

<context>
@.planning/STATE.md
</context>

<tasks>

<task type="auto">
  <name>Task 1: Add execution notes section to all GSD persona files</name>
  <files>
    agents/gsd-codebase-mapper.md
    agents/gsd-integration-checker.md
    agents/gsd-roadmapper.md
    agents/gsd-debugger.md
    agents/gsd-executor.md
    agents/gsd-phase-researcher.md
    agents/gsd-plan-checker.md
    agents/gsd-planner.md
    agents/gsd-project-researcher.md
    agents/gsd-research-synthesizer.md
    agents/gsd-verifier.md
  </files>
  <action>
Add a new section at the end of each GSD persona file (before the final blank line if present).

The section should be:

```markdown

<execution_notes>
**Avoid heredocs for code execution.** When you need to execute throwaway Python code, write it to a file instead of using bash heredocs:

```bash
mkdir -p /tmp/cc && cat > /tmp/cc/script_$(date +%s%N).py << 'PYEOF'
# your Python code here
PYEOF
python /tmp/cc/script_$(date +%s%N).py
```

Or more simply, write the file using the Write tool, then execute it:
- Write to: `/tmp/cc/{descriptive_name}.py`
- Execute: `python /tmp/cc/{descriptive_name}.py`

This avoids heredoc parsing issues and provides cleaner error messages.
</execution_notes>
```

Process all 11 files. For each file:
1. Read the current content
2. Append the execution_notes section at the end
3. Write the updated content
  </action>
  <verify>
Run: `grep -l "/tmp/cc/" agents/gsd-*.md | wc -l`
Expected: 11 (all persona files contain the instruction)
  </verify>
  <done>All 11 GSD persona files contain the execution_notes section with heredoc avoidance instruction</done>
</task>

</tasks>

<verification>
- [ ] `grep -c "execution_notes" agents/gsd-*.md` shows 11 files with matches
- [ ] `grep -c "/tmp/cc/" agents/gsd-*.md` shows 11 files with matches
- [ ] Sample check: Read agents/gsd-executor.md and confirm execution_notes section appears at end
</verification>

<success_criteria>
- All 11 GSD persona files (agents/gsd-*.md) contain the new execution_notes section
- The instruction clearly explains to avoid heredocs and use /tmp/cc/ instead
- No existing content in the persona files is removed or corrupted
</success_criteria>

<output>
Update STATE.md Quick Tasks Completed table with:
| 002 | Add heredoc avoidance instruction to GSD personas | {date} | [002-add-heredoc-avoidance-instruction-to-gsd-personas](./quick/002-add-heredoc-avoidance-instruction-to-gsd-personas/) |
</output>
