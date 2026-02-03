# Codebase Concerns

**Analysis Date:** 2026-02-02

## Tech Debt

**1,445-line installation script:**
- Issue: `bin/install.js` is a monolithic 1,445-line file handling multi-runtime installation, frontmatter conversion, settings management, and uninstallation logic
- Files: `bin/install.js`
- Impact: Difficult to maintain, test, and debug. High cognitive load for contributors. Frontmatter conversion logic (Claude Code → OpenCode/Gemini) is complex and error-prone
- Fix approach: Extract into modules: `lib/installer.js`, `lib/frontmatter-converter.js`, `lib/settings-manager.js`, `lib/uninstaller.js`. Add unit tests for frontmatter conversion edge cases

**Missing build artifact directory:**
- Issue: `hooks/dist/` is gitignored (build artifact) but required by npm package. Build happens via `npm run build:hooks` in `prepublishOnly` hook
- Files: `scripts/build-hooks.js`, `package.json`, `.gitignore`
- Impact: Local development requires manual build step. Contributors may forget to build before testing install, leading to missing hooks directory errors
- Fix approach: Add prebuild check in install.js to verify dist exists, provide helpful error message. Document build requirement in CONTRIBUTING.md

**No automated testing:**
- Issue: No test files found (`*.test.*`, `*.spec.*`). No test framework configured despite being a complex meta-prompting system with multi-runtime support
- Files: None (that's the problem)
- Impact: Regressions slip through. Installer logic handles 3 runtimes × 2 locations × multiple config edge cases without test coverage. Frontmatter conversion bugs surface in production
- Fix approach: Add Vitest or Jest. Start with critical paths: installer runtime detection, frontmatter conversion (Claude→OpenCode, Claude→Gemini), settings.json manipulation

**Silent error swallowing in hooks:**
- Issue: Hooks use empty catch blocks that suppress all errors
- Files: `hooks/gsd-statusline.js:61,74,84`, `hooks/gsd-check-update.js:41,46`
- Impact: Debugging failures is impossible. Statusline breaks silently on malformed JSON. Update checker fails silently on network errors, users never know
- Fix approach: Add structured logging to `~/.claude/logs/hooks.log`. Emit errors to stderr with context. Add graceful degradation messages (e.g., "Status unavailable")

**Legacy flag maintained for backward compatibility:**
- Issue: `--both` flag kept as "legacy" (line 25) but still fully supported alongside new `--all` flag
- Files: `bin/install.js:25,33-34`
- Impact: API surface confusion. Documentation burden (must explain both). Migration path unclear (when to deprecate?)
- Fix approach: Add deprecation warning when `--both` is used: "Warning: --both is deprecated, use --all instead". Remove in v2.0

## Known Bugs

**Hooks directory not created during build:**
- Symptoms: Installation fails with "hooks/dist not found" if `npm run build:hooks` hasn't been run
- Files: `scripts/build-hooks.js:20-22`, `package.json:44-45`
- Trigger: Developer runs `npx .` locally without running build script first, or CI skips `prepublishOnly` hook
- Workaround: Manually run `npm run build:hooks` before testing installer

**Update check uses synchronous spawn in background process:**
- Symptoms: Hook spawns background Node process that runs `npm view` synchronously with 10s timeout
- Files: `hooks/gsd-check-update.js:24-59`
- Trigger: Every new Claude Code session
- Workaround: None needed (works, but architecturally fragile)

## Security Considerations

**Installer executes user-provided config directory paths:**
- Risk: `--config-dir` flag accepts arbitrary paths, expanded with `expandTilde()`, then used in file operations
- Files: `bin/install.js:121-143,159-164`
- Current mitigation: Path validation checks for empty values, but no sanitization for path traversal or injection
- Recommendations: Add path validation to reject `..`, absolute paths outside home directory, and symlinks. Whitelist allowed config directory patterns

**No integrity checks on hook scripts:**
- Risk: Hooks copied from `hooks/dist/` to user config directories without verification
- Files: `scripts/build-hooks.js:24-36`
- Current mitigation: None
- Recommendations: Add SHA256 checksums for hook files in `package.json`, verify during copy. Protect against supply chain attacks if npm package is compromised

**Frontmatter conversion strips content without validation:**
- Risk: `stripSubTags()` function uses regex to transform HTML in agent documents for Gemini compatibility
- Files: `bin/install.js:292-294,372`
- Current mitigation: Only runs on .md files from trusted source (npm package)
- Recommendations: Add schema validation after frontmatter conversion to ensure output is valid YAML. Prevent malformed agents from breaking Gemini CLI

**Credentials exposed in environment variable checks:**
- Risk: Templates reference sensitive env var patterns (API_KEY, SECRET, TOKEN, PASSWORD) without guidance on secure storage
- Files: `get-shit-done/templates/user-setup.md`, `get-shit-done/templates/codebase/integrations.md`
- Current mitigation: Documentation recommends `.env.local` (gitignored) and 1Password
- Recommendations: Add pre-commit hook to detect common secret patterns. Reference security best practices in all templates mentioning credentials

## Performance Bottlenecks

**Statusline reads filesystem on every render:**
- Problem: `hooks/gsd-statusline.js` reads todos directory, parses JSON files, sorts by mtime on every statusline render (potentially multiple times per second)
- Files: `hooks/gsd-statusline.js:48-63`
- Cause: No caching, filesystem operations in hot path
- Improvement path: Cache todos for 1-2 seconds, only re-read when mtime changes. Use streaming JSON parser for large todo files

**Update check runs synchronous `npm view` with 10s timeout:**
- Problem: Background process blocks on `execSync` call to npm registry
- Files: `hooks/gsd-check-update.js:45`
- Cause: Using synchronous API in what should be async background task
- Improvement path: Use async `exec` or native HTTPS request to npm registry API. Fail fast after 2s (not 10s)

**Large documentation files loaded into memory:**
- Problem: Agent and workflow files range from 8KB to 56KB. `gsd-planner.md` (44KB), `execute-plan.md` (56KB)
- Files: `agents/*.md`, `get-shit-done/workflows/*.md`
- Cause: Design choice - Claude Code loads full agent specs into context
- Improvement path: Not actionable (system constraint). Monitor for context window exhaustion when multiple agents spawn

## Fragile Areas

**Multi-runtime frontmatter conversion:**
- Files: `bin/install.js:198-372`
- Why fragile: Handles 3 different agent formats (Claude Code, OpenCode, Gemini) with overlapping but incompatible frontmatter schemas. String manipulation of YAML frontmatter (not proper YAML parsing). Easy to introduce format bugs that only surface at runtime
- Safe modification: Add YAML parser (js-yaml) to parse/modify/serialize instead of string manipulation. Add schema validation tests. Test against all agent files in `agents/` directory
- Test coverage: None

**Settings.json manipulation during install/uninstall:**
- Files: `bin/install.js:177-195,800-831`
- Why fragile: Mutates existing settings.json without backup. Filters hooks arrays, removes empty objects, deletes keys. Race conditions possible if multiple installers run concurrently
- Safe modification: Create backup before modifying (`settings.json.backup`). Use atomic writes (write to temp file, rename). Add rollback on error
- Test coverage: None

**Hook registration across 3 runtimes:**
- Files: `bin/install.js:1070-1146` (SessionStart hooks), `bin/install.js:1147-1210` (PostToolUse hooks - Gemini only)
- Why fragile: Different hook formats for Claude Code (array of hook objects), OpenCode (theme-based), Gemini (post_use array). Easy to break one runtime when updating hook logic
- Safe modification: Extract runtime-specific hook builders. Add integration tests that install to temp config dirs and verify settings.json output matches expected format
- Test coverage: None

**Version detection for update checks:**
- Files: `hooks/gsd-check-update.js:16-40`
- Why fragile: Checks project directory first (`cwd/.claude/get-shit-done/VERSION`), falls back to global (`~/.claude/get-shit-done/VERSION`). VERSION file may be missing or contain invalid semver
- Safe modification: Add semver validation (`/^\d+\.\d+\.\d+$/`). Handle missing VERSION files gracefully (assume 0.0.0). Log warnings to help users debug
- Test coverage: None

## Scaling Limits

**Number of commands (27) and agents (11):**
- Current capacity: 27 command files, 11 agent files totaling ~200KB of markdown
- Limit: Claude Code context window (~200K tokens). Loading all docs for complex workflows may exceed limits
- Scaling path: Add lazy loading for commands (only load when invoked). Compress redundant documentation. Split monolithic workflows into focused sub-workflows

**Installer supports 3 runtimes with combinatorial complexity:**
- Current capacity: 3 runtimes × 2 locations × multiple config formats = 6+ code paths
- Limit: Adding new runtime (e.g., Cline, Cursor) requires touching multiple conversion functions, hook formats, permission systems
- Scaling path: Plugin architecture for runtime adapters. Each runtime provides adapter implementing `convertAgent()`, `installHooks()`, `uninstall()` interface

## Dependencies at Risk

**esbuild (0.24.0) only in devDependencies:**
- Risk: Listed in package.json but not currently used (hooks are pure Node, no bundling needed per scripts/build-hooks.js)
- Impact: Dead dependency. Confusing for contributors (why is esbuild here?)
- Migration plan: Remove esbuild from package.json unless future bundling is planned. Update CONTRIBUTING.md to clarify hooks are unbundled

**Zero runtime dependencies:**
- Risk: While impressive for security/install speed, means all logic is hand-rolled (no YAML parser, no schema validator, no test framework)
- Impact: Higher maintenance burden. Frontmatter conversion uses fragile string manipulation instead of proper parsing
- Migration plan: Consider adding minimal dependencies for critical paths: `js-yaml` for frontmatter, `ajv` for schema validation. Keep install fast by bundling with esbuild for hooks if needed

## Missing Critical Features

**No rollback mechanism for failed installations:**
- Problem: If installation fails mid-process (e.g., after copying commands but before hooks), state is inconsistent
- Blocks: Clean uninstall (uninstaller assumes complete install), retry attempts (files may be partially written)
- Priority: Medium

**No verification that installed agents are loadable:**
- Problem: After converting frontmatter for OpenCode/Gemini, no check that resulting files are valid
- Blocks: Silent failures at runtime (agent fails to load, user sees cryptic error)
- Priority: High

**No logging framework:**
- Problem: Hooks use `console.error` or swallow errors silently. No structured logging
- Blocks: Debugging production issues (users can't send logs), telemetry (can't track adoption, errors)
- Priority: Medium

## Test Coverage Gaps

**Installer logic (all scenarios):**
- What's not tested: Runtime selection, location selection, config-dir handling, frontmatter conversion, settings.json manipulation, uninstallation, hook registration
- Files: `bin/install.js` (entire 1,445 lines)
- Risk: Regressions in multi-runtime support, config edge cases, Windows compatibility
- Priority: Critical

**Frontmatter conversion for all 3 runtimes:**
- What's not tested: Claude Code → OpenCode agent conversion, Claude Code → Gemini agent conversion, tool name mapping, color stripping, HTML tag stripping
- Files: `bin/install.js:198-372`
- Risk: Agents fail to load in OpenCode/Gemini after conversion, tools not available, color field breaks Gemini
- Priority: High

**Hook functionality (statusline, update check):**
- What's not tested: Statusline rendering with/without todos, context bar calculations, update check version comparison, cache file handling
- Files: `hooks/gsd-statusline.js`, `hooks/gsd-check-update.js`
- Risk: Statusline breaks on malformed JSON, update checker shows wrong version, cache corruption
- Priority: Medium

---

*Concerns audit: 2026-02-02*
