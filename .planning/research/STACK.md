# MODEL_PROFILE Read Locations

## Summary

**Total files with MODEL_PROFILE usage: 12 files**

**Common pattern (used in all 12 files):**
```bash
MODEL_PROFILE=$(cat .planning/config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"' || echo "balanced")
```

**Where MODEL_PROFILE is set:**
- `/gsd:settings` - Interactive settings UI that updates config.json
- `/gsd:set-profile` - Direct command to switch profile
- `/gsd:new-project` - Initial project setup

**Reference documentation:**
- `get-shit-done/references/model-profiles.md` - Master reference table and philosophy

## Files That Read MODEL_PROFILE

### commands/gsd/audit-milestone.md
- **Pattern:** Standard bash extraction
- **Context:** Spawns gsd-integration-checker agent

### commands/gsd/debug.md
- **Pattern:** Standard bash extraction
- **Context:** Spawns gsd-debugger agent

### commands/gsd/execute-phase.md
- **Pattern:** Standard bash extraction
- **Context:** Spawns gsd-executor and gsd-verifier agents

### commands/gsd/new-milestone.md
- **Pattern:** Standard bash extraction
- **Context:** Spawns research and roadmap agents

### commands/gsd/new-project.md
- **Pattern:** Standard bash extraction
- **Context:** Spawns research and roadmap agents during initialization

### commands/gsd/plan-phase.md
- **Pattern:** Standard bash extraction
- **Context:** Spawns gsd-phase-researcher, gsd-planner, gsd-plan-checker

### commands/gsd/quick.md
- **Pattern:** Standard bash extraction
- **Context:** Spawns gsd-planner and gsd-executor

### commands/gsd/research-phase.md
- **Pattern:** Standard bash extraction
- **Context:** Spawns gsd-phase-researcher

### get-shit-done/workflows/execute-phase.md
- **Pattern:** Standard bash extraction
- **Context:** Wave-based execution orchestrator

### get-shit-done/workflows/execute-plan.md
- **Pattern:** Standard bash extraction
- **Context:** Single plan execution

### get-shit-done/workflows/map-codebase.md
- **Pattern:** Standard bash extraction
- **Context:** Parallel codebase analysis

### get-shit-done/workflows/verify-work.md
- **Pattern:** Standard bash extraction
- **Context:** UAT verification workflow

## Files That Write MODEL_PROFILE

### commands/gsd/settings.md
- **Method:** AskUserQuestion presents options, writes to config.json
- **Options:** Quality, Balanced (Recommended), Budget

### commands/gsd/set-profile.md
- **Method:** Validates argument, updates config.json
- **Valid values:** quality, balanced, budget

### commands/gsd/new-project.md
- **Method:** AskUserQuestion during project setup
- **Options:** Balanced (Recommended), Quality, Budget

## Key Insights

1. **Bash pattern is standardized** - All 12 files use identical bash command
2. **Default is "balanced"** - All files fall back to "balanced" if config missing
3. **No environment variable fallback** - Currently no GSD_DEFAULT_MODEL_PROFILE support
4. **Three write locations** - Only settings.md, set-profile.md, and new-project.md write the profile

---
*Research: 2026-02-02*
