# Research Summary: MODEL_PROFILE Enhancement

## Overview

Adding "unlimited" model profile and GSD_DEFAULT_MODEL_PROFILE environment variable to GSD.

## Key Findings

### Stack
- **12 files** read MODEL_PROFILE using identical bash pattern
- **3 files** write MODEL_PROFILE (settings, set-profile, new-project)
- **1 master reference** (model-profiles.md) defines canonical table
- **No env var support** currently exists

### Features (Model Lookup Tables)
- **11 agents** have model mappings across 3 profiles
- **12 distributed tables** (subsets of master) in commands/workflows
- **Unlimited profile** = opus for all 11 agents

### Architecture
- **Flow:** config.json → bash read → lookup table → template variable → Task() call
- **Meta-prompting:** Claude interprets markdown tables, not coded logic
- **Change surface:** 12 files need new bash pattern + lookup table column

### Pitfalls
- **Incomplete updates** - Must update all 12 files with new column
- **UI gaps** - settings.md, set-profile.md, new-project.md need "unlimited" option
- **Documentation scatter** - 5+ docs need consistent updates
- **Fallback chain** - config.json → env var → "balanced" precedence

## Implementation Checklist

### Model Lookup Tables (12 files)
- [ ] commands/gsd/audit-milestone.md
- [ ] commands/gsd/debug.md
- [ ] commands/gsd/execute-phase.md
- [ ] commands/gsd/new-milestone.md
- [ ] commands/gsd/new-project.md
- [ ] commands/gsd/plan-phase.md
- [ ] commands/gsd/quick.md
- [ ] commands/gsd/research-phase.md
- [ ] get-shit-done/workflows/execute-phase.md
- [ ] get-shit-done/workflows/execute-plan.md
- [ ] get-shit-done/workflows/map-codebase.md
- [ ] get-shit-done/workflows/verify-work.md

### UI Updates (3 files)
- [ ] commands/gsd/settings.md - Add "Unlimited" option
- [ ] commands/gsd/set-profile.md - Add validation + examples
- [ ] commands/gsd/new-project.md - Add "Unlimited" option

### Documentation (5 files)
- [ ] get-shit-done/references/model-profiles.md - Master table + philosophy
- [ ] commands/gsd/help.md - Profile list
- [ ] README.md - Profile documentation
- [ ] CHANGELOG.md - New feature entry

### Env Var Pattern
New bash pattern for all 12 files:
```bash
CONFIG_PROFILE=$(cat .planning/config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"')
MODEL_PROFILE="${CONFIG_PROFILE:-${GSD_DEFAULT_MODEL_PROFILE:-balanced}}"
```

## Unlimited Profile Definition

| Agent | Model |
|-------|-------|
| gsd-planner | opus |
| gsd-roadmapper | opus |
| gsd-executor | opus |
| gsd-phase-researcher | opus |
| gsd-project-researcher | opus |
| gsd-research-synthesizer | opus |
| gsd-debugger | opus |
| gsd-codebase-mapper | opus |
| gsd-verifier | opus |
| gsd-plan-checker | opus |
| gsd-integration-checker | opus |

---
*Research completed: 2026-02-02*
