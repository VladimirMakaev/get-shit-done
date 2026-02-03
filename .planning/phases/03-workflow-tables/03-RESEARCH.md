# Phase 3: Workflow Tables - Research

**Researched:** 2026-02-02
**Domain:** Markdown table editing (documentation consistency)
**Confidence:** HIGH

## Summary

Phase 3 adds the "unlimited" column to model lookup tables in 4 workflow files. This is a direct continuation of Phase 2 work, applying the exact same pattern to workflow files instead of command files. Each workflow file contains a markdown table that maps agent names to model assignments per profile, and needs the `unlimited` column added.

The 4 target workflow files are in `get-shit-done/workflows/`:
- `execute-phase.md` - Phase execution orchestrator
- `execute-plan.md` - Plan execution workflow
- `map-codebase.md` - Codebase mapping orchestrator
- `verify-work.md` - UAT and verification workflow

**Primary recommendation:** Edit each workflow file's model lookup table to add `unlimited` column as the first profile column (after Agent), with values from the canonical model-profiles.md reference.

## Standard Stack

This phase requires no libraries or code - it's pure markdown documentation editing.

### Core
| Tool | Purpose | Why Standard |
|------|---------|--------------|
| Markdown | Document format | Native to Claude Code workflows |
| Markdown tables | Data structure | Existing pattern in all workflow files |

### Supporting
N/A - documentation-only phase.

### Alternatives Considered
N/A - no alternatives needed for direct file editing.

## Architecture Patterns

### Target Files and Their Tables

All 4 files are in `get-shit-done/workflows/`:

| File | Agents in Table | Current Columns |
|------|-----------------|-----------------|
| execute-phase.md | gsd-executor, gsd-verifier, general-purpose | quality, balanced, budget |
| execute-plan.md | gsd-executor | quality, balanced, budget |
| map-codebase.md | gsd-codebase-mapper | quality, balanced, budget |
| verify-work.md | gsd-planner, gsd-plan-checker | quality, balanced, budget |

### Pattern 1: Column Addition to Existing Table
**What:** Add new column while preserving existing structure
**When to use:** All 4 files follow this pattern
**Example (from Phase 2):**
```markdown
# Current format (3 columns):
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-executor | opus | sonnet | sonnet |

# Target format (4 columns):
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-executor | opus | opus | sonnet | sonnet |
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
| gsd-executor | opus | opus | sonnet | sonnet |
| gsd-codebase-mapper | opus | sonnet | haiku | haiku |
| gsd-verifier | opus | sonnet | sonnet | haiku |
| gsd-plan-checker | opus | sonnet | sonnet | haiku |
```

**Note:** `general-purpose` row in execute-phase.md uses dashes (---) for all profiles - this should remain unchanged.

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

### Pitfall 5: Forgetting General-Purpose Row
**What goes wrong:** Adding opus to the general-purpose row
**Why it happens:** Applying same pattern to special row
**How to avoid:** general-purpose row uses dashes (---) for all profiles, keep unchanged
**Warning signs:** File diff shows general-purpose row changed

### Pitfall 6: Wrong Agent Model Lookups
**What goes wrong:** Using wrong model value for an agent
**Why it happens:** Not consulting model-profiles.md master reference
**How to avoid:** Always look up values from `get-shit-done/references/model-profiles.md`
**Warning signs:** Values don't match master reference

## Code Examples

Verified patterns from the 4 workflow files:

### execute-phase.md Current Table
```markdown
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-executor | opus | sonnet | sonnet |
| gsd-verifier | sonnet | sonnet | haiku |
| general-purpose | --- | --- | --- |
```

### execute-phase.md Target Table
```markdown
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-executor | opus | opus | sonnet | sonnet |
| gsd-verifier | opus | sonnet | sonnet | haiku |
| general-purpose | --- | --- | --- | --- |
```

### execute-plan.md Current Table
```markdown
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-executor | opus | sonnet | sonnet |
```

### execute-plan.md Target Table
```markdown
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-executor | opus | opus | sonnet | sonnet |
```

### map-codebase.md Current Table
```markdown
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-codebase-mapper | sonnet | haiku | haiku |
```

### map-codebase.md Target Table
```markdown
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-codebase-mapper | opus | sonnet | haiku | haiku |
```

### verify-work.md Current Table
```markdown
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-planner | opus | opus | sonnet |
| gsd-plan-checker | sonnet | sonnet | haiku |
```

### verify-work.md Target Table
```markdown
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-planner | opus | opus | opus | sonnet |
| gsd-plan-checker | opus | sonnet | sonnet | haiku |
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| 3 profiles in tables | 4 profiles (adding unlimited) | Phase 1-3 | Users can select opus for all agents |

**Current state:**
- model-profiles.md (master) updated with unlimited column (Phase 1 complete)
- 8 command files updated with unlimited column (Phase 2 complete)
- 4 workflow files still have 3-column tables (this phase)

## Open Questions

None for this phase - the scope is well-defined:
1. Add unlimited column to 4 workflow files
2. Use opus for all agents in unlimited column (except general-purpose which stays ---)
3. Match column order and format from master reference

## Sources

### Primary (HIGH confidence)
- `/Users/vmakaev/NonWork/get-shit-done/get-shit-done/references/model-profiles.md` - Canonical master reference (Phase 1 output)
- `/Users/vmakaev/NonWork/get-shit-done/get-shit-done/workflows/*.md` - All 4 workflow files analyzed directly
- `/Users/vmakaev/NonWork/get-shit-done/.planning/phases/02-command-tables/02-RESEARCH.md` - Phase 2 patterns (verified)

### Secondary (MEDIUM confidence)
- `/Users/vmakaev/NonWork/get-shit-done/.planning/REQUIREMENTS.md` - TABLE-10 through TABLE-13 specifications

### Tertiary (LOW confidence)
None - all findings from authoritative project sources.

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - This is pure markdown, no libraries needed
- Architecture: HIGH - All 4 files analyzed, patterns clear from Phase 2
- Pitfalls: HIGH - Based on actual file analysis and Phase 2 experience

**Research date:** 2026-02-02
**Valid until:** Stable (documentation format unlikely to change)
