# Pitch Template: The SHIPWRIGHT.md Format

The pitch is the contract between you and Shipwright. It's written during Shape and read at the start of every other phase.

---

## Pitch Section: Blank Template

```markdown
## Pitch

**Problem:** Who specifically has this problem? What do they do today without your product? Why isn't that good enough?

**Time budget:** How much time do you want to spend? (1 week, 2 weeks, etc.)

**Solution sketch:** What are the 2-3 key screens or interactions? What does the first-time user do?

**Risks:** What could spiral out of control? What are you explicitly NOT building in cycle 1?

**Not building:** [Explicit scope boundaries. Be specific.]

**Validation:** Method chosen (landing page test, fake door test, concierge test, research-only). Signal received. Decision: proceed or reshape.

**Research:** Top competitors found. Gaps identified. Demand signals (Reddit threads, reviews, tweets, etc.). Strength of signal.

**Stack:** What are you building (web app, bot, extension, landing page, etc.)? Which stack selected? Why?
```

---

## Filled Example: Habit Tracker for Parents

This is a real example of a shaped idea. Not perfect, but complete.

```markdown
## Pitch

**Problem:** Parents want to build consistent routines with their kids (bedtime, chores, reading) but forget to track progress and lose motivation after a week. Right now they use paper charts on the fridge that nobody updates, or they try to remember in their head and give up.

**Time budget:** 2 weeks

**Solution sketch:**
- Screen 1: Parent creates 3-5 habits (name, frequency like "daily" or "Mon/Wed/Fri", emoji)
- Screen 2: Daily check-in where parent and kid tap a button to mark "done" for the day, see current streak
- Screen 3: Weekly summary showing what got done and the longest streak

First-time user: Parent signs up, creates one habit (e.g., "bedtime routine"), shares a link with their kid. Both get daily email reminders. Kid taps "done," parent sees it in their next check-in.

**Risks:** Notification system could spiral (push, SMS, Slack integration, etc.). Decision: email-only for cycle 1. No integration with other apps.

Not building: Social features (comparing with other families), gamification (badges, levels), mobile app, analytics dashboard, family leaderboards.

**Not building:** Social features, gamification, mobile app, analytics, leaderboards, notifications beyond email.

**Validation:** Research-only. Found 4 Reddit threads in r/parenting asking for "family habit tracker" or "way to track habits with kids" (120+ combined upvotes). No direct competitor. Streaks exists but iOS-only and not parent-focused. Habitica exists but gamified for individuals, not for parent-child. Decision: Conviction is high enough to proceed to Build (I've been tracking my kids' bedtime on paper for 6 months and would use this immediately).

**Research:**
- Competitors: Habitica (gamified, individual-focused), Streaks (iOS only, $3.99), Loop Habit Tracker (open source, no family mode), Productive (mobile-first)
- Gaps: None of these target parent-child shared routines
- Demand signals: r/parenting threads "easy habit tracker for kids," "how do you track chores," "motivation app that's not too complex" (4 threads, 120+ upvotes combined)
- App store complaint themes: "Too many features," "Too gamified," "No family sharing"
- Signal strength: Medium-High. Clear gap. Real demand. Low competition.

**Stack:** Web app (not mobile in cycle 1). Selected: Next.js 16 + Vercel + Clerk for auth. Why: Can ship fast, deploy in seconds, Clerk handles auth so I don't rebuild it, Vercel scales free for low traffic.
```

---

## What Each Section Means

### Problem

This is the hardest section to write. It should answer:
- Who specifically? (Not "parents" but "parents of kids aged 5-10")
- What's their current workaround? (What do they do *right now* instead of using your product?)
- Why is that workaround not good enough? (Is it slow? Annoying? Forgotten?)

**Common mistake:** Writing the features instead of the problem. Don't write "Screen 1: Create habits, Screen 2: Daily check-in." Write "Parents forget to check progress, kids lose motivation."

**Good sign:** Someone reads your problem statement and says "Oh yeah, that's exactly me."

### Time Budget

A constraint. The one thing that forces scope discipline.

Examples: "2 weeks," "40 hours," "1 month."

Not examples: "However long it takes," "Until it's perfect," "TBD."

This is referenced every time scope pressure hits during Build. "You said 2 weeks. We're at day 8 with 40% of tickets done. What do we cut?"

### Solution Sketch

The 2-3 key screens or interactions. Not exhaustive. Not detailed. Just the core.

**Example good solution sketch:**
- Screen 1: Parent creates habit
- Screen 2: Daily check-in (tap button, see streak)
- Screen 3: Weekly summary

**Example bad solution sketch:**
- Create habits
- Edit habits
- Delete habits
- View habits
- Share habits
- See analytics
- Invite family members
- Set reminders
- ...

The second list isn't a sketch. It's feature creep. The first one is a sketch.

**Rule of thumb:** If your solution sketch has more than 3-4 items, you're not sketching. You're brainstorming. Cut it.

### Risks

What could blow up? What are you saying "no" to?

**Risks example:** "Notification system could spiral (email, push, SMS, Slack, browser). Decision: email-only in cycle 1."

**Not building example:** "No social features (comparing families, leaderboards, comments)."

The word "Decision:" is key. You're not listing hypotheticals. You're saying what you've decided.

### Validation

Only fill this in after you've run a validation test.

- Method: Which one did you use?
- Signal: What did you find?
- Decision: Proceed to Build, reshape, or kill?

If you're proceeding direct to Build without validation (Path A), write: "None. Builder has conviction (personal pain, named users, seen demand)."

### Research

What the research-agent found. Or what you found yourself.

- Competitors: Name them. Briefly describe what they do.
- Gaps: Where are the competitors weak?
- Demand signals: Threads, reviews, upvotes. Numbers matter.
- Signal strength: Low, Medium, or High.

Don't write 50 pages of research. Write 1 page. Cite numbers. Be specific.

### Stack

What kind of thing are you building? Which stack are you using?

- Building: "Web app"
- Stack: "Next.js 16 + Vercel"
- Why: "Fast to ship, Clerk handles auth, scales free initially"

Short. Justified. Done.

---

## Pitch Quality Checklist

Before moving to Build, check:

- [ ] Problem statement: Someone reads it and says "that's me"
- [ ] Time budget: A specific number (weeks or hours)
- [ ] Solution sketch: 2-4 key screens, not a feature list
- [ ] Risks: Specific decisions about what's excluded
- [ ] Not building: Clear scope boundaries
- [ ] Validation: Either signals + decision, or explicit "high conviction"
- [ ] Research: Competitors named, gaps described, demand signals with numbers
- [ ] Stack: Type of thing + selected stack + one-line reason

If any box is empty or vague, the pitch isn't ready. Go back and think harder. You're not done shaping yet.

---

## Common Pitch Mistakes

| Mistake | What it looks like | How to fix it |
|---------|-------------------|--------------|
| Problem is a feature | "An app that tracks daily habits" | Write the pain: "People lose motivation because they can't see progress" |
| Solution is a checklist | 20 screens listed | Cut it to 3. What's essential? |
| Time budget is vague | "A couple months" | Be specific: "2 weeks" or "40 hours" |
| Risks are hypothetical | "Notifications could get complex" | Make a decision: "Email-only in cycle 1" |
| No validation story | Empty Validation section | Run a test, or write "Builder has personal pain" |
| Research is missing | No competitor section | Spend 20 minutes. Name 3 competitors. Find 2 Reddit threads. Done. |
| Stack is unclear | "Some kind of web app" | Pick one: Next.js? Astro? Rails? Why? |

---

## After Pitch: What Happens

Once the pitch is in SHIPWRIGHT.md, it moves with you through every phase.

**Build phase:** Read the pitch first. Before you write a ticket, ask: "Does this match the spirit of the pitch?" If execution drifts, either update the pitch (if it's a good pivot) or cut the scope (if it's creep).

**Ship phase:** Read the pitch again. "The success metric is people using this for its core job: building routine with their kid. What's one thing that proves we nailed that?"

**Learn phase:** Compare what you built to what you pitched. "We said 2 weeks. We shipped in 10 days. We said daily check-in would be the core interaction. Users use it for that 80% of the time. We also built weekly summary (not in pitch) and users love it. What does that mean for cycle 2?"

The pitch is the contract. Execution is the evidence. Learning is the synthesis.

---

## References

- See `shape.md` for how the pitch gets created
- See `state-model.md` for where the pitch lives (SHIPWRIGHT.md)
- See `personality.md` for language rules (simple English, no jargon)
