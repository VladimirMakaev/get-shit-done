# Architecture

**Analysis Date:** 2026-02-02

## Pattern Overview

**Overall:** Command-based meta-prompting orchestration system

**Key Characteristics:**
- Command frontmatter defines entry points and tool permissions
- Multi-agent orchestration with fresh context per subagent
- Document-driven state management through `.planning/` directory
- Template-based artifact generation with XML/markdown hybrid prompts
- Workflow composition through context injection (`@` file references)

## Layers

**CLI Layer:**
- Purpose: Installation and global configuration
- Location: `bin/install.js`
- Contains: Node.js installer script, runtime detection, config directory setup
- Depends on: Native Node.js modules (fs, path, os)
- Used by: End users via `npx get-shit-done-cc`

**Command Layer:**
- Purpose: User-facing commands that orchestrate workflows
- Location: `commands/gsd/*.md`
- Contains: Command definitions with YAML frontmatter, orchestration logic
- Depends on: Agents, workflows, templates, references
- Used by: Claude Code/OpenCode/Gemini via `/gsd:command-name`

**Agent Layer:**
- Purpose: Specialized subagents spawned by orchestrators
- Location: `agents/*.md`
- Contains: Agent role definitions, execution logic, state management protocols
- Depends on: Workflows, references, templates
- Used by: Commands via Task tool spawning

**Workflow Layer:**
- Purpose: Reusable process definitions for complex operations
- Location: `get-shit-done/workflows/*.md`
- Contains: Multi-step procedures, decision trees, subprocess orchestration
- Depends on: References, templates
- Used by: Commands and agents via `@~/.claude/get-shit-done/workflows/` references

**Reference Layer:**
- Purpose: Cross-cutting guidance documents
- Location: `get-shit-done/references/*.md`
- Contains: Git integration patterns, UI conventions, TDD methodology, verification patterns
- Depends on: Nothing (leaf nodes)
- Used by: All layers via `@~/.claude/get-shit-done/references/` injection

**Template Layer:**
- Purpose: Structured document scaffolds
- Location: `get-shit-done/templates/*.md`
- Contains: Markdown templates for PROJECT.md, REQUIREMENTS.md, codebase docs, etc.
- Depends on: Nothing (leaf nodes)
- Used by: Agents and workflows for artifact generation

**Hooks Layer:**
- Purpose: Runtime integrations for statusline and update checks
- Location: `hooks/*.js`
- Contains: JavaScript hooks that integrate with IDE statusline
- Depends on: Node.js runtime
- Used by: Claude Code/OpenCode/Gemini via settings.json configuration

## Data Flow

**Project Initialization Flow:**

1. User runs `/gsd:new-project` command
2. Command orchestrator loads `commands/gsd/new-project.md`
3. Executes questioning workflow from `references/questioning.md`
4. Spawns research agents (`gsd-project-researcher`, `gsd-research-synthesizer`) via Task tool
5. Spawns roadmap agent (`gsd-roadmapper`) to create ROADMAP.md
6. Writes artifacts to `.planning/` directory (PROJECT.md, REQUIREMENTS.md, ROADMAP.md, STATE.md)
7. Initializes git repository and creates initialization commit

**Phase Execution Flow:**

1. User runs `/gsd:plan-phase [N]` → orchestrator loads codebase docs from `.planning/codebase/`
2. Spawns `gsd-phase-researcher` if domain research needed
3. Spawns `gsd-planner` to create PLAN.md files (2-3 tasks each, wave-based)
4. Spawns `gsd-plan-checker` to verify plans against requirements
5. User runs `/gsd:execute-phase [N]` → orchestrator analyzes dependencies
6. Groups plans into execution waves (parallel where possible)
7. Spawns `gsd-executor` agents (one per plan) with fresh context each
8. Each executor commits per task, writes SUMMARY.md
9. Orchestrator updates STATE.md and ROADMAP.md

**Verification Flow:**

1. User runs `/gsd:verify-work` → orchestrator loads REQUIREMENTS.md and STATE.md
2. Spawns `gsd-verifier` agent with verification patterns
3. Agent tests implementation against acceptance criteria
4. Writes VERIFICATION.md with pass/fail/gap results
5. If gaps found, `/gsd:plan-phase --gaps` creates fix plans

**State Management:**
- All project state lives in `.planning/` directory (gitignored or committed based on config.json)
- STATE.md accumulates decisions, blockers, alignment status across phases
- Each command reads STATE.md first to load project memory
- Subagents inherit context through `@.planning/STATE.md` injection

## Key Abstractions

**Command Definition:**
- Purpose: Define entry points with tool permissions and context
- Examples: `commands/gsd/new-project.md`, `commands/gsd/execute-phase.md`
- Pattern: YAML frontmatter + XML-structured prompt body

**Agent Definition:**
- Purpose: Specialized executors with defined roles and responsibilities
- Examples: `agents/gsd-planner.md`, `agents/gsd-executor.md`, `agents/gsd-verifier.md`
- Pattern: YAML frontmatter + role/philosophy/process sections

**Context Injection:**
- Purpose: Include external documents into prompt context
- Examples: `@~/.claude/get-shit-done/references/ui-brand.md`, `@.planning/STATE.md`
- Pattern: `@` prefix followed by absolute or relative path

**Wave-Based Parallelization:**
- Purpose: Execute independent plans concurrently, dependent plans sequentially
- Examples: Phase execution with dependency analysis in `workflows/execute-phase.md`
- Pattern: Analyze imports/dependencies → group into waves → spawn parallel subagents

**Model Profile System:**
- Purpose: Select appropriate LLM model based on task complexity and budget
- Examples: quality=opus/opus, balanced=opus/sonnet, budget=sonnet/haiku
- Pattern: Read from `.planning/config.json`, lookup in model profile table

## Entry Points

**Installation Entry Point:**
- Location: `bin/install.js`
- Triggers: `npx get-shit-done-cc` from terminal
- Responsibilities: Detect runtime (Claude/OpenCode/Gemini), copy files to config dir, update settings.json, install hooks

**Command Entry Points:**
- Location: `commands/gsd/*.md`
- Triggers: User types `/gsd:command-name` in Claude Code/OpenCode/Gemini
- Responsibilities: Validate environment, load context, spawn subagents, coordinate workflow, write artifacts

**Hook Entry Points:**
- Location: `hooks/gsd-statusline.js`, `hooks/gsd-check-update.js`
- Triggers: IDE startup or periodic checks (configured in settings.json)
- Responsibilities: Display current phase in statusline, check npm for updates

## Error Handling

**Strategy:** Fail-fast with clear user guidance

**Patterns:**
- Environment validation at start of every command (check for `.planning/` existence)
- Git repo initialization if missing (GSD projects always have git)
- STATE.md reconstruction if missing but artifacts exist
- Explicit error messages with remediation steps (e.g., "Run /gsd:new-project first")
- Checkpoint protocol during execution (pause at verification points)
- Deviation handling in executor (detect drift from plan, auto-fix minor issues)

## Cross-Cutting Concerns

**Logging:** Minimal - relies on IDE conversation history as audit trail

**Validation:**
- Command arguments parsed and normalized before execution
- Phase numbers support integer (1, 2) and decimal (1.1, 2.3) formats
- Plan verification loop with `gsd-plan-checker` before execution
- Post-execution verification with `gsd-verifier` against requirements

**Authentication:** Not applicable - runs entirely in local IDE environment

**Configuration:**
- Global: `~/.claude/settings.json`, `~/.config/opencode/settings.json`, `~/.gemini/config.json`
- Per-project: `.planning/config.json` (model_profile, commit_docs, branching_strategy)
- Environment detection: Auto-detect from CLAUDE_CONFIG_DIR, OPENCODE_CONFIG_DIR, GEMINI_CONFIG_DIR

**Extensibility:**
- Commands are markdown files (easy to add new commands)
- Agents are markdown files (easy to add new agent types)
- Templates are markdown (easy to customize output formats)
- Model profiles support quality/balanced/budget presets

---

*Architecture analysis: 2026-02-02*
