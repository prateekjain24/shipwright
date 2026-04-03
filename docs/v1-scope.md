# V1 Scope & Roadmap

---

## V1 Story

**Old story (v2):** "Idea to scaffolded code in 15 minutes."

**New story (v3):** "Idea to live URL with your first users in 60 minutes."

The difference matters. Scaffolded code competes with `create-next-app`. A live URL with users competes with nothing -- no tool does this end-to-end today.

---

## V1 Success Criteria

A user installs Shipwright, types `/shipwright "habit tracker for parents"`, and within 60 minutes:
1. Has answered reflection questions that sharpened their thinking
2. Has seen competitor research and signs of demand
3. Has a shaped pitch they believe in (or has killed the idea -- also a win)
4. Has a working prototype with core functionality
5. Has a live URL they can share
6. Has basic analytics instrumented
7. Has a first-10-users distribution plan
8. Has a 7-day check-in scheduled

That's the full job: idea to learning. Not idea to code.

---

## What V1 Includes

### Skills

| Skill | What it does |
|-------|-------------|
| `skills/shipwright/SKILL.md` | Primary orchestrator. Handles `/shipwright "idea"` with phase transitions. |
| `skills/shape/SKILL.md` | Shape phase: reflection, research, validation paths, pitch generation |
| `skills/build/SKILL.md` | Build phase: ticket breakdown, worktree-isolated code gen, mid-build reflection |
| `skills/ship/SKILL.md` | Ship phase: deploy, basic analytics, SEO, distribution plan |
| `skills/brain/SKILL.md` | Project state: init `.shipwright/`, maintain SHIPWRIGHT.md, `/brain` status |

### Agents

| Agent | What it does |
|-------|-------------|
| `agents/research-agent.md` | Competitor scan, app store reviews, signs of demand (runs during Shape) |
| `agents/code-agent.md` | Worktree-isolated code generation with stack awareness (runs during Build) |
| `agents/review-agent.md` | Code review + quality checks before merge (runs during Build) |

### Stacks (V1)

| Stack | Why in V1 |
|-------|----------|
| `stacks/nextjs/` | Most common choice for web apps/SaaS |
| `stacks/landing-page/` | Needed for validation path (fake door, waitlist) |
| `stacks/chrome-extension/` | Popular side project type, different enough to prove stack-agnostic design |

### Onboarding

| Entry point | V1 support |
|-------------|-----------|
| New idea (`/shipwright "idea"`) | Full support |
| Existing repo (`/shipwright import`) | Basic support (scan + context rebuild) |
| Existing product (`/learn`) | Basic support (structured retrospective) |

### Reference Docs

- `skills/shipwright/references/reflection-guide.md`
- `skills/shipwright/references/cycle-overview.md`
- `skills/shape/references/shape-principles.md`
- `skills/shape/references/validation-checklist.md`
- `skills/shape/references/pitch-template.md`
- `skills/build/references/ticket-structure.md`
- `skills/build/references/worktree-patterns.md`
- `skills/ship/references/seo-checklist.md`
- `skills/ship/references/analytics-setup.md`
- `skills/ship/references/gtm-playbook.md`

---

## What V1 Does NOT Include

| Deferred | Why | Target version |
|----------|-----|----------------|
| Learn phase skill | V1 focuses on getting to a URL. Learn is manual for now (structured questions in `/learn`). | V2 |
| Telegram bot, CLI tool, Slack app stacks | Validate core with 3 stacks first | V2 |
| Analytics MCP integrations (Amplitude, Mixpanel) | Requires MCP connectors not yet widely available | V3 |
| Scheduled metrics pulls | Depends on analytics integrations | V3 |
| Hooks (pre-build, pre-ship) | Nice-to-have, not critical path | V2 |
| Review agent (advanced) | Basic code review is V1; advanced quality gates are V2 | V2 |

---

## Roadmap

| Version | What ships | Key milestone | User story |
|---------|-----------|---------------|------------|
| **V1** | Shape + Build + Ship (deploy) + 3 stacks + onboarding | "Idea to live URL in 60 minutes" | Builder goes from idea to deployed product with users |
| **V2** | Learn (full) + 3 more stacks + hooks | "Full cycle with real user data" | Builder completes a cycle and starts cycle 2 informed by data |
| **V3** | Analytics-powered Learn + MCP connectors | "Automated learning" | Builder gets weekly insights without running `/learn` manually |
| **V4** | Multi-project + template library + community stacks | "Portfolio builder" | Builder manages multiple projects, shares stacks |

---

## Open Questions

1. **Multi-project:** Can Shipwright manage multiple projects? Or one per workspace? (Deferred to V4)
2. **Team mode:** Should it support multiple builders on the same project? (Probably not -- builder-first means solo-first)
3. **Template library:** Should V1 ship with pre-shaped example pitches? (Maybe 2-3 as onboarding aids)
4. **Plugin marketplace:** Should individual stacks be installable as add-ons? (Deferred to V4)
5. **Monetization:** Free core, paid stacks or analytics integrations? (Decide after V2 based on usage)

---

## Plugin Anatomy (Directory Structure)

```
shipwright/
  .claude-plugin/
    plugin.json
  skills/
    shipwright/
      SKILL.md
      references/
        reflection-guide.md
        cycle-overview.md
    shape/
      SKILL.md
      references/
        shape-principles.md
        validation-checklist.md
        pitch-template.md
    build/
      SKILL.md
      references/
        ticket-structure.md
        worktree-patterns.md
    ship/
      SKILL.md
      references/
        seo-checklist.md
        analytics-setup.md
        gtm-playbook.md
    brain/
      SKILL.md
  stacks/
    nextjs/
      setup.md
      structure.md
      deploy.md
      gotchas.md
    chrome-extension/
      setup.md
      structure.md
      deploy.md
      gotchas.md
    landing-page/
      setup.md
      structure.md
      deploy.md
      gotchas.md
  agents/
    research-agent.md
    code-agent.md
    review-agent.md
  docs/
    (architecture docs -- this folder)
  README.md
  ARCHITECTURE.md
```

### plugin.json

```json
{
  "name": "shipwright",
  "version": "0.1.0",
  "description": "Tell Shipwright what you want to build. It helps you think, then ships it.",
  "author": {
    "name": "Prateek Jain"
  },
  "license": "MIT",
  "keywords": ["product", "ship", "builder", "prototype", "deploy", "shape-up"]
}
```
