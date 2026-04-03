# TICKET-013: Learn skill (basic)

**Status:** pending
**Points:** 3
**Depends on:** TICKET-002

## What to build
Create a basic Learn phase skill at skills/learn/SKILL.md for V1.

No analytics integrations. Focus on structured retrospective questions, cycle report generation, and re-engagement check-in setup to close the loop.

Note: per v1-scope.md, Learn is listed as V2 for the full version, but this minimal version is needed in V1 to complete the cycle.

Must follow the skill skeleton from docs/skill-contract.md and include metadata block.

Use docs/learn.md as source of truth.
Language must be simple English, no em dashes (use --), no jargon.

## Acceptance criteria
- SKILL.md follows skeleton structure exactly
- Includes metadata block with trigger_keywords for learn command
- Steps include: reload context from SHIPWRIGHT.md, ask retrospective questions, synthesize cycle report, archive to cycles/, draft next cycle pitch (or recommend kill), set up check-in reminder
- Includes the critical kill question ("Is this worth another cycle?")
- Tone is honest and forward-looking, not celebratory
- Language is simple and clear, no jargon or corporate filler

## Notes
