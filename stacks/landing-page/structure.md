# Landing Page Structure

Standard section layout and file organization for both plain HTML and Astro.

---

## Standard Section Layout

Every landing page has these sections in this order:

1. **Hero** -- Big headline, subheading, CTA button. Goal: grab attention in 3 seconds.
2. **Problem** -- Paint the pain. Who has it, what they do today, why it sucks.
3. **Solution** -- Show how you fix it. Can be text, image, or demo video.
4. **Pricing** -- (Optional) If you have pricing, show it here. Skip for waitlists.
5. **CTA** -- Final call to action. Join waitlist or buy now.
6. **Footer** -- Links, copyright, unsubscribe if needed.

---

## Plain HTML Structure

Use this tree for single-page HTML sites.

```
landing-page/
├── index.html              # All content in one file
├── script.js               # Form handling + analytics
├── style.css               # Tailwind output (git-ignore this)
├── tailwind.config.js      # Tailwind config
├── package.json
├── .gitignore              # Add dist/, node_modules/, .env.local
└── public/                 # (Optional) Static assets
    ├── images/
    │   ├── hero.jpg
    │   ├── problem.jpg
    │   └── logo.svg
    └── favicon.ico
```

### index.html with all sections

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="One sentence pitch">
  <title>Product Name</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-white text-gray-900 font-sans">

  <!-- Navigation (optional) -->
  <nav class="sticky top-0 bg-white border-b border-gray-200 px-4 py-3">
    <div class="max-w-6xl mx-auto flex justify-between items-center">
      <h1 class="text-xl font-bold">Product Name</h1>
      <a href="#cta" class="text-blue-600 hover:text-blue-700">Get Early Access</a>
    </div>
  </nav>

  <!-- Hero -->
  <section class="min-h-screen flex items-center justify-center px-4 py-20 bg-gradient-to-br from-blue-50 to-indigo-50">
    <div class="max-w-3xl text-center">
      <h1 class="text-6xl font-black mb-6 text-gray-900">
        Headline that says what you do
      </h1>
      <p class="text-xl text-gray-600 mb-8 leading-relaxed">
        Problem statement. One sentence that explains the pain point.
      </p>
      <button onclick="document.getElementById('modal').classList.remove('hidden')" class="bg-blue-600 text-white px-8 py-4 rounded-lg font-bold text-lg hover:bg-blue-700 transition">
        Join the waitlist
      </button>
      <p class="text-sm text-gray-500 mt-4">
        Add a reassurance: "No credit card required" or "Early access only"
      </p>
    </div>
  </section>

  <!-- Problem -->
  <section class="py-20 px-4 bg-gray-50">
    <div class="max-w-4xl mx-auto">
      <h2 class="text-4xl font-bold mb-12 text-center">The Problem</h2>
      <div class="grid md:grid-cols-2 gap-12 items-center">
        <div>
          <h3 class="text-2xl font-bold mb-4">What users do today</h3>
          <ul class="space-y-4 text-gray-600">
            <li class="flex gap-3">
              <span class="text-red-500 font-bold">✗</span>
              <span>Pain point 1</span>
            </li>
            <li class="flex gap-3">
              <span class="text-red-500 font-bold">✗</span>
              <span>Pain point 2</span>
            </li>
            <li class="flex gap-3">
              <span class="text-red-500 font-bold">✗</span>
              <span>Pain point 3</span>
            </li>
          </ul>
        </div>
        <img src="public/images/problem.jpg" alt="Problem illustration" class="rounded-lg shadow-lg">
      </div>
    </div>
  </section>

  <!-- Solution -->
  <section class="py-20 px-4">
    <div class="max-w-4xl mx-auto">
      <h2 class="text-4xl font-bold mb-12 text-center">How We Fix It</h2>
      <div class="grid md:grid-cols-2 gap-12 items-center">
        <img src="public/images/solution.jpg" alt="Solution demo" class="rounded-lg shadow-lg">
        <div>
          <h3 class="text-2xl font-bold mb-4">Here is how it works</h3>
          <ul class="space-y-4 text-gray-600">
            <li class="flex gap-3">
              <span class="text-green-500 font-bold">✓</span>
              <span>Benefit 1</span>
            </li>
            <li class="flex gap-3">
              <span class="text-green-500 font-bold">✓</span>
              <span>Benefit 2</span>
            </li>
            <li class="flex gap-3">
              <span class="text-green-500 font-bold">✓</span>
              <span>Benefit 3</span>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </section>

  <!-- Pricing (optional, skip for waitlists) -->
  <section class="py-20 px-4 bg-gray-50">
    <div class="max-w-4xl mx-auto">
      <h2 class="text-4xl font-bold mb-12 text-center">Simple Pricing</h2>
      <div class="grid md:grid-cols-3 gap-8">
        <!-- Plan 1 -->
        <div class="bg-white p-8 rounded-lg border border-gray-200 hover:shadow-lg transition">
          <h3 class="text-xl font-bold mb-4">Starter</h3>
          <p class="text-3xl font-bold mb-6">$29<span class="text-base font-normal text-gray-500">/mo</span></p>
          <ul class="space-y-3 mb-8 text-gray-600">
            <li>✓ Feature 1</li>
            <li>✓ Feature 2</li>
          </ul>
          <button onclick="checkout('price_starter')" class="w-full bg-blue-600 text-white py-2 rounded font-semibold hover:bg-blue-700">
            Get Started
          </button>
        </div>
      </div>
    </div>
  </section>

  <!-- Main CTA -->
  <section id="cta" class="py-20 px-4 bg-blue-600 text-white text-center">
    <div class="max-w-2xl mx-auto">
      <h2 class="text-4xl font-bold mb-6">Ready to get started?</h2>
      <p class="text-lg mb-8 opacity-90">Join 500+ early users on the waitlist.</p>
      <button onclick="document.getElementById('modal').classList.remove('hidden')" class="bg-white text-blue-600 px-8 py-4 rounded-lg font-bold text-lg hover:bg-gray-100 transition">
        Get Early Access
      </button>
    </div>
  </section>

  <!-- Footer -->
  <footer class="py-12 px-4 bg-gray-900 text-gray-400">
    <div class="max-w-4xl mx-auto grid md:grid-cols-4 gap-8 mb-8">
      <div>
        <h4 class="text-white font-bold mb-4">Product</h4>
        <ul class="space-y-2">
          <li><a href="#features" class="hover:text-white">Features</a></li>
          <li><a href="#pricing" class="hover:text-white">Pricing</a></li>
        </ul>
      </div>
      <div>
        <h4 class="text-white font-bold mb-4">Company</h4>
        <ul class="space-y-2">
          <li><a href="/blog" class="hover:text-white">Blog</a></li>
          <li><a href="/about" class="hover:text-white">About</a></li>
        </ul>
      </div>
      <div>
        <h4 class="text-white font-bold mb-4">Legal</h4>
        <ul class="space-y-2">
          <li><a href="/privacy" class="hover:text-white">Privacy</a></li>
          <li><a href="/terms" class="hover:text-white">Terms</a></li>
        </ul>
      </div>
      <div>
        <h4 class="text-white font-bold mb-4">Follow</h4>
        <ul class="space-y-2">
          <li><a href="https://twitter.com/..." class="hover:text-white">Twitter</a></li>
          <li><a href="https://github.com/..." class="hover:text-white">GitHub</a></li>
        </ul>
      </div>
    </div>
    <div class="border-t border-gray-700 pt-8 text-center">
      <p>&copy; 2026 Your Company. All rights reserved.</p>
      <p class="text-sm mt-2"><a href="/unsubscribe" class="hover:text-white">Unsubscribe from emails</a></p>
    </div>
  </footer>

  <!-- Waitlist Modal -->
  <div id="modal" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center px-4 z-50">
    <div class="bg-white p-8 rounded-xl shadow-2xl max-w-md w-full">
      <h2 class="text-2xl font-bold mb-2">Get early access</h2>
      <p class="text-gray-600 mb-6">Join the waitlist. We launch in Q3 2026.</p>

      <form id="waitlist-form" class="space-y-4">
        <input
          type="email"
          id="email"
          placeholder="your@email.com"
          class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600"
          required
        />
        <input
          type="text"
          id="name"
          placeholder="Your name (optional)"
          class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-600"
        />
        <button
          type="submit"
          class="w-full bg-blue-600 text-white py-3 rounded-lg font-bold hover:bg-blue-700 transition"
        >
          Join the waitlist
        </button>
      </form>

      <p class="text-xs text-gray-500 mt-4">
        We will not spam you. <a href="/privacy" class="underline">Privacy policy</a>
      </p>

      <button onclick="document.getElementById('modal').classList.add('hidden')" class="absolute top-4 right-4 text-gray-500 hover:text-gray-700">
        ✕
      </button>
    </div>
  </div>

  <!-- Close modal on background click -->
  <script>
    document.getElementById('modal').addEventListener('click', (e) => {
      if (e.target.id === 'modal') {
        document.getElementById('modal').classList.add('hidden');
      }
    });
  </script>

  <script src="script.js"></script>

</body>
</html>
```

---

## Astro Structure

Use this tree for multi-page sites.

```
landing-page/
├── src/
│   ├── layouts/
│   │   └── Base.astro              # Shared header, footer, meta tags
│   ├── pages/
│   │   ├── index.astro             # Home (hero, problem, solution, CTA)
│   │   ├── pricing.astro           # Pricing page (optional)
│   │   ├── privacy.astro           # Privacy policy
│   │   ├── blog/
│   │   │   ├── index.astro         # Blog listing
│   │   │   └── [slug].astro        # Blog post (dynamic)
│   │   └── api/
│   │       ├── waitlist.ts         # Email capture endpoint
│   │       └── checkout.ts         # Stripe checkout (optional)
│   ├── components/
│   │   ├── Hero.astro
│   │   ├── Problem.astro
│   │   ├── Solution.astro
│   │   ├── Pricing.astro
│   │   ├── Waitlist.astro          # Reusable form component
│   │   ├── Footer.astro
│   │   └── Nav.astro
│   ├── styles/
│   │   └── global.css              # Global styles (Tailwind imports)
│   └── types/
│       └── blog.ts                 # TypeScript types
├── public/
│   ├── images/
│   │   ├── hero.jpg
│   │   ├── problem.jpg
│   │   └── solution.jpg
│   ├── favicon.ico
│   └── robots.txt
├── astro.config.mjs
├── tailwind.config.cjs
├── tsconfig.json
├── package.json
└── .gitignore
```

### src/layouts/Base.astro

```astro
---
interface Props {
  title: string;
  description?: string;
}

const { title, description = "Your product tagline" } = Astro.props;
---

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="description" content={description} />
    <title>{title}</title>
    <link rel="icon" type="image/x-icon" href="/favicon.ico" />
  </head>
  <body>
    <slot />
  </body>
</html>

<style is:global>
  @import '@fontsource/inter/400.css';
  @import '@fontsource/inter/600.css';
  @import '@fontsource/inter/700.css';

  body {
    font-family: 'Inter', sans-serif;
  }
</style>
```

### src/pages/index.astro

```astro
---
import Base from '../layouts/Base.astro';
import Nav from '../components/Nav.astro';
import Hero from '../components/Hero.astro';
import Problem from '../components/Problem.astro';
import Solution from '../components/Solution.astro';
import CTA from '../components/CTA.astro';
import Footer from '../components/Footer.astro';
---

<Base title="Home" description="Your product pitch">
  <Nav />
  <Hero />
  <Problem />
  <Solution />
  <CTA />
  <Footer />
</Base>
```

### src/components/Hero.astro

```astro
<section class="min-h-screen flex items-center justify-center px-4 py-20 bg-gradient-to-br from-blue-50 to-indigo-50">
  <div class="max-w-3xl text-center">
    <h1 class="text-6xl font-black mb-6 text-gray-900">
      Headline here
    </h1>
    <p class="text-xl text-gray-600 mb-8">
      Problem statement.
    </p>
    <button onclick="document.getElementById('waitlist').scrollIntoView({ behavior: 'smooth' })" class="bg-blue-600 text-white px-8 py-4 rounded-lg font-bold hover:bg-blue-700">
      Join the waitlist
    </button>
  </div>
</section>
```

### src/components/Waitlist.astro

```astro
<section id="waitlist" class="py-20 px-4 bg-blue-50">
  <div class="max-w-2xl mx-auto">
    <h2 class="text-3xl font-bold text-center mb-8">Get early access</h2>
    <form id="waitlist-form" class="bg-white p-8 rounded-lg shadow-lg">
      <input
        type="email"
        placeholder="your@email.com"
        required
        class="w-full px-4 py-3 border border-gray-300 rounded-lg mb-4 focus:outline-none focus:ring-2 focus:ring-blue-600"
      />
      <input
        type="text"
        placeholder="Your name (optional)"
        class="w-full px-4 py-3 border border-gray-300 rounded-lg mb-6 focus:outline-none focus:ring-2 focus:ring-blue-600"
      />
      <button type="submit" class="w-full bg-blue-600 text-white py-3 rounded-lg font-bold hover:bg-blue-700">
        Join
      </button>
      <p class="text-xs text-gray-500 mt-4 text-center">
        No spam. Unsubscribe anytime.
      </p>
    </form>
  </div>
</section>

<script>
  document.getElementById('waitlist-form').addEventListener('submit', async (e) => {
    e.preventDefault();
    const form = e.target as HTMLFormElement;
    const email = (form.elements.namedItem('email') as HTMLInputElement).value;

    try {
      const res = await fetch('/api/waitlist', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email }),
      });

      if (res.ok) {
        alert('Thanks! Check your email.');
        form.reset();
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

---

## Image Best Practices

1. **Hero image:** 1200x800px. Compress to < 200KB. Use jpg or webp.
2. **Section images:** 600x400px. Keep under 100KB each.
3. **Use srcset for responsive images:**

```html
<img
  src="public/images/hero.jpg"
  srcset="public/images/hero-sm.jpg 640w, public/images/hero.jpg 1200w"
  sizes="(max-width: 640px) 100vw, 1200px"
  alt="Descriptive text"
  class="rounded-lg shadow-lg"
/>
```

4. **Lazy load below the fold:**

```html
<img loading="lazy" src="public/images/section-2.jpg" alt="..." />
```

---

## Form Structure (Waitlist)

The form should be:

1. **Simple.** Email + optional name. That's it.
2. **Visible.** Don't hide in a modal unless you need to. Modal is fine for single page.
3. **Inline.** For multi-page, put it in a dedicated section. For single page, modal is okay.
4. **Fast.** Should submit in < 1 second. If slower, show a loading spinner.
5. **Clear.** Success message: "Check your email." Error message: "Try again."

**Example form with validation:**

```html
<form id="waitlist-form" class="space-y-4">
  <input
    type="email"
    id="email"
    placeholder="your@email.com"
    class="w-full px-4 py-3 border border-gray-300 rounded-lg"
    required
    pattern="[a-z0-9._%+\-]+@[a-z0-9.\-]+\.[a-z]{2,}$"
  />
  <button
    type="submit"
    id="submit-btn"
    class="w-full bg-blue-600 text-white py-3 rounded-lg font-bold hover:bg-blue-700 disabled:opacity-50"
  >
    Join
  </button>
</form>

<script>
  const form = document.getElementById('waitlist-form');
  const btn = document.getElementById('submit-btn');

  form.addEventListener('submit', async (e) => {
    e.preventDefault();
    btn.disabled = true;
    btn.textContent = 'Joining...';

    try {
      const res = await fetch('/api/waitlist', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email: form.email.value }),
      });

      if (res.ok) {
        btn.textContent = '✓ Check your email!';
        form.reset();
      } else {
        btn.textContent = 'Try again';
        btn.disabled = false;
      }
    } catch (err) {
      console.error(err);
      btn.textContent = 'Network error';
      btn.disabled = false;
    }
  });
</script>
```

---

## Key Rules for Build Skill

1. Hero is the first impression. Make it clear in 5 seconds.
2. Problem and solution should be separate sections.
3. Always include one CTA per section (button or link).
4. Images in public/images. Add to .gitignore.
5. Keep forms simple. Email only for validation tests.
6. Mobile first. Test on phone before deploying.
