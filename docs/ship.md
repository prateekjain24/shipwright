# Phase 3: SHIP

**Purpose:** Get the prototype into real users' hands. A URL someone can visit. Analytics so you know what happens. A plan for who sees it first.

**Mode:** Execute (with a shift to encouraging during distribution). Deploy is mechanical. Distribution is where the builder needs a push. See [behavioral-contract.md](behavioral-contract.md).

**Why this is in V1:** Code on a laptop is not a product. The job isn't "help me write code" -- it's "help me learn if this thing is worth building." You can't learn without users. You can't get users without shipping.

---

## Sub-Phases

### 3a. Deploy

Uses `stacks/{stack}/deploy.md` for stack-specific instructions.

**What Shipwright does:**
- Reads the deploy guide for the selected stack
- Runs the deploy commands (e.g., `vercel --prod`)
- Verifies the deploy succeeded (checks URL, runs a basic smoke test)
- Saves the deploy URL to project.json and SHIPWRIGHT.md

**Key principle:** Deploy should take under 5 minutes. If it's taking longer, the stack guide is wrong.

**V1 stacks with deploy support:**
- Next.js -> Vercel
- Landing page -> Vercel/Netlify
- Chrome extension -> build + zip (manual Chrome Web Store upload)

### 3b. Analytics

Instrument core events so the builder can actually learn from usage.

**Minimal instrumentation (V1):**
- Page views (which pages, how often)
- Core action (the one thing the builder wants users to do)
- Sign-ups (if auth exists)

**How Shipwright does it:**
- Adds a lightweight analytics snippet (Plausible, Umami, or simple custom events)
- Instruments the core action identified during Shape ("What's the one thing you want users to do?")
- Sets a baseline: "You'll be able to see if anyone does X within the first week."

**Not in V1:** Amplitude/Mixpanel integrations, funnel analysis, cohort tracking. Those come in V4 with MCP connectors.

### 3c. SEO (web stacks only)

Basic SEO so the product is discoverable:
- Meta title and description (from the pitch)
- Open Graph tags (for social sharing)
- Sitemap and robots.txt
- Structured data where relevant

### 3d. Distribution

This is where most side projects die. They ship and then... nothing. Shipwright helps with the first push.

**What Shipwright generates:**
- **One-liner:** A single sentence describing what this does and for whom
- **Tweet/post:** Ready to copy-paste for Twitter/X, Reddit, HN
- **3-sentence pitch:** For sharing in communities or DMs
- **First-10-users plan:** A specific list of where to share and who to tell

**Reflection pause (Ship-specific):**
- "What does success look like in week 1? Pick one number."
- "Who are the first 10 people you'll share this with? Name them."
- "What's the one thing you want users to do on first visit?"
- "What would make you decide this wasn't worth it?"

---

## Ship Output

Updates to SHIPWRIGHT.md:
```markdown
## Ship
**Deploy URL:** https://habit-tracker.vercel.app
**Deployed:** 2026-04-10
**Analytics:** Plausible (page views + habit-created event)
**SEO:** Meta tags, OG image, sitemap
**Distribution plan:**
- Share in r/parenting (done)
- DM 5 parent friends (done)
- Post in Singapore parents WhatsApp group (pending)
**Week 1 success metric:** 10 habits created by non-me users
```

Updates to project.json:
- `deployed: true`
- `deployUrl: "https://..."`
- `phase: "learn"`

---

## Transition to Learn

After deploy, Shipwright sets up the re-engagement loop (see [learn.md](learn.md)):

- If in Cowork: offers to create a scheduled task that checks in after 7 days
- If in Claude Code: adds a note to SHIPWRIGHT.md with a reminder date
- Either way: "I'll check in with you in a week to see how it's going. In the meantime, share it with your first 10 users."
