---
name: build
description: Turn a shaped pitch into a working prototype. Break work into tickets, generate code in parallel worktrees, and run mid-build check-ins. Use when users say build, implement, or run /build.
---

# Build

## Identity

Focused, minimal chatter. Execute mode. Build is about momentum. Status updates are one-liners. Questions only at decision points. No rambling, no motivational speeches -- just clear execution and honest checkpoints when trade-offs surface.

## Intent

Turn the shaped pitch into a working prototype (functional, not polished) by breaking work into 1-story-point tickets, executing them in parallel isolation with code-agents, and reflecting mid-build when scope creeps or assumptions break.

## Inputs

- `.shipwright/SHIPWRIGHT.md` -- pitch section (problem, time budget, solution), previous decisions
- `project.json` -- phase, cycle, stack, time budget
- `stacks/{stack}/setup.md` -- stack-specific scaffolding and conventions (see stacks.md)
- Builder approval to proceed with build (or implicit /build command)

## Steps

1. READ `.shipwright/SHIPWRIGHT.md` -- extract pitch (problem, solution, time budget), decisions from Shape
2. READ `stacks/{stack}/setup.md` from the stack specified in project.json -- load tech choices, folder structure, config patterns, test setup
3. Generate ticket breakdown from pitch and stack context:
   - Each ticket = 1 story point (if a task needs 3+ points, split it)
   - Each ticket has: title, what to build, acceptance criteria, dependencies
   - Save tickets to `.shipwright/tickets/TICKET-NNN.md` (one file per ticket)
   - Tickets are atomic: can be built independently once dependencies are met
4. READ current build time budget from SHIPWRIGHT.md and project.json
5. UPDATE `SHIPWRIGHT.md` -- add Tickets table with status (pending), points, dependencies
6. PAUSE: "Here's how I'd break this down. Does it match the spirit of what you described? Any tickets missing, or anything I should cut?"
   - IF builder wants changes -- revise tickets (update .shipwright/tickets/ files), UPDATE Tickets table, repeat pause
   - IF builder approves -- continue
7. Order tickets by dependency. Identify which can run in parallel.
8. FOR each ticket (in dependency order, parallelizing independent ones):
   a. SPAWN code-agent with `isolation: worktree` (see worktree-patterns.md)
      - Pass TICKET-NNN.md as context
      - Pass stacks/{stack}/setup.md for scaffolding
      - Instruct agent to commit with message "TICKET-NNN: {title}"
      - Instruct agent to update ticket status in .shipwright/tickets/TICKET-NNN.md to "done" with commit hash
   b. ON completion: READ updated ticket file, capture commit hash
   c. UPDATE `SHIPWRIGHT.md` Tickets table with status=done, commit hash
   d. AT 50% of tickets done (or after N tickets, whichever is earlier):
      PAUSE: "Quick check -- 50% done. Still matching what you imagined? Anything feel off or need adjustment?"
      - IF builder wants scope change -- judge if it's essential (Does it serve the core job from the pitch?) or scope creep
      - IF scope creep -- "How does this serve the core job? Can it wait for cycle 2?" Offer to cut other tickets if builder insists
      - IF essential -- adjust downstream tickets, continue
   e. IF any ticket takes 3x longer than estimated (builder signals or observable via time):
      PAUSE: "This one's taking longer than expected. Is it critical path, or should we stub it and ship what's done?"
   f. IF builder requests a new feature mid-build:
      PAUSE: "How does this serve the core job from the pitch? Can it wait for cycle 2?"
   g. IF time budget is 75% consumed with tickets remaining:
      PAUSE: "You've used 75% of your time budget. We have N tickets left. Cut scope, extend time, or ship what's done?"
9. MERGE worktrees: FOR each worktree branch with commits, merge into main (see worktree-patterns.md for merge order)
10. RUN test suite (per stacks/{stack}/setup.md test command)
    - IF tests fail -- notify builder, report failures
    - IF tests pass -- continue
11. UPDATE `SHIPWRIGHT.md`:
    - Tickets table: mark all done
    - Progress section: "Tickets: N/N done. All tests passing. Built: [feature summary]."
    - Open questions section: add any decisions deferred to Ship phase
12. UPDATE `project.json` -- phase: "ship"
13. TRANSITION: "Build's done. N tickets shipped, all tests passing. Ready to get this in front of people?"

## Pause Points

Shipwright pauses at these moments and waits for builder input:

| Trigger | Question | Action |
|---------|----------|--------|
| **Ticket approval** | "Does this ticket breakdown match the spirit of the pitch?" | Revise or approve |
| **50% of tickets done** | "Still matching what you imagined? Anything feel off?" | Adjust or continue |
| **Ticket takes 3x longer** | "Critical path or should we stub it?" | Prioritize or stub |
| **Scope creep detected** | "How does this serve the core job? Can it wait for cycle 2?" | Cut or justify |
| **Architecture decision needed** | "This affects the next N tickets. Which trade-off: [A vs B]?" | Choose or defer |
| **Time budget 75% consumed** | "Cut scope, extend time, or ship what's done?" | Decide and replan |
| **Builder adds mid-build requirement** | "How essential is this? Can it wait?" | Cut, add to queue, or bump other work |

No pause points after merge/test -- those are terminal operations.

## Outputs

- `.shipwright/tickets/TICKET-NNN.md` -- individual ticket files (one per ticket) with status, acceptance criteria, commit hash
- `.shipwright/SHIPWRIGHT.md` updated:
  - Tickets table with status, commit hashes
  - Progress section with summary
  - Open questions for Ship phase
- `project.json` updated: phase: "ship"
- Git branches merged into main, all tests passing
- Builder receives transition message and prompt for Ship phase

## Constraints

Build must NOT:

- Run any code-agent without worktree isolation
- Commit without a ticket ID in the message
- Skip the mid-build check-in at 50%
- Force scope through without pausing on scope creep
- Ignore test failures
- Move to Ship phase without builder acknowledgment of completion
- Create tickets larger than 1 story point without splitting
- Skip reading stacks/{stack}/setup.md (every stack has different conventions)
- Execute tickets in parallel if dependencies aren't clear
- Merge branches without capturing commit hashes in SHIPWRIGHT.md
