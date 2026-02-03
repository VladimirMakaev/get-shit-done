# Codebase Structure

**Analysis Date:** 2026-02-02

## Directory Layout

```
get-shit-done/
├── bin/                    # CLI entry point and installer
├── commands/               # User-facing command definitions
│   └── gsd/               # GSD command library (29 commands)
├── agents/                 # Specialized subagent definitions (11 agents)
├── get-shit-done/         # Framework core
│   ├── references/        # Cross-cutting guidance docs
│   ├── workflows/         # Reusable orchestration procedures
│   └── templates/         # Artifact scaffolds
├── hooks/                  # IDE integration hooks
├── scripts/                # Build and utility scripts
├── .planning/              # Project-specific planning state (created per-project)
│   └── codebase/          # Codebase analysis docs (created by map-codebase)
├── assets/                 # Documentation assets
└── .github/                # GitHub repository config
```

## Directory Purposes

**bin/**
- Purpose: Package entry point and installation script
- Contains: `install.js` (Node.js CLI installer)
- Key files: `bin/install.js` - handles runtime detection, file copying, settings.json updates, hook installation

**commands/gsd/**
- Purpose: User-facing command definitions that orchestrate GSD workflows
- Contains: 29 markdown command files with YAML frontmatter
- Key files:
  - `new-project.md`: Initialize new project with questioning → research → requirements → roadmap
  - `new-milestone.md`: Start new milestone cycle (brownfield equivalent of new-project)
  - `plan-phase.md`: Create executable phase plans with research and verification loop
  - `execute-phase.md`: Execute all plans in a phase with wave-based parallelization
  - `verify-work.md`: Verify implementation against requirements
  - `progress.md`: Show current project status
  - `quick.md`: Fast-track for simple changes (question → plan → execute → verify)
  - `map-codebase.md`: Analyze codebase and generate .planning/codebase/ docs
  - `debug.md`: Diagnose issues with context dumps
  - `help.md`: Command reference and examples

**agents/**
- Purpose: Specialized subagent definitions spawned by commands
- Contains: 11 markdown agent files with role definitions and execution logic
- Key files:
  - `gsd-planner.md`: Creates executable phase plans with task breakdown and dependency analysis
  - `gsd-executor.md`: Executes PLAN.md files with atomic commits and deviation handling
  - `gsd-verifier.md`: Tests implementation against requirements
  - `gsd-codebase-mapper.md`: Explores codebase focus area and writes analysis documents
  - `gsd-roadmapper.md`: Creates phase structure from requirements
  - `gsd-phase-researcher.md`: Researches domain knowledge for specific phase
  - `gsd-project-researcher.md`: Researches domains for new project
  - `gsd-plan-checker.md`: Verifies plans against requirements before execution
  - `gsd-debugger.md`: Diagnoses execution issues and proposes fixes
  - `gsd-integration-checker.md`: Validates integration points
  - `gsd-research-synthesizer.md`: Synthesizes research into actionable insights

**get-shit-done/references/**
- Purpose: Cross-cutting guidance documents injected into prompts
- Contains: 9 reference documents
- Key files:
  - `checkpoints.md`: Checkpoint protocol for pausing/resuming execution
  - `git-integration.md`: Git commit patterns and branching strategies
  - `questioning.md`: Deep questioning methodology for requirements gathering
  - `verification-patterns.md`: Testing and verification approaches
  - `tdd.md`: Test-driven development workflow (RED/GREEN/REFACTOR)
  - `ui-brand.md`: Console output formatting and emoji conventions
  - `continuation-format.md`: Handoff format for pausing work
  - `planning-config.md`: Configuration options for .planning/config.json
  - `model-profiles.md`: Model selection strategy (quality/balanced/budget)

**get-shit-done/workflows/**
- Purpose: Reusable orchestration procedures for complex multi-step operations
- Contains: 12 workflow documents
- Key files:
  - `execute-plan.md`: Execute single PLAN.md with executor agent
  - `execute-phase.md`: Orchestrate wave-based parallel plan execution
  - `verify-work.md`: Verification workflow with verifier agent
  - `verify-phase.md`: Verify single phase against criteria
  - `map-codebase.md`: Parallel mapper orchestration (tech/arch/quality/concerns)
  - `discovery-phase.md`: Exploratory investigation workflow
  - `discuss-phase.md`: Interactive phase refinement
  - `complete-milestone.md`: Milestone completion and archiving
  - `transition.md`: State transitions between phases
  - `resume-project.md`: Resume paused project from STATE.md
  - `diagnose-issues.md`: Debug workflow for troubleshooting

**get-shit-done/templates/**
- Purpose: Structured document scaffolds for artifact generation
- Contains: Template directories and files
- Key files:
  - `project.md`: PROJECT.md template (what/value/requirements/context/history)
  - `requirements.md`: REQUIREMENTS.md template (goals/non-goals/assumptions)
  - `roadmap.md`: ROADMAP.md template (phases with goals/deliverables/dependencies)
  - `state.md`: STATE.md template (position/decisions/concerns/alignment)
  - `summary.md`: SUMMARY.md template (what shipped, deviations, learnings)
  - `context.md`: CONTEXT.md template (background for phase planning)
  - `config.json`: Configuration template (model_profile, commit_docs, branching)
  - `codebase/`: Codebase analysis templates (STACK.md, ARCHITECTURE.md, etc.)
  - `research-project/`: Research project templates

**hooks/**
- Purpose: IDE integration hooks for statusline and update checks
- Contains: JavaScript hook files executed by IDE
- Key files:
  - `gsd-statusline.js`: Displays current phase in IDE statusline
  - `gsd-check-update.js`: Checks npm for new versions periodically

**scripts/**
- Purpose: Build and maintenance scripts
- Contains: Node.js build scripts
- Key files:
  - `build-hooks.js`: Compiles hooks (if needed for future TypeScript migration)

**.planning/** (created per-project)
- Purpose: Project-specific planning state and artifacts
- Contains: Project context, requirements, roadmap, phase plans, state
- Key files:
  - `PROJECT.md`: Living project context (what/value/requirements/history)
  - `REQUIREMENTS.md`: Scoped requirements for current milestone
  - `ROADMAP.md`: Phase structure with execution order
  - `STATE.md`: Project memory (position, decisions, blockers)
  - `config.json`: Project configuration (model_profile, commit_docs)
  - `phases/NN-name/`: Phase directories with PLAN.md and SUMMARY.md files
  - `research/`: Domain research documents
  - `codebase/`: Codebase analysis documents (STACK.md, ARCHITECTURE.md, etc.)

**.planning/codebase/** (created by map-codebase)
- Purpose: Codebase analysis documents for context injection
- Contains: 7 structured analysis documents
- Key files:
  - `STACK.md`: Technology stack (languages/frameworks/dependencies)
  - `INTEGRATIONS.md`: External APIs and services
  - `ARCHITECTURE.md`: Pattern, layers, data flow
  - `STRUCTURE.md`: Directory layout and file organization
  - `CONVENTIONS.md`: Coding style and patterns
  - `TESTING.md`: Testing framework and patterns
  - `CONCERNS.md`: Technical debt and issues

**assets/**
- Purpose: Documentation assets for README and guides
- Contains: Images, diagrams, terminal recordings

**.github/**
- Purpose: GitHub repository configuration
- Contains: Pull request template, workflow definitions

## Key File Locations

**Entry Points:**
- `bin/install.js`: CLI installer (Node.js entry point)
- `commands/gsd/*.md`: Command entry points (IDE invokes via `/gsd:command-name`)

**Configuration:**
- `package.json`: npm package definition, dependencies, build scripts
- `.planning/config.json`: Per-project GSD configuration
- `~/.claude/settings.json`: Global Claude Code configuration (installed by bin/install.js)
- `~/.config/opencode/settings.json`: Global OpenCode configuration
- `~/.gemini/config.json`: Global Gemini configuration

**Core Logic:**
- Commands orchestrate: `commands/gsd/*.md`
- Agents execute: `agents/*.md`
- Workflows define procedures: `get-shit-done/workflows/*.md`
- References provide guidance: `get-shit-done/references/*.md`

**Testing:**
- No automated test suite detected (manual verification via `/gsd:verify-work`)

## Naming Conventions

**Files:**
- Commands: `kebab-case.md` (e.g., `new-project.md`, `execute-phase.md`)
- Agents: `gsd-role.md` (e.g., `gsd-planner.md`, `gsd-executor.md`)
- Workflows: `kebab-case.md` (e.g., `execute-plan.md`, `verify-work.md`)
- References: `kebab-case.md` (e.g., `git-integration.md`, `ui-brand.md`)
- Templates: `lowercase.md` or `template-name.md`
- Planning artifacts: `UPPERCASE.md` (e.g., `PROJECT.md`, `ROADMAP.md`, `STATE.md`)
- Hooks: `gsd-hook-name.js` (e.g., `gsd-statusline.js`)

**Directories:**
- Framework core: `get-shit-done/` (single directory for all framework files)
- User commands: `commands/gsd/` (namespaced under gsd/)
- Agent definitions: `agents/` (flat structure)
- Planning state: `.planning/` (hidden directory, created per-project)
- Phase directories: `NN-kebab-case/` or `NN.N-kebab-case/` (e.g., `01-foundation/`, `01.1-hotfix/`)

**Identifiers:**
- Commands: `/gsd:kebab-case` (e.g., `/gsd:new-project`, `/gsd:execute-phase`)
- Agents: `gsd-role` (e.g., `gsd-planner`, `gsd-executor`)
- Plans: `NN-NN-PLAN.md` (e.g., `01-01-PLAN.md`, `01-02-PLAN.md`)
- Summaries: `NN-NN-SUMMARY.md` (e.g., `01-01-SUMMARY.md`)

## Where to Add New Code

**New Command:**
- Implementation: `commands/gsd/command-name.md`
- Pattern: Copy existing command, update frontmatter (name, description, allowed-tools)
- Registration: No registration needed - commands auto-discovered by IDE
- Invocation: `/gsd:command-name`

**New Agent:**
- Implementation: `agents/gsd-role.md`
- Pattern: Copy existing agent, define role/philosophy/process sections
- Spawning: Use Task tool in command/workflow with agent name

**New Workflow:**
- Implementation: `get-shit-done/workflows/workflow-name.md`
- Pattern: Document step-by-step procedure with bash examples
- Usage: Inject via `@~/.claude/get-shit-done/workflows/workflow-name.md` in command execution_context

**New Reference:**
- Implementation: `get-shit-done/references/topic-name.md`
- Pattern: Write guidance document with examples
- Usage: Inject via `@~/.claude/get-shit-done/references/topic-name.md` in command/agent/workflow

**New Template:**
- Implementation: `get-shit-done/templates/template-name.md`
- Pattern: Create markdown scaffold with placeholder sections
- Usage: Reference in agent/workflow when generating artifacts

**New Hook:**
- Implementation: `hooks/gsd-hook-name.js`
- Pattern: Node.js script with minimal dependencies
- Installation: Update `bin/install.js` to configure in settings.json

**Utilities:**
- Shared helpers: Currently none - each component is self-contained markdown
- If needed: Create `get-shit-done/utils/` for reusable logic

## Special Directories

**.planning/**
- Purpose: Project-specific planning state (created by `/gsd:new-project`)
- Generated: Yes (by commands)
- Committed: Configurable (controlled by `.planning/config.json` commit_docs setting)
- Contains: Living documents that evolve throughout project lifecycle

**.planning/phases/**
- Purpose: Phase-specific plans and summaries
- Generated: Yes (by `/gsd:plan-phase` and `/gsd:execute-phase`)
- Committed: Follows `.planning/` commit strategy
- Structure: `NN-phase-name/` directories with `NN-NN-PLAN.md` and `NN-NN-SUMMARY.md` files

**.planning/codebase/**
- Purpose: Codebase analysis for brownfield projects
- Generated: Yes (by `/gsd:map-codebase`)
- Committed: Follows `.planning/` commit strategy
- Contains: 7 analysis documents (STACK, INTEGRATIONS, ARCHITECTURE, STRUCTURE, CONVENTIONS, TESTING, CONCERNS)

**.planning/research/**
- Purpose: Domain research documents
- Generated: Yes (by research agents during project/milestone initialization)
- Committed: Follows `.planning/` commit strategy
- Contains: Research documents organized by domain

**node_modules/**
- Purpose: npm dependencies (only esbuild for build)
- Generated: Yes (by `npm install`)
- Committed: No (gitignored)

**.git/**
- Purpose: Git repository state
- Generated: Yes (by `git init` or user)
- Committed: No (git internal directory)
- Notes: GSD always initializes git if missing

---

*Structure analysis: 2026-02-02*
