# Onboarding: Meeting Builders Where They Are

Not every builder starts from zero. Shipwright supports three entry points.

---

## Entry Point 1: New Idea (default)

**Trigger:** `/shipwright "habit tracker for parents"`

The builder has an idea but no code. This is the default flow: Shape -> Build -> Ship -> Learn.

See [shape.md](shape.md) for the full Shape workflow.

---

## Entry Point 2: Existing Repo

**Trigger:** `/shipwright import`

The builder has a half-built project. Maybe they started it months ago and abandoned it, or they built it with another tool and want Shipwright to take over.

### What Shipwright Does

1. **Scan the repo:** Read package.json, directory structure, README, existing tests. Identify the stack, dependencies, and current state.

2. **Reconstruct context:** Ask the builder:
   - "What is this project supposed to do? One sentence."
   - "Where did you leave off? What works and what doesn't?"
   - "Why did you stop working on it?"
   - "What would make you excited to pick it back up?"

3. **Generate SHIPWRIGHT.md:** Create the state file retroactively:
   - Pitch section (from builder's answers + repo scan)
   - Tickets (inferred from what exists vs. what's described)
   - Mark completed tickets based on existing code
   - Identify pending work

4. **Set the phase:** Based on what exists:
   - Code exists but incomplete -> `phase: "build"`, resume from where they left off
   - Code exists and works -> `phase: "ship"`, help them deploy
   - Code exists and is deployed -> `phase: "learn"`, help them evaluate

5. **project.json:**
   ```json
   {
     "entryPoint": "existing-repo",
     "phase": "build",
     ...
   }
   ```

### The Key Insight

The hardest part of resuming an abandoned project isn't the code -- it's the context. What was I thinking? What decisions did I make? What's left? Shipwright rebuilds that context so the builder can resume in minutes, not hours.

---

## Entry Point 3: Existing Product

**Trigger:** `/learn` (on a project without .shipwright/ state) or `/shipwright import` on a deployed project

The builder has something live but it's stalling. No users, or users aren't doing what they hoped. This is the highest-urgency entry point -- these builders have a burning job to be done.

### What Shipwright Does

1. **Gather context:**
   - "What's the URL?"
   - "How long has it been live?"
   - "What did you hope would happen? What actually happened?"
   - "Do you have any data? (analytics screenshots, feedback, reviews)"

2. **Quick assessment:** Shipwright categorizes the situation:
   - **Distribution problem:** Product works, nobody knows about it -> Ship phase (distribution sub-phase)
   - **Product problem:** People visit but don't stick -> Learn phase (what's not working?)
   - **Problem problem:** The underlying need isn't strong enough -> reshape or kill

3. **Generate SHIPWRIGHT.md:** Retroactive state file with emphasis on the Learn/Ship sections.

4. **Set the phase:** Usually `learn` or `ship` depending on the diagnosis.

5. **First action:** Unlike new projects, existing products need immediate triage, not lengthy reflection. Shipwright should have a suggestion within the first 5 minutes.

### Reflection for Existing Products

Different questions than for new ideas:

- "When was the last time someone used this who isn't you?"
- "If you had to get 5 new users this week, where would you find them?"
- "What's the one thing that would make you proud of this product?"
- "Is this worth saving, or is the learning the valuable part?"

---

## Summary

| Entry point | Trigger | Builder has | Shipwright starts at | Key value |
|-------------|---------|-------------|---------------------|-----------|
| New idea | `/shipwright "idea"` | Nothing | Shape | Help think clearly |
| Existing repo | `/shipwright import` | Code (incomplete) | Build or Ship | Rebuild context, resume |
| Existing product | `/learn` or `/shipwright import` | Live product | Learn or Ship | Diagnose and unblock |
