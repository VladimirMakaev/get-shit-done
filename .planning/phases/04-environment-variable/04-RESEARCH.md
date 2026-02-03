# Phase 4: Environment Variable - Research

**Researched:** 2026-02-02
**Domain:** Bash environment variable handling, configuration fallback chains
**Confidence:** HIGH

## Summary

This phase implements `GSD_DEFAULT_MODEL_PROFILE` environment variable support across all 12 files that read `MODEL_PROFILE`. The current codebase uses an identical bash pattern in all files that reads from `config.json` and falls back to `"balanced"`. The enhancement requires a two-stage fallback: config.json takes precedence, then environment variable, then hardcoded default.

The implementation is straightforward bash parameter expansion. The new pattern uses nested `${VAR:-${OTHER:-default}}` syntax which is POSIX-compliant and works in all bash versions. The key insight is that the current single-line pattern must be split into two lines to enable the intermediate environment variable check.

**Primary recommendation:** Replace the single-line MODEL_PROFILE pattern with a two-line pattern that first extracts config.json value (possibly empty), then applies the fallback chain via nested parameter expansion.

## Standard Stack

This phase uses only bash shell scripting - no external libraries required.

### Core
| Tool | Version | Purpose | Why Standard |
|------|---------|---------|--------------|
| Bash | 3.2+ | Shell scripting | Default on macOS, universal availability |
| grep | POSIX | Pattern extraction from JSON | Already used in current pattern |
| cat | POSIX | File reading | Already used in current pattern |
| tr | POSIX | Character translation | Already used in current pattern |

### Supporting
| Tool | Purpose | When to Use |
|------|---------|-------------|
| Parameter expansion | Variable defaulting | `${VAR:-default}` for fallback chains |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Nested `${:-}` expansion | Multiple if statements | More verbose, same result |
| grep/tr pipeline | jq for JSON parsing | jq not guaranteed available |

**Installation:** None required - uses built-in shell features.

## Architecture Patterns

### Current Pattern (all 12 files)
```bash
MODEL_PROFILE=$(cat .planning/config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"' || echo "balanced")
```

**Problem:** Falls back directly to "balanced" - no environment variable check.

### New Pattern (target for all 12 files)
```bash
# Read from config.json (may be empty if not set)
CONFIG_PROFILE=$(cat .planning/config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"')
# Fallback chain: config.json -> env var -> "balanced"
MODEL_PROFILE="${CONFIG_PROFILE:-${GSD_DEFAULT_MODEL_PROFILE:-balanced}}"
```

**Key differences:**
1. First line removes `|| echo "balanced"` - allows empty result
2. Second line uses nested parameter expansion for fallback chain
3. Result: config.json wins if set, then env var, then "balanced"

### Pattern Variations

**Variant A: Single line (NOT recommended)**
```bash
MODEL_PROFILE="${$(cat .planning/config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"'):-${GSD_DEFAULT_MODEL_PROFILE:-balanced}}"
```
This syntax is INVALID in bash - cannot nest command substitution directly in parameter expansion this way.

**Variant B: Using intermediate variable (RECOMMENDED)**
```bash
CONFIG_PROFILE=$(cat .planning/config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"')
MODEL_PROFILE="${CONFIG_PROFILE:-${GSD_DEFAULT_MODEL_PROFILE:-balanced}}"
```
This is the recommended pattern - clean, readable, and works in all bash versions.

### Anti-Patterns to Avoid
- **Using `:-` with command substitution directly:** `${$(cmd):-default}` is invalid syntax
- **Forgetting to remove `|| echo "balanced"`:** Would make config.json always return something, breaking the fallback chain
- **Using `:-` when `:=` is needed:** `:-` substitutes, `:=` assigns and substitutes - we want `:-`
- **Inconsistent patterns across files:** All 12 files must use identical pattern for maintainability

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| JSON parsing | Custom awk/sed parsers | Existing grep chain | Already works, tested |
| Fallback logic | if/else blocks | Nested `${:-}` expansion | One-liner, standard bash |
| Environment reading | Manual checks | Direct `$VAR` reference | Bash handles automatically |

**Key insight:** The existing grep pipeline for JSON extraction is already proven in 12 files. The only change needed is the fallback logic, which bash parameter expansion handles natively.

## Common Pitfalls

### Pitfall 1: Forgetting to Remove `|| echo "balanced"`
**What goes wrong:** If the original `|| echo "balanced"` is kept, CONFIG_PROFILE always has a value, making the env var fallback unreachable.
**Why it happens:** Copy-paste without understanding the change.
**How to avoid:** The first line must end with `tr -d '"')` NOT `|| echo "balanced")`.
**Warning signs:** `GSD_DEFAULT_MODEL_PROFILE` is set but not being used.

### Pitfall 2: Empty String vs Unset Variable
**What goes wrong:** `${VAR:-default}` treats empty string ("") and unset the same way - both trigger the default.
**Why it happens:** Bash parameter expansion behavior.
**How to avoid:** This is actually desired behavior here. If config.json has no model_profile, we get empty string, which triggers env var check.
**Warning signs:** None - this is correct behavior.

### Pitfall 3: Incorrect Precedence Order
**What goes wrong:** Env var overrides config.json when it shouldn't.
**Why it happens:** Checking env var before config.json.
**How to avoid:** Pattern must be `${CONFIG_PROFILE:-${GSD_DEFAULT_MODEL_PROFILE:-balanced}}` - config first, env second.
**Warning signs:** Users complain config.json settings are ignored.

### Pitfall 4: Inconsistent Updates Across Files
**What goes wrong:** Some files updated, others not - partial implementation.
**Why it happens:** 12 files is a lot to update manually.
**How to avoid:** Verify all 12 files after update, use verification script.
**Warning signs:** MODEL_PROFILE works in some commands but not others.

### Pitfall 5: Testing Only Happy Path
**What goes wrong:** Edge cases fail (no config.json, no env var, empty values).
**Why it happens:** Testing with both set, not testing fallbacks.
**How to avoid:** Test all 4 scenarios:
1. config.json set, env var set - config.json wins
2. config.json not set, env var set - env var wins
3. config.json not set, env var not set - "balanced" wins
4. config.json set, env var not set - config.json wins

**Warning signs:** Works in development but fails for users without config.json.

## Code Examples

### Complete Pattern for All Files
```bash
# Read from config.json (may be empty if not set)
CONFIG_PROFILE=$(cat .planning/config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"')
# Fallback chain: config.json -> env var -> "balanced"
MODEL_PROFILE="${CONFIG_PROFILE:-${GSD_DEFAULT_MODEL_PROFILE:-balanced}}"
```

### Verification Script
```bash
# Test all fallback scenarios
echo "=== Testing MODEL_PROFILE fallback chain ==="

# Scenario 1: config.json set, env var set
echo '{"model_profile": "quality"}' > /tmp/test-config.json
export GSD_DEFAULT_MODEL_PROFILE=unlimited
CONFIG_PROFILE=$(cat /tmp/test-config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"')
MODEL_PROFILE="${CONFIG_PROFILE:-${GSD_DEFAULT_MODEL_PROFILE:-balanced}}"
echo "Scenario 1 (config=quality, env=unlimited): $MODEL_PROFILE"  # Should be: quality

# Scenario 2: config.json not set, env var set
rm /tmp/test-config.json
export GSD_DEFAULT_MODEL_PROFILE=unlimited
CONFIG_PROFILE=$(cat /tmp/test-config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"')
MODEL_PROFILE="${CONFIG_PROFILE:-${GSD_DEFAULT_MODEL_PROFILE:-balanced}}"
echo "Scenario 2 (config=none, env=unlimited): $MODEL_PROFILE"  # Should be: unlimited

# Scenario 3: Neither set
unset GSD_DEFAULT_MODEL_PROFILE
CONFIG_PROFILE=$(cat /tmp/test-config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"')
MODEL_PROFILE="${CONFIG_PROFILE:-${GSD_DEFAULT_MODEL_PROFILE:-balanced}}"
echo "Scenario 3 (config=none, env=none): $MODEL_PROFILE"  # Should be: balanced

# Scenario 4: config.json set, env var not set
echo '{"model_profile": "budget"}' > /tmp/test-config.json
unset GSD_DEFAULT_MODEL_PROFILE
CONFIG_PROFILE=$(cat /tmp/test-config.json 2>/dev/null | grep -o '"model_profile"[[:space:]]*:[[:space:]]*"[^"]*"' | grep -o '"[^"]*"$' | tr -d '"')
MODEL_PROFILE="${CONFIG_PROFILE:-${GSD_DEFAULT_MODEL_PROFILE:-balanced}}"
echo "Scenario 4 (config=budget, env=none): $MODEL_PROFILE"  # Should be: budget

rm -f /tmp/test-config.json
echo "=== All scenarios tested ==="
```

## Files to Update

### Commands (8 files)
| File | Line Pattern Location |
|------|----------------------|
| commands/gsd/audit-milestone.md | Step 0. Resolve Model Profile |
| commands/gsd/debug.md | Resolve model profile section |
| commands/gsd/execute-phase.md | Step 0 or early in process |
| commands/gsd/new-milestone.md | Model profile resolution |
| commands/gsd/new-project.md | Model profile resolution |
| commands/gsd/plan-phase.md | Step 1 Resolve Model Profile |
| commands/gsd/quick.md | Model profile resolution |
| commands/gsd/research-phase.md | Model profile resolution |

### Workflows (4 files)
| File | Line Pattern Location |
|------|----------------------|
| get-shit-done/workflows/execute-phase.md | resolve_model_profile step |
| get-shit-done/workflows/execute-plan.md | resolve_model_profile step |
| get-shit-done/workflows/map-codebase.md | resolve_model_profile step |
| get-shit-done/workflows/verify-work.md | resolve_model_profile step |

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Hardcoded defaults | config.json with fallback | GSD 1.0 | User configuration |
| Single source (config) | Multi-source fallback chain | This phase | Environment-level defaults |

**Deprecated/outdated:**
- Direct `|| echo "balanced"` fallback: Replaced by two-stage fallback with env var support

## Open Questions

1. **Should env var validation happen?**
   - What we know: The pattern accepts any string for GSD_DEFAULT_MODEL_PROFILE
   - What's unclear: Should invalid values (e.g., "invalid") be rejected?
   - Recommendation: No validation needed at read time - model lookup tables already handle unknown profiles by using the profile name as-is (would result in no match, which is an error at spawn time). Keep implementation simple.

2. **Should there be a warning for invalid env var values?**
   - What we know: Invalid values would cause task spawning to fail later
   - What's unclear: Whether early warning is helpful
   - Recommendation: Defer to Phase 5 (UI) or out of scope - error at spawn time is sufficient feedback.

## Sources

### Primary (HIGH confidence)
- Codebase analysis: All 12 files examined directly via grep
- Prior research: `.planning/research/ARCHITECTURE.md`, `.planning/research/STACK.md`, `.planning/research/PITFALLS.md`

### Secondary (MEDIUM confidence)
- [Bash Parameter Expansion - Linux Hint](https://linuxhint.com/bash_parameter_expansion/) - Nested expansion syntax
- [Guide to Parameter Expansion in Bash - StackAbuse](https://stackabuse.com/guide-to-parameter-expansion-in-bash/) - `:-` operator behavior

### Tertiary (LOW confidence)
- None - all claims verified with primary sources

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - Using existing bash tools, no new dependencies
- Architecture: HIGH - Pattern derived from codebase analysis, verified syntax
- Pitfalls: HIGH - Derived from understanding existing pattern and common bash mistakes

**Research date:** 2026-02-02
**Valid until:** 2026-03-02 (30 days - stable bash patterns)
