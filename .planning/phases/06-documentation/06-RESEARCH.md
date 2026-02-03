# Phase 6: Documentation - Research

**Researched:** 2026-02-02
**Domain:** Markdown documentation updates (user-facing docs)
**Confidence:** HIGH

## Summary

Phase 6 completes the "unlimited" model profile feature by updating four user-facing documentation files. This is a documentation-only phase with no code changes. The requirements are:

1. **DOC-01**: Update model-profiles.md - Already complete from Phase 1 (table has unlimited column, philosophy section exists). Verify and close.
2. **DOC-02**: Update help.md - Add "unlimited" to the profile list in `/gsd:set-profile` command description.
3. **DOC-03**: Update README.md - Add "unlimited" row to Model Profiles table and document GSD_DEFAULT_MODEL_PROFILE env var.
4. **DOC-04**: Update CHANGELOG.md - Add entry under `[Unreleased]` for the new feature.

All files follow established markdown patterns. Changes are straightforward text additions.

**Primary recommendation:** Single plan with 4 tasks (one per requirement). DOC-01 is likely already satisfied - verify and mark complete. Remaining three require simple text insertions.

## Standard Stack

This phase requires no libraries or code - it's pure markdown documentation.

### Core
| Tool | Purpose | Why Standard |
|------|---------|--------------|
| Markdown | Document format | Native format for all GSD docs |
| Keep a Changelog | CHANGELOG format | Established standard (1.1.0) |

### Supporting
N/A - documentation-only phase.

### Alternatives Considered
None - markdown is the only option for these files.

## Current File Analysis

### model-profiles.md (DOC-01)

**Location:** `/Users/vmakaev/NonWork/get-shit-done/get-shit-done/references/model-profiles.md`

**Current State:**
- Table already includes `unlimited` column (added in Phase 1)
- Philosophy section already has `**unlimited** - Maximum reasoning power` entry
- Design rationale section has "Why Opus for all in unlimited?" entry

**Action:** Verify completeness and mark DOC-01 as complete. No additional changes needed.

### help.md (DOC-02)

**Location:** `/Users/vmakaev/NonWork/get-shit-done/commands/gsd/help.md`

**Current State (lines 317-322):**
```markdown
**`/gsd:set-profile <profile>`**
Quick switch model profile for GSD agents.

- `quality` — Opus everywhere except verification
- `balanced` — Opus for planning, Sonnet for execution (default)
- `budget` — Sonnet for writing, Haiku for research/verification
```

**Changes needed:**
1. Add `unlimited` bullet point as first option in the profile list (before `quality`)
2. Follow existing format: `- \`unlimited\` — [description]`

**Target state:**
```markdown
- `unlimited` — Opus for all agents (maximum quality)
- `quality` — Opus everywhere except verification
- `balanced` — Opus for planning, Sonnet for execution (default)
- `budget` — Sonnet for writing, Haiku for research/verification
```

### README.md (DOC-03)

**Location:** `/Users/vmakaev/NonWork/get-shit-done/README.md`

**Current State - Model Profiles Table (lines 493-499):**
```markdown
| Profile | Planning | Execution | Verification |
|---------|----------|-----------|--------------|
| `quality` | Opus | Opus | Sonnet |
| `balanced` (default) | Opus | Sonnet | Sonnet |
| `budget` | Sonnet | Sonnet | Haiku |
```

**Current State - Switch profiles example (lines 501-503):**
```markdown
Switch profiles:
\`\`\`
/gsd:set-profile budget
\`\`\`
```

**Current State - Configuration table (lines 486-490):**
No env var documentation section currently exists.

**Changes needed:**
1. Add `unlimited` row as first row in Model Profiles table
2. Document GSD_DEFAULT_MODEL_PROFILE in a new "Environment Variables" section or integrate with configuration

**Target state for Model Profiles table:**
```markdown
| Profile | Planning | Execution | Verification |
|---------|----------|-----------|--------------|
| `unlimited` | Opus | Opus | Opus |
| `quality` | Opus | Opus | Sonnet |
| `balanced` (default) | Opus | Sonnet | Sonnet |
| `budget` | Sonnet | Sonnet | Haiku |
```

**Target state for env var documentation:**
```markdown
### Environment Variables

| Variable | Purpose | Default |
|----------|---------|---------|
| `GSD_DEFAULT_MODEL_PROFILE` | Default model profile when no config.json exists | `balanced` |

Example: `GSD_DEFAULT_MODEL_PROFILE=unlimited claude --dangerously-skip-permissions`
```

**Placement decision:**
- Add env var section after "Model Profiles" subsection (within Configuration section)
- Keep it concise - one table row and one example line

### CHANGELOG.md (DOC-04)

**Location:** `/Users/vmakaev/NonWork/get-shit-done/CHANGELOG.md`

**Current State (lines 1-8):**
```markdown
# Changelog

All notable changes to GSD will be documented in this file.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]
```

**Changes needed:**
Add entry under `[Unreleased]` section for the new feature.

**Target state:**
```markdown
## [Unreleased]

### Added
- **Unlimited model profile** — Opus for all agents without exception (`/gsd:set-profile unlimited`)
- **GSD_DEFAULT_MODEL_PROFILE** environment variable — Set default profile when no config.json exists (fallback chain: config.json -> env var -> "balanced")
```

**Keep a Changelog format:**
- Use `### Added` for new features
- Use `### Changed` for changes to existing functionality
- Use `### Fixed` for bug fixes
- Use `### Removed` for removed features

## Architecture Patterns

### Pattern 1: Bullet List Profile Documentation
**What:** Profile options documented as bullet list with backtick-quoted name and em-dash description
**When to use:** help.md profile list
**Example:**
```markdown
- `unlimited` — Opus for all agents (maximum quality)
```

### Pattern 2: Markdown Table Row Addition
**What:** Add row to existing table matching column structure
**When to use:** README.md Model Profiles table
**Example:**
```markdown
| `unlimited` | Opus | Opus | Opus |
```

### Pattern 3: Keep a Changelog Entry
**What:** Features listed under version headers with category sections
**When to use:** CHANGELOG.md entries
**Example:**
```markdown
### Added
- **Feature name** — Description of the feature
```

### Anti-Patterns to Avoid
- **Inconsistent ordering:** Profile order should be unlimited | quality | balanced | budget everywhere
- **Missing backticks:** Profile names should be in backticks (\`unlimited\`) in all docs
- **Vague descriptions:** Be specific about what unlimited means (Opus for ALL agents)
- **Breaking changelog format:** Follow Keep a Changelog conventions exactly

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Profile description | New descriptions | Copy from model-profiles.md | Consistency across docs |
| Changelog format | Custom format | Keep a Changelog 1.1.0 | Established standard |

**Key insight:** All patterns already exist in these files. This phase is pure extension.

## Common Pitfalls

### Pitfall 1: DOC-01 Duplication
**What goes wrong:** Re-adding content that Phase 1 already added
**Why it happens:** Not checking current file state
**How to avoid:** Read model-profiles.md first; verify unlimited content exists before marking complete
**Warning signs:** Duplicate rows or sections in model-profiles.md

### Pitfall 2: Profile Order Inconsistency
**What goes wrong:** unlimited listed in wrong position (e.g., last instead of first)
**Why it happens:** Not following established tier order
**How to avoid:** Always: unlimited | quality | balanced | budget (highest to lowest)
**Warning signs:** Different order in help.md vs README.md

### Pitfall 3: Missing Env Var Documentation
**What goes wrong:** Adding unlimited to profile tables but forgetting the env var requirement (DOC-03)
**Why it happens:** Focusing on profile, forgetting second part of DOC-03
**How to avoid:** DOC-03 has two parts: profile table AND env var documentation
**Warning signs:** README.md has no mention of GSD_DEFAULT_MODEL_PROFILE

### Pitfall 4: Changelog Under Wrong Section
**What goes wrong:** Entry added under wrong version header or category
**Why it happens:** Editing wrong line in large file
**How to avoid:** Add under [Unreleased], then add ### Added subsection
**Warning signs:** Entry appears under dated version instead of [Unreleased]

### Pitfall 5: Breaking Changelog Link References
**What goes wrong:** Footer link references broken after adding content
**Why it happens:** CHANGELOG.md has link references at bottom; inserting content in wrong place
**How to avoid:** Add content after `## [Unreleased]` line, before `## [1.11.1]` line
**Warning signs:** Markdown link references don't resolve

## Code Examples

### help.md - Updated Profile List
```markdown
**`/gsd:set-profile <profile>`**
Quick switch model profile for GSD agents.

- `unlimited` — Opus for all agents (maximum quality)
- `quality` — Opus everywhere except verification
- `balanced` — Opus for planning, Sonnet for execution (default)
- `budget` — Sonnet for writing, Haiku for research/verification
```

### README.md - Updated Model Profiles Table
```markdown
### Model Profiles

Control which Claude model each agent uses. Balance quality vs token spend.

| Profile | Planning | Execution | Verification |
|---------|----------|-----------|--------------|
| `unlimited` | Opus | Opus | Opus |
| `quality` | Opus | Opus | Sonnet |
| `balanced` (default) | Opus | Sonnet | Sonnet |
| `budget` | Sonnet | Sonnet | Haiku |

Switch profiles:
```
/gsd:set-profile unlimited
```
```

### README.md - Environment Variable Documentation
```markdown
### Environment Variables

| Variable | Default | What it does |
|----------|---------|--------------|
| `GSD_DEFAULT_MODEL_PROFILE` | `balanced` | Default model profile when no `.planning/config.json` exists |

Use to set system-wide default: `GSD_DEFAULT_MODEL_PROFILE=unlimited`
```

### CHANGELOG.md - New Entry
```markdown
## [Unreleased]

### Added
- **Unlimited model profile** — Use Opus for all agents without exception (`/gsd:set-profile unlimited` or set in `/gsd:settings`)
- **GSD_DEFAULT_MODEL_PROFILE** environment variable — Set default profile when no config.json exists (fallback: config.json -> env var -> "balanced")
```

## Exact Change Locations

| File | Line Range | Change Type | Description |
|------|------------|-------------|-------------|
| model-profiles.md | - | Verify only | Confirm Phase 1 content exists, no changes needed |
| help.md | 320 | Insert bullet | Add `unlimited` as first profile bullet before `quality` |
| README.md | 494 | Insert row | Add `unlimited` row as first data row in Model Profiles table |
| README.md | 528 (approx) | Insert section | Add Environment Variables subsection after Workflow Agents section |
| CHANGELOG.md | 8 | Insert section | Add `### Added` section after `## [Unreleased]` with two bullet points |

## Plan Structure Recommendation

**Recommended: 1 plan with 4 tasks**

| Task | Requirement | File | Type |
|------|-------------|------|------|
| 1 | DOC-01 | model-profiles.md | Verify (no changes) |
| 2 | DOC-02 | help.md | Insert 1 bullet |
| 3 | DOC-03 | README.md | Insert row + add section |
| 4 | DOC-04 | CHANGELOG.md | Insert entry |

**Rationale:**
- All changes are independent (no dependencies)
- All changes are small text insertions
- Single plan simplifies verification
- Matches pattern from Phase 5

## Open Questions

None. All patterns are clear from existing documentation.

## Sources

### Primary (HIGH confidence)
- `/Users/vmakaev/NonWork/get-shit-done/get-shit-done/references/model-profiles.md` - Current state verified (unlimited exists from Phase 1)
- `/Users/vmakaev/NonWork/get-shit-done/commands/gsd/help.md` - Full file read, line numbers verified
- `/Users/vmakaev/NonWork/get-shit-done/README.md` - Full file read, line numbers verified
- `/Users/vmakaev/NonWork/get-shit-done/CHANGELOG.md` - Full file read, format verified
- `/Users/vmakaev/NonWork/get-shit-done/.planning/REQUIREMENTS.md` - DOC-01 through DOC-04 requirements

### Secondary (MEDIUM confidence)
- Keep a Changelog 1.1.0 specification - CHANGELOG.md header references this standard

### Tertiary (LOW confidence)
None - all findings from authoritative project sources.

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - This is pure markdown, no libraries needed
- File structure: HIGH - All files read and analyzed
- Change locations: HIGH - Line numbers verified against actual content
- Pitfalls: HIGH - Based on actual file analysis and cross-referencing requirements

**Research date:** 2026-02-02
**Valid until:** Stable (documentation patterns unlikely to change)
