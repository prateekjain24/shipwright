# TICKET-014: Marketplace config

**Status:** pending
**Points:** 1
**Depends on:** TICKET-001

## What to build

Create `.claude-plugin/marketplace.json` so Shipwright can be distributed as an installable plugin via Claude Code's marketplace system.

Users will install with:
```
/plugin marketplace add prateekjain24/shipwright
/plugin install shipwright@shipwright
```

## Marketplace.json format

Located at `.claude-plugin/marketplace.json`:

```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "shipwright",
  "description": "An opinionated plugin for PM-builders. Tell it what you want to build. It helps you think, then ships it.",
  "owner": {
    "name": "Prateek Jain"
  },
  "metadata": {
    "version": "0.1.0"
  },
  "plugins": [
    {
      "name": "shipwright",
      "source": ".",
      "description": "Tell Shipwright what you want to build. It helps you think, then ships it.",
      "version": "0.1.0",
      "author": { "name": "Prateek Jain" },
      "license": "MIT",
      "category": "productivity",
      "tags": ["product", "ship", "builder", "prototype", "deploy", "shape-up"],
      "homepage": "https://github.com/prateekjain24/shipwright",
      "repository": "https://github.com/prateekjain24/shipwright"
    }
  ]
}
```

## Key decisions

- Source type: relative path `"."` (plugin is the repo itself, not a subdirectory)
- Category: "productivity" (not "development" -- this is for PM-builders, not devs)
- Tags match plugin.json keywords for consistency
- Owner/author should match GitHub account

## Acceptance criteria

- marketplace.json is valid JSON
- Schema reference is correct
- Plugin source points to repo root
- Description matches ARCHITECTURE.md tagline
- Tags and keywords are consistent with plugin.json
- Category is "productivity"
- Homepage and repository URLs are set (can be placeholder until repo is public)

## Notes
(empty for now)
