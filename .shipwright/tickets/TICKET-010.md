# TICKET-010: Chrome extension stack component

**Status:** pending
**Points:** 3
**Depends on:** none

## What to build
Create 4 reference docs under stacks/chrome-extension/ for the Build skill to read when scaffolding a Chrome extension: setup.md, structure.md, deploy.md, gotchas.md.

Use docs/stacks.md (Chrome extension section) as source of truth. Expand each into a full standalone guide. Language must be simple English, no em dashes (use --), no jargon.

## Acceptance criteria
- setup.md covers Manifest v3, project structure, TypeScript + Vite/Plasmo setup, required permissions
- structure.md describes recommended folder structure including popup, background scripts, and content scripts
- deploy.md explains build process, zip creation, Chrome Web Store submission steps, and screenshot requirements
- gotchas.md lists CSP issues, service worker limitations, and typical review timeline delays
- All 4 files are useful as standalone references (an agent reads one file in isolation)
- Language is simple and clear, no jargon or corporate filler

## Notes
