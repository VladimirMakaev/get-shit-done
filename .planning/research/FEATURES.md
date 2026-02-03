# Model Lookup Tables

## Summary

Found **12 model lookup tables** across the GSD codebase covering **11 unique agents**.

All tables follow the standard 3-profile structure: quality | balanced | budget

## Master Table

### get-shit-done/references/model-profiles.md

```
| Agent | quality | balanced | budget |
|-------|---------|----------|--------|
| gsd-planner | opus | opus | sonnet |
| gsd-roadmapper | opus | sonnet | sonnet |
| gsd-executor | opus | sonnet | sonnet |
| gsd-phase-researcher | opus | sonnet | haiku |
| gsd-project-researcher | opus | sonnet | haiku |
| gsd-research-synthesizer | sonnet | sonnet | haiku |
| gsd-debugger | opus | sonnet | sonnet |
| gsd-codebase-mapper | sonnet | haiku | haiku |
| gsd-verifier | sonnet | sonnet | haiku |
| gsd-plan-checker | sonnet | sonnet | haiku |
| gsd-integration-checker | sonnet | sonnet | haiku |
```

## Distributed Tables

Each workflow/command contains a subset of the master table for agents it spawns:

| File | Agents |
|------|--------|
| workflows/execute-plan.md | gsd-executor |
| workflows/execute-phase.md | gsd-executor, gsd-verifier |
| workflows/verify-work.md | gsd-planner, gsd-plan-checker |
| workflows/map-codebase.md | gsd-codebase-mapper |
| commands/gsd/quick.md | gsd-planner, gsd-executor |
| commands/gsd/plan-phase.md | gsd-phase-researcher, gsd-planner, gsd-plan-checker |
| commands/gsd/debug.md | gsd-debugger |
| commands/gsd/research-phase.md | gsd-phase-researcher |
| commands/gsd/new-project.md | gsd-project-researcher, gsd-research-synthesizer, gsd-roadmapper |
| commands/gsd/audit-milestone.md | gsd-integration-checker |
| commands/gsd/new-milestone.md | gsd-project-researcher, gsd-research-synthesizer, gsd-roadmapper |

## Agent Coverage

All 11 unique agents with current profile mappings:

| Agent | quality | balanced | budget | Role |
|-------|---------|----------|--------|------|
| gsd-planner | opus | opus | sonnet | Planning |
| gsd-roadmapper | opus | sonnet | sonnet | Planning |
| gsd-executor | opus | sonnet | sonnet | Execution |
| gsd-phase-researcher | opus | sonnet | haiku | Research |
| gsd-project-researcher | opus | sonnet | haiku | Research |
| gsd-research-synthesizer | sonnet | sonnet | haiku | Research |
| gsd-debugger | opus | sonnet | sonnet | Execution |
| gsd-codebase-mapper | sonnet | haiku | haiku | Research |
| gsd-verifier | sonnet | sonnet | haiku | Verification |
| gsd-plan-checker | sonnet | sonnet | haiku | Verification |
| gsd-integration-checker | sonnet | sonnet | haiku | Verification |

## "Unlimited" Profile Values

For the new "unlimited" profile, all agents should use **opus**:

| Agent | unlimited |
|-------|-----------|
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
*Research: 2026-02-02*
