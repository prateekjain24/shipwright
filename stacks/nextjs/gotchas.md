# Next.js Gotchas

Things that waste time if you don't know them.

## "use client" -- When You Actually Need It

**By default, all components in `src/app/` are Server Components.** They run only on the server. This is good. Keep it that way.

Add `"use client"` only when you need client-side features:
- React hooks: `useState`, `useEffect`, `useContext`, `useReducer`
- Event listeners: `onClick`, `onChange`, `onSubmit`
- Browser APIs: `localStorage`, `window`, `document`
- Real-time updates: WebSockets, polling

### Example: When You Need "use client"

```typescript
// src/components/TodoForm.tsx
'use client';  // Must add this because we use hooks

import { useState } from 'react';

export default function TodoForm() {
  const [title, setTitle] = useState('');  // Hook requires 'use client'

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    // Submit to API
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={title} onChange={(e) => setTitle(e.target.value)} />
      <button type="submit">Add</button>
    </form>
  );
}
```

### Example: When You Don't Need "use client"

```typescript
// src/components/TodoList.tsx
// No 'use client' needed

import { getTodos } from '@/lib/db';

export default async function TodoList() {
  const todos = await getTodos();  // Server function, no hook

  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  );
}
```

### Rules of Thumb

- **Server Components (no "use client")** -- data fetching, direct database access, secrets in code
- **Client Components ("use client")** -- forms, interactivity, hooks, event handlers
- **Layouts can be Server Components** -- they don't need "use client" unless they use hooks

When in doubt, don't add "use client". If you need a hook, the error message will tell you.

## Hydration Mismatches

Hydration is when Next.js attaches React on the client to the HTML from the server.

**Hydration mismatch happens when the server HTML doesn't match the client HTML.** This breaks interactivity.

### Common Cause: Using `new Date()` or Random Data

```typescript
// WRONG - causes hydration mismatch
'use client';
export default function Timestamp() {
  return <div>{new Date().toString()}</div>;  // Different on server and client
}
```

The server renders one timestamp. The browser renders a different one. Mismatch.

### Fix: Use `useEffect` to Add Client-Only Content

```typescript
'use client';
import { useEffect, useState } from 'react';

export default function Timestamp() {
  const [mounted, setMounted] = useState(false);

  useEffect(() => {
    setMounted(true);
  }, []);

  if (!mounted) return <div>Loading...</div>;
  return <div>{new Date().toString()}</div>;
}
```

Or suppress hydration mismatch in development only:

```typescript
'use client';
import { useEffect, useState } from 'react';

export default function Timestamp() {
  const [date, setDate] = useState<string>('');

  useEffect(() => {
    setDate(new Date().toString());
  }, []);

  return <div>{date || 'Loading...'}</div>;
}
```

### Other Common Causes

- Component that checks `window` directly (use `typeof window !== 'undefined'`)
- Different data fetching in server vs client
- CSS-in-JS that generates different values on each render

If you see "Hydration failed," look for any dynamic content that's different between server and client.

## Dynamic Imports

Use dynamic imports to delay loading large components until needed. This speeds up the initial page.

### Example

```typescript
import dynamic from 'next/dynamic';

const HeavyChart = dynamic(() => import('@/components/Chart'), {
  loading: () => <p>Loading chart...</p>,
  ssr: false,  // Don't render on server, only client (optional)
});

export default function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <HeavyChart />
    </div>
  );
}
```

The Chart component is loaded only when the page renders, not during initial load.

### When to Use Dynamic Imports

- Large libraries (charts, editors, maps)
- Components that need "use client" but aren't critical to initial render
- Third-party widgets that slow down the page

### ssr: false Explained

By default, `dynamic()` renders the component on the server. If a component doesn't work on the server (e.g., uses `window`), add `ssr: false`:

```typescript
const Modal = dynamic(() => import('@/components/Modal'), {
  ssr: false,  // Only render on client
});
```

This prevents build errors if the component can't run on the server.

## Environment Variables

### Naming

- `NEXT_PUBLIC_*` -- Browser can see it
- No prefix -- Server-only, secret

```
NEXT_PUBLIC_API_URL=https://api.example.com  # Exposed to browser
DATABASE_URL=postgres://...                  # Server-only secret
```

### File Naming

- `.env.local` -- Local development, don't commit, loaded first
- `.env.development` -- Development environment, can commit
- `.env.production` -- Production, but Vercel ignores this

**For Vercel:** Set variables in the Vercel dashboard, not in `.env.production`.

### Loading Order

Next.js loads vars in this order (first match wins):

1. `.env.local` (highest priority)
2. `.env.{NODE_ENV}` (e.g., `.env.development`)
3. `.env` (lowest priority)

So `.env.local` always overrides everything.

### Using in Code

```typescript
// Available at build time and runtime
const apiUrl = process.env.NEXT_PUBLIC_API_URL;

// Server-only, build time only
const dbUrl = process.env.DATABASE_URL;
```

### Common Mistake: Forgetting NEXT_PUBLIC_

```typescript
// WRONG - variable not loaded
const apiUrl = process.env.API_URL;  // undefined in browser

// CORRECT
const apiUrl = process.env.NEXT_PUBLIC_API_URL;  // works
```

If a public URL isn't showing up in the browser, check that it has the `NEXT_PUBLIC_` prefix.

### NEXT_PUBLIC_ Variables Are Build-Time Only

`NEXT_PUBLIC_` variables get inlined into the JavaScript bundle at build time. They are NOT read at runtime.

This means:
- Changing `NEXT_PUBLIC_` values after build has no effect
- Each environment (staging, production) needs its own build
- You can't "build once, deploy many" with `NEXT_PUBLIC_` variables

If you need runtime config on the client, pass it from a Server Component:

```typescript
// app/page.tsx (Server Component -- reads env at runtime)
export default function Page() {
  const apiUrl = process.env.API_URL; // server-only, runtime
  return <ClientComponent apiUrl={apiUrl} />;
}
```

## Image Optimization Quirks

Next.js optimizes images automatically with `next/image`. But there are quirks.

### Always Specify Width and Height

```typescript
import Image from 'next/image';

// WRONG - causes layout shift
<Image src="/logo.png" alt="Logo" />

// CORRECT - prevents layout shift
<Image src="/logo.png" alt="Logo" width={200} height={100} />
```

Without width/height, Next.js doesn't know how much space to reserve. The page jumps when the image loads.

### For Responsive Images, Use fill

```typescript
<div style={{ position: 'relative', width: '100%', height: 300 }}>
  <Image
    src="/hero.jpg"
    alt="Hero"
    fill
    style={{ objectFit: 'cover' }}
  />
</div>
```

### Images Must Be in public/ or External URLs

```typescript
// OK - from public folder
<Image src="/logo.png" ... />

// OK - external URL
<Image src="https://example.com/logo.png" ... />

// WRONG - can't import relative paths
<Image src="../../images/logo.png" ... />
```

If Next.js can't find the image, you'll see "Image Optimization failed."

### Optimization Happens at Build Time

For production builds, Next.js generates optimized versions of all images. This can make builds slower if you have many large images.

Use dynamic imports to defer image optimization:

```typescript
import dynamic from 'next/dynamic';

const HeroImage = dynamic(
  () => import('@/components/HeroImage'),
  { loading: () => <div>Loading...</div> }
);
```

### Image domains

If loading images from external domains, add them to `next.config.ts`:

```typescript
// next.config.ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  images: {
    domains: ['api.example.com', 'cdn.example.com'],
  },
};

export default nextConfig;
```

## Common Build Errors

### "Cannot find module '@/components/Button'"

Check:
- Component file exists: `src/components/Button.tsx`
- Path is correct
- File is exported: `export default function Button() { ... }`

### "Type 'X' is not assignable to type 'Y'"

TypeScript error. Fix the type mismatch:

```typescript
// WRONG
const count: string = 123;

// CORRECT
const count: number = 123;
```

Run `npm run build` locally to catch these before deploying.

### "Unexpected token `export`"

You're importing from a client-only library in a Server Component. Either:
1. Move the import to a Client Component
2. Use the library in an API route instead

### "next/server not found"

Install Next.js:

```bash
npm install next
```

### "Build timeout" (on Vercel)

Your build is taking too long. Options:
- Remove unused dependencies
- Use dynamic imports for heavy libraries
- Move long-running tasks to API routes
- Split your app into smaller pages

## Deployment Issues

### Environment variables don't load on Vercel

You set them in `.env.local` locally, but forgot to add them to Vercel.

Fix:
1. Go to Vercel dashboard > your project
2. Settings > Environment Variables
3. Add each variable

Then trigger a redeploy (or push to git).

### API routes return 404 on production

Check:
- Route file is in `src/app/api/` folder
- File is named `route.ts` or `route.js`, not something else
- Function is exported: `export async function GET() { ... }`

Common mistake: putting API code in a page file instead of a route file.

### "Module not found" error on deploy

You're using a dependency that's in `package.json` locally but not installed on Vercel.

Fix:
```bash
npm install
git add package-lock.json
git commit -m "Update dependencies"
git push
```

Vercel reinstalls and redeploys.

## Performance

### Slow page loads

Check:
- Are you fetching too much data on the server?
- Are you using dynamic imports for heavy components?
- Are you using Server Components for data fetching? (good)
- Are you avoiding waterfalls in data fetching?

Data waterfall example:

```typescript
// BAD - fetches user, then posts (waterfall)
const user = await getUser();
const posts = await getPosts(user.id);

// GOOD - fetch in parallel
const [user, posts] = await Promise.all([
  getUser(),
  getPosts(user.id),
]);
```

### Large bundle size

Check which dependencies are bloating your bundle:

```bash
npm install -g next-bundle-analyzer
npm run build --analyze
```

Use dynamic imports for big libraries:

```typescript
const RichEditor = dynamic(() => import('@/components/RichEditor'), {
  ssr: false,
});
```

## Debugging

### Enable verbose logging

```bash
npm run dev -- --debug
```

### Check the build output

```bash
npm run build
```

This shows all warnings and errors.

### Use React DevTools

Install the React DevTools browser extension. It helps debug component trees and hooks.

### Check Vercel logs

After deploying, check the Vercel dashboard > Deployments > Logs to see what went wrong.
