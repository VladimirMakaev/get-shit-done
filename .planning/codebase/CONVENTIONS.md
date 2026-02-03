# Coding Conventions

**Analysis Date:** 2026-02-02

## Naming Patterns

**Files:**
- Markdown agents: `gsd-{purpose}.md` (e.g., `gsd-executor.md`, `gsd-planner.md`)
- Markdown commands: `{command-name}.md` in nested structure (e.g., `commands/gsd/help.md`)
- JavaScript hooks: `gsd-{purpose}.js` (e.g., `gsd-statusline.js`, `gsd-check-update.js`)
- JavaScript utilities: `{purpose}.js` (e.g., `install.js`, `build-hooks.js`)
- Templates: `{concept}.md` or `{concept}.json` (kebab-case for multi-word)
- Workflows: `{workflow-name}.md` (kebab-case)

**Functions:**
- camelCase for function names: `getDirName()`, `expandTilde()`, `readSettings()`
- Descriptive verbs: `copyWithPathReplacement()`, `cleanupOrphanedHooks()`

**Variables:**
- camelCase: `hasGlobal`, `settingsPath`, `explicitConfigDir`
- SCREAMING_SNAKE_CASE for constants: `HOOKS_DIR`, `DIST_DIR`, `HOOKS_TO_COPY`
- Semantic prefixes for booleans: `has*`, `is*`, `should*`

**Types:**
- Not applicable (pure JavaScript codebase, no TypeScript)

## Code Style

**Formatting:**
- No automated formatter detected
- 2-space indentation (consistent across all `.js` files)
- No semicolons enforced (mix of with/without)
- String literals: mix of single quotes and template literals

**Linting:**
- No ESLint or other linter detected
- No `.eslintrc*`, `eslint.config.*`, or `biome.json` found

## Import Organization

**Order:**
1. Node.js built-in modules (`fs`, `path`, `os`, `child_process`, `readline`)
2. Local relative imports (`require('../package.json')`)
3. No third-party dependencies in production code

**Path Aliases:**
- Not used
- All imports are relative paths (e.g., `require('../package.json')`)

## Error Handling

**Patterns:**
- Try-catch for JSON parsing: return empty object on failure
  ```javascript
  try {
    return JSON.parse(fs.readFileSync(settingsPath, 'utf8'));
  } catch (e) {
    return {};
  }
  ```
- Silent failures in background processes (statusline, update checks)
- Explicit error messages with colored output for CLI commands
- `process.exit(1)` for fatal errors in install script
- Existence checks before file operations: `fs.existsSync()` before reads/writes

## Logging

**Framework:** Native `console` methods

**Patterns:**
- Console output with ANSI color codes for CLI feedback
  - `\x1b[36m` (cyan) for primary info
  - `\x1b[32m` (green) for success checkmarks
  - `\x1b[33m` (yellow) for warnings
  - `\x1b[2m` (dim) for secondary info
- Silent operation in hooks (no console output unless error)
- Structured output for user-facing commands (banners, formatted lists)
- No debug logging framework

## Comments

**When to Comment:**
- Function-level JSDoc for complex functions (limited usage)
- Inline comments for non-obvious logic (e.g., regex patterns, platform differences)
- Section headers with comment blocks for major code sections
- Shebang for executable scripts: `#!/usr/bin/env node`

**JSDoc/TSDoc:**
- Minimal JSDoc usage
- Used for complex functions with parameters:
  ```javascript
  /**
   * Copy commands to a flat structure for OpenCode
   * @param {string} srcDir - Source directory
   * @param {string} destDir - Destination directory
   */
  ```

## Function Design

**Size:** Functions range from 10-80 lines, with some complex installers up to 150 lines

**Parameters:**
- 1-4 parameters typical
- Use object destructuring for optional parameters (minimal usage)
- Default parameters: `function getGlobalDir(runtime, explicitDir = null)`

**Return Values:**
- Early returns for guard clauses
- Return objects for multiple values: `{ settingsPath, settings, statuslineCommand, runtime }`
- Return booleans for validation functions
- `void` (implicit undefined) for side-effect functions

## Module Design

**Exports:**
- No explicit exports (scripts are entry points, not libraries)
- Functions defined in same file where used
- Global scope functions for reuse within file

**Barrel Files:**
- Not applicable (no module system)

## Markdown Conventions

**YAML Frontmatter:**
- Used in agent and command files
- Standard fields: `name`, `description`, `tools`, `color`
- Tools as comma-separated list or YAML array

**XML Tags:**
- Semantic containers: `<role>`, `<objective>`, `<process>`, `<step>`, `<task>`
- Attributes use kebab-case: `type="checkpoint:human-verify"`, `gate="blocking"`
- Nested markdown headers within XML tags (not nested XML)

**Step Naming:**
- snake_case for step names: `<step name="load_project_state">`
- Priority attribute when order matters: `<step name="load_plan" priority="first">`

## Platform Compatibility

**Path Handling:**
- Use `path.join()` for all path construction
- Convert backslashes to forward slashes for Node.js compatibility: `path.replace(/\\/g, '/')`
- Expand tilde manually for shell paths: `expandTilde()` function

**Cross-Platform:**
- `windowsHide: true` for child processes to prevent console flash on Windows
- `os.homedir()` instead of `~` in code
- Platform-agnostic shebang: `#!/usr/bin/env node`

---

*Convention analysis: 2026-02-02*
