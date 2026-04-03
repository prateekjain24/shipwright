# TICKET-011: Landing page stack component

**Status:** pending
**Points:** 3
**Depends on:** none

## What to build
Create 4 reference docs under stacks/landing-page/ for the Build skill to read when scaffolding a landing page: setup.md, structure.md, deploy.md, gotchas.md.

Use docs/stacks.md (landing page section) as source of truth. Expand each into a full standalone guide. Language must be simple English, no em dashes (use --), no jargon.

This stack is especially important because it supports the validation path: fake door tests and waitlists.

## Acceptance criteria
- setup.md covers Astro or plain HTML + Tailwind, Stripe integration basics
- structure.md describes recommended sections: hero, problem, solution, pricing, CTA, footer
- deploy.md explains Vercel/Netlify one-click deployment, custom domain setup, Stripe webhook configuration
- gotchas.md lists common Stripe integration issues, CORS problems, and typical mistakes
- Includes guidance for waitlist and email capture flows (used in validation path)
- All 4 files are useful as standalone references (an agent reads one file in isolation)
- Language is simple and clear, no jargon or corporate filler

## Notes
