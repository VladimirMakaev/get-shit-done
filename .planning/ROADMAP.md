# Roadmap: GSD Model Profile Enhancement

## Overview

This roadmap adds the "unlimited" model profile (Opus for all agents) and GSD_DEFAULT_MODEL_PROFILE environment variable to GSD. The work progresses from master reference through distributed tables, environment variable support, user interface updates, and documentation. Each phase builds on the previous to ensure consistent model profile configuration across all workflows.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [x] **Phase 1: Master Reference** - Define unlimited profile in canonical model-profiles.md ✓
- [x] **Phase 2: Command Tables** - Add unlimited column to 8 command lookup tables ✓
- [x] **Phase 3: Workflow Tables** - Add unlimited column to 4 workflow lookup tables ✓
- [x] **Phase 4: Environment Variable** - Implement GSD_DEFAULT_MODEL_PROFILE with fallback chain ✓
- [x] **Phase 5: User Interface** - Add unlimited option to settings, set-profile, new-project ✓
- [x] **Phase 6: Documentation** - Update help, README, CHANGELOG with new feature ✓

## Phase Details

### Phase 1: Master Reference
**Goal**: The canonical model-profiles.md defines the unlimited profile with Opus for all agents
**Depends on**: Nothing (first phase)
**Requirements**: TABLE-01
**Success Criteria** (what must be TRUE):
  1. model-profiles.md contains unlimited column with opus for all 11 agents
  2. Philosophy section explains when to use unlimited profile
  3. Table format matches existing quality/balanced/budget structure
**Plans**: 1 plan

Plans:
- [x] 01-01-PLAN.md — Add unlimited profile to model-profiles.md (table, philosophy, rationale) ✓

### Phase 2: Command Tables
**Goal**: All 8 command files have unlimited column in their model lookup tables
**Depends on**: Phase 1
**Requirements**: TABLE-02, TABLE-03, TABLE-04, TABLE-05, TABLE-06, TABLE-07, TABLE-08, TABLE-09
**Success Criteria** (what must be TRUE):
  1. audit-milestone.md lookup table includes unlimited column
  2. debug.md lookup table includes unlimited column
  3. execute-phase.md command lookup table includes unlimited column
  4. new-milestone.md, new-project.md, plan-phase.md, quick.md, research-phase.md all include unlimited column
  5. All tables specify opus for relevant agents in unlimited column
**Plans**: 1 plan

Plans:
- [x] 02-01-PLAN.md — Add unlimited column to all 8 command file lookup tables ✓

### Phase 3: Workflow Tables
**Goal**: All 4 workflow files have unlimited column in their model lookup tables
**Depends on**: Phase 2
**Requirements**: TABLE-10, TABLE-11, TABLE-12, TABLE-13
**Success Criteria** (what must be TRUE):
  1. execute-phase.md workflow lookup table includes unlimited column
  2. execute-plan.md lookup table includes unlimited column
  3. map-codebase.md lookup table includes unlimited column
  4. verify-work.md lookup table includes unlimited column
**Plans**: 1 plan

Plans:
- [x] 03-01-PLAN.md — Add unlimited column to all 4 workflow file lookup tables ✓

### Phase 4: Environment Variable
**Goal**: All 12 files support GSD_DEFAULT_MODEL_PROFILE with proper fallback chain
**Depends on**: Phase 3
**Requirements**: ENV-01, ENV-02
**Success Criteria** (what must be TRUE):
  1. Setting GSD_DEFAULT_MODEL_PROFILE=unlimited uses unlimited profile when no config.json exists
  2. config.json model_profile takes precedence over env var
  3. Fallback chain works: config.json -> env var -> "balanced"
  4. All 12 files use identical bash pattern for reading MODEL_PROFILE
**Plans**: 2 plans

Plans:
- [x] 04-01-PLAN.md — Update MODEL_PROFILE pattern in 8 command files ✓
- [x] 04-02-PLAN.md — Update MODEL_PROFILE pattern in 4 workflow files ✓

### Phase 5: User Interface
**Goal**: Users can select and configure unlimited profile through GSD commands
**Depends on**: Phase 4
**Requirements**: UI-01, UI-02, UI-03
**Success Criteria** (what must be TRUE):
  1. /gsd:settings shows "Unlimited" as a selectable option
  2. /gsd:set-profile accepts "unlimited" and shows it in examples
  3. /gsd:new-project offers "Unlimited" when configuring model profile
**Plans**: 1 plan

Plans:
- [x] 05-01-PLAN.md — Add Unlimited option to settings.md, set-profile.md, and new-project.md ✓

### Phase 6: Documentation
**Goal**: Users can discover and understand the unlimited profile and env var through documentation
**Depends on**: Phase 5
**Requirements**: DOC-01, DOC-02, DOC-03, DOC-04
**Success Criteria** (what must be TRUE):
  1. model-profiles.md explains unlimited profile purpose and trade-offs
  2. help.md lists unlimited alongside quality/balanced/budget
  3. README.md documents GSD_DEFAULT_MODEL_PROFILE env var
  4. CHANGELOG.md has entry for new feature
**Plans**: 1 plan

Plans:
- [x] 06-01-PLAN.md — Update help.md, README.md, and CHANGELOG.md with unlimited profile and env var documentation ✓

## Progress

**Execution Order:**
Phases execute in numeric order: 1 -> 2 -> 3 -> 4 -> 5 -> 6

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Master Reference | 1/1 | Complete ✓ | 2026-02-02 |
| 2. Command Tables | 1/1 | Complete ✓ | 2026-02-02 |
| 3. Workflow Tables | 1/1 | Complete ✓ | 2026-02-02 |
| 4. Environment Variable | 2/2 | Complete ✓ | 2026-02-02 |
| 5. User Interface | 1/1 | Complete ✓ | 2026-02-02 |
| 6. Documentation | 1/1 | Complete ✓ | 2026-02-02 |
