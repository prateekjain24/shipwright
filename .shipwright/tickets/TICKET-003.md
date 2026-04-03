# TICKET-003: Research agent

**Status:** pending
**Points:** 1
**Depends on:** none

## What to build

agents/research-agent.md -- runs during Shape phase to scan competitors, app store reviews, Reddit/forum threads, and market signals.

Must follow agent format with metadata (name, description, model hint).

Must follow language rules: simple English, no em dashes, no jargon.

## Acceptance criteria

- Scans for competitors (top 5 in the space)
- Checks app store reviews for the category
- Searches Reddit, HN, Twitter for signs of demand
- Produces a research brief (competitors, gap, signs of demand, app store complaints)
- Brief format matches the example in docs/shape.md
- Uses web search tools
- Language is simple, no jargon

## Notes

