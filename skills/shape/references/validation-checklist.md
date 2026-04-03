# Validation Methods: Practical Checklist

When the builder has a hunch but not conviction, use one of these validation methods. Each tests demand in a different way, with different speed and cost.

---

## Landing Page Test

**What it is:** A simple landing page with a waitlist signup. Deploy it. Share it with your audience. Count signups.

**Time:** 30 minutes

**When to use:** You're confident about WHO your audience is and WHERE to reach them. (Can you tweet it? Post it to Reddit? Share it in a Slack community?)

**How Shipwright does it:**

1. Generate a landing page from `stacks/landing-page/` stack
   - Hero headline describing the problem
   - 2-3 sentence explanation of the solution
   - "Notify me when it launches" form with email input
   - Stripe integration ready (optional)

2. Deploy to Vercel (one-click from GitHub)

3. Builder shares link in 2-3 places (Twitter, Reddit, relevant Slack communities, newsletters)

4. Collect signups for 5-7 days

5. Analyze: How many signups per share? How many replies asking "when does it launch?" or offering feedback?

**What signal to look for:**

| Signal | Strength | What to do |
|--------|----------|-----------|
| 20+ signups in 5 days, 3+ replies asking "when?" | Strong | Proceed to Build |
| 5-20 signups, minimal engagement | Weak | Reshape and re-test or pivot |
| 0-5 signups | Very weak | Kill or major pivot |

**Why this works:** If people are willing to put their email in, there's real interest. If they reply asking when it ships, there's urgency.

**Common pitfall:** Builder doesn't share aggressively. Silence doesn't mean no demand -- it means no distribution. Try different channels before killing the idea.

---

## Fake Door Test

**What it is:** Add a button to an existing product that users can't use yet. Track how many click it.

**Time:** 15 minutes

**When to use:** You already have a product with users. You want to test if they want a new feature before building it.

**How Shipwright does it:**

1. Add a new nav item or button: "Habits" or "Teams" or "Analytics" (whatever the feature is)

2. Button goes to a waitlist or "coming soon" screen

3. Track clicks with basic analytics (Mixpanel, Segment, or just server logs)

4. Run the test for 1-2 weeks

5. Look at: How many users clicked? Of all visitors, what percentage clicked?

**What signal to look for:**

| Signal | Strength | What to do |
|--------|----------|-----------|
| 30%+ of users click (or 5+ daily) | Strong | Build it |
| 5-30% click | Weak | Test in a different way or reconsider |
| <5% click | Very weak | Don't build |

**Why this works:** Actual user behavior is more honest than surveys. If they don't click, they don't want it.

**Common pitfall:** Button is buried in the UI. Make it prominent so people see it. If the click rate is low, it might be a visibility problem, not a demand problem.

---

## Concierge Test

**What it is:** You deliver the value manually to 3-5 users. They don't use software -- they use you.

**Time:** 1 hour per person (3-5 hours total)

**When to use:** You want to validate the core interaction without building anything. Or you want to discover exactly how people would use the product before you design it.

**How Shipwright does it:**

1. Pick 3-5 potential users (people in your audience, ideally with real pain)

2. Reach out: "I'm testing an idea. Would you spend 30 minutes with me?"

3. For each person, you manually do what the product would do:
   - Example: Habit tracker. You ask them to name 3 habits. You send them daily check-in emails. You manually tally their progress. After 1 week, you send a summary.
   - Example: Scheduling tool. You ask when they're free, check calendars yourself, and send them proposed times via email.

4. After each session, ask:
   - "Would you pay for this?"
   - "How often would you use it?"
   - "What was the hardest part?"
   - "What did I do that surprised you?"

5. Look for: Do they follow through? Do they ask when it launches? Do they tell their friends?

**What signal to look for:**

| Signal | Strength | What to do |
|--------|----------|-----------|
| 3-5 people complete the test, 2+ ask "when can I buy?" or bring friends | Strong | Build it |
| 1-2 people complete, lukewarm feedback | Weak | Reshape (you missed something) or pivot |
| 0 people complete, everyone ghosts | Very weak | Kill or major pivot |

**Why this works:** You get the highest-fidelity signal. You see exactly how people interact. You learn their language and their real pain points.

**Common pitfall:** You're not fully manual. If the concierge test starts feeling too much like real software, it's not a test anymore -- you're just building without code. Stay manual. Do it by hand.

---

## Research-Only

**What it is:** Deep scan of competitors, app store reviews, social signals. Synthesize a demand brief. Take 10 minutes.

**Time:** 10 minutes

**When to use:** You need to move fast. Or the landing page test won't work (B2B, niche market, hard to reach).

**How Shipwright does it:**

1. Research-agent scans:
   - Top 5 direct competitors: pricing, features, reviews
   - App store reviews for the category: what people complain about?
   - Reddit/Twitter/HN: are people asking for this? How many threads?
   - Job postings: are companies hiring for this skill? (If so, there's demand)

2. Synthesize: "Here's what I found. Does this change your thinking?"

3. Evaluate: Is there an obvious gap? Are people asking for this? Is there a competitor problem you can solve?

**What signal to look for:**

| Signal | Strength | What to do |
|--------|----------|-----------|
| 10+ Reddit threads asking for it, 0 direct competitors, 100+ upvotes total | Strong | Proceed to Build |
| 3-5 threads, 1 competitor with weak reviews | Medium | Test with landing page or proceed |
| 1-2 threads, established competitor doing it well | Weak | Reshape or kill |
| 0 threads, 3+ strong competitors | Very weak | Kill |

**Why this works:** Reddit doesn't lie. If people aren't asking for it, demand is probably low. If people are asking and no one's solving it, you have a gap.

**Common pitfall:** Confusing "interesting" with "viable." Lots of Reddit threads about flying cars is interesting. But no one can actually build that. Focus on demand for things that are buildable in your time budget.

---

## Choosing Your Validation Method

| Your situation | Best method | Why |
|---|---|---|
| You know your audience and can reach them | Landing page | Fastest signal, highest-fidelity audience |
| You have an existing product with users | Fake door | Actual user data, no extra effort |
| You want to deeply understand usage | Concierge | Highest quality learning, real behavior |
| You're in a niche or hard-to-reach market | Research-only | Fast, reveals gaps and demand signals |
| You're uncertain | Research-only first, then landing page | Find the gap, then test if people care |

---

## After Validation: Three Paths

### Proceed to Build

You got strong signals. People want this. Or you have conviction from the test.

UPDATE `SHIPWRIGHT.md` with validation results. SET `phase: "build"`. Ship it.

### Reshape and Re-Test

Signals were weak, but the idea has potential. You learned something in the test.

Update your pitch based on what you learned. Pick a different validation method. Test again. (This is fine. Two quick tests beat one wrong build.)

### Kill the Idea

Signals were very weak. No demand. Or you found a strong competitor that does it better.

Kill it. Document why in `SHIPWRIGHT.md`. Next cycle, pick a different idea.

All three are good outcomes. Killing early is better than shipping to zero users.

---

## References

- See `shape-principles.md` for why validation matters
- See `shape.md` for the full Shape workflow
- See `stacks/landing-page/` for landing page stack setup
