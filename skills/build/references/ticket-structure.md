# Ticket Structure

This doc explains how to break work into 1-story-point tickets and the file format agents read before building.

---

## What Makes a Good Ticket

A good ticket is:

- **Atomic:** Can be built, tested, committed, and reviewed independently. No hidden dependencies (dependencies are explicit in the ticket file).
- **1 story point:** A strong developer finishes it in 2-4 hours. If it needs more, split it.
- **Clear acceptance criteria:** An agent can finish and know it's done. No ambiguity.
- **Explicit dependencies:** Lists other tickets it depends on, so the orchestrator can order them right.

Bad tickets are too big (5+ points), too vague ("improve UI"), or hidden dependencies (ticket A doesn't mention it needs ticket B).

---

## Ticket File Format

Each ticket lives in `.shipwright/tickets/TICKET-NNN.md`. Here's the format from build.md:

```markdown
# TICKET-003: Habit CRUD

**Status:** pending
**Points:** 1
**Depends on:** TICKET-001

## What to build

Create/read/update/delete habits. Each habit has: name, emoji, frequency (daily/weekly), created date.

## Acceptance criteria

- Create habit with name, emoji, frequency
- List all habits on main screen
- Edit habit name/emoji/frequency
- Delete habit with confirmation
- Data persists in database

## Notes

[Agent fills in during execution]
```

Fields:

- **Status:** pending, in-progress, done
- **Points:** Always 1. If you think it's more, split the ticket.
- **Depends on:** Comma-separated list of TICKET-NNNs this ticket can't start until they're done. Leave blank if none.
- **What to build:** Free-form description of the feature or change. 2-3 sentences. Answer: what does the user see or experience?
- **Acceptance criteria:** Bulleted checklist. Each item is one thing an agent can code and test. 3-5 items usually.
- **Notes:** Agents add context during execution (blockers, decisions, what they actually built vs what was asked).

---

## Dependency Ordering

When you have a list of tickets, order them:

1. **No dependencies first:** Tickets with no "Depends on" can start immediately and run in parallel.
2. **Chain dependencies:** If TICKET-002 depends on TICKET-001, and TICKET-003 depends on TICKET-002, they must run in order: 001 -> 002 -> 003.
3. **Fan-out:** If multiple tickets depend on one (e.g., TICKET-002, TICKET-003, TICKET-004 all depend on TICKET-001), they can all run in parallel after TICKET-001 finishes.
4. **Parallelization:** Independent chains can run at the same time. E.g., if you have [001 -> 002] and [003 -> 004], you can spawn 001 and 003 in parallel worktrees.

Visualize the dependency graph before assigning to agents.

---

## Estimation Rules

When breaking work into tickets:

| Situation | Rule |
|-----------|------|
| Task feels like 3+ points | Split it. Find the natural sub-steps. |
| Unclear what "done" looks like | Too ambitious. Narrow the scope or add acceptance criteria. |
| Task needs a design decision | Make the decision upfront (in ticket notes), or cut it and defer to cycle 2. |
| Estimate is "maybe 2 hours, maybe 6" | Split it or add more detail to acceptance criteria. |
| Ticket depends on 3+ other tickets | You might be breaking work too granularly. Combine some. |
| Ticket says "refactor X" | Too vague. Specify what refactoring is needed and why. If it's not in service of a feature, cut it for cycle 2. |

---

## When to Split vs Combine

**Split if:**

- Ticket is 3+ points (too big to finish in one cycle)
- Ticket has sub-steps that could be independent features (e.g., "user auth" -> "signup", "login", "password reset")
- A ticket's acceptance criteria list has 8+ items (break into smaller tickets)
- Ticket depends on a blocking decision (move the decision to a separate, quick ticket)

**Combine if:**

- Two tickets are so tightly coupled that building them in isolation creates rework (rare, but happens)
- One ticket is truly a sub-step of another (e.g., "deploy to production" is part of the "CI/CD setup" ticket, not separate)

**The principle:** A ticket should be completable and mergeable on its own. If you have to wait for another ticket before you can test yours, it's a dependency (explicitly list it). If you can't merge yours without the other one, it should be a single ticket.

---

## Ticket Naming Convention

Use `TICKET-NNN` where NNN is zero-padded (TICKET-001, TICKET-002, etc.). Start from 001 for each build phase.

Reasons:

- Zero-padding makes sorting predictable
- Short ID makes commit messages and file names clean
- Numbering is sequential (easier for agents to trace which tickets are done)

---

## Example: Breaking a Habit Tracker into Tickets

Pitch: "Build a habit tracker. Users create habits (e.g., 'exercise'), mark daily completion, see a calendar view of their streaks."

Naive: One big ticket. Bad.

Better breakdown:

```
TICKET-001: Database schema for habits
  - Create Habit table: id, name, emoji, frequency, createdAt
  - Depends on: none
  - Points: 1

TICKET-002: Database schema for completions
  - Create Completion table: id, habitId, date, completed
  - Depends on: TICKET-001 (need habit.id foreign key)
  - Points: 1

TICKET-003: API: Create/list/edit/delete habits
  - Endpoints: POST /habits, GET /habits, PUT /habits/:id, DELETE /habits/:id
  - Depends on: TICKET-001
  - Points: 1

TICKET-004: API: Mark habit complete
  - Endpoint: POST /habits/:id/mark (toggles today's completion)
  - Depends on: TICKET-002, TICKET-003 (need habits API first)
  - Points: 1

TICKET-005: Frontend: Home screen layout
  - Habit list view, add habit button, search
  - No backend calls yet (stub data)
  - Depends on: none
  - Points: 1

TICKET-006: Frontend: Create habit form
  - Modal/form to add a new habit (name, emoji, frequency picker)
  - Depends on: TICKET-005 (integrate with home screen)
  - Points: 1

TICKET-007: Frontend: Connect create habit to API
  - Form submits to POST /habits, shows response
  - Depends on: TICKET-003, TICKET-006
  - Points: 1

TICKET-008: Frontend: Daily completion toggle
  - Click habit to mark done/undone, call API
  - Depends on: TICKET-004, TICKET-007
  - Points: 1

TICKET-009: Frontend: Streak calendar
  - Show calendar grid, highlight completion dates
  - Depends on: TICKET-008 (need completion data first)
  - Points: 1
```

Order: 001, 002, 003, 004 can happen in phases:
- Phase 1 (parallel): 001, 005
- Phase 2 (parallel): 002 (after 001), 003 (after 001), 006 (after 005)
- Phase 3 (parallel): 004 (after 002 and 003), 007 (after 003 and 006)
- Phase 4: 008 (after 004 and 007)
- Phase 5: 009 (after 008)

This is realistic: 9 tickets, each 1 point, ~2-3 days of work total. Parallelizable where possible. Clear dependencies.
