# Phase 1: SHAPE

**Purpose:** Think clearly about what to build. The output is the pitch section of SHIPWRIGHT.md, but the real value is the thinking that produces it.

**Core insight:** Shaping isn't requirements gathering. It's helping the builder develop conviction -- or kill the idea early. Both are good outcomes.

**Mode:** Interview (curious, probing). This is the most reflection-heavy phase. Shipwright slows the builder down here so everything after can move fast. See [behavioral-contract.md](behavioral-contract.md) for mode details.

---

## Workflow

1. User runs `/shipwright "habit tracker for parents"` or `/shape`
2. Shipwright asks reflection questions (see [reflection.md](reflection.md))
3. In parallel: research-agent scans competitors, app store reviews, Reddit/Twitter signals
4. Shipwright asks about time budget: "How much time do you want to spend?"
5. Shipwright asks about stack: "What kind of thing are you building?" (see [stacks.md](stacks.md))
6. Shipwright helps sketch the solution: "What are the 2-3 key screens or interactions?"
7. Shipwright identifies risks ("What could spiral here?") and explicit exclusions
8. **Validation gate:** Before committing to Build, Shipwright asks: "How confident are you that people want this? Do you want to test demand first?"
9. Output: SHIPWRIGHT.md created with pitch section filled in (see [state-model.md](state-model.md))
10. Transition: either to Validate (if demand is uncertain) or Build (if builder has conviction)

---

## Two Paths Out of Shape

This is a key design decision. Not every idea should go straight to code.

### Path A: Direct to Build

For builders who already have conviction -- they've talked to users, they have personal pain, they've seen the demand. Shipwright skips validation and moves to Build.

Signals that suggest this path:
- Builder can name specific people with the problem
- Builder has a personal workaround they're tired of
- Builder has seen signs of demand (Reddit threads, tweets, app store reviews)
- Research-agent confirms an underserved niche

### Path B: Validate First

For builders with a hunch but not conviction. Shipwright helps test demand *before* writing application code.

**Validation methods (lightest to heaviest):**

| Method | What Shipwright does | Time | Signal |
|--------|---------------------|------|--------|
| **Landing page test** | Generates a landing page with waitlist signup using `stacks/landing-page/`. Deploys it. | 30 min | Email signups |
| **Fake door test** | Adds a CTA or feature button that tracks clicks but doesn't do anything yet. | 15 min | Click-through rate |
| **Concierge test** | Shipwright drafts a manual service flow. Builder delivers the value by hand to 3-5 people. | 1 hour | Willingness to pay / repeat |
| **Research-only** | Research-agent runs a deep scan: competitors, reviews, forums, social signals. Shipwright synthesizes a demand brief. | 10 min | Market signal strength |

After validation, the builder decides: proceed to Build, reshape, or kill the idea. All three are valid outcomes.

**Validation output:** Added to SHIPWRIGHT.md under a `## Validation` section:
```markdown
## Validation
**Method:** Landing page test
**Duration:** 5 days
**Result:** 47 signups from 2 Reddit posts. 3 people replied to confirmation email asking when it launches.
**Decision:** Proceed to Build. Focus on daily check-in (most requested feature).
```

---

## Reflection Questions (Shape-Specific)

See [reflection.md](reflection.md) for principles. Shape-specific questions:

**Problem validation:**
- "Who specifically has this problem? Can you name 3 real people?"
- "What do they do today without your product? Why isn't that good enough?"
- "How much pain is this? A minor annoyance or a hair-on-fire problem?"

**Scope discipline:**
- "What's the smallest version of this that would still be useful?"
- "What would make you kill this project after 2 weeks?"
- "If a competitor launched this tomorrow, would you still build it? Why?"

**Is there real demand?**
- "How would you test if people want this *before* writing any code?"
- "If you tweeted about this right now, who would reply?"
- "Would anyone pay for this? How much? Have you asked?"

**Risk surfacing:**
- "What's the one thing that could spiral out of control?"
- "What are you explicitly NOT building in cycle 1?"

---

## Research Agent (Parallel)

While the builder answers reflection questions, the research-agent runs in parallel:

**What it does:**
- Scans top 5 competitors (features, pricing, reviews)
- Checks app store reviews for the category (complaints = opportunities)
- Searches Reddit, Twitter/X, HN for signs of demand
- Summarizes findings in a brief

**What it produces:**
A research brief added to SHIPWRIGHT.md:
```markdown
## Research
**Competitors:** Habitica (gamified, not parent-focused), Streaks (iOS only, no family), Loop (open source, no kid mode)
**Gap:** None specifically target parent-child habit building
**Signs of demand:** 4 Reddit threads in r/parenting requesting kid habit trackers (120+ upvotes combined)
**App store complaints:** "Too complex for kids" (Habitica), "No family sharing" (Streaks)
```

**Important:** Research informs reflection, not replaces it. Shipwright shows findings and asks: "I found 3 competitors -- none target parents specifically. Does that change your thinking about scope?"

---

## Shape Output

The Shape phase produces the pitch section of SHIPWRIGHT.md. See [state-model.md](state-model.md) for the full spec. Key sections:

- **Problem:** Who has it, what they do today, why it's not good enough
- **Time budget:** How long the builder wants to spend
- **Solution sketch:** 2-3 key screens or interactions
- **Risks:** What could spiral, explicit exclusions
- **Validation:** (if Path B) What was tested, what was learned
- **Research:** Competitor scan, signs of demand
- **Stack:** Selected stack and rationale (see [stacks.md](stacks.md))
