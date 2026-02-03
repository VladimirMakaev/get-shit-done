# Phase 1: Master Reference - Research

**Researched:** 2026-02-02
**Domain:** Markdown documentation / configuration reference
**Confidence:** HIGH

## Summary

Phase 1 adds the "unlimited" model profile to the canonical `model-profiles.md` reference file. This is a documentation modification task requiring: (1) adding a new column to the existing 11-agent model lookup table, (2) adding philosophy documentation explaining when to use unlimited, and (3) maintaining consistency with existing table format.

The existing table has 3 profiles (quality, balanced, budget) with varying model assignments. The unlimited profile is straightforward: **opus for all 11 agents**. The philosophy section should explain the use case (maximum reasoning power when quota/cost is not a constraint) and trade-offs (highest token spend).

**Primary recommendation:** Add `unlimited` column to existing table structure, document as "maximum quality when cost is not a constraint" in profile philosophy section.

## Standard Stack

This phase requires no libraries or code - it's pure markdown documentation.

### Core
| Tool | Purpose | Why Standard |
|------|---------|--------------|
| Markdown | Document format | Native to Claude Code slash commands |
| Markdown tables | Data structure | Existing pattern in model-profiles.md |

### Supporting
N/A - documentation-only phase.

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Markdown table | YAML/JSON config | Would require code changes; markdown is interpreted by Claude |

## Architecture Patterns

### Existing Document Structure
```
model-profiles.md
├── Header (title, purpose)
├── Profile Definitions (TABLE - the 11-agent lookup)
├── Profile Philosophy (quality/balanced/budget explanations)
├── Resolution Logic (how profiles are used)
├── Switching Profiles (user instructions)
└── Design Rationale (why specific assignments)
```

### Pattern 1: Column Addition to Existing Table
**What:** Add new column while preserving existing structure
**When to use:** Extending lookup tables with new profile
**Example:**
```markdown
# Current format (3 columns):
| Agent | `quality` | `balanced` | `budget` |
|-------|-----------|------------|----------|
| gsd-planner | opus | opus | sonnet |

# Extended format (4 columns):
| Agent | `unlimited` | `quality` | `balanced` | `budget` |
|-------|-------------|-----------|------------|----------|
| gsd-planner | opus | opus | opus | sonnet |
```

**Note:** Column order matters for readability. Place `unlimited` first (highest tier) for logical left-to-right quality degradation.

### Pattern 2: Philosophy Section Entry
**What:** Document new profile in existing philosophy section
**When to use:** Adding new profile to reference docs
**Example:**
```markdown
**unlimited** - Maximum reasoning power
- Opus for all agents without exception
- Use when: API quota is not a constraint, critical or complex work
- Trade-off: Highest token spend
```

### Anti-Patterns to Avoid
- **Inconsistent column order:** All tables across codebase should use same column order (unlimited | quality | balanced | budget)
- **Incomplete agent coverage:** Must include all 11 agents, not a subset
- **Vague philosophy:** Be specific about when to use and trade-offs

## Don't Hand-Roll

Not applicable - this is a documentation phase with no code.

## Common Pitfalls

### Pitfall 1: Agent Count Mismatch
**What goes wrong:** Missing an agent or adding duplicates
**Why it happens:** Manual counting errors
**How to avoid:** Verify table has exactly 11 rows (matching existing table)
**Warning signs:** Row count differs between profiles

### Pitfall 2: Inconsistent Model Values
**What goes wrong:** Some agents have non-opus values in unlimited
**Why it happens:** Copy-paste from existing columns
**How to avoid:** Verify every cell in unlimited column shows "opus"
**Warning signs:** Any haiku or sonnet in unlimited column

### Pitfall 3: Column Order Inconsistency
**What goes wrong:** This file uses different order than distributed tables (Phase 2-3)
**Why it happens:** Phases done at different times
**How to avoid:** Establish order now: unlimited | quality | balanced | budget
**Warning signs:** Confusion when comparing master to distributed tables

### Pitfall 4: Philosophy Section Incomplete
**What goes wrong:** Users don't understand when to use unlimited
**Why it happens:** Focusing only on the table, forgetting documentation
**How to avoid:** Update both table AND philosophy section
**Warning signs:** Philosophy section still only mentions 3 profiles

### Pitfall 5: Format Breaks Table Parsing
**What goes wrong:** Claude fails to interpret table correctly
**Why it happens:** Extra spaces, missing pipes, inconsistent alignment
**How to avoid:** Match exact format of existing table (backticks around profile names in header)
**Warning signs:** Table renders incorrectly in markdown preview

## Code Examples

### Complete Updated Table
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

### Philosophy Section Addition
```markdown
**unlimited** - Maximum reasoning power
- Opus for all agents without exception
- Every operation uses the most capable model
- Use when: API quota is not a constraint, critical architecture work, complex debugging
- Trade-off: Highest token spend, suitable for paid API or high-quota users
```

### Design Rationale Addition
```markdown
**Why Opus for all in unlimited?**
When cost is not a constraint, there's no reason to use lower-capability models. The unlimited profile is for users with paid API access or high quotas who want maximum quality for every operation.
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| 3 profiles only | 4 profiles (adding unlimited) | This phase | Users with high quota can use opus everywhere |

**Current state:**
- model-profiles.md has quality/balanced/budget (3 profiles)
- 11 agents with varying model assignments per profile
- No "everything opus" option exists

## Open Questions

None for this phase - the scope is well-defined:
1. Add unlimited column with opus for all agents
2. Add philosophy section for unlimited
3. Match existing table format

## Sources

### Primary (HIGH confidence)
- `/Users/vmakaev/NonWork/get-shit-done/get-shit-done/references/model-profiles.md` - Existing table format, all 11 agents verified
- `/Users/vmakaev/NonWork/get-shit-done/.planning/research/FEATURES.md` - Agent inventory confirmed
- `/Users/vmakaev/NonWork/get-shit-done/.planning/research/SUMMARY.md` - Unlimited profile definition confirmed

### Secondary (MEDIUM confidence)
- `/Users/vmakaev/NonWork/get-shit-done/.planning/REQUIREMENTS.md` - TABLE-01 requirement specification

### Tertiary (LOW confidence)
None - all findings from authoritative project sources.

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - This is pure markdown, no libraries needed
- Architecture: HIGH - Existing document structure is clear from source file
- Pitfalls: HIGH - Based on actual file analysis and prior research

**Research date:** 2026-02-02
**Valid until:** Stable (documentation format unlikely to change)
