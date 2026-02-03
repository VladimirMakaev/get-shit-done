# External Integrations

**Analysis Date:** 2026-02-02

## APIs & External Services

**GitHub:**
- GitHub repository hosting - https://github.com/glittercowboy/get-shit-done
- Used for source code distribution, issue tracking
- No API integration - manual/user-driven

**npm Registry:**
- Package distribution via https://registry.npmjs.org
- Package: `get-shit-done-cc`
- Version checking via `npm view get-shit-done-cc version` (in update check hook)

**Discord:**
- Community server link: https://discord.gg/5JJgD5svVS
- No programmatic integration - reference only in documentation

## Data Storage

**Databases:**
- None

**File Storage:**
- Local filesystem only
- Project planning data: `.planning/` directory in project root
- Runtime config: `~/.claude/`, `~/.config/opencode/`, or `~/.gemini/`
- Cache directory: `~/.claude/cache/` (for update check results)

**Caching:**
- File-based cache for update checks
  - Location: `~/.claude/cache/gsd-update-check.json`
  - Stores: latest version, check timestamp, update availability

## Authentication & Identity

**Auth Provider:**
- None - GSD operates within host runtimes (Claude Code, OpenCode, Gemini)
  - Host runtime handles authentication to AI providers
  - GSD reads/writes files with user's filesystem permissions

## Monitoring & Observability

**Error Tracking:**
- None

**Logs:**
- Console output only (installation messages, hook output)
- Status written to `~/.claude/cache/gsd-update-check.json` by update check hook

## CI/CD & Deployment

**Hosting:**
- npm registry for package distribution

**CI Pipeline:**
- None detected - manual publishing workflow
  - `prepublishOnly` script runs `npm run build:hooks`
  - Builds hooks to `hooks/dist/` before publishing to npm

## Environment Configuration

**Required env vars:**
- None required - all configuration optional with sensible defaults

**Optional env vars:**
- `CLAUDE_CONFIG_DIR` - Override default Claude Code config directory
- `OPENCODE_CONFIG_DIR` - Override default OpenCode config directory
- `OPENCODE_CONFIG` - Path to OpenCode config file (directory derived from this)
- `XDG_CONFIG_HOME` - XDG Base Directory spec compliance for OpenCode
- `GEMINI_CONFIG_DIR` - Override default Gemini config directory

**Secrets location:**
- None - GSD has no secrets or API keys
  - Relies on host runtime's authentication

## Webhooks & Callbacks

**Incoming:**
- None

**Outgoing:**
- None

## Runtime Integration

**Claude Code Integration:**
- Installs to: `~/.claude/` (global) or `./.claude/` (local)
- Registers commands in: `commands/gsd/` directory
- Registers agents in: `agents/` directory
- Hooks registered in: `settings.json` hooks.SessionStart array
- Statusline: `settings.json` statusLine field

**OpenCode Integration:**
- Installs to: `~/.config/opencode/` (global) or `./.opencode/` (local)
- Commands flattened to: `command/gsd-*.md` (singular, flat structure)
- Permissions configured in: `~/.config/opencode/opencode.json`
  - Grants read/external_directory access to GSD docs

**Gemini CLI Integration:**
- Installs to: `~/.gemini/` (global) or `./.gemini/` (local)
- Commands as TOML: `commands/gsd/*.toml` (converted from markdown)
- Agents with tools array: Frontmatter converted to Gemini tool names
- Experimental agents flag: `settings.json` experimental.enableAgents = true

**Git Integration:**
- Used by installer to check if `.planning` is gitignored
- Used by hooks to detect repository state
- No direct git API usage - spawns `git` commands via `child_process`

---

*Integration audit: 2026-02-02*
