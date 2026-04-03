# Skill Contract

Every SKILL.md in Shipwright follows the same skeleton. This makes skills predictable for agents (any agent can pick up any skill without re-learning the format) and maintainable for contributors.

---

## The Skeleton

Every SKILL.md has these sections, in this order:

```markdown
# {Phase/Skill Name}

## Identity
Who is Shipwright in this phase? What's the tone?
(See personality.md for voice guidelines.)

## Intent
What does success look like when this skill completes?
One sentence. Not a paragraph.

## Inputs
What state does this skill read before starting?
(File paths, project.json fields, builder context.)

## Steps
The hybrid workflow: structured steps with flexible thinking.
(See below for the hybrid format.)

## Pause Points
When does this skill stop and ask the builder?
(Explicit list of triggers and questions.)

## Outputs
What state does this skill write when done?
(File paths, project.json updates, transitions.)

## Constraints
What this skill must NOT do.
Hard boundaries, not suggestions.
```

---

## Hybrid Workflow Format

The Steps section uses a hybrid format: numbered steps with clear state reads/writes, plus natural language for decisions and reflection.

**Rules:**
- Steps are numbered. An agent follows them in order.
- State reads/writes are explicit: `READ .shipwright/SHIPWRIGHT.md` or `UPDATE project.json -> phase: "build"`
- Decision points use `IF/THEN` with natural language conditions (not code)
- Pause points reference the Pause Points section
- Reflection is inline, not deferred: "Before generating tickets, ask yourself: does this ticket list match the spirit of the pitch?"
- Loops are explicit: `FOR each ticket` or `REPEAT until builder approves`

**Example (Build phase):**

```markdown
## Steps

1. READ .shipwright/SHIPWRIGHT.md -> extract pitch, decisions, stack
2. READ stacks/{stack}/setup.md -> load stack-specific context
3. Generate ticket breakdown from pitch:
   - Each ticket = 1 story point
   - Each ticket has: title, what to build, acceptance criteria, dependencies
   - Save individual tickets to .shipwright/tickets/TICKET-NNN.md
4. UPDATE SHIPWRIGHT.md -> add tickets table
5. PAUSE: "Here's how I'd break this down. Does it match the spirit of what you described?"
   - IF builder wants changes -> revise tickets, GOTO 4
   - IF builder approves -> continue
6. FOR each ticket (in dependency order, parallelize independent ones):
   a. SPAWN code-agent with ticket context (isolation: worktree)
   b. ON completion: UPDATE ticket status + commit hash in SHIPWRIGHT.md
   c. AT 50% tickets done:
      PAUSE: "Quick check -- still matching what you imagined?"
   d. IF any ticket takes 3x longer than expected:
      PAUSE: "This one's fighting back. Critical path, or stub it?"
   e. IF builder requests new feature mid-build:
      "How does this serve the core job? Can it wait for cycle 2?"
7. MERGE worktree branches
8. RUN tests -> report results
9. UPDATE SHIPWRIGHT.md -> progress section
10. UPDATE project.json -> phase: "ship"
11. TRANSITION: "Build's done. Ready to get this in front of people?"
```

**What makes this hybrid:**
- Steps 1-2, 4, 7-10 are rigid (state operations, always the same)
- Steps 3, 6c, 6d, 6e are flexible (natural language, judgment calls)
- Pause points are explicit but the conversation within them is free-form

---

## Applying the Skeleton to Each Phase

| Phase | Tone | How it runs | When it pauses |
|-------|------|------------|----------------|
| Shape | Curious, probing | Linear with research running in parallel | After reflection questions, before validation decision, before Build |
| Build | Focused, minimal chatter | Ticket loop with mid-build check-ins | At 50% done, on scope creep, on architecture decisions, at time budget pressure |
| Ship | Encouraging, practical | Sequential: deploy, analytics, SEO, distribution | Before deploy, after deploy (success metric), before distribution |
| Learn | Honest, forward-looking | Structured conversation, then synthesis, then next cycle | After retrospective questions, on kill/continue decision, before cycle 2 pitch |
| Brain | Utility, no personality | Read/write state, no builder interaction | None (brain is non-interactive) |

---

## Skill Metadata

Each SKILL.md starts with a metadata block at the top for routing:

```markdown
<!-- SHIPWRIGHT:SKILL
name: build
trigger_keywords: ["build", "code", "implement", "tickets"]
mode: execute
phase: build
-->
```

| Field | Purpose |
|-------|---------|
| `name` | Skill identifier |
| `trigger_keywords` | Words that route to this skill (for implicit mode detection) |
| `mode` | How Shipwright behaves: interview, assess, execute, reflect |
| `phase` | Which phase this skill belongs to |

This metadata supports both implicit routing (keyword detection) and explicit routing (`/build` command).
