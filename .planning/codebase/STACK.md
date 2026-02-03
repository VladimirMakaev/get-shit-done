# Technology Stack

**Analysis Date:** 2026-02-02

## Languages

**Primary:**
- JavaScript (Node.js) - All runtime code (installation, hooks, build scripts)

**Secondary:**
- Markdown - Agent definitions, command definitions, templates, documentation
- YAML - Frontmatter metadata in markdown files
- TOML - Generated for Gemini CLI command format
- JSON - Configuration files, package metadata

## Runtime

**Environment:**
- Node.js 16.7.0 or higher (specified in `package.json` engines field)

**Package Manager:**
- npm (standard Node.js package manager)
- Lockfile: `package-lock.json` present

## Frameworks

**Core:**
- None - Pure Node.js with built-in modules only

**Testing:**
- None detected

**Build/Dev:**
- esbuild 0.24.0 - For bundling hooks before publishing

## Key Dependencies

**Critical:**
- None in production - Zero runtime dependencies (pure Node.js)

**Infrastructure:**
- Built-in Node.js modules only:
  - `fs` - File system operations
  - `path` - Path manipulation
  - `os` - OS-level utilities (home directory, platform detection)
  - `readline` - Interactive CLI prompts
  - `child_process` - Spawning processes (git, npm)

## Configuration

**Environment:**
- Runtime detection via environment variables and default paths:
  - Claude Code: `CLAUDE_CONFIG_DIR` or `~/.claude`
  - OpenCode: `OPENCODE_CONFIG_DIR`, `OPENCODE_CONFIG`, `XDG_CONFIG_HOME/opencode`, or `~/.config/opencode`
  - Gemini: `GEMINI_CONFIG_DIR` or `~/.gemini`
- No `.env` files - configuration through runtime config directories
- Settings stored in `settings.json` in each runtime's config directory

**Build:**
- `scripts/build-hooks.js` - Copies hooks to `hooks/dist/` for distribution
- Build triggered by `prepublishOnly` npm lifecycle hook
- No transpilation - JavaScript runs directly in Node.js

## Platform Requirements

**Development:**
- Node.js 16.7.0+
- Unix-like environment (Mac, Linux, Windows with compatible shell)
- Git (for version control integration)

**Production:**
- Deployed as npm package `get-shit-done-cc`
- Installed globally via `npx` or locally per project
- Runs in Claude Code, OpenCode, or Gemini CLI environments
- Cross-platform compatible (Mac, Windows, Linux)

---

*Stack analysis: 2026-02-02*
