# MODEL_PROFILE Data Flow

## Summary

MODEL_PROFILE flows through GSD as a **meta-prompting pattern**:

1. **config.json** stores the profile value
2. **Orchestrator commands** contain bash instructions to read it
3. **Lookup tables** in markdown map profile + agent to model names
4. **Claude interprets** instructions and resolves template variables
5. **Task() calls** receive the resolved model parameter

## Flow Steps

### Step 1: Config Storage
- **Location:** `.planning/config.json`
- **Field:** `model_profile`
- **Values:** `"quality"`, `"balanced"`, or `"budget"`
- **Default:** `"balanced"` (if not set)

### Step 2: Profile Read
- **Location:** All orchestrator commands and workflows
- **Method:** Bash command extraction via grep
```bash
MODEL_PROFILE=$(cat .planning/config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"' || echo "balanced")
```

### Step 3: Model Lookup
- **Location:** Embedded lookup tables in each command/workflow
- **Format:** Markdown tables showing agent → profile → model mapping
- **Reference:** Canonical table in `get-shit-done/references/model-profiles.md`

### Step 4: Model Resolution
- **Performed by:** Claude Code (AI interpretation, not coded logic)
- **Pattern:** Claude reads lookup table and MODEL_PROFILE, resolves template variables
- **Variables:** `{researcher_model}`, `{planner_model}`, `{executor_model}`, etc.

### Step 5: Task Spawning
- **Location:** Task() calls throughout orchestrator commands
- **Format:** `model="{agent_type_model}"`

## Change Points for Env Var Support

To add `GSD_DEFAULT_MODEL_PROFILE` environment variable:

### Files to Modify (12 total)

**Commands (8 files):**
- commands/gsd/audit-milestone.md
- commands/gsd/debug.md
- commands/gsd/execute-phase.md
- commands/gsd/new-milestone.md
- commands/gsd/new-project.md
- commands/gsd/plan-phase.md
- commands/gsd/quick.md
- commands/gsd/research-phase.md

**Workflows (4 files):**
- get-shit-done/workflows/execute-phase.md
- get-shit-done/workflows/execute-plan.md
- get-shit-done/workflows/map-codebase.md
- get-shit-done/workflows/verify-work.md

### New Bash Pattern

```bash
# Current
MODEL_PROFILE=$(cat .planning/config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"' || echo "balanced")

# With env var support (config.json takes precedence)
CONFIG_PROFILE=$(cat .planning/config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"')
MODEL_PROFILE="${CONFIG_PROFILE:-${GSD_DEFAULT_MODEL_PROFILE:-balanced}}"
```

**Precedence chain:** config.json → GSD_DEFAULT_MODEL_PROFILE → "balanced"

---
*Research: 2026-02-02*
