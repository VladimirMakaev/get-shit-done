# Potential Pitfalls

## Summary

10 critical pitfalls identified across 5 categories:
1. Incomplete table updates (12 files)
2. Missing UI options (3 files)
3. Documentation inconsistencies (6+ files)
4. Environment variable complexity
5. Backwards compatibility

## Pitfalls

### 1. Incomplete Model Lookup Table Updates

**Risk:** 12 files have embedded lookup tables. Missing one means agents spawn with wrong models.

**Prevention:**
- Update ALL 12 files with new "unlimited" column
- Verify each table shows `opus` for all agents

**Files:**
- commands/gsd/audit-milestone.md
- commands/gsd/debug.md
- commands/gsd/execute-phase.md
- commands/gsd/new-milestone.md
- commands/gsd/new-project.md
- commands/gsd/plan-phase.md
- commands/gsd/quick.md
- commands/gsd/research-phase.md
- get-shit-done/workflows/execute-phase.md
- get-shit-done/workflows/execute-plan.md
- get-shit-done/workflows/map-codebase.md
- get-shit-done/workflows/verify-work.md

### 2. Missing /gsd:settings UI Option

**Risk:** Users can't select "unlimited" from interactive settings.

**Prevention:**
- Add "Unlimited" option to AskUserQuestion in settings.md
- Description: "Opus for all agents (maximum quality, highest cost)"

**Files:** commands/gsd/settings.md

### 3. Missing /gsd:set-profile Validation

**Risk:** Command rejects "unlimited" as invalid profile.

**Prevention:**
- Update validation to accept: quality, balanced, budget, unlimited
- Update error message to list all 4 options

**Files:** commands/gsd/set-profile.md

### 4. Missing /gsd:new-project Option

**Risk:** New projects can't be initialized with unlimited profile.

**Prevention:**
- Add "Unlimited" to model profile AskUserQuestion

**Files:** commands/gsd/new-project.md

### 5. Documentation Inconsistencies

**Risk:** Conflicting information about available profiles.

**Files to update:**
- get-shit-done/references/model-profiles.md (master table)
- commands/gsd/help.md
- commands/gsd/set-profile.md (profiles table)
- README.md
- CHANGELOG.md

### 6. Environment Variable Fallback Chain

**Risk:** Wrong precedence if implemented incorrectly.

**Correct precedence:** config.json → GSD_DEFAULT_MODEL_PROFILE → "balanced"

**Prevention:**
```bash
CONFIG_PROFILE=$(cat .planning/config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"')
MODEL_PROFILE="${CONFIG_PROFILE:-${GSD_DEFAULT_MODEL_PROFILE:-balanced}}"
```

### 7. Missing Reference Documentation

**Risk:** Users don't understand when to use unlimited.

**Prevention:**
- Add unlimited row to model-profiles.md
- Add philosophy section explaining use case
- Document cost implications

### 8. Config Schema Type Mismatches

**Risk:** Schema comments don't include unlimited.

**Prevention:**
- Update `"quality|balanced|budget"` to `"quality|balanced|budget|unlimited"`
- Check: new-project.md, settings.md

### 9. Label vs Value Case Mismatch

**Risk:** UI shows "Unlimited" but config needs "unlimited".

**Prevention:**
- Ensure label maps to lowercase config value
- Test full flow: UI selection → config.json verification

### 10. Confirmation Display Missing

**Risk:** `/gsd:set-profile unlimited` shows empty table.

**Prevention:**
- Update display logic in set-profile.md
- Add unlimited example to examples section

## Consistency Checks

After implementation:

- [ ] All 12 files have identical bash pattern with env var support
- [ ] All 12 lookup tables have unlimited column with opus values
- [ ] `/gsd:set-profile unlimited` succeeds
- [ ] `/gsd:settings` shows Unlimited option
- [ ] `/gsd:new-project` shows Unlimited option
- [ ] model-profiles.md has unlimited row and philosophy
- [ ] README.md documents unlimited profile
- [ ] CHANGELOG.md mentions new profile
- [ ] Existing configs still work (backwards compatible)
- [ ] Missing env var falls back to "balanced"

---
*Research: 2026-02-02*
