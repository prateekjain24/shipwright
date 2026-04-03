# Next.js Project Structure

Recommended folder layout for a Shipwright Next.js project.

## Directory Tree

```
my-project/
├── .shipwright/
│   ├── tickets/
│   │   ├── 001-landing-page.md
│   │   ├── 002-auth.md
│   │   └── ...
│   └── docs/
│       └── notes.md
├── src/
│   ├── app/
│   │   ├── layout.tsx          # Root layout (all pages inherit this)
│   │   ├── page.tsx            # Home page, / route
│   │   ├── globals.css         # Global styles
│   │   ├── (auth)/             # Route group, no URL segment
│   │   │   ├── layout.tsx      # Auth layout
│   │   │   ├── login/
│   │   │   │   └── page.tsx    # /login route
│   │   │   └── signup/
│   │   │       └── page.tsx    # /signup route
│   │   ├── dashboard/
│   │   │   ├── layout.tsx      # Dashboard layout
│   │   │   ├── page.tsx        # /dashboard route
│   │   │   ├── settings/
│   │   │   │   └── page.tsx    # /dashboard/settings route
│   │   │   └── [id]/           # Dynamic segment, matches /dashboard/123
│   │   │       └── page.tsx
│   │   └── api/
│   │       ├── todos/
│   │       │   ├── route.ts    # GET, POST for /api/todos
│   │       │   └── [id]/
│   │       │       └── route.ts # GET, PUT, DELETE for /api/todos/123
│   │       ├── auth/
│   │       │   └── route.ts
│   │       └── webhooks/
│   │           └── stripe.ts
│   ├── components/
│   │   ├── ui/                 # Reusable UI primitives
│   │   │   ├── Button.tsx
│   │   │   ├── Card.tsx
│   │   │   └── Modal.tsx
│   │   ├── layout/             # Page layout components
│   │   │   ├── Header.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   └── Footer.tsx
│   │   └── features/           # Feature-specific components
│   │       ├── TodoList.tsx
│   │       └── TodoItem.tsx
│   ├── lib/
│   │   ├── api.ts              # API client helpers
│   │   ├── db.ts               # Database setup
│   │   ├── auth.ts             # Auth helpers
│   │   ├── utils.ts            # Utility functions
│   │   └── validators.ts       # Input validation
│   └── types/
│       ├── todo.ts
│       ├── user.ts
│       └── index.ts            # Export all types
├── public/
│   ├── logo.png
│   ├── favicon.ico
│   └── robots.txt
├── .env.local                  # Local secrets, do NOT commit
├── .env.example                # Template for env vars
├── .gitignore
├── package.json
├── tsconfig.json
├── tailwind.config.ts
├── next.config.ts
└── README.md
```

## Naming Conventions

**Folders:** lowercase, hyphenated
```
src/components/
src/lib/
src/app/dashboard/
src/app/api/todos/
```

**Files:** PascalCase for React components, lowercase for utilities
```
src/components/Button.tsx       # Component, exports <Button>
src/lib/api.ts                  # Utilities, exports functions
src/types/todo.ts               # Types, exports interfaces
```

**Routes:** Use hyphens, not camelCase
```
src/app/auth/                   # Correct
src/app/Auth/                   # Wrong
src/app/sign-up/                # Correct
src/app/signUp/                 # Wrong
```

## Key Folders Explained

### src/app/

The file-based router. Every folder is a route, every `page.tsx` is a page.

```
src/app/todos/page.tsx → http://yoursite.com/todos
src/app/todos/[id]/page.tsx → http://yoursite.com/todos/123
```

**Route groups** in parentheses don't create URL segments:
```
src/app/(auth)/login/page.tsx → http://yoursite.com/login (not /auth/login)
src/app/(auth)/signup/page.tsx → http://yoursite.com/signup
```

Use route groups to:
- Share a layout among related pages
- Organize code without changing URLs
- Create multiple root layouts

**API routes** live in `src/app/api/`:
```
src/app/api/todos/route.ts → GET/POST /api/todos
src/app/api/todos/[id]/route.ts → GET/PUT/DELETE /api/todos/123
```

### src/components/

Reusable React components.

Group by purpose:
- **ui/** -- Dumb, stateless components (Button, Card, Modal)
- **layout/** -- Page frame components (Header, Sidebar, Footer)
- **features/** -- Smart, feature-specific components (TodoList, UserProfile)

Keep components small and focused. If a component gets over 200 lines, consider splitting it.

### src/lib/

Helper functions and setup code.

Common files:
- `api.ts` -- Fetch wrapper, error handling
- `db.ts` -- Database connection
- `auth.ts` -- Session helpers
- `utils.ts` -- Reusable functions
- `validators.ts` -- Input validation (zod, valibot, etc.)

### src/types/

TypeScript type definitions. Keep them grouped by domain.

```typescript
// src/types/todo.ts
export interface Todo {
  id: number;
  title: string;
  done: boolean;
  createdAt: Date;
}

export type CreateTodoInput = Omit<Todo, 'id' | 'createdAt'>;
```

Then import:
```typescript
import { Todo } from '@/types/todo';
```

### public/

Static files served as-is. Images, fonts, PDFs, etc.

Access them directly:
```tsx
<img src="/logo.png" alt="Logo" />
```

Files here are cached aggressively. If you change a file, the old version may persist in browsers until the cache expires.

## Layout and Nesting

Every `layout.tsx` wraps all child pages.

```
src/app/layout.tsx              # Wraps everything
  src/app/page.tsx              # Home
  src/app/dashboard/layout.tsx  # Wraps dashboard pages
    src/app/dashboard/page.tsx  # Dashboard home
    src/app/dashboard/settings/page.tsx # Settings
  src/app/(auth)/layout.tsx     # Wraps auth pages
    src/app/(auth)/login/page.tsx
    src/app/(auth)/signup/page.tsx
```

Put global setup (providers, navigation, fonts) in the root layout:

```typescript
// src/app/layout.tsx
import './globals.css';
import Header from '@/components/layout/Header';
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'My App',
  description: 'App description',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <Header />
        {children}
        <Footer />
      </body>
    </html>
  );
}
```

Feature-specific layouts go in their respective folders:

```typescript
// src/app/dashboard/layout.tsx
import Sidebar from '@/components/layout/Sidebar';

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="flex">
      <Sidebar />
      <main className="flex-1">{children}</main>
    </div>
  );
}
```

## Metadata

Add SEO metadata to page and layout files:

```typescript
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Home',
  description: 'Welcome to my app',
  openGraph: {
    title: 'Home',
    description: 'Welcome to my app',
    url: 'https://yoursite.com',
    type: 'website',
  },
};

export default function HomePage() {
  return <h1>Home</h1>;
}
```

For dynamic pages, use `generateMetadata`:

```typescript
export async function generateMetadata({
  params,
}: {
  params: { id: string };
}): Promise<Metadata> {
  const post = await getPost(params.id);
  return {
    title: post.title,
    description: post.excerpt,
  };
}
```

## Database and External Services

Keep setup code in `src/lib/`.

Example database setup:

```typescript
// src/lib/db.ts
import { Pool } from 'pg';

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
});

export async function query(sql: string, values?: any[]) {
  return pool.query(sql, values);
}
```

Then import and use in API routes:

```typescript
// src/app/api/todos/route.ts
import { query } from '@/lib/db';

export async function GET() {
  const result = await query('SELECT * FROM todos');
  return NextResponse.json(result.rows);
}
```

## When to Use Dynamic Imports

For large components or third-party libraries, use dynamic imports to split them into separate bundles:

```typescript
import dynamic from 'next/dynamic';

const HeavyEditor = dynamic(
  () => import('@/components/RichEditor'),
  { loading: () => <p>Loading...</p> }
);

export default function Page() {
  return <HeavyEditor />;
}
```

This delays loading until the component is needed.

## Testing Structure (Optional)

Add tests alongside code:

```
src/
├── components/
│   ├── Button.tsx
│   └── Button.test.tsx
├── lib/
│   ├── api.ts
│   └── api.test.ts
└── app/
    └── api/
        ├── todos/
        │   ├── route.ts
        │   └── route.test.ts
```

Or keep tests in a separate `__tests__/` folder:

```
__tests__/
├── unit/
│   └── lib/
│       └── api.test.ts
├── integration/
│   └── api/
│       └── todos.test.ts
└── e2e/
    └── auth.test.ts
```

Choose one approach and stick with it.
