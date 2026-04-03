# Shipwright - Architecture & Vision (v3)

> Tell Shipwright what you want to build. It helps you think, then ships it.

Shipwright is an opinionated, open-source Claude Code plugin for PM-builders. It takes you from idea to learning -- through a structured cycle: think clearly, validate fast, build small, ship to users, learn from what happens.

---

## Philosophy

**1. Reflection before action.** Shipwright's most important job is asking the right questions -- not once at the start, but continuously. The best products come from clear thinking, not fast typing. See [reflection.md](reflection.md).

**2. Learning is the product, not code.** Code is a means. The real output is validated learning: did anyone want this? What surprised you? What's next? Every cycle ends with a decision, not a deploy.

**3. Ship, don't document.** Existing PM plugins generate artifacts (PRDs, roadmaps, competitive briefs). Shipwright generates working software. Documents exist only to serve the build.

**4. One way, not ten frameworks.** Most PM plugins offer a buffet of frameworks (RICE, MoSCoW, ICE, Kano...). Shipwright has one opinionated path: Shape, Build, Ship, Learn. The methodology is the engine; plain English is the interface.

**5. Stateful, not stateless.** Every other plugin forgets you between sessions. Shipwright treats your project as the central thing. It knows where you are, what you decided, and what's next -- and it pulls you back when you drift.

**6. Small batches, tight loops.** Set a time budget. Shape just enough. Build a working prototype. Ship it. Learn from real usage. Repeat.

**7. Builder-first.** Built for people who ship side projects, micro-SaaS, and personal tools. No Jira. No sprint ceremonies. No stakeholder alignment decks.

**8. Meet builders where they are.** Not everyone starts from zero. Shipwright works whether you have a vague idea, a half-built repo, or a live product that's stalling. See [onboarding.md](onboarding.md).

**9. Mentor, not assistant.** Shipwright has a personality: an informal mentor who pushes back, asks hard questions, and once in a while says something that makes you stop and think. See [personality.md](personality.md).

---

## The Shipwright Cycle

```
    SHAPE                BUILD                SHIP                 LEARN
    -----                -----                ----                 -----
    Think clearly        Break into tickets   Deploy               Reflect on data
    Validate demand      Generate code        Instrument           Collect feedback
    Sketch or test       Parallel agents      Distribute           Decide what's next
    Scope tightly        Frequent commits     Launch basics        Pull builder back

    Output:              Output:              Output:              Output:
    SHIPWRIGHT.md        Working prototype    Live product         Cycle report
    (pitch section)      Passing tests        With analytics       Next cycle pitch
```

Reflection runs throughout -- not just at phase boundaries. See [reflection.md](reflection.md).

### Modes

Shipwright operates in four modes. Commands explicitly select a mode. Natural language implicitly routes to one. See [behavioral-contract.md](behavioral-contract.md) for routing rules.

| Mode | Tone | Explicit triggers | Implicit signals |
|------|---------|------------------|-----------------|
| Interview | Curious, probing | `/shape`, `/shipwright "idea"` | "I want to build...", vague concepts |
| Assess | Diagnostic | `/shipwright import`, `/learn` on existing product | "I have a repo...", "My product isn't growing" |
| Execute | Focused, efficient | `/build`, `/ship` | "Let's build it", "Ship it" |
| Reflect | Honest, forward-looking | `/learn` | "How did it go?", "What's next?" |

### Commands

| Command | What it does |
|---------|-------------|
| `/shipwright "idea"` | Full flow: Shape -> Build -> Ship -> Learn |
| `/shipwright import` | Onboard an existing project. See [onboarding.md](onboarding.md) |
| `/shape` | Jump to Shape phase (or re-shape current project) |
| `/build` | Jump to Build phase (warns if no pitch exists) |
| `/ship` | Jump to Ship phase (warns if build incomplete) |
| `/learn` | Jump to Learn phase (works for existing products too) |
| `/brain` | Update project context, show status, sync memory |

---

## Document Index

This architecture is split into focused files. Each file is self-contained -- an agent operating in one phase only needs to read that phase's file plus this index.

### Core

| File | What it covers | When to read |
|------|---------------|--------------|
| [personality.md](personality.md) | Shipwright's voice: mentor tone, when to go deep | Always -- this defines how Shipwright talks |
| [behavioral-contract.md](behavioral-contract.md) | Operating rules, mode routing, sequential thinking | Always -- this defines how Shipwright behaves |
| [skill-contract.md](skill-contract.md) | SKILL.md skeleton, hybrid workflow format | When writing or editing any skill |
| [reflection.md](reflection.md) | Continuous reflection design, question principles | Every phase -- this is the differentiator |
| [state-model.md](state-model.md) | project.json + SHIPWRIGHT.md spec | When reading/writing project state |
| [onboarding.md](onboarding.md) | Entry points: new idea, existing repo, live product | When a builder starts for the first time |

### Phases

| File | What it covers | When to read |
|------|---------------|--------------|
| [shape.md](shape.md) | Shape phase: reflection, validation, demand testing | During `/shape` or start of `/shipwright` |
| [build.md](build.md) | Build phase: tickets, worktrees, code generation | During `/build` |
| [ship.md](ship.md) | Ship phase: deploy, analytics, SEO, distribution | During `/ship` |
| [learn.md](learn.md) | Learn phase: retrospective, re-engagement, next cycle | During `/learn` or between sessions |

### Reference

| File | What it covers | When to read |
|------|---------------|--------------|
| [stacks.md](stacks.md) | Stack selection + component structure | During Shape (selection) and Build (setup) |
| [v1-scope.md](v1-scope.md) | V1 scope, success criteria, roadmap | Planning and prioritization |
| [primitives-map.md](primitives-map.md) | How plugin primitives map to phases | Plugin development |

---

## References

- [Shape Up by Basecamp](https://basecamp.com/shapeup) -- methodology backbone
- [Claude Code Plugins Docs](https://code.claude.com/docs/en/plugins) -- plugin architecture
- [Git Worktrees in Claude Code](https://code.claude.com/docs/en/common-workflows) -- worktree patterns
