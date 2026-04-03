# Project State Model

Shipwright keeps state simple. Two files plus an archive folder.

```
.shipwright/
  project.json          # Machine-readable state (minimal)
  SHIPWRIGHT.md          # Human-readable everything (single source of truth)
  tickets/              # Individual ticket files for agent context
    TICKET-001.md
    TICKET-002.md
  cycles/               # Archived cycle snapshots
    cycle-1.md
    cycle-2.md
```

---

## project.json (machine state)

Minimal. Only what code needs to read programmatically.

```json
{
  "name": "habit-tracker",
  "created": "2026-04-03",
  "phase": "build",
  "cycle": 1,
  "stack": "nextjs",
  "deployed": false,
  "deployUrl": null,
  "entryPoint": "new",
  "nextCheckIn": null
}
```

| Field | Type | Purpose |
|-------|------|---------|
| `name` | string | Project slug |
| `created` | date | When the project was initialized |
| `phase` | enum | Current phase: shape, build, ship, learn |
| `cycle` | number | Current cycle number |
| `stack` | string | Selected stack (matches `stacks/` folder name) |
| `deployed` | boolean | Whether the project is live |
| `deployUrl` | string/null | Live URL |
| `entryPoint` | enum | How the builder started: new, existing-repo, existing-product |
| `nextCheckIn` | date/null | When the next re-engagement check-in is scheduled |

---

## SHIPWRIGHT.md (single source of truth)

One file that combines pitch, decisions, progress, and context. Claude reads it at the start of every session. Claude updates it after every meaningful action.

```markdown
# Habit Tracker for Parents

## Pitch
**Problem:** Parents want to build consistent routines with their kids (bedtime, chores, reading) but forget to track progress and lose motivation after a week. Current workaround: paper charts on the fridge that nobody updates.

**Time budget:** 2 weeks

**Solution sketch:**
- Screen 1: Add habits (name, frequency, emoji)
- Screen 2: Daily check-in (tap to mark done, streak counter)
- Screen 3: Weekly summary (what got done, longest streak)

**Risks:** Notification system could spiral. Decision: email-only for cycle 1.
**Not building:** Social features, gamification, mobile app.

## Validation
**Method:** Research-only (Path A -- builder has personal pain)
**Signal:** 4 Reddit threads requesting kid-friendly habit trackers. No direct competitor.
**Decision:** Proceed to Build.

## Research
**Competitors:** Habitica (gamified, not parent-focused), Streaks (iOS only, no family), Loop (open source, no kid mode)
**Gap:** None specifically target parent-child habit building
**Demand signals:** 4 Reddit threads in r/parenting (120+ upvotes combined)

## Decisions
- Stack: Next.js 16 + Vercel (2026-04-03)
- Auth: Clerk (2026-04-04)
- No mobile in cycle 1 (explicit no-go)

## Tickets

| # | Ticket | Status | Commit |
|---|--------|--------|--------|
| 1 | Set up Next.js project | done | a1b2c3d |
| 2 | Auth with Clerk | done | e4f5g6h |
| 3 | Habit CRUD | in-progress | -- |
| 4 | Daily check-in screen | pending | -- |
| 5 | Weekly summary | pending | -- |

## Progress
3/5 tickets done. On track for 2-week appetite. No blockers.

## Ship
**Deploy URL:** (pending)
**Week 1 success metric:** 10 habits created by non-me users

## Open Questions
- Should streaks reset on weekends or count continuously?
- What's the right onboarding length?

## Context for Next Session
Build phase. Ticket 3 in progress -- habit creation form works, need edit and delete. After this, ticket 4 is the next priority.
```

---

## Why One File?

- No sync issues between multiple files
- Claude reads one thing, writes one thing
- Human-readable at a glance
- Git diff shows exactly what changed
- A new session can get full context in one read

---

## Cycle Archives

When a cycle completes (Learn phase finishes), the current SHIPWRIGHT.md pitch/tickets/ship/learnings are archived to `.shipwright/cycles/cycle-N.md`. Then SHIPWRIGHT.md is updated with the new cycle's pitch.

This keeps SHIPWRIGHT.md focused on *current* state while preserving history.
