# Shipwright

> Idea to live URL in 60 minutes. Not idea to code. Idea to users.

Shipwright is an open-source Claude Code plugin for builders who ship. It takes your idea through four phases -- think, build, deploy, learn -- and brings you back when it's time to decide what's next.

No PRDs. No frameworks. No alignment decks. Just a working product with real users.

## Who is this for?

People who build side projects, micro-SaaS, and personal tools on evenings and weekends. You have ideas. You want to test them fast. You don't want to spend 3 hours on a PRD for something that might not matter.

Shipwright is not a coding assistant. It's a thinking + shipping partner.

## What happens when you run it

```
/shipwright "habit tracker for parents"
```

**Shape** (10 min) -- Shipwright asks you hard questions. Not a form. A conversation. It runs competitor research in the background. You end up with a sharp pitch or a killed idea. Both are wins.

**Build** (30 min) -- Your pitch turns into tickets. Code agents work in parallel using git worktrees. Mid-build check-ins catch scope creep before it catches you.

**Ship** (15 min) -- Deploy to a live URL. Analytics wired up. SEO basics done. A plan to get your first 10 users.

**Learn** (5 min + 7 days later) -- Shipwright schedules a check-in. When you come back, it asks: what happened? What surprised you? Reshape, pivot, or archive. Then the cycle restarts.

## Install

```bash
claude plugin add prateekjain24/shipwright
```

## Commands

| Command | What it does |
|---------|-------------|
| `/shipwright "idea"` | Full cycle: Shape, Build, Ship, Learn |
| `/shipwright import` | Bring in an existing repo or product |
| `/shape` | Rethink what you're building |
| `/build` | Start (or resume) building |
| `/ship` | Deploy and distribute |
| `/learn` | Reflect on what happened |
| `/brain` | Show project status |

## Stacks (V1)

Shipwright picks the right tech based on what you're building.

| Stack | Best for |
|-------|----------|
| Next.js | Web apps, SaaS, anything with a backend |
| Landing Page | Waitlist tests, fake door validation, simple marketing sites |
| Chrome Extension | Browser tools, productivity add-ons |

Each stack includes setup instructions, folder structure, deploy commands, and gotchas. Shipwright's code agents read these so you don't have to.

## How it's different

**From coding assistants** (Copilot, Cursor, Claude Code alone) -- These help you write code. Shipwright helps you decide what code is worth writing. Then writes it. Then ships it. Then brings you back to learn from it.

**From PM tools** (the ones that generate PRDs and roadmaps) -- Those produce documents. Shipwright produces working software. Documents exist only to serve the build.

**From boilerplate generators** (create-next-app, Yeoman) -- These give you scaffolding. Shipwright gives you a live URL with analytics and a plan to get users.

## Architecture

Shipwright is built as a Claude Code plugin with skills, agents, and stack components.

```
shipwright/
  skills/        -- Phase logic (shape, build, ship, learn, brain)
  agents/        -- Subagents (research, code gen, code review)
  stacks/        -- Tech-specific instructions (nextjs, landing-page, chrome-extension)
  docs/          -- Architecture decisions and contracts
```

Deep dive: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)

## Design principles

**Reflection before action.** Shipwright's most important job is asking the right questions. Not once at the start, but continuously throughout the build.

**Small batches, tight loops.** Set a time budget. Shape just enough. Build a working prototype. Ship it. Learn from real usage. Repeat.

**Stateful, not stateless.** Shipwright knows where you are, what you decided, and what's next. It pulls you back when you drift.

**One path, plain English.** No framework buffet. No jargon. Shape, Build, Ship, Learn. That's it.

## Roadmap

| Version | Milestone |
|---------|-----------|
| **V1** | Idea to live URL in 60 minutes (3 stacks, full Shape/Build/Ship) |
| **V2** | Full Learn cycle + 3 more stacks + hooks |
| **V3** | Analytics-powered learning with MCP connectors |
| **V4** | Multi-project support + community stacks |

## Contributing

This is early. If you ship things and want to help, open an issue or PR. Especially interested in new stack components and real-world usage reports.

## License

MIT
