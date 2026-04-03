# Shipwright

> Tell Shipwright what you want to build. It helps you think, then ships it.

Shipwright is an opinionated, open-source Claude Code plugin for PM-builders. It takes you from idea to learning -- through a structured cycle: think clearly, validate fast, build small, ship to users, learn from what happens.

## Install

```bash
/plugin marketplace add prateekjain24/shipwright
/plugin install shipwright@shipwright
```

## Usage

```bash
/shipwright "habit tracker for parents"
```

That's it. Shipwright asks you the right questions, helps you shape the idea, builds a working prototype, deploys it, and sets up a check-in so you actually learn from what happens.

## Commands

| Command | What it does |
|---------|-------------|
| `/shipwright "idea"` | Full flow: Shape, Build, Ship, Learn |
| `/shipwright import` | Onboard an existing project |
| `/shape` | Jump to Shape phase |
| `/build` | Jump to Build phase |
| `/ship` | Jump to Ship phase |
| `/learn` | Jump to Learn phase |
| `/brain` | Show project status |

## How it works

Shipwright runs a 4-phase cycle:

**Shape** -- Think clearly about what to build. Reflection questions, competitor research, validation. Output: a pitch you believe in (or a killed idea -- also a win).

**Build** -- Turn the pitch into a working prototype. Tickets, parallel code generation, mid-build check-ins.

**Ship** -- Get it live. Deploy, analytics, SEO, distribution plan. A URL you can share, not code on your laptop.

**Learn** -- Close the loop. What happened? What surprised you? What's next? Or is it time to move on?

## Docs

Everything lives in [docs/](docs/):

- [Architecture & Vision](docs/ARCHITECTURE.md) -- start here
- [Personality](docs/personality.md) -- how Shipwright talks
- [Behavioral Contract](docs/behavioral-contract.md) -- how Shipwright behaves
- [Skill Contract](docs/skill-contract.md) -- how skills are structured

## License

MIT
