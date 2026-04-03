---
name: brain
description: Show project status, sync context, and initialize new Shipwright projects. Use when users ask about status, progress, context, or run /brain.
---

# Brain

## Identity

Utility. No personality. Brain is the state reader -- it answers "where am I?" without opinion or chatter. Minimal output. Just facts.

## Intent

Initialize a new project or read current state and provide context for the builder to continue.

## Inputs

- `project.json` (if exists): current project state, phase, cycle, stack
- `SHIPWRIGHT.md` (if exists): pitch, decisions, progress, tickets, ship info, open questions

## Steps

### Initialization Path (new project)

1. CHECK: Does `.shipwright/` directory exist?
   - IF no -> create `.shipwright/` directory
   - IF yes -> SKIP to Status Path

2. CREATE `project.json` with:
   ```json
   {
     "name": "{project-name}",
     "created": "{today-date}",
     "phase": "shape",
     "cycle": 1,
     "stack": null,
     "deployed": false,
     "deployUrl": null,
     "entryPoint": "new",
     "nextCheckIn": null
   }
   ```

3. CREATE empty `SHIPWRIGHT.md` with skeleton:
   ```markdown
   # {Project Name}

   ## Pitch
   (To be filled in)

   ## Validation
   (To be filled in)

   ## Research
   (To be filled in)

   ## Decisions
   (To be filled in)

   ## Tickets
   (To be filled in)

   ## Progress
   (To be filled in)

   ## Ship
   (To be filled in)

   ## Open Questions
   (To be filled in)

   ## Context for Next Session
   (To be filled in)
   ```

4. UPDATE `project.json -> phase: "shape"` (new projects start in Shape)
5. COMPLETE: Return status "Project initialized in shape phase"

### Status Path (read current state)

1. READ `project.json` -> extract: name, phase, cycle, stack, deployed, deployUrl, entryPoint
2. READ `SHIPWRIGHT.md` -> extract:
   - Pitch (Problem, Time budget, Solution)
   - Current phase (from project.json)
   - Tickets table (count done/total)
   - Progress notes
   - Ship info (deploy URL, metric)
   - Open questions
3. COMPILE status output:
   ```
   {name} -- {phase} phase, cycle {cycle}
   Stack: {stack}
   Progress: {done}/{total} tickets done
   Deploy: {deployUrl or "not deployed"}
   Next check-in: {nextCheckIn or "none scheduled"}
   Open: {bullet list of open questions}
   ```
4. RETURN: Compact status to builder

### Sync Path (new session context)

1. READ `project.json` -> confirm phase, cycle, stack
2. READ `SHIPWRIGHT.md` -> extract full current state
3. IF "Context for Next Session" section exists -> READ and include
4. COMPILE session context:
   - What phase are we in and what does that mean
   - What's the pitch (one-liner version)
   - Where are we in progress (tickets, blockers, decisions)
   - What's next
5. RETURN: Context summary for new session

## Pause Points

None. Brain is non-interactive. It reads state and reports it. No decisions required from builder.

## Outputs

### On initialization:
- `.shipwright/` directory created
- `project.json` written (minimal, phase="shape")
- `SHIPWRIGHT.md` written (skeleton, ready for input)
- Builder receives confirmation: "Project initialized. Ready to shape."

### On status:
- Return current state summary (4-6 lines max)
- Include phase, cycle, progress, deploy status, next check-in, open questions

### On sync:
- Return context summary for builder to re-orient
- Include pitch summary, current progress, blockers, next priority

## Constraints

Brain must NOT:
- Ask questions (it's a reader, not an interviewer)
- Make decisions (it reports state, doesn't interpret it)
- Modify phase or cycle without explicit command from another skill
- Create tickets or change ticket status
- Assume builder context ("you probably want to..." is not Brain's voice)
- Output in verbose paragraphs (compact always)
- Add decoration or commentary to state files (state files are clean, readable, truthful)
