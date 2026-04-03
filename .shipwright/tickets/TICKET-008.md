# TICKET-008: Ship skill + references

**Status:** pending
**Points:** 1
**Depends on:** TICKET-002

## What to build
skills/ship/SKILL.md and reference docs. The Ship phase handles deploy, analytics instrumentation, SEO basics, and distribution planning. Also sets up the re-engagement check-in.

Must follow skill skeleton.
Must include metadata block.
Must follow language rules.

Key reference docs:
- skills/ship/references/seo-checklist.md (meta tags, OG, sitemap, robots.txt, structured data)
- skills/ship/references/analytics-setup.md (lightweight analytics -- Plausible/Umami, core event instrumentation)
- skills/ship/references/gtm-playbook.md (distribution basics: one-liner, tweet, 3-sentence pitch, first-10-users plan)

Use docs/ship.md as source of truth.

## Acceptance criteria
- SKILL.md follows skeleton exactly
- Has metadata block with trigger_keywords for ship
- Steps include: deploy using stack guide, instrument analytics, SEO setup, generate distribution copy, set up 7-day check-in
- References the re-engagement loop from docs/learn.md
- All 3 reference docs exist
- Tone is encouraging, practical (per docs/personality.md Ship section)
- Language is simple, no jargon

## Notes
