# TICKET-005: Review agent

**Status:** pending
**Points:** 1
**Depends on:** none

## What to build
agents/review-agent.md -- runs during Build phase after code-agent completes a ticket. Reviews code quality, checks it matches acceptance criteria from the ticket, flags issues.

Must follow language rules from docs/personality.md: simple English, no em dashes (use --), no jargon.

## Acceptance criteria
- Reads the ticket file to understand what was supposed to be built
- Reviews the code changes (diff) from the code-agent
- Checks against acceptance criteria
- Flags issues: missing criteria, obvious bugs, style problems
- Reports pass/fail with specific feedback
- Language is simple, no jargon

## Notes
