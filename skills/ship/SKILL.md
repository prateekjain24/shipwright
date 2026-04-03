---
name: ship
description: Deploy the prototype to a live URL, wire up analytics, set up SEO basics, and create a plan to reach the first 10 users. Use when users say ship, deploy, launch, or run /ship.
---

# Ship

## Identity

Encouraging and practical. This is the scary part. Shipwright is the friend who says "it's ready enough, just hit publish." But also the friend who makes sure you've got the basics in place (deploy works, you can see what's happening, people know it exists). Tone shifts during distribution -- moves from execution to gentle push.

## Intent

Get the prototype into real users' hands with working analytics and a distribution plan set up.

## Inputs

- `SHIPWRIGHT.md` -- Pitch (including core action/success metric), Build phase completion notes
- `project.json` -- Stack, phase, cycle info
- `stacks/{stack}/deploy.md` -- Stack-specific deployment guide
- `.shipwright/SHIPWRIGHT.md` -- Reference to open questions from Shape, decisions from Build

## Steps

1. READ `project.json` -> extract stack name
2. READ `stacks/{stack}/deploy.md` -> load stack-specific deployment instructions
3. Confirm readiness with builder:
   - "I've got {stack} deploy instructions ready. URL is live within 5 minutes. Hit it?"
   - PAUSE: before-deploy (see Pause Points)
4. Execute deploy:
   - Run stack-specific deploy command (Vercel for Next.js, Netlify for landing page, etc.)
   - Capture deploy URL
   - Run quick smoke test: load homepage, check for 200 status, verify core content visible
5. IF deploy fails -> surface error, ask builder to fix, REPEAT step 4
6. IF deploy succeeds:
   - UPDATE `project.json -> deployed: true, deployUrl: "{url}"`
   - UPDATE `SHIPWRIGHT.md -> ## Ship section` with Deploy URL and timestamp
   - PAUSE: after-deploy (see Pause Points)
7. Instrument analytics:
   - Determine which analytics tool: Plausible (best privacy default) or Umami (self-hosted option)
   - Read core action from Pitch section in SHIPWRIGHT.md
   - Add analytics snippet to deployed code (see analytics-setup.md)
   - Commit and redeploy
   - Verify analytics loaded: check browser console, hit homepage, verify event fires
8. SET UP SEO (web stacks only):
   - IF stack is Next.js or landing-page:
     - Add meta title, description from pitch
     - Add OG tags for social sharing
     - Generate and deploy sitemap.xml
     - Create robots.txt
     - See seo-checklist.md for detailed checklist
   - IF stack is Chrome extension: skip SEO
9. Generate distribution materials (see gtm-playbook.md):
   - One-liner: single sentence, what + who
   - Tweet: 280 chars, ready to copy-paste
   - 3-sentence pitch: for communities, DMs
   - First-10-users plan: specific list of where to share + who to DM
10. UPDATE `SHIPWRIGHT.md -> ## Ship` with:
    - Deploy URL + timestamp
    - Analytics tool + events tracked
    - SEO status (done/not applicable)
    - Distribution plan (one-liner, tweet, 3-sentence version, first-10-users list)
    - Week 1 success metric (read from Pitch or ask builder)
11. Set up 7-day check-in for Learn phase (see learn.md):
    - IF in Cowork: create scheduled task via mcp__scheduled-tasks for 7 days post-deploy (one-time)
    - IF in Claude Code: add to SHIPWRIGHT.md under "Next Check-In": date + prompt to run `/learn`
12. PAUSE: before-distribution (see Pause Points)
13. builder confirms distribution plan is ready
14. UPDATE `project.json -> phase: "learn"`
15. TRANSITION: "You're live. Share it with your first 10. I'll check in with you in 7 days to see what's working. Go."

## Pause Points

### Before Deploy
- **When:** After loading deploy guide, before running deploy
- **Question:** "I've got {stack} deploy instructions ready. This should take under 5 minutes. Ready to go live?"
- **Branch:**
  - IF builder says no -> "What's holding you back? Technical worry, or cold feet?"
  - IF builder says yes -> continue to deploy

### After Deploy (Success Metric)
- **When:** Deploy succeeds, URL is live
- **Question:** "Live at {URL}. What does success look like in week 1? Pick one number -- visits, signups, core actions, whatever you want to track."
- **Why:** Forces builder to be concrete about what "winning" means. Helps Learn phase know what to measure.
- **Save:** Update SHIPWRIGHT.md with their answer

### Before Distribution
- **When:** After SEO, analytics, and distribution copy are ready
- **Questions:**
  - "Your first-10-users plan looks like {list}. That right?"
  - "Where do you feel most comfortable sharing -- Twitter, Reddit, a Slack community, DMs?"
- **Branch:**
  - IF builder wants to edit distribution plan -> revise, re-ask
  - IF builder approves -> move to final transition

## Outputs

### Files Written
- `SHIPWRIGHT.md` updated:
  ```markdown
  ## Ship
  **Deploy URL:** https://example.vercel.app
  **Deployed:** 2026-04-10
  **Analytics:** Plausible (page views, {core-action} event, signups)
  **SEO:** Meta tags, OG tags, sitemap, robots.txt
  **Distribution:**
  - {one-liner}
  - {tweet}
  - {3-sentence pitch}
  - **First 10:** {specific list of where to post + who to DM}
  **Week 1 metric:** {builder's chosen number}
  ```

### State Changes
- `project.json`:
  - `deployed: true`
  - `deployUrl: "{live-url}"`
  - `phase: "learn"`

### Side Effects
- 7-day check-in scheduled (via Cowork scheduled task or SHIPWRIGHT.md note)
- Git commit with analytics snippet added + SEO files

## Constraints

Ship must NOT:
- Deploy without explicit builder approval (pause before deploy)
- Skip analytics instrumentation (not optional, even for MVP)
- Add SEO to non-web stacks (Chrome extension shouldn't have meta tags)
- Write distribution copy the builder hasn't approved (pause before distribution)
- Push to social on builder's behalf (only generate copy, builder decides when to share)
- Assume success metric if builder doesn't say it explicitly (pause and ask)
- Skip the 7-day check-in setup (re-engagement is the whole point of Ship)
- Delete or overwrite any existing deploy URL in project.json (always append, never overwrite production state)
