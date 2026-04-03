# Phase 4: LEARN

**Purpose:** Close the loop. Reflect on what happened. Decide what's next. This is the phase that makes Shipwright a cycle, not a one-shot tool.

**Mode:** Reflect (honest, thoughtful, forward-looking). No sugar-coating. Everything is framed as learning, not failure. See [behavioral-contract.md](behavioral-contract.md).

**Key insight:** The hardest part of side projects isn't building -- it's coming back. Learn exists to pull the builder back, give them a reason to engage, and help them decide whether to invest another cycle.

---

## The Re-Engagement Problem

Side projects die from abandonment. The builder ships, gets distracted by life, and never looks at it again. By the time they come back (if they come back), they've lost context and motivation.

Shipwright solves this with **active re-engagement** -- not passive "come back whenever."

### Re-Engagement Loop

```
Ship complete
    |
    v
Shipwright sets a check-in (7 days post-deploy)
    |
    v
Check-in fires: "Your habit tracker has been live for a week.
                  12 visits, 3 signups. Want to look at what's working?"
    |
    v
Builder engages -> Learn phase starts
    |
    v
Learn produces a cycle report + next cycle pitch
    |
    v
"Ready for cycle 2? Here's what I'd suggest building next."
```

### How Check-Ins Work

**In Cowork (preferred):** Shipwright creates a scheduled task via `mcp__scheduled-tasks`:
- 7-day post-deploy check-in (one-time)
- Optional: weekly metrics summary (recurring, if builder opts in)

**In Claude Code:** Shipwright adds to SHIPWRIGHT.md:
```markdown
## Next Check-In
**When:** 2026-04-17 (7 days post-deploy)
**What to review:** Visit count, signups, core action usage
**Prompt:** Run `/learn` to start your cycle review
```

**What a check-in message looks like:**
> "Your habit tracker has been live for 7 days. Here's what I can see:
> - Deploy URL is still up
> - You mentioned your success metric was '10 habits created by non-me users'
>
> How did it go? Want to run through a quick retrospective?"

The check-in isn't just a reminder. It re-loads context so the builder doesn't have to remember where they left off.

---

## Learn Workflow

### V1: Structured Retrospective (no integrations needed)

1. Shipwright reads SHIPWRIGHT.md to reload full project context
2. Asks reflection questions (see below)
3. Builder shares any data they have (screenshots, numbers, feedback)
4. Shipwright synthesizes into a cycle report
5. Shipwright drafts the pitch for the next cycle (or recommends killing the project)
6. Archives current cycle to `.shipwright/cycles/cycle-N.md`
7. SHIPWRIGHT.md updated with new cycle's pitch section

### V4 (future): Analytics-Powered Learn

- Pull Amplitude/Mixpanel data via MCP connectors
- Automated weekly metrics summaries
- App store review monitoring
- Anomaly alerts ("signups dropped 50% this week")

---

## Reflection Questions (Learn-Specific)

### Usage & Behavior
- "What surprised you about how people used it?"
- "Did anyone use it in a way you didn't expect?"
- "What's the one feature people keep asking for?"

### Builder Learning
- "What would you NOT build if you started over?"
- "What's the most valuable thing you learned this cycle?"
- "What assumption from Shape turned out to be wrong?"

### Go/No-Go for Next Cycle
- "Is this worth another cycle? Be honest."
- "If yes: what's the ONE thing that would make the biggest difference?"
- "If no: what did you learn that you can apply to your next project?"

### The Kill Question

Shipwright doesn't just encourage building. It also helps builders quit gracefully:

> "You set a success metric of 10 habits created. You got 2. That's useful data. Options:
> 1. Reshape: maybe the problem is real but the solution is wrong
> 2. Pivot: take what you learned and apply it to a different idea
> 3. Archive: write down what you learned and move on
>
> All three are good outcomes. Which feels right?"

---

## Cycle Report

Archived to `.shipwright/cycles/cycle-N.md`:

```markdown
# Cycle 1 Report - Habit Tracker for Parents

**Duration:** 2026-04-03 to 2026-04-17
**Time budget:** 2 weeks (used ~12 days)

## What shipped
- Web app with habit CRUD, daily check-in, weekly summary
- Deployed to Vercel
- Shared in r/parenting, DM'd 5 friends

## Metrics
- 47 visits, 8 signups, 23 habits created
- 3 users returned after day 1
- Most created habit: "Bedtime routine"

## Key learnings
- Parents want kid-facing UI (not just parent dashboard)
- Weekly summary was the most visited page
- Nobody used the emoji picker (remove it)

## What's next
Cycle 2 pitch: Add a kid-friendly check-in view (big buttons, fun animations). Cut emoji picker. Add streak notifications via email.
```

---

## Transition to Next Cycle

After Learn, SHIPWRIGHT.md is updated with a new pitch section for cycle 2. The cycle counter increments. The builder can run `/shipwright` again and the flow picks up from the new pitch.

The goal: each cycle gets tighter. Cycle 1 is exploratory. Cycle 2 is informed. Cycle 3 is refined. By cycle 4, the builder either has something real or has learned enough to move on.
