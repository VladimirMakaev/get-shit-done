# GSD Model Profile Enhancement

## What This Is

A contribution to the GSD workflow system that adds an "unlimited" model profile (Opus for all agents) and introduces the GSD_DEFAULT_MODEL_PROFILE environment variable for flexible default configuration.

## Core Value

Model profile configuration must be consistent across all workflows — every place that reads MODEL_PROFILE must support the new "unlimited" option and respect the environment variable fallback.

## Requirements

### Validated

- ✓ Model profile system exists with quality/balanced/budget presets — existing
- ✓ Config stored in `.planning/config.json` with `model_profile` key — existing
- ✓ `/gsd:settings` command allows changing model profile — existing

### Active

- [ ] Add "unlimited" profile that uses Opus for all agents
- [ ] Add GSD_DEFAULT_MODEL_PROFILE env var as default (fallback to "balanced")
- [ ] Update all workflows that read MODEL_PROFILE to support new profile
- [ ] Update /gsd:settings to include "unlimited" as selectable option

### Out of Scope

- Per-agent model overrides — complexity not justified for this contribution
- Custom profile definitions — would require schema changes

## Context

The GSD system uses a model profile system to select appropriate LLM models based on task complexity and budget. Currently supports three profiles:
- **quality**: Opus for research/roadmap agents
- **balanced**: Sonnet for most agents (default)
- **budget**: Haiku where possible

Model profile is read from `.planning/config.json` and used in model lookup tables embedded in each workflow/command file.

## Constraints

- **Consistency**: All lookup tables must include the new "unlimited" row
- **Backwards compatibility**: Existing configs without the env var must continue working
- **Discovery**: Must find ALL places where MODEL_PROFILE is read/used

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Opus everywhere for unlimited | Maximum quality, user explicitly opts in | — Pending |
| Env var fallback chain: config.json → env var → "balanced" | Flexibility without breaking existing setups | — Pending |

---
*Last updated: 2026-02-02 after initialization*
