# Analytics Setup

Instrument your project so you can see what's happening. You need to know:
1. How many people visit?
2. Do they do the one thing you want them to do? (core action)
3. Do they come back?

This is lightweight instrumentation. Not Amplitude. Not Mixpanel. Just enough to learn.

---

## Pick Your Tool

| Tool | Best for | Self-hosted? | Privacy | Setup time |
|------|----------|-----------|---------|-----------|
| **Plausible** | Privacy-first default | No (SaaS) | GDPR compliant, minimal cookies | 5 min |
| **Umami** | Low cost, self-hosted | Yes (on your server) | Private, you own data | 15 min |

### Plausible
- Simple, reliable, works everywhere
- No cookie banner needed (privacy by default)
- Costs money ($9/month or ~$90/year for side projects)
- Best for: first project, don't want infrastructure headache

### Umami
- Self-hosted on your own server
- Free (or ~$5/month if you use a provider)
- Privacy by design
- Best for: already hosting something, want full control, costs matter

**Recommendation for Ship:** Start with Plausible. It's simpler. You can migrate to Umami later if you want.

---

## Add the Analytics Snippet (Plausible)

1. Sign up at https://plausible.io (select your domain, create account)
2. Go to settings -> "Script setup"
3. Copy the script tag (looks like `<script defer data-domain="yourdomain.com" src="..."></script>`)
4. Add it to your `<head>` element (after your meta tags, before closing `</head>`)

### Example (Next.js in `pages/_app.js` or `app/layout.js`):
```jsx
export default function RootLayout({ children }) {
  return (
    <html>
      <head>
        <title>Your Title</title>
        <meta name="description" content="..."/>
        <script defer data-domain="yoursite.com" src="https://plausible.io/js/script.js"></script>
      </head>
      <body>{children}</body>
    </html>
  )
}
```

### Example (plain HTML):
```html
<!DOCTYPE html>
<html>
  <head>
    <title>Your Title</title>
    <meta name="description" content="..."/>
    <script defer data-domain="yoursite.com" src="https://plausible.io/js/script.js"></script>
  </head>
  <body>
    <!-- your content -->
  </body>
</html>
```

---

## What Events to Track

### Event 1: Page Views (Automatic)
Plausible tracks page views automatically. You don't need to do anything. Just deploy.

### Event 2: Core Action (Custom)
This is the one thing you want users to do (from your Pitch in SHIPWRIGHT.md).

**Examples:**
- Habit tracker: "habit created" (user clicks "Add habit")
- Newsletter signup: "newsletter signup" (user submits email)
- Chrome extension: "extension installed" (once)

**How to trigger:**
```javascript
// Plausible event tracking
window.plausible && window.plausible('Habit Created');
```

Place this in your button click handler:
```jsx
<button onClick={() => {
  createHabit();
  window.plausible && window.plausible('Habit Created');
}}>
  Add Habit
</button>
```

### Event 3: Sign-ups (if applicable)
If your project has auth/signups, track when users create an account:
```javascript
window.plausible && window.plausible('Signup');
```

---

## How to Verify It's Working

1. **In Plausible dashboard:**
   - Open your Plausible account
   - You should see a graph with your recent visitors
   - Wait 10-30 seconds after visiting your site for the data to appear

2. **In browser console:**
   - Open DevTools (F12)
   - Go to Network tab
   - Visit your site
   - Look for a request to `plausible.io` or `events.plausible.io`
   - If you see it, tracking is live

3. **Trigger a custom event:**
   - Do the core action on your site (create a habit, sign up, etc.)
   - In Plausible, check "Events" -> you should see your custom event appear

---

## What You'll See in Plausible

Once you have traffic:

| Metric | What it means | Why it matters |
|--------|--------------|----------------|
| Visitors | Number of unique people | Growth indicator |
| Page views | Total page views | Engagement depth |
| Core action (custom event) | Number of times users did the thing | Product-market fit signal |
| Bounce rate | % of visitors who leave without action | Site quality signal |
| Session duration | How long visitors stay | Engagement quality |

### Example dashboard reading:
- 47 visitors this week
- 23 did the core action
- 48% bounce rate (people arriving but not engaging)
- 2 signups

**What this tells you:** The site is getting traffic. Some people are using it. But almost half aren't engaging. Time to look at Learn phase (learn.md) and ask users what's missing.

---

## One-Minute Troubleshooting

| Problem | Likely cause | Fix |
|---------|-------------|-----|
| No visitors showing up | Script not deployed yet | Wait 10-30 seconds, refresh Plausible dashboard |
| Script tag shows in page source but no data | Script blocked by ad blocker | Check in private/incognito window, or just ignore (real users matter more) |
| Custom event not firing | Event code not in right place | Check browser console for JavaScript errors, verify click handler is wired up |
| Bounce rate very high | Site not compelling | Save screenshots for Learn phase review |

---

## Analytics Philosophy

You're not trying to be a growth hacker. You just need to know:
1. Is anyone visiting? (page views)
2. Are they doing the thing? (core action metric)
3. How many are coming back? (return visitor %)

That's it. Everything else is Learn phase thinking.

Read [learn.md](../../../docs/learn.md) for how to use these numbers to decide what to do next.
