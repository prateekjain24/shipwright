# Cycle Overview -- The 4-Phase Journey

This is the "map" the orchestrator uses to know where the builder is and what comes next. Condensed from docs/ARCHITECTURE.md.

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

---

## The 4 Phases

### Shape (Think Clearly)

**Entry:** New idea, no code

**What happens:** Shipwright asks uncomfortable questions to test conviction. Builder defines:
- Who specifically has the problem (not "users")
- What they do without the product
- The smallest useful version
- The time budget (constraint that shapes everything)
- Key interactions and flows (not a feature list)
- What could spiral out of control

**Output:** Clear pitch in SHIPWRIGHT.md with problem statement, time budget, and solution sketch

**Decision gate:** High conviction? Build now. Uncertain? Validate first (landing page test, fake door, concierge, or research-only)

**Next phase:** Build (if high conviction) or Validate (if uncertain). After validation, always Build.

---

### Build (Make It Work)

**Entry:** Clear pitch in SHIPWRIGHT.md, time budget known, stack selected

**What happens:** Shipwright breaks the pitch into 1-point tickets, spawns code-agents in parallel (worktree isolation), and watches for scope creep and assumption failures.

**Key moments:**
- Ticket breakdown: pause for builder approval
- 50% done: quick gut check ("still matching what you imagined?")
- If any ticket takes 3x longer: ask if it's critical or should be stubbed
- If scope creeps: push back ("serves the core job or scope creep?")
- If time budget 75% consumed with tickets remaining: cut scope, extend time, or ship what's done

**Output:** Working prototype with all tests passing, committed to main branch. Tickets table in SHIPWRIGHT.md with commit hashes.

**Decision gate:** All tests pass. If not, fix or stub.

**Next phase:** Ship

---

### Ship (Get Real Users)

**Entry:** Working prototype with tests passing, deployable code

**What happens:** Shipwright deploys the product, instruments analytics, sets up SEO basics, and generates distribution copy.

**Steps:**
1. Deploy to production (Vercel, Netlify, etc.)
2. Add analytics snippet (Plausible or Umami) with core action instrumentation
3. Add SEO (meta tags, OG tags, sitemap, robots.txt) -- unless Chrome extension
4. Generate distribution copy: one-liner, tweet, 3-sentence pitch, first-10-users list
5. Set up 7-day check-in for Learn phase

**Builder pauses:**
- Before deploy: "Ready to go live?"
- After deploy: "What does success look like in week 1? Pick one number."
- Before distribution: "First 10 people plan looks right?"

**Output:** Live product at a URL, with analytics tracking. Distribution copy ready. SHIPWRIGHT.md updated with deploy URL, success metric, and distribution plan.

**Decision gate:** Deploy succeeds and builder approves distribution plan.

**Next phase:** Learn (automatic 7-day check-in)

---

### Learn (Reflect & Decide)

**Entry:** Live product with 1+ weeks of data

**What happens:** Shipwright asks retrospective questions, uses structured thinking to diagnose what happened, and guides the builder to a decision: Reshape the problem, Pivot to something new, or Archive and move on.

**Steps:**
1. Ask retrospective questions: "What surprised you? What do users keep asking for? What would you NOT build if you started over?"
2. Builder shares data (visits, signups, usage, quotes, distribution results)
3. Use structured thinking to diagnose: Why did things go the way they did? Luck? Distribution? Product? Timing?
4. Ask the Kill Question with explicit options:
   - Reshape: maybe the problem is real but the solution is wrong
   - Pivot: take what you learned and apply it to a different idea
   - Archive: write down what you learned and move on

**If Archive:**
- Write cycle report to .shipwright/cycles/cycle-{N}.md
- Update project.json -> phase: "archived"
- Deep one-liner: "Killing an idea early isn't failure. It's the cheapest lesson you'll ever get."

**If Reshape or Pivot:**
- Write cycle report
- Draft next cycle pitch based on learnings
- Pause for builder approval of new pitch
- Create new Pitch section in SHIPWRIGHT.md
- Update project.json -> cycle: {N+1}, phase: "shape"
- Set 7-day check-in for next cycle
- Deep one-liner: "Cycle 1 was a guess. Cycle 2 is a conversation with your users. That's when it gets interesting."

**Output:** Cycle report (archived) OR new cycle pitch + updated state (reshaping/pivoting)

---

## How State Flows Between Phases

| Data | Shape | Build | Ship | Learn |
|------|-------|-------|------|-------|
| **Pitch** | Written | Read (no changes) | Read (no changes) | Read (diagnose against) |
| **Time budget** | Set as constraint | Used to pace work | (N/A) | Used for next cycle |
| **Stack** | Selected | Used for scaffolding | Used for deploy | Noted for next cycle |
| **Tickets** | (none) | Written, executed, status tracked | Reviewed for completeness | (reflected on) |
| **Deploy URL** | (none) | (none) | Created | Used for analytics |
| **Analytics** | (none) | (none) | Instrumented | Read and analyzed |
| **Decisions** | Recorded | Added to | (reflected on) | Informed next cycle |

**Key principle:** Each phase reads the output of the previous phase. No phase overwrites what came before without builder approval.

---

## How Cycles Repeat

1. **Cycle 1:** New idea -> Shape -> Build -> Ship -> Learn -> (decision)
2. **If Reshape/Pivot:** Cycle 2 starts at Shape with new pitch. Same four phases.
3. **If Archive:** Project ends. Learnings saved to cycle report.
4. **Multiple cycles:** Each cycle increments `project.json -> cycle: {N}`. SHIPWRIGHT.md grows with new pitch sections for each cycle.

**Reflection runs throughout** -- not just at phase transitions. See reflection-guide.md for when to trigger hard questions mid-phase.

---

## Entry Points

Shipwright supports three ways to start:

| Entry Point | Trigger | Builder has | Starts at |
|-------------|---------|-------------|-----------|
| **New idea** | `/shipwright "idea"` | Nothing | Shape |
| **Existing repo** | `/shipwright import` | Half-built code | Build or Ship (assessed) |
| **Existing product** | `/learn` (no state) or `/shipwright import` (live URL) | Live product | Learn or Ship (diagnosed) |

See docs/onboarding.md for details on each entry point.
