# TICKET-002: Brain skill (state management)

**Status:** pending
**Points:** 1
**Depends on:** TICKET-001

## What to build

skills/brain/SKILL.md -- the state management skill. Handles /brain command. Initializes .shipwright/ directory, creates project.json and SHIPWRIGHT.md, shows status.

Must follow the skill skeleton from docs/skill-contract.md: Identity, Intent, Inputs, Steps, Pause Points, Outputs, Constraints.

Must include metadata block at top (name, trigger_keywords, mode, phase).

Must follow language rules from docs/personality.md: simple English, no em dashes (use --), no jargon, cite filenames.

## Acceptance criteria

- Follows skill skeleton exactly (all 7 sections)
- Has metadata block
- Steps section uses hybrid workflow format (numbered steps, explicit state reads/writes)
- Can initialize a new project (create .shipwright/, project.json, SHIPWRIGHT.md)
- Can show current status (phase, tickets, progress)
- Can sync memory across sessions
- Language is simple, no jargon

## Notes

