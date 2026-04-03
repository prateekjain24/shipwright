# Landing Page Setup

Pick the right tech for your scale and needs. Astro for multi-page sites. Plain HTML + Tailwind for single pages or quick waitlist tests.

---

## When to Use What

| Need | Pick | Why |
|------|------|-----|
| Single page waitlist (validation test) | Plain HTML + Tailwind | Fastest. No build step. Deploy in 10 minutes. |
| Multi-page (blog, pricing pages, docs) | Astro | Fast by default. Partial hydration. SEO built in. |
| Dynamic content (user accounts, etc) | Next.js instead | Landing pages don't need server state. |

---

## Plain HTML + Tailwind (Fastest)

**When to use:** Fake door tests, waitlists, landing page validation tests.

### Init

```bash
mkdir landing-page && cd landing-page
npm init -y
npm install -D tailwindcss @tailwindcss/cli
```

Note: Tailwind v4 uses CSS-first config. No `tailwind.config.js` needed by default. Import Tailwind in your CSS with `@import "tailwindcss";`.

### File structure

```
landing-page/
  index.html           # Hero, waitlist form, CTA
  style.css            # Tailwind source (import + custom styles)
  dist/style.css       # Tailwind output (generated)
  script.js            # Form handling
  package.json
```

### style.css

```css
@import "tailwindcss";
```

### package.json scripts

```json
{
  "scripts": {
    "dev": "npx @tailwindcss/cli -i ./style.css -o ./dist/style.css --watch",
    "build": "npx @tailwindcss/cli -i ./style.css -o ./dist/style.css"
  }
}
```

### index.html structure

Start with this skeleton. Fill in content for your product.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Your Product Name</title>
  <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
</head>
<body class="bg-white text-gray-900">
  <!-- Hero -->
  <section class="min-h-screen flex items-center justify-center px-4 bg-gradient-to-br from-blue-50 to-white">
    <div class="max-w-2xl text-center">
      <h1 class="text-5xl font-bold mb-6">Your headline here</h1>
      <p class="text-xl text-gray-600 mb-8">One sentence problem statement</p>
      <button class="bg-blue-600 text-white px-8 py-3 rounded-lg font-semibold hover:bg-blue-700">
        Join the waitlist
      </button>
    </div>
  </section>

  <!-- Waitlist Form (Modal or inline) -->
  <div id="modal" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center">
    <form id="waitlist-form" class="bg-white p-8 rounded-lg shadow-xl max-w-md w-full">
      <h2 class="text-2xl font-bold mb-4">Get early access</h2>
      <input type="email" id="email" placeholder="you@example.com" class="w-full px-4 py-2 border rounded-lg mb-4" required>
      <button type="submit" class="w-full bg-blue-600 text-white py-2 rounded-lg font-semibold hover:bg-blue-700">
        Join
      </button>
      <p class="text-sm text-gray-500 mt-4">No spam. Unsubscribe anytime.</p>
    </form>
  </div>

  <!-- Problem section -->
  <section class="py-16 px-4 bg-gray-50">
    <div class="max-w-4xl mx-auto">
      <h2 class="text-3xl font-bold mb-8">The problem</h2>
      <p class="text-lg text-gray-600">Describe what users struggle with today.</p>
    </div>
  </section>

  <!-- Solution section -->
  <section class="py-16 px-4">
    <div class="max-w-4xl mx-auto">
      <h2 class="text-3xl font-bold mb-8">How we fix it</h2>
      <p class="text-lg text-gray-600">Describe your approach in one sentence.</p>
    </div>
  </section>

  <!-- CTA section -->
  <section class="py-16 px-4 bg-blue-600 text-white text-center">
    <h2 class="text-3xl font-bold mb-4">Ready to try it?</h2>
    <button class="bg-white text-blue-600 px-8 py-3 rounded-lg font-semibold hover:bg-gray-100">
      Join the waitlist
    </button>
  </section>

  <!-- Footer -->
  <footer class="py-8 px-4 bg-gray-100 text-center text-gray-600">
    <p>&copy; 2026 Your Company. No spam, no BS.</p>
  </footer>

  <script src="script.js"></script>
</body>
</html>
```

### script.js (Form handling + email capture)

```javascript
// Open/close modal
document.querySelectorAll('button').forEach(btn => {
  if (btn.textContent.includes('waitlist')) {
    btn.addEventListener('click', () => {
      document.getElementById('modal').classList.remove('hidden');
    });
  }
});

// Form submission
document.getElementById('waitlist-form').addEventListener('submit', async (e) => {
  e.preventDefault();
  const email = document.getElementById('email').value;

  // Send to email service (see Email Capture Setup below)
  try {
    const res = await fetch('/api/waitlist', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email })
    });

    if (res.ok) {
      alert('Thanks! Check your email.');
      document.getElementById('modal').classList.add('hidden');
      document.getElementById('waitlist-form').reset();
    } else {
      alert('Something went wrong. Try again.');
    }
  } catch (err) {
    console.error(err);
    alert('Network error. Try again.');
  }
});
```

---

## Astro (Multi-page)

**When to use:** Landing page + blog, pricing page, docs, or anything with multiple routes.

### Init

```bash
npm create astro@latest landing-page -- --template minimal --typescript --no-install
cd landing-page
npm install
```

### File structure

```
landing-page/
  src/
    layouts/
      Base.astro       # Shared header, footer, meta
    pages/
      index.astro      # Hero, waitlist form
      pricing.astro    # Pricing table (if needed)
      blog/
        [slug].astro   # Blog posts
    components/
      Waitlist.astro   # Reusable form
      Hero.astro
      Problem.astro
    api/
      waitlist.ts      # API handler
  public/
    images/
      logo.svg
      hero.jpg
  astro.config.mjs
  tailwind.config.cjs
```

### astro.config.mjs

```javascript
import { defineConfig } from 'astro/config';
import tailwind from '@astrojs/tailwind';

export default defineConfig({
  integrations: [tailwind()],
  output: 'server', // Required for API routes (waitlist form). Use 'static' only if no API routes needed.
});
```

### src/pages/index.astro

```astro
---
import Base from '../layouts/Base.astro';
import Waitlist from '../components/Waitlist.astro';
import Hero from '../components/Hero.astro';
import Problem from '../components/Problem.astro';
---

<Base title="Your Product">
  <Hero />
  <Problem />
  <Waitlist />
</Base>
```

### src/components/Waitlist.astro

```astro
---
// Email capture form. See Email Capture Setup below.
---

<section class="py-16 px-4 bg-blue-600 text-white text-center">
  <h2 class="text-3xl font-bold mb-8">Get early access</h2>
  <form id="waitlist-form" class="max-w-md mx-auto">
    <input
      type="email"
      placeholder="you@example.com"
      class="w-full px-4 py-2 rounded mb-4 text-gray-900"
      required
    />
    <button
      type="submit"
      class="w-full bg-white text-blue-600 py-2 rounded font-semibold hover:bg-gray-100"
    >
      Join
    </button>
    <p class="text-sm mt-4">No spam. Unsubscribe anytime.</p>
  </form>
</section>

<script>
  document.getElementById('waitlist-form').addEventListener('submit', async (e) => {
    e.preventDefault();
    const email = (e.target.email as HTMLInputElement).value;

    try {
      const res = await fetch('/api/waitlist', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email })
      });

      if (res.ok) {
        alert('Thanks! Check your email.');
        (e.target as HTMLFormElement).reset();
      } else {
        alert('Something went wrong.');
      }
    } catch (err) {
      console.error(err);
      alert('Network error.');
    }
  });
</script>
```

### src/api/waitlist.ts

```typescript
import type { APIRoute } from 'astro';

const RESEND_API_KEY = import.meta.env.RESEND_API_KEY;

export const POST: APIRoute = async ({ request }) => {
  if (request.method !== 'POST') {
    return new Response(JSON.stringify({ error: 'Method not allowed' }), { status: 405 });
  }

  const { email } = await request.json();

  if (!email || !email.includes('@')) {
    return new Response(JSON.stringify({ error: 'Valid email required' }), { status: 400 });
  }

  // Send to Resend or your email service
  try {
    const res = await fetch('https://api.resend.com/emails', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${RESEND_API_KEY}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        from: 'noreply@yourdomain.com',
        to: email,
        subject: 'You are on the waitlist',
        html: '<p>Thanks for signing up! We will email you when we launch.</p>',
      }),
    });

    if (res.ok) {
      return new Response(JSON.stringify({ success: true }), { status: 200 });
    } else {
      return new Response(JSON.stringify({ error: 'Failed to send email' }), { status: 500 });
    }
  } catch (err) {
    console.error(err);
    return new Response(JSON.stringify({ error: 'Server error' }), { status: 500 });
  }
};
```

---

## Email Capture Setup

Never use mailto links. Use a real service.

### Option A: Resend (Recommended for builders)

Simple, free tier, good API.

**Setup:**

1. Go to [resend.com](https://resend.com)
2. Sign up with your email
3. Get API key from dashboard
4. Add your domain (or use resend subdomain for free)
5. Store key in `.env.local` (plain HTML) or Vercel env vars (Astro)

**Plain HTML:**

```bash
npm install resend
```

Create `api.js` or use a serverless function.

**Astro:**

Already covered in `src/api/waitlist.ts` above.

### Option B: Mailchimp (If you want a subscriber list UI)

**Setup:**

1. Go to [mailchimp.com](https://mailchimp.com)
2. Create audience
3. Get API key and audience ID
4. Use their API or Forms integration

**Gotcha:** Mailchimp signup confirmation emails are slow. Test before launching.

### Option C: Plain Email (Not Recommended)

If you must use plain email (no service):

```javascript
// This is SLOW and UNRELIABLE for validation tests
const mailto = `mailto:your-email@gmail.com?subject=New signup&body=${encodeURIComponent(email)}`;
window.location.href = mailto;
```

Skip this. Use Resend or Mailchimp. Takes 5 minutes to set up.

---

## Stripe Integration (If you have pricing)

Only add Stripe if you need to test payment. Don't add it for waitlist tests.

### Setup (Astro or plain HTML)

```bash
npm install @stripe/stripe-js
```

### Environment variables

Add to `.env.local`:

```
STRIPE_PUBLIC_KEY=pk_test_...
STRIPE_SECRET_KEY=sk_test_...
```

### Create a Checkout Session

```typescript
// In Astro API route or serverless function
import Stripe from 'stripe';

const stripe = new Stripe(import.meta.env.STRIPE_SECRET_KEY);

export const POST: APIRoute = async ({ request }) => {
  const { priceId } = await request.json();

  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    line_items: [{ price: priceId, quantity: 1 }],
    mode: 'payment',
    success_url: `${import.meta.env.SITE}/success`,
    cancel_url: `${import.meta.env.SITE}/pricing`,
  });

  return new Response(JSON.stringify({ url: session.url }), { status: 200 });
};
```

**See deploy.md for webhook setup.**

---

## Build and Test Locally

Plain HTML:

```bash
npm run dev
# Open http://localhost:3000
```

Astro:

```bash
npm run dev
# Open http://localhost:3000
```

Test form submission locally. Make sure email capture logs are visible in the browser console.

---

## Deployment Commands

See deploy.md for full setup. Quick version:

**Vercel:**

```bash
npm i -g vercel
vercel
```

**Netlify:**

```bash
npm i -g netlify-cli
netlify deploy --prod
```

---

## Key Rules for Build Skill

1. Plain HTML builds in seconds. Use for fast validation tests.
2. Astro takes 1 minute to init. Use if you need multiple pages.
3. Always set up email capture BEFORE deployment. Don't deploy a form that doesn't work.
4. Store secrets in environment variables. Never commit API keys.
5. Test form locally with console.log() to debug CORS or fetch errors.
