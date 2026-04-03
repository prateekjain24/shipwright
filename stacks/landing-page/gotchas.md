# Landing Page Gotchas

Things that will waste an hour if you don't know them. Or you'll launch with a broken form. Or Stripe will silently fail. Read this before deploying.

---

## Stripe Integration Pitfalls

### Test vs Live Keys

Stripe gives you two sets of keys:

| Key | Use | Looks like |
|-----|-----|-----------|
| Test key | Development, testing, validation | `pk_test_...` |
| Live key | Real payments | `pk_live_...` |

**Gotcha:** If you use test keys in production, no real money changes hands. Users see the form, click "Buy", then nothing happens. You think no one's buying. Actually, your keys are wrong.

**Fix:**
1. In development, always use `pk_test_` and `sk_test_`
2. Before launch, go to Stripe dashboard and get `pk_live_` and `sk_live_`
3. Add to Vercel/Netlify as environment variables
4. Test with a real card (small amount like $1)
5. Check Stripe dashboard that the charge succeeded

### Webhook Signing Secret

Stripe sends a webhook to your server when a payment succeeds. The webhook includes a signature. You must verify the signature.

**Gotcha:** If you skip signature verification, attackers can fake webhooks. They can "confirm" a payment that never happened.

**Don't do this:**

```typescript
// WRONG. Don't verify signature.
const event = JSON.parse(await request.text());
if (event.type === 'checkout.session.completed') {
  markUserAsPaid(event.data.object.customer_email);
}
```

**Do this:**

```typescript
// RIGHT. Verify signature first.
const signature = request.headers.get('stripe-signature');
const body = await request.text();

const event = stripe.webhooks.constructEvent(
  body,
  signature,
  webhookSecret // From environment variables
);

if (event.type === 'checkout.session.completed') {
  markUserAsPaid(event.data.object.customer_email);
}
```

### Webhook Delivery Failures

Stripe sends webhooks to your endpoint. If your endpoint is slow or returns a 500 error, Stripe retries for 3 days.

**Gotcha:** You think payments aren't confirmed because the webhook isn't arriving. Actually, your API is crashing.

**Fix:**
1. Check Vercel/Netlify logs for errors: `vercel logs --follow`
2. Go to Stripe dashboard > Developers > Webhooks > Your endpoint > Events
3. Click an event and look at "Request body" and "Response"
4. If response is 500, your function crashed. Fix it and re-deploy
5. Stripe will retry automatically

### Test Payment Card Numbers

When testing, use Stripe's test cards, not your real card.

| Card | Use |
|------|-----|
| 4242 4242 4242 4242 | Successful payment (any future expiry, any CVC) |
| 4000 0000 0000 0002 | Payment declined |
| 4000 0025 0000 3155 | 3D Secure (2FA) |

**Gotcha:** If you test with your real card, you'll charge yourself. Go check your credit card statement.

---

## Email Capture Pitfalls

### Don't Use mailto Links

```html
<!-- WRONG. This doesn't work. -->
<a href="mailto:your-email@gmail.com?subject=New signup&body=email@example.com">
  Sign up
</a>
```

Why:
- Requires user to have email client installed
- Email client opens on user's computer
- No confirmation email sent to the user
- For validation tests, you won't know if signup worked

**Do this:** Use Resend, Mailchimp, or similar.

### Resend API Errors (Silent Failures)

When you call Resend to send an email, the API might return 200 OK but the email still fails.

**Gotcha:** Form says "Thanks! Check your email." User never gets an email. They think you're spam.

**Fix:**

```typescript
const res = await fetch('https://api.resend.com/emails', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${RESEND_API_KEY}`,
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    from: 'noreply@yourdomain.com',  // MUST be your domain
    to: email,
    subject: 'You are on the waitlist',
    html: '<p>Thanks for signing up!</p>',
  }),
});

const data = await res.json();

// ALWAYS check the response
if (!res.ok) {
  console.error('Resend error:', data);
  return new Response(JSON.stringify({ error: data.message }), { status: 500 });
}

console.log('Email sent. Message ID:', data.id);
return new Response(JSON.stringify({ success: true }), { status: 200 });
```

### From Address Must Be Your Domain

If you use Resend, the "from" address must be your domain:

**Wrong:**
```javascript
from: 'noreply@gmail.com'  // Won't work
```

**Right:**
```javascript
from: 'noreply@yourdomain.com'  // Works
```

Go to Resend dashboard > Domain Management > Add domain > Verify with DNS record.

### Mailchimp Confirmation Emails Are Slow

Mailchimp sends a confirmation email: "Confirm your subscription by clicking this link."

**Gotcha:** User signs up. You expect them to get email instantly. Mailchimp takes 5-10 minutes to send it. User thinks nothing happened.

**Fix:** Tell users to wait: "Check your email in a few minutes to confirm."

Or use a service that sends emails instantly (Resend, SendGrid, etc).

---

## CORS Issues

CORS means "Cross-Origin Resource Sharing." It's a browser security feature.

**Gotcha:** Form on `yourdomain.com` tries to POST to `/api/waitlist`. Browser blocks it with "CORS error."

Browser thinks: "This page is from yourdomain.com. It's trying to talk to... yourdomain.com? That's the same origin. Why is there an error?"

Actually, it's because your API endpoint is returning the wrong headers.

**Fix:**

For Astro, no fix needed. Routes in `src/pages/api/` automatically allow same-origin requests.

For plain HTML calling a Node.js or external API, add headers:

```javascript
response.setHeader('Access-Control-Allow-Origin', '*');
response.setHeader('Access-Control-Allow-Methods', 'POST, GET');
response.setHeader('Access-Control-Allow-Headers', 'Content-Type');
```

Or use a library like `cors`:

```javascript
import cors from 'cors';

app.use(cors());
```

---

## Image Optimization

### Images Make Landing Pages Slow

One 1MB hero image can slow your page from 2 seconds to 8 seconds.

**Gotcha:** You test locally. Page loads instantly. You deploy. Users on 4G see blank page for 5 seconds. They leave.

**Fix:**

1. **Compress images.** Use tinypng.com or imageoptim.com
   - Hero image: < 200KB
   - Section images: < 100KB each
   - Use WebP format if browser supports it

2. **Use srcset for responsive images:**

```html
<img
  src="images/hero.jpg"
  srcset="images/hero-sm.jpg 640w, images/hero-lg.jpg 1200w"
  sizes="(max-width: 640px) 100vw, 1200px"
  alt="Hero"
/>
```

3. **Lazy load images below the fold:**

```html
<img loading="lazy" src="images/section2.jpg" alt="..." />
```

4. **Use Next.js Image or Astro Image component:**

```astro
<Image
  src={heroImage}
  alt="Hero"
  width={1200}
  height={800}
/>
```

These auto-optimize and resize images for you.

### Vercel Image Optimization

Vercel automatically compresses images when you deploy. But you still should compress locally.

Test page speed:
```bash
# Locally:
npm run build
npm run preview
```

Then open Chrome DevTools > Lighthouse. Run performance audit. Target 90+.

---

## Mobile Responsiveness

### Gotcha: Desktop-first design

You build a beautiful landing page on your laptop. Looks great on 1440px width. You deploy. Half your users are on mobile (375px). Layout falls apart.

**Fix:**

1. **Mobile first.** Design for 375px first. Then add styles for bigger screens.

```css
/* Mobile first */
.hero h1 {
  font-size: 32px;
}

/* Desktop */
@media (min-width: 768px) {
  .hero h1 {
    font-size: 48px;
  }
}
```

2. **Test on real phones.** Don't just resize browser. Use actual phone or Android emulator.

3. **Use Tailwind responsive classes:**

```html
<!-- 32px on mobile, 48px on desktop -->
<h1 class="text-3xl md:text-5xl">
  Headline
</h1>
```

4. **Watch out for form inputs on mobile.**

```html
<!-- WRONG. Too small on mobile -->
<input type="email" class="px-4 py-2" />

<!-- RIGHT. Bigger tap target on mobile -->
<input type="email" class="px-4 py-3" />
```

Tap targets should be at least 44px tall.

### Common mobile breaks

- Form input too small (< 44px height)
- Buttons too small (< 48px)
- Text too small (< 16px)
- No padding on edges (content touches screen edge)
- Footer links stack vertically but are too wide

---

## Form Submission Errors

### Gotcha: Form submits but no response shown

User clicks "Join". Nothing happens. They think the button is broken. They click again. Form submits twice.

**Fix:**

1. **Show loading state:**

```html
<button id="submit-btn">Join</button>

<script>
  form.addEventListener('submit', async (e) => {
    e.preventDefault();
    btn.disabled = true;
    btn.textContent = 'Joining...';
    // ... submit ...
    btn.disabled = false;
    btn.textContent = 'Join';
  });
</script>
```

2. **Show success/error message:**

```html
<div id="message"></div>

<script>
  const msg = document.getElementById('message');

  if (res.ok) {
    msg.textContent = '✓ Check your email!';
    msg.className = 'text-green-600';
  } else {
    msg.textContent = '✗ Try again';
    msg.className = 'text-red-600';
  }
</script>
```

### Gotcha: Form resets on page navigation

User submits form. Page reloads. Form clears. User sees a blank form and thinks nothing happened.

**Fix:**

Don't reload the page after form submission. Just show a success message:

```javascript
// WRONG
fetch('/api/waitlist').then(() => {
  location.reload();  // Page reloads, confusing
});

// RIGHT
fetch('/api/waitlist').then(() => {
  btn.textContent = '✓ Check your email!';  // Success shown, page stays
});
```

---

## Common Deploy Failures

### API route doesn't exist

**Gotcha:** Form tries to POST to `/api/waitlist`. You get 404 error. "Not Found."

**Fix:**
- For Astro: Create `src/pages/api/waitlist.ts`
- For plain HTML: You need a backend (Vercel Functions, Netlify Functions, etc)
- Check that the file path is exactly right

### Environment variables not set

**Gotcha:** Form submits. API tries to call Resend. API crashes because `RESEND_API_KEY` is undefined.

**Fix:**
1. Create `.env.local` locally
2. Add to Vercel dashboard under Environment Variables
3. Re-deploy: `vercel --prod`
4. Check logs: `vercel logs`

### Astro output setting wrong

**Gotcha:** Astro deployed to Vercel but forms don't work. API routes return 404.

**Fix:**

Make sure `astro.config.mjs` has:

```javascript
export default defineConfig({
  output: 'server',  // NOT 'static'
});
```

If `output: 'static'`, Astro doesn't build API routes.

### Site builds locally but fails on deploy

**Gotcha:** `npm run build` works locally. Deploy fails with "build error."

**Fix:**
1. Check Vercel build logs
2. Common issues:
   - Missing dependency in package.json
   - TypeScript error (strict mode)
   - Missing environment variable
3. Run `npm install` and `npm run build` locally to debug

---

## Validation Test Best Practices

### Test form before promoting

Don't post your landing page link to Twitter yet.

**Before going public:**

1. Fill form yourself. Get confirmation email.
2. Open in incognito window. Fill form. Get email.
3. Test on phone. Fill form. Get email.
4. Check Stripe/Resend dashboard that events were logged.
5. Wait 24 hours. Check for any errors in logs.

### Track utm parameters

If you're posting to Twitter, Reddit, etc:

```
https://yourdomain.com?utm_source=twitter&utm_campaign=launch
```

This tells you which posts drive signups. Add to your landing page tracking.

### Monitor email deliverability

Some providers have strict spam filters.

**Test:**
- Send to Gmail, Outlook, Yahoo addresses
- Check spam folder
- Gmail marks as "via resend.com" -- that's okay

If emails go to spam:
- Check Resend dashboard for bounce/complaint rates
- Ask Resend support to warm up your domain
- Takes a few days to build sender reputation

---

## Security Basics

### Validate email format

Never trust the browser. Always validate on the server.

```typescript
// Check email format
const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

if (!emailRegex.test(email)) {
  return new Response(JSON.stringify({ error: 'Invalid email' }), { status: 400 });
}
```

### Rate limit form submissions

If form has no rate limit, attackers can spam your waitlist with 10,000 signups in 5 seconds.

**Fix:**

For Vercel/Netlify, add rate limiting middleware:

```typescript
// Simple rate limit by IP
const rateLimit = new Map();

export const POST: APIRoute = async ({ request }) => {
  const ip = request.headers.get('x-forwarded-for') || 'unknown';

  if (rateLimit.has(ip) && rateLimit.get(ip) > Date.now()) {
    return new Response(JSON.stringify({ error: 'Too many requests' }), { status: 429 });
  }

  // Allow 5 requests per hour per IP
  rateLimit.set(ip, Date.now() + 3600000);

  // ... rest of form handling ...
};
```

Or use a library like `p-ratelimit`.

### Never log passwords or API keys

If you add logging to debug issues, never log sensitive data:

```typescript
// WRONG
console.log('API key:', import.meta.env.RESEND_API_KEY);

// RIGHT
console.log('API key:', import.meta.env.RESEND_API_KEY?.slice(0, 10) + '...');
```

---

## Key Rules for Build Skill

1. Test the entire flow before deploying: fill form, get email, check confirmation.
2. Use test Stripe keys for validation. Switch to live keys before real payments.
3. Verify webhook signatures. Don't trust the signature, trust nothing.
4. Compress all images. Hero < 200KB. Sections < 100KB.
5. Mobile test on real phone or emulator. Not just browser resize.
6. Show loading state on form submission. User needs feedback.
7. Add environment variables to Vercel/Netlify before deploying API code.
8. Check logs after deploy. `vercel logs` or Netlify dashboard.
9. Email from address must be your domain (for Resend).
10. Rate limit form submissions to prevent spam.

---

## Resend Domain Verification (2026)

Resend now requires DKIM + SPF setup before sending from your domain in production.

**Steps:**
1. Add your domain in Resend dashboard > Domain Management
2. Add the DNS records Resend gives you (DKIM, SPF, DMARC)
3. Wait for verification (usually a few minutes, sometimes hours)
4. Test send to Gmail, Outlook, Yahoo before launch
5. Monitor bounce rates in Resend dashboard (target: under 2%)

Without domain verification, emails go to spam or don't send at all.

---

## Tailwind v4 Changes

Tailwind v4 uses CSS-first config. No `tailwind.config.js` needed.

**Old way (v3):**
```bash
npx tailwindcss init
# Edit tailwind.config.js
```

**New way (v4):**
```css
/* style.css */
@import "tailwindcss";
```

For Astro, use `@tailwindcss/vite` plugin instead of PostCSS:
```bash
npm install @tailwindcss/vite
```

If using the CDN for quick prototyping, the new URL is:
```html
<script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
```

---

## Payment Links vs Checkout Sessions

Stripe offers two approaches. Pick one, don't mix them.

| Approach | Best for | Expiry | Code needed |
|----------|----------|--------|-------------|
| Payment Links | Static pricing, one product | Never expires | None (create in Stripe dashboard) |
| Checkout Sessions | Dynamic pricing, custom flows | 24 hours | API integration required |

For simple landing pages with fixed pricing, Payment Links are faster. Create in Stripe dashboard, embed the link. No webhook handling needed.

---

## Form Double Submission

Users on slow connections click submit twice. This creates duplicate entries.

**Fix:** Disable the button during submission AND debounce the handler:

```javascript
let submitting = false;

form.addEventListener('submit', async (e) => {
  e.preventDefault();
  if (submitting) return;
  submitting = true;
  btn.disabled = true;
  btn.textContent = 'Joining...';

  try {
    await fetch('/api/waitlist', { method: 'POST', ... });
    btn.textContent = 'Done!';
  } catch (err) {
    btn.textContent = 'Try again';
    btn.disabled = false;
    submitting = false;
  }
});
```
