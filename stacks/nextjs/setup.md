# Next.js Setup

Init a Next.js project from scratch for Shipwright.

## Before You Start

`create-next-app` creates a subdirectory by default. This is a trap.

If you run `npx create-next-app@latest my-app`, it creates `my-app/my-app/`. To avoid this, either:

1. Create the target directory first, then use `.` as the path argument (recommended)
2. Or use the full path if the directory doesn't exist yet

**Option 1 is cleaner.**

## Correct Init Sequence

```bash
mkdir my-project
cd my-project
npx create-next-app@latest . --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"
```

When prompted, accept the defaults for everything.

After the script finishes, verify the structure:

```
my-project/
  src/
    app/
      page.tsx
      layout.tsx
    ...
  public/
  .next/
  package.json
  tsconfig.json
  tailwind.config.ts
  next.config.ts
```

## Add Shipwright Directories

AFTER Next.js init, add the Shipwright structure:

```bash
mkdir -p .shipwright/tickets .shipwright/docs
```

Create `.shipwright/config.json`:

```json
{
  "project": "my-project",
  "stack": "nextjs",
  "created": "2026-04-03"
}
```

## Key Choices Explained

**App Router, not Pages Router.** App Router is the modern default. It uses `src/app/` and supports Server Components by default. Pages Router is deprecated.

**src/ directory.** This keeps application code separate from config files at the root. Cleaner.

**TypeScript.** Type safety prevents bugs. All Shipwright projects use TypeScript.

**Tailwind CSS.** Built-in utility CSS. No need for a separate styling setup.

**ESLint.** Catches errors early. Runs on commit via git hooks (set up later if needed).

**Server Components by default.** Only add "use client" when you need client-side features like hooks, state, event listeners. See gotchas.md.

**Import alias @/\*.** Makes imports cleaner: `import Button from "@/components/Button"` instead of `import Button from "../../../components/Button"`.

## Environment Setup

Create `.env.local` in the project root:

```
# .env.local
# Variables here are loaded at build and runtime
# Do NOT commit this file

NEXT_PUBLIC_API_URL=http://localhost:3000
```

Rules:
- `.env.local` is local-only, add to `.gitignore` (already done by create-next-app)
- `NEXT_PUBLIC_` prefix = exposed to the browser (use for API URLs, public keys)
- No prefix = server-only (use for secrets, API keys, database URLs)

For deployed apps, set these variables in the Vercel dashboard. See deploy.md.

## Run Locally

```bash
npm run dev
```

App runs at http://localhost:3000.

Changes hot-reload instantly.

## Folder Structure for a Typical Feature

After init, here's how to structure your first feature. See structure.md for the full tree.

```
src/app/
  page.tsx           # Home page
  layout.tsx         # Global layout
  /todos/            # Feature folder
    page.tsx         # /todos route
    layout.tsx       # Todos-specific layout (optional)
src/components/
  TodoList.tsx       # Reusable component
  TodoItem.tsx
src/lib/
  api.ts             # API helpers
  db.ts              # Database setup
src/types/
  todo.ts            # TypeScript types
```

## API Routes

No Express server. Use Next.js Route Handlers in `src/app/api/`.

Example: Create `src/app/api/todos/route.ts`:

```typescript
import { NextRequest, NextResponse } from 'next/server';

export async function GET() {
  const todos = [
    { id: 1, title: 'Learn Next.js' },
  ];
  return NextResponse.json(todos);
}

export async function POST(request: NextRequest) {
  const body = await request.json();
  // Save to database
  return NextResponse.json({ id: 2, ...body }, { status: 201 });
}
```

Then call it from the client:

```typescript
const response = await fetch('/api/todos');
const todos = await response.json();
```

## Next Steps

1. Verify the app runs: `npm run dev`
2. Delete the default homepage (`src/app/page.tsx`) and create your own
3. Follow structure.md to organize components and lib files
4. Add your first route under `src/app/` or API handler under `src/app/api/`
5. When ready to deploy, follow deploy.md
