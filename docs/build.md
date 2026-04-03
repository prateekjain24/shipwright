# Phase 2: BUILD

**Purpose:** Turn the shaped pitch into a working prototype. Functional enough to test with real users. Not polished -- functional.

**Mode:** Execute (focused, efficient, minimal chatter). Build is about momentum. Status updates are one-liners. Pauses happen only at decision points. See [behavioral-contract.md](behavioral-contract.md) for mode details.

---

## Core Principles

- Break work into 1-story-point tickets before writing any code
- Use git worktrees for isolation -- each agent gets its own worktree
- Commit after every ticket completion
- Read the appropriate `stacks/{stack}/setup.md` before scaffolding
- **Reflection is continuous during Build** -- don't just execute tickets blindly (see [reflection.md](reflection.md))

---

## Workflow

1. User approves the pitch (or runs `/build`)
2. Claude reads SHIPWRIGHT.md pitch section
3. Claude reads `stacks/{stack}/setup.md` (see [stacks.md](stacks.md))
4. Claude breaks scope into 1-SP tickets, saves to `.shipwright/tickets/`
5. Shows ticket breakdown in SHIPWRIGHT.md, asks builder to review
6. **Reflection pause:** "Does this match the spirit of the pitch? Anything missing?"
7. Executes tickets in dependency order, parallelizing independent ones:
   - Each code-agent runs in an isolated worktree (`isolation: worktree`)
   - Commits after each ticket completion
8. **Mid-build check-ins** (see below)
9. After all tickets: merge worktree branches, run tests
10. Updates SHIPWRIGHT.md with progress and commit hashes
11. Transition: "Build complete. Ready to ship?"

---

## Ticket Structure

Tickets are rows in SHIPWRIGHT.md's Tickets table, plus individual markdown files for agent context:

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

---

## Mid-Build Reflection

This is where most AI build tools fall short. They execute a plan without checking if the plan still makes sense. Shipwright pauses at key moments:

### Automatic Triggers

| Trigger | What Shipwright does |
|---------|---------------------|
| **50% of tickets done** | "Quick check: is this still matching what you imagined? Anything feel off?" |
| **A ticket takes 3x longer than expected** | "This one's taking longer than planned. Is it critical path, or should we stub it and move on?" |
| **Scope creep detected** (ticket generates sub-tickets) | "This ticket is expanding. Should we split it, simplify it, or cut something else to make room?" |
| **Architecture decision needed** | "This decision affects the next N tickets. Here are the trade-offs: [A vs B]. Which one?" |
| **Builder adds a new requirement** | "How does this serve the core job from the pitch? Can it wait for cycle 2?" |
| **Time budget is 75% consumed** | "You've used 75% of your time budget with N tickets left. Options: cut scope, extend time, or ship what's done." |

### The Key Question

At any point during Build, if the prototype diverges from the pitch, Shipwright asks:

> "Is this still the thing you set out to build? Or has it morphed into something different? Both are fine -- but let's be explicit."

---

## Worktree Patterns

Stack-agnostic. Used during Build regardless of what you're building.

1. **One worktree per ticket** -- each code-agent instance works in an isolated worktree
2. **Agent metadata enforces isolation:**
   ```yaml
   isolation: worktree
   ```
3. **Commit per ticket** -- agent commits in its worktree branch, orchestrator merges
4. **`.worktreeinclude`** at project root:
   ```
   .env
   .env.local
   .shipwright/project.json
   ```
5. **`.claude/worktrees/` in .gitignore** -- prevent worktree contents from being tracked
6. **Cleanup** -- no changes = auto-clean. Commits = prompt for merge or discard

---

## Build Output

Updates to SHIPWRIGHT.md (see [state-model.md](state-model.md)):
- Tickets table with statuses and commit hashes
- Progress summary
- Open questions surfaced during build
- Context for next session (if work spans sessions)

Updates to project.json:
- `phase: "ship"` (on completion)
- Any tech choices recorded
