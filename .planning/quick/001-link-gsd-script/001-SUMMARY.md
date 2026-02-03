---
phase: quick-001
plan: 01
subsystem: devtools
tags: [development, symlinks, testing]

requires: []
provides:
  - Development script for symlinking GSD from repository to ~/.claude
affects: []

tech-stack:
  added: []
  patterns: [bash-scripting]

key-files:
  created:
    - ~/.local/bin/link_gsd.sh
  modified: []

key-decisions:
  - "Script placed at ~/.local/bin/ instead of ~/scripts (permissions issue)"
  - "Agent files linked individually to preserve non-GSD agents"
  - "Backups use timestamp suffix for traceability"

patterns-established:
  - "Development symlink pattern for testing GSD changes"

duration: 1 min
completed: 2026-02-02
---

# Quick Task 001: Link GSD Script Summary

**Created development script at ~/.local/bin/link_gsd.sh for symlinking GSD files from repository to ~/.claude**

## Performance

- **Duration:** 1 min
- **Completed:** 2026-02-02
- **Tasks:** 1
- **Files created:** 1

## Accomplishments

- Created ~/.local/bin/link_gsd.sh script (4.1KB, 115 lines)
- Script validates repository structure before linking
- Backs up existing installations with timestamp suffix
- Links get-shit-done/, commands/gsd/, and gsd-*.md agent files
- Agent files linked individually to preserve non-GSD agents like linter-code-reviewer.md

## Usage

```bash
# From repository root:
link_gsd.sh

# Or with explicit path:
link_gsd.sh /path/to/get-shit-done
```

## What Gets Linked

| Source | Destination |
|--------|-------------|
| `repo/get-shit-done/` | `~/.claude/get-shit-done/` |
| `repo/commands/gsd/` | `~/.claude/commands/gsd/` |
| `repo/agents/gsd-*.md` | `~/.claude/agents/gsd-*.md` |

## Deviations from Plan

- Path changed from ~/scripts/ to ~/.local/bin/ due to permissions

## Issues Encountered

- ~/scripts/ not writable (sandbox restrictions) - resolved by using ~/.local/bin/

---
*Quick Task: 001-link-gsd-script*
*Completed: 2026-02-02*
