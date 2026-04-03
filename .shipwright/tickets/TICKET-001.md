# TICKET-001: Plugin scaffold

**Status:** pending
**Points:** 1
**Depends on:** none

## What to build

Create the plugin directory structure and config files. This is the foundation everything else sits on.

Deliverables:
- .claude-plugin/plugin.json with name, version, description, author, license, keywords
- .claude-plugin/mcp.json with sequential-thinking MCP server config
- Empty directory structure: skills/shipwright/, skills/shape/, skills/build/, skills/ship/, skills/brain/, agents/, stacks/nextjs/, stacks/chrome-extension/, stacks/landing-page/
- skills/shipwright/references/, skills/shape/references/, skills/build/references/, skills/ship/references/

## Acceptance criteria

- plugin.json is valid JSON with correct metadata
- mcp.json includes sequential-thinking server
- All directories exist
- No placeholder files -- just the structure

## Notes

