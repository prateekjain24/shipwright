# TICKET-012: Orchestrator skill + references

**Status:** pending
**Points:** 5
**Depends on:** TICKET-002, TICKET-006, TICKET-007, TICKET-008

## What to build
Create the main Shipwright orchestrator skill at skills/shipwright/SKILL.md and two reference docs.

This is the entry point for /shipwright "idea" and /shipwright import. It routes to the correct phase, manages transitions, and holds the full cycle together.

Must follow the skill skeleton from docs/skill-contract.md and include metadata block.

Key reference docs to create:
- skills/shipwright/references/reflection-guide.md (question design principles, condensed from docs/reflection.md)
- skills/shipwright/references/cycle-overview.md (how the 4 phases connect, condensed from ARCHITECTURE.md)

Use docs/behavioral-contract.md for mode routing rules (interview, assess, execute, reflect).
Use docs/onboarding.md for the 3 entry points.
Language must be simple English, no em dashes (use --), no jargon. Cite filenames in documentation.

## Acceptance criteria
- SKILL.md follows skeleton structure exactly
- Includes metadata block with trigger_keywords for main command
- Steps include: detect mode (implicit or explicit), route to correct phase skill, manage transitions between phases
- Handles both /shipwright "idea" (full flow) and /shipwright import (existing project) entry points
- References all 4 phase skills
- Both reference docs exist and are coherent standalone documents
- Personality and mentor voice shine through (see docs/personality.md)
- Language is simple and clear, no jargon, filenames cited where relevant

## Notes
