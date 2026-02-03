# Phase 2: Command Tables - Research

**Researched:** 2026-02-02
**Domain:** Markdown table editing (documentation consistency)
**Confidence:** HIGH

## Summary

Phase 2 adds the "unlimited" column to model lookup tables in 8 command files. This is a straightforward file editing task with no code or external libraries involved. Each command file contains a markdown table that maps agent names to model assignments per profile. The task is to add a new `unlimited` column matching the pattern established in Phase 1's master reference.

All 8 files follow the same pattern: a markdown table with header row `| Agent | quality | balanced | budget |` needs to become `| Agent | unlimited | quality | balanced | budget |`. The unlimited column value for every agent is `opus` (per prior decision 01-01).

**Primary recommendation:** Edit each file's model lookup table to add `unlimited` column as the first profile column (after Agent), with `opus` for all agents.

## Standard Stack

This phase requires no libraries or code - it's pure markdown documentation editing.

### Core
| Tool | Purpose | Why Standard |
|------|---------|--------------|
| Markdown | Document format | Native to Claude Code slash commands |
| Markdown tables | Data structure | Existing pattern in all command files |

### Supporting
N/A - documentation-only phase.

### Alternatives Considered
N/A - no alternatives needed for direct file editing.

## Architecture Patterns

### Target Files and Their Tables

All 8 files are in `commands/gsd/`:

| File | Agents in Table | Current Columns |
|------|-----------------|-----------------|
| audit-milestone.md | gsd-integration-checker | quality, balanced, budget |
| debug.md | gsd-debugger | quality, balanced, budget |
| execute-phase.md | gsd-executor, gsd-verifier | quality, balanced, budget |
| new-milestone.md | gsd-project-researcher, gsd-research-synthesizer, gsd-roadmapper | quality, balanced, budget |
| new-project.md | gsd-project-researcher, gsd-research-synthesizer, gsd-roadmapper | quality, balanced, budget |
| plan-phase.md | gsd-phase-researcher, gsd-planner, gsd-plan-checker | quality, balanced, budget |
| quick.md | gsd-planner, gsd-executor | quality, balanced, budget |
| research-phase.md | gsd-phase-researcher | quality, balanced, budget |

### Pattern 1: Column Addition to Existing Table
**What:** Add new column while preserving existing structure
**When to use:** All 8 files follow this pattern
**Example:**
```markdown
# Current format (3 columns):
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-debugger | opus | sonnet | sonnet |

# Target format (4 columns):
| Agent | unlimited | quality | balanced | budget |
|-------|-----------|---------|----------|--------|
| gsd-debugger | opus | opus | sonnet | sonnet |
```

**Critical:** Column order is `unlimited | quality | balanced | budget` (highest to lowest tier, left to right) per Phase 1 decision.

### Pattern 2: Agent-to-Model Lookup from Master Reference
**What:** Use canonical model-profiles.md as source of truth for all values
**When to use:** When adding unlimited column values
**Reference table (from Phase 1):**

```markdown
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-planner | opus | opus | opus | sonnet |
| gsd-roadmapper | opus | opus | sonnet | sonnet |
| gsd-executor | opus | opus | sonnet | sonnet |
| gsd-phase-researcher | opus | opus | sonnet | haiku |
| gsd-project-researcher | opus | opus | sonnet | haiku |
| gsd-research-synthesizer | opus | sonnet | sonnet | haiku |
| gsd-debugger | opus | opus | sonnet | sonnet |
| gsd-codebase-mapper | opus | sonnet | haiku | haiku |
| gsd-verifier | opus | sonnet | sonnet | haiku |
| gsd-plan-checker | opus | sonnet | sonnet | haiku |
| gsd-integration-checker | opus | sonnet | sonnet | haiku |
```

### Anti-Patterns to Avoid
- **Mismatched column order:** All tables must use `unlimited | quality | balanced | budget` order
- **Non-opus unlimited values:** Per prior decision 01-01, unlimited profile uses opus for ALL agents
- **Header format mismatch:** Use backticks around profile names in headers for consistency with master reference

## Don't Hand-Roll

Not applicable - this is a documentation phase with no code.

## Common Pitfalls

### Pitfall 1: Column Order Inconsistency
**What goes wrong:** Different files have columns in different order
**Why it happens:** Copy-paste errors or not following established pattern
**How to avoid:** Always use order: `unlimited | quality | balanced | budget`
**Warning signs:** Comparing tables across files shows different column positions

### Pitfall 2: Header Separator Row Length Mismatch
**What goes wrong:** Table renders incorrectly due to wrong number of dashes/columns
**Why it happens:** Adding column to header but forgetting separator row
**How to avoid:** Add `-----------|` to separator row for new column
**Warning signs:** Markdown preview shows broken table

### Pitfall 3: Missing Backticks on Profile Names
**What goes wrong:** Inconsistency with master reference format
**Why it happens:** Not checking master reference format
**How to avoid:** Use backticks: `` `unlimited` `` not just `unlimited` in header
**Warning signs:** Header format differs from model-profiles.md

### Pitfall 4: Incorrect Model Values in Existing Columns
**What goes wrong:** Accidentally changing existing quality/balanced/budget values
**Why it happens:** Column insertion error
**How to avoid:** Only add the unlimited column; don't modify existing values
**Warning signs:** Running git diff shows changes to non-unlimited columns

### Pitfall 5: Inconsistent Alignment/Spacing
**What goes wrong:** Tables look different across files
**Why it happens:** Different whitespace handling
**How to avoid:** Match exact spacing pattern of existing table
**Warning signs:** Visual inspection shows formatting differences

## Code Examples

Verified patterns from the 8 command files:

### audit-milestone.md Current Table
```markdown
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-integration-checker | sonnet | sonnet | haiku |
```

### audit-milestone.md Target Table
```markdown
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-integration-checker | opus | sonnet | sonnet | haiku |
```

### debug.md Current Table
```markdown
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-debugger | opus | sonnet | sonnet |
```

### debug.md Target Table
```markdown
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-debugger | opus | opus | sonnet | sonnet |
```

### execute-phase.md Current Table
```markdown
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-executor | opus | sonnet | sonnet |
| gsd-verifier | sonnet | sonnet | haiku |
```

### execute-phase.md Target Table
```markdown
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-executor | opus | opus | sonnet | sonnet |
| gsd-verifier | opus | sonnet | sonnet | haiku |
```

### new-milestone.md / new-project.md Current Table
```markdown
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-project-researcher | opus | sonnet | haiku |
| gsd-research-synthesizer | sonnet | sonnet | haiku |
| gsd-roadmapper | opus | sonnet | sonnet |
```

### new-milestone.md / new-project.md Target Table
```markdown
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-project-researcher | opus | opus | sonnet | haiku |
| gsd-research-synthesizer | opus | sonnet | sonnet | haiku |
| gsd-roadmapper | opus | opus | sonnet | sonnet |
```

### plan-phase.md Current Table
```markdown
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-phase-researcher | opus | sonnet | haiku |
| gsd-planner | opus | opus | sonnet |
| gsd-plan-checker | sonnet | sonnet | haiku |
```

### plan-phase.md Target Table
```markdown
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-phase-researcher | opus | opus | sonnet | haiku |
| gsd-planner | opus | opus | opus | sonnet |
| gsd-plan-checker | opus | sonnet | sonnet | haiku |
```

### quick.md Current Table
```markdown
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-planner | opus | opus | sonnet |
| gsd-executor | opus | sonnet | sonnet |
```

### quick.md Target Table
```markdown
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-planner | opus | opus | opus | sonnet |
| gsd-executor | opus | opus | sonnet | sonnet |
```

### research-phase.md Current Table
```markdown
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-phase-researcher | opus | sonnet | haiku |
```

### research-phase.md Target Table
```markdown
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-phase-researcher | opus | opus | sonnet | haiku |
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| 3 profiles in tables | 4 profiles (adding unlimited) | Phase 1-2 | Users can select opus for all agents |

**Current state:**
- model-profiles.md (master) updated with unlimited column (Phase 1 complete)
- 8 command files still have 3-column tables
- 4 workflow files (Phase 3) still have 3-column tables

## Open Questions

None for this phase - the scope is well-defined:
1. Add unlimited column to 8 command files
2. Use opus for all agents in unlimited column
3. Match column order and format from master reference

## Sources

### Primary (HIGH confidence)
- `/Users/vmakaev/NonWork/get-shit-done/get-shit-done/references/model-profiles.md` - Canonical master reference (Phase 1 output)
- `/Users/vmakaev/NonWork/get-shit-done/commands/gsd/*.md` - All 8 command files analyzed directly

### Secondary (MEDIUM confidence)
- `/Users/vmakaev/NonWork/get-shit-done/.planning/REQUIREMENTS.md` - TABLE-02 through TABLE-09 specifications
- `/Users/vmakaev/NonWork/get-shit-done/.planning/STATE.md` - Prior decisions 01-01

### Tertiary (LOW confidence)
None - all findings from authoritative project sources.

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - This is pure markdown, no libraries needed
- Architecture: HIGH - All 8 files analyzed, patterns clear
- Pitfalls: HIGH - Based on actual file analysis and Phase 1 experience

**Research date:** 2026-02-02
**Valid until:** Stable (documentation format unlikely to change)
