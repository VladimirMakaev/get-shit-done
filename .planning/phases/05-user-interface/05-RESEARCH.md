# Phase 5: User Interface - Research

**Researched:** 2026-02-02
**Domain:** GSD command file structure and model profile UI patterns
**Confidence:** HIGH

## Summary

This phase adds the "Unlimited" profile option to three GSD user-facing commands: `/gsd:settings`, `/gsd:set-profile`, and `/gsd:new-project`. The research examined the current structure of each file to identify exact locations for changes.

All three files follow a consistent pattern: model profile options are presented in `AskUserQuestion` calls (settings.md, new-project.md) or validation logic and examples (set-profile.md). Each file already contains the other three profiles (quality, balanced, budget), so adding "unlimited" follows the established pattern.

Per STATE.md decisions: column order is unlimited | quality | balanced | budget (highest to lowest tier), and unlimited uses opus for ALL 11 agents.

**Primary recommendation:** Add "Unlimited" as the first option in each profile selector, matching the tier order from the lookup tables.

## Current File Structure

### settings.md

**Location:** `/Users/vmakaev/NonWork/get-shit-done/commands/gsd/settings.md`

**Model Profile UI (lines 48-54):**
```javascript
{
  question: "Which model profile for agents?",
  header: "Model",
  multiSelect: false,
  options: [
    { label: "Quality", description: "Opus everywhere except verification (highest cost)" },
    { label: "Balanced (Recommended)", description: "Opus for planning, Sonnet for execution/verification" },
    { label: "Budget", description: "Sonnet for writing, Haiku for research/verification (lowest cost)" }
  ]
}
```

**Changes needed:**
1. Add "Unlimited" option as FIRST option in array (before Quality)
2. Update config.json write section to include "unlimited" as valid value (line 104)
3. Update confirmation table to show "unlimited" in possible values (line 129)

**Description pattern:** Each option has label and description. Unlimited should follow same format.

### set-profile.md

**Location:** `/Users/vmakaev/NonWork/get-shit-done/commands/gsd/set-profile.md`

**Validation logic (lines 27-30):**
```
if $ARGUMENTS.profile not in ["quality", "balanced", "budget"]:
  Error: Invalid profile "$ARGUMENTS.profile"
  Valid profiles: quality, balanced, budget
  STOP
```

**Profile descriptions table (lines 14-20):**
```markdown
<profiles>
| Profile | Description |
|---------|-------------|
| **quality** | Opus everywhere except read-only verification |
| **balanced** | Opus for planning, Sonnet for execution/verification (default) |
| **budget** | Sonnet for writing, Haiku for research/verification |
</profiles>
```

**Examples section (lines 74-106):**
Shows two examples: `budget` and `quality` - need to add `unlimited` example.

**Changes needed:**
1. Update description in frontmatter (line 6) to include "unlimited"
2. Add "unlimited" row to profiles table (insert before quality, per tier order)
3. Update validation list to include "unlimited" (line 27)
4. Update error message to show "unlimited" (line 29)
5. Add example for `/gsd:set-profile unlimited` after existing examples

### new-project.md

**Location:** `/Users/vmakaev/NonWork/get-shit-done/commands/gsd/new-project.md`

**Model Profile UI (lines 326-335):**
```javascript
{
  header: "Model Profile",
  question: "Which AI models for planning agents?",
  multiSelect: false,
  options: [
    { label: "Balanced (Recommended)", description: "Sonnet for most agents - good quality/cost ratio" },
    { label: "Quality", description: "Opus for research/roadmap - higher cost, deeper analysis" },
    { label: "Budget", description: "Haiku where possible - fastest, lowest cost" }
  ]
}
```

**Config.json template (lines 341-353):**
```json
{
  "model_profile": "quality|balanced|budget",
  ...
}
```

**Note:** new-project.md uses different order (balanced first as recommended). Need to decide if "Unlimited" goes first (per tier order) or if recommended stays first.

**Changes needed:**
1. Add "Unlimited" option to Model Profile question (line 329-334)
2. Update config.json template to include "unlimited" in valid values (line 346)

## Architecture Patterns

### Pattern 1: AskUserQuestion Option Structure
**What:** All profile options follow `{ label: "X", description: "Y" }` format
**When to use:** settings.md and new-project.md profile selection
**Example:**
```javascript
{ label: "Unlimited", description: "Opus for all agents - maximum quality, highest cost" }
```

### Pattern 2: Validation Arrays
**What:** set-profile.md validates against array of valid profile names
**When to use:** Anywhere profile string is validated
**Example:**
```
if $ARGUMENTS.profile not in ["unlimited", "quality", "balanced", "budget"]:
```

### Pattern 3: Markdown Description Tables
**What:** set-profile.md uses markdown table for profile descriptions
**When to use:** Displaying profile options in documentation-style format
**Example:**
```markdown
| **unlimited** | Opus for all agents without exception |
```

### UI Order Convention

**Lookup tables:** unlimited | quality | balanced | budget (highest to lowest tier)

**UI options:** Two patterns exist:
1. **settings.md:** Lists by tier (Quality first, then Balanced, then Budget)
2. **new-project.md:** Lists Balanced first (recommended), then Quality, then Budget

**Recommendation:** Follow each file's existing convention:
- settings.md: Add Unlimited FIRST (before Quality), matching tier order
- new-project.md: Add Unlimited LAST but before closing bracket, keep Balanced (Recommended) first since it's the default

### Anti-Patterns to Avoid
- **Inconsistent ordering:** Don't put Unlimited in different positions across files without reason
- **Missing validation:** Don't add UI option without updating validation logic
- **Description mismatch:** Ensure description accurately reflects unlimited behavior (opus for ALL agents)

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Profile description | Invent new descriptions | Copy from model-profiles.md | Ensures consistency |
| Validation logic | Custom validation | Extend existing array | Matches established pattern |

**Key insight:** All patterns already exist. This phase is pure extension, not invention.

## Common Pitfalls

### Pitfall 1: Inconsistent Descriptions
**What goes wrong:** Description for unlimited differs between settings.md, set-profile.md, and new-project.md
**Why it happens:** Copying from different sources or improvising
**How to avoid:** Use model-profiles.md as source of truth for philosophy, adapt for UI context
**Warning signs:** Different wording across files

### Pitfall 2: Forgetting Validation Update
**What goes wrong:** UI shows Unlimited option but set-profile.md rejects "unlimited" as invalid
**Why it happens:** Updating UI without checking validation logic
**How to avoid:** Treat UI-02 (set-profile) as critical - it's the validation point
**Warning signs:** Error when running `/gsd:set-profile unlimited`

### Pitfall 3: Wrong Option Position
**What goes wrong:** Unlimited added in wrong position (e.g., last in settings.md, first in new-project.md)
**Why it happens:** Not analyzing existing ordering convention
**How to avoid:** Follow file's existing pattern
**Warning signs:** Inconsistent feel between commands

### Pitfall 4: Missing config.json Template Update
**What goes wrong:** new-project.md shows Unlimited option but generated config.json shows wrong value
**Why it happens:** UI updated but template string not updated
**How to avoid:** Search for all occurrences of "quality|balanced|budget" in each file
**Warning signs:** config.json has unexpected model_profile value

## Code Examples

### settings.md - Model Profile Options (updated)
```javascript
// Source: settings.md lines 47-55, with unlimited added first
{
  question: "Which model profile for agents?",
  header: "Model",
  multiSelect: false,
  options: [
    { label: "Unlimited", description: "Opus for all agents (maximum quality, highest cost)" },
    { label: "Quality", description: "Opus everywhere except verification (highest cost)" },
    { label: "Balanced (Recommended)", description: "Opus for planning, Sonnet for execution/verification" },
    { label: "Budget", description: "Sonnet for writing, Haiku for research/verification (lowest cost)" }
  ]
}
```

### set-profile.md - Profiles Table (updated)
```markdown
// Source: set-profile.md lines 14-20, with unlimited added first
<profiles>
| Profile | Description |
|---------|-------------|
| **unlimited** | Opus for all agents without exception |
| **quality** | Opus everywhere except read-only verification |
| **balanced** | Opus for planning, Sonnet for execution/verification (default) |
| **budget** | Sonnet for writing, Haiku for research/verification |
</profiles>
```

### set-profile.md - Validation (updated)
```
// Source: set-profile.md lines 27-30, with unlimited added
if $ARGUMENTS.profile not in ["unlimited", "quality", "balanced", "budget"]:
  Error: Invalid profile "$ARGUMENTS.profile"
  Valid profiles: unlimited, quality, balanced, budget
  STOP
```

### set-profile.md - Example (new)
```markdown
// Add after existing examples, before </examples>
**Switch to unlimited mode:**
```
/gsd:set-profile unlimited

Model profile set to: unlimited

Agents will now use:
| Agent | Model |
|-------|-------|
| gsd-planner | opus |
| gsd-executor | opus |
| gsd-verifier | opus |
| ... | ... |
```
```

### new-project.md - Model Profile Options (updated)
```javascript
// Source: new-project.md lines 326-335, with unlimited added
{
  header: "Model Profile",
  question: "Which AI models for planning agents?",
  multiSelect: false,
  options: [
    { label: "Balanced (Recommended)", description: "Sonnet for most agents - good quality/cost ratio" },
    { label: "Quality", description: "Opus for research/roadmap - higher cost, deeper analysis" },
    { label: "Unlimited", description: "Opus for all agents - maximum quality, highest cost" },
    { label: "Budget", description: "Haiku where possible - fastest, lowest cost" }
  ]
}
```

### new-project.md - config.json Template (updated)
```json
// Source: new-project.md lines 341-352, add unlimited to valid values
{
  "model_profile": "unlimited|quality|balanced|budget",
  ...
}
```

## Exact Change Locations

| File | Line Range | Change Type | Description |
|------|------------|-------------|-------------|
| settings.md | 49-54 | Insert option | Add Unlimited as first option in options array |
| settings.md | 104 | Update template | Add "unlimited" to model_profile valid values |
| settings.md | 129 | Update display | Add "unlimited" to possible profile values shown |
| set-profile.md | 6 | Update description | Add "unlimited" to profile list in frontmatter |
| set-profile.md | 17-19 | Insert row | Add unlimited row to profiles table (first row after header) |
| set-profile.md | 27 | Update validation | Add "unlimited" to validation array |
| set-profile.md | 29 | Update error msg | Add "unlimited" to valid profiles list in error |
| set-profile.md | 105-106 | Insert example | Add unlimited example after quality example |
| new-project.md | 329-334 | Insert option | Add Unlimited option (position: after Quality, before Budget) |
| new-project.md | 346 | Update template | Add "unlimited" to model_profile valid values |

## Plan Structure Recommendation

This phase should be **1 plan with 3 tasks** (one per file) or **3 plans** (one per requirement).

**Recommended: 1 plan with 3 sequential sections**
- All changes are simple text additions
- No dependencies between files
- Simpler verification at end

**Alternative: 3 plans (one per UI requirement)**
- UI-01: settings.md changes
- UI-02: set-profile.md changes
- UI-03: new-project.md changes

Either approach works. The single plan is simpler since all changes are small and independent.

## Open Questions

None. All patterns are clear from existing code.

## Sources

### Primary (HIGH confidence)
- `/Users/vmakaev/NonWork/get-shit-done/commands/gsd/settings.md` - Full file read
- `/Users/vmakaev/NonWork/get-shit-done/commands/gsd/set-profile.md` - Full file read
- `/Users/vmakaev/NonWork/get-shit-done/commands/gsd/new-project.md` - Full file read
- `/Users/vmakaev/NonWork/get-shit-done/get-shit-done/references/model-profiles.md` - Master profile definitions
- `/Users/vmakaev/NonWork/get-shit-done/.planning/STATE.md` - Prior decisions

## Metadata

**Confidence breakdown:**
- File structure: HIGH - Direct file reads
- Change locations: HIGH - Line numbers verified against actual content
- Patterns: HIGH - Patterns extracted from existing code
- Pitfalls: HIGH - Based on analysis of file dependencies

**Research date:** 2026-02-02
**Valid until:** Indefinite (internal codebase patterns are stable)
