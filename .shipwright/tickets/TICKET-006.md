# TICKET-006: Shape skill + references

**Status:** pending
**Points:** 1
**Depends on:** TICKET-002

## What to build
skills/shape/SKILL.md and reference docs under skills/shape/references/. The Shape phase skill handles reflection questions, parallel research dispatch, stack selection, validation paths (direct-to-build vs validate-first), and pitch generation.

Must follow the skill skeleton from docs/skill-contract.md: Identity, Intent, Inputs, Steps, Pause Points, Outputs, Constraints.
Must include metadata block at top.
Must follow language rules: simple English, no em dashes (use --), no jargon, cite filenames.

Key reference docs to create:
- skills/shape/references/shape-principles.md (core shaping concepts in plain English)
- skills/shape/references/validation-checklist.md (quick validation methods -- landing page, fake door, concierge, research-only)
- skills/shape/references/pitch-template.md (SHIPWRIGHT.md pitch section format)

Use docs/shape.md as the source of truth for content. The SKILL.md should be the executable hybrid workflow version; references are supporting material.

## Acceptance criteria
- SKILL.md follows skeleton exactly (all 7 sections)
- Has metadata block with trigger_keywords for shape
- Steps section uses hybrid workflow format
- Two paths out of Shape: direct-to-build and validate-first (per docs/shape.md)
- References research-agent for parallel competitor scan
- All 3 reference docs exist and are useful
- Tone is curious, probing (per docs/personality.md Shape section)
- Language is simple, no jargon, cites filenames

## Notes
