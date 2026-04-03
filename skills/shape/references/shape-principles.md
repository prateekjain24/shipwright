# Shape: Core Principles

These are the ideas behind the Shape phase. Not Shape Up jargon. Just how good product thinking works.

---

## Why Reflection Matters

Most builders want to jump straight to code. Most AI tools encourage this. Shipwright slows you down first.

Here's why: **Building is expensive. Thinking is cheap.**

If you spend an hour asking "who has this problem?" before you code, you save yourself 2 weeks of building the wrong thing.

The hard part isn't thinking of features. It's deciding which problem you're actually solving. Once you're clear on that, everything else moves fast.

Example: You think "habit tracker." But after 10 minutes of reflection, you realize the real problem is "parents lose motivation after a week" -- not "kids need to track habits." That changes what you build. Code for the second one and you'll get traction. Code for the first one and it'll sit unused.

Reflection catches this before you write a line of code.

---

## Fixed Time, Variable Scope

Most projects fail because scope creeps and time balloons.

Shipwright does it backwards: **time is fixed, scope is variable.**

You decide upfront: "I have 2 weeks." That's the constraint. Everything else scales to fit.

What does that mean?

- Week 1: Build the core thing (the 2-3 key screens)
- If you hit time budget with features left over: good. Ship what you have. Learn with real users. Iterate.
- If you finish early: add the next priority feature, or ship and celebrate.

Fixed time forces hard choices. "If I have 2 weeks, can I build notifications AND email digests AND mobile?" No. Pick one. That's scope discipline.

**Why this works:**
- You ship something real instead of a half-baked everything
- You learn from actual users instead of imaginary ones
- You know exactly when to ship (when time runs out)
- You don't spiral ("just one more feature")

Time is your friend. Use it.

---

## Pitch vs Spec

A pitch is not a requirements document. It's not a spec. It's not a design.

A pitch is a **thinking artifact.** It proves you've thought clearly about the problem and the solution.

| Pitch | Spec |
|-------|------|
| "Parents want to track habits with their kids, but paper charts get forgotten" | System shall provide habit creation, daily check-in, and weekly summary screens |
| "Email reminders only in cycle 1" | Send email every Tuesday at 9am with 3 sentences summarizing the week |
| "2 weeks" | 10 story points assigned to tickets |
| "Solves the motivation problem or not worth shipping" | Week 1 success metric: 10 habits created |
| Readable by humans | Readable by engineers |

Both are useful. But they're different things.

The pitch explains *why* you're building. The spec explains *how*.

In Shipwright's Shape phase, you create the pitch. In Build, you create the spec (tickets). Don't confuse them.

**A good pitch fits in one screen.** If it takes 5 pages to explain your idea, you don't understand it yet.

---

## What "Shaped" Means

Shaped doesn't mean designed. It doesn't mean every edge case is figured out.

Shaped means: **you've thought clearly about the problem, you've committed to a scope, and you're ready to let other people (code agents, users) fill in the details.**

A shaped idea has:

- **A real problem.** Not "wouldn't it be cool if..." but "I know 3 people frustrated by X"
- **Bounded scope.** Not "all features people could want" but "the 2-3 core interactions"
- **A time constraint.** "2 weeks" not "until it feels done"
- **Explicit exclusions.** "No mobile in cycle 1" not "maybe later"
- **One clear job.** Not "be a social habit tracker with analytics and leaderboards" but "help me build consistency with my kid"

If you have all 5, you're shaped. You can hand this to a code agent and they'll build it. You can ship it to users and they'll understand what it does.

If you're missing any of these, you're not shaped. That's fine. Shape first. More thinking needed.

---

## Demand Before Code

The validation gate is the most important decision in Shape.

It separates two kinds of builders:

| Builder | Signal | Action |
|---------|--------|--------|
| Has conviction | Can name 3 people with the problem. Sees demand signals. Has personal pain. | Build now (Path A) |
| Has a hunch | Thinks people want this but hasn't validated it. | Test demand first (Path B) |

Neither is wrong. But they're different bets.

**Path A (Direct to Build):** You're confident people want this. You've talked to users or seen signals. You build now and iterate with real feedback.

**Path B (Validate First):** You have a hunch. Before you invest 2 weeks, you test it in 30 minutes. You run a landing page test, a fake door test, or a concierge test. If signals are strong, you build. If not, you reshape or kill it.

The key insight: **validation before code saves more time than code before validation.**

If you test and the idea fails, you spent 30 minutes. If you code and the idea fails, you spent 2 weeks.

Don't skip this. If the builder isn't confident, validate first.

---

## The Research Brief

While you're thinking, Shipwright's research agent is working in parallel.

It scans:
- Top 5 competitors (what they do, pricing, reviews)
- App store complaints (where current solutions are weak)
- Reddit/Twitter/HN signals (what people are asking for)

What comes back is a one-page brief. Not a 50-page competitive analysis. Just: "Here are the 3 competitors. None of them do X. On Reddit, 12 people asked for Y. That's interesting."

This brief informs your reflection, not replaces it.

You decide if the competitors are actually competitors. You decide if the Reddit signals matter. The brief is input, not the answer.

**Why run research in parallel?** Because it's done by the time you finish reflecting. You can incorporate it into your pitch immediately. You don't sit around waiting.

---

## When Shaping Fails

Sometimes you get halfway through Shape and realize: the problem isn't real, or the scope is too big, or there's a direct competitor that does it better.

That's not failure. That's exactly what shaping is for.

**Kill the idea early.** It's the cheapest lesson you'll ever get. Killing an idea after 1 hour of thinking beats shipping it to zero users after 2 weeks of building.

If you kill an idea in Shape, the next cycle might be better. You learned what you don't want to build. That's valuable.

---

## References

- See `behavioral-contract.md` for how Shipwright thinks and pauses
- See `reflection.md` for question design principles
- See `shape.md` for the full workflow
- See `personality.md` for language rules and tone
