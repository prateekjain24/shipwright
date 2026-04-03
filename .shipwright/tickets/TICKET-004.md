# TICKET-004: Code agent

**Status:** pending
**Points:** 1
**Depends on:** none

## What to build

agents/code-agent.md -- runs during Build phase. Generates code in an isolated worktree for a single ticket. Stack-aware (reads stacks/{stack}/setup.md before coding).

Must follow agent format with metadata.

Must follow language rules.

## Acceptance criteria

- Runs in isolation (worktree mode)
- Reads ticket file for context (what to build, acceptance criteria)
- Reads stacks/{stack}/setup.md for stack-specific patterns
- Commits after completing the ticket
- Reports back with commit hash and summary
- Handles errors gracefully (reports blockers, doesn't silently fail)
- Language is simple, no jargon

## Notes

