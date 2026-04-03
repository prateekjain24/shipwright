# TICKET-009: Next.js stack component

**Status:** pending
**Points:** 3
**Depends on:** none

## What to build
Create 4 reference docs under stacks/nextjs/ for the Build skill to read when scaffolding a Next.js project: setup.md, structure.md, deploy.md, gotchas.md.

Use docs/stacks.md (Next.js section) as source of truth. Expand each into a full standalone guide. Language must be simple English, no em dashes (use --), no jargon.

## Acceptance criteria
- setup.md includes complete init sequence, common pitfalls, correct create-next-app command, App Router setup, src/ directory layout, Server Components overview
- structure.md describes recommended folder structure for a typical Shipwright project
- deploy.md covers Vercel deployment steps, environment variables, custom domain setup
- gotchas.md lists version-specific traps and common mistakes builders encounter
- All 4 files are useful as standalone references (an agent reads one file in isolation)
- Language is simple and clear, no jargon or corporate filler

## Notes
