---
phase: quick-001
plan: 01
type: execute
wave: 1
depends_on: []
files_modified:
  - ~/.local/bin/link_gsd.sh
autonomous: true
---

<objective>
Create a script at ~/scripts/link_gsd.sh that symlinks GSD files from this repository into ~/.claude for testing purposes.

Purpose: Developers can test GSD changes directly from the repository without running the installer.
Output: Executable script that creates symlinks for agents, commands, and get-shit-done directories.
</objective>

<tasks>

<task type="auto">
  <name>Task 1: Create ~/scripts directory and link_gsd.sh script</name>
  <files>~/scripts/link_gsd.sh</files>
  <action>
Create the ~/scripts directory if it doesn't exist, then create link_gsd.sh with:
- Shebang and error handling
- Variables for repo path and target path
- Backup existing directories (move to .backup suffix)
- Create symlinks for:
  - agents/gsd-*.md files (individual symlinks)
  - commands/gsd directory (directory symlink)
  - get-shit-done directory (directory symlink)
- Make script executable
  </action>
  <verify>ls -la ~/scripts/link_gsd.sh && head -30 ~/scripts/link_gsd.sh</verify>
  <done>Script created at ~/scripts/link_gsd.sh with proper symlink logic</done>
</task>

</tasks>

<success_criteria>
- ~/scripts/link_gsd.sh exists and is executable
- Script creates symlinks for all GSD components
- Script handles existing installations by backing them up
</success_criteria>

<output>
After completion, create `.planning/quick/001-link-gsd-script/001-SUMMARY.md`
</output>
