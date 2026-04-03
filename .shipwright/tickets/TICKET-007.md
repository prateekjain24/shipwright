# TICKET-007: Build skill + references

**Status:** pending
**Points:** 1
**Depends on:** TICKET-002

## What to build
skills/build/SKILL.md and reference docs. The Build phase skill breaks the pitch into tickets, executes them with worktree-isolated code-agents, does mid-build check-ins, merges and tests.

Must follow skill skeleton from docs/skill-contract.md.
Must include metadata block.
Must follow language rules.

Key reference docs:
- skills/build/references/ticket-structure.md (how to break work into 1-SP tickets)
- skills/build/references/worktree-patterns.md (git worktree usage patterns)

Use docs/build.md as source of truth.

## Acceptance criteria
- SKILL.md follows skeleton exactly
- Has metadata block with trigger_keywords for build
- Steps include: read pitch, read stack setup, generate tickets, get approval, execute ticket loop, mid-build reflection triggers, merge, test, transition
- Mid-build check-ins at 50%, on scope creep, on time budget pressure (per docs/build.md)
- References code-agent and review-agent
- Both reference docs exist
- Tone is focused, minimal chatter (per docs/personality.md Build section)
- Language is simple, no jargon

## Notes
