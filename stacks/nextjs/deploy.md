# Next.js Deployment

Deploy to production on Vercel, the official Next.js platform.

## Why Vercel

Vercel is built by the team that created Next.js. It's the easiest way to deploy:
- Zero-config deployment
- Automatic git integration
- Preview deployments for pull requests
- Serverless functions out of the box
- No build step -- just `git push`

You can use Netlify or other platforms, but Vercel is the fastest path.

## Prerequisites

1. Next.js project on GitHub, GitLab, or Bitbucket
2. Vercel account (free tier is plenty) at https://vercel.com

## Deployment Steps

### Step 1: Push to Git

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/yourusername/my-project.git
git push -u origin main
```

### Step 2: Deploy via Vercel Dashboard

1. Go to https://vercel.com/new
2. Choose your git platform (GitHub, GitLab, Bitbucket)
3. Select your repository
4. Click "Import"
5. Vercel auto-detects Next.js and configures everything
6. Click "Deploy"

Done. Your app is live at `your-project.vercel.app`.

### Step 3: Add Environment Variables

Vercel automatically reads `.env.local` locally, but for production, you must set variables in the dashboard.

**To add a variable:**

1. Go to your project on Vercel
2. Settings > Environment Variables
3. Add each variable:
   - Name: `DATABASE_URL`
   - Value: `postgres://...`
   - Select scopes: Production, Preview, Development (or custom)
4. Click "Save and Deploy"

Vercel re-deploys automatically with the new variables.

**Variables with NEXT_PUBLIC_ are exposed to the browser:**

```
NEXT_PUBLIC_API_URL=https://api.example.com  # Safe, public
DATABASE_URL=postgres://...                  # Secret, server-only
```

### Step 4: Add a Custom Domain (Optional)

1. Go to your project on Vercel
2. Settings > Domains
3. Enter your domain
4. Update your domain's DNS settings (Vercel provides instructions)
5. Wait 24-48 hours for DNS to propagate

You can also use Vercel domains (`my-project.vercel.app`), which work instantly.

## Common Deployment Scenarios

### Auto-Deploy on Git Push

Connected your repo? Every push to `main` auto-deploys. No CLI needed.

```bash
git commit -m "Add todos feature"
git push origin main
# Vercel automatically deploys. Check the dashboard.
```

### Preview Deployments

Every pull request gets a temporary deployment at `my-project-pr-123.vercel.app`.

Useful for:
- Showing changes to teammates
- Testing before merge
- Catching build errors early

Share the preview URL in your PR for feedback.

### Rollback to Previous Deployment

1. Go to Vercel dashboard > Deployments
2. Find the version you want
3. Click the three dots > Promote to Production

Instant rollback, no rebuild.

### Deploy from CLI (Manual)

If you don't want git integration, deploy directly:

```bash
npm install -g vercel
vercel --prod
```

Follow the prompts. Your app deploys and you get a URL.

## Environment Variables Guide

### .env.local (Local Development)

Add to `.gitignore` automatically by `create-next-app`. Never commit secrets here.

```
# .env.local
DATABASE_URL=postgres://localhost/mydb
NEXT_PUBLIC_API_URL=http://localhost:3000
SECRET_KEY=my-secret-key-123
```

Loaded at build and runtime.

### .env.example (Template, Commit This)

Keep a template in git so teammates know what variables to set:

```
# .env.example
DATABASE_URL=
NEXT_PUBLIC_API_URL=
SECRET_KEY=
```

Copy to `.env.local` and fill in values.

### .env.production (Production Only)

Some apps use separate `.env.production` for prod-only vars, but **Vercel doesn't support this file**.

Instead, set variables in the Vercel dashboard under Settings > Environment Variables. Choose which scope (Production, Preview, Development) for each variable.

### Variable Naming Rules

- `NEXT_PUBLIC_*` -- Exposed to browser, safe for API URLs and public keys
- No prefix -- Server-only, use for secrets and database URLs
- UPPERCASE_WITH_UNDERSCORES -- Convention
- Never use lowercase or camelCase for env vars

```
NEXT_PUBLIC_API_URL=https://api.example.com   ✓ Correct
DATABASE_URL=postgres://...                   ✓ Correct
API_SECRET=abc123                             ✓ Correct
apiUrl=https://api.example.com                ✗ Wrong (lowercase)
next_public_api_url=https://...               ✗ Wrong (lowercase)
```

## Build Errors

### Error: "Cannot find module"

Usually means a missing dependency or wrong import path.

```bash
npm install
npm run build
```

Check that all imports use the correct path, e.g., `@/components/Button`, not `../Button`.

### Error: "Type 'X' is not assignable to type 'Y'"

TypeScript error. Fix the type:

```typescript
// Wrong
const count: string = 123;

// Correct
const count: number = 123;
```

Run `npm run build` to see all errors before deploying.

### Error: "Image optimization failed"

Usually an issue with the `next/image` component.

Check:
- Image path is correct: `/public/image.png`
- Image exists in the `public/` folder
- Width and height are specified (or use `fill`)

See gotchas.md for more image quirks.

### Error: "Build took too long"

Vercel times out builds after 45 minutes on free tier (15 minutes on some builds). This usually means:
- A long-running build step (compilation, bundling)
- Large dependencies being installed
- Database migrations running during build

Fixes:
- Remove unused dependencies
- Use dynamic imports for large libraries
- Run migrations separately, not during build
- Move slow tasks to API routes instead of build time

## Health Check After Deploy

After deploying, visit your app and test:

1. **Homepage loads** -- `https://your-project.vercel.app`
2. **API routes work** -- Hit an API route manually
3. **Environment variables load** -- Check if they're accessible
4. **Database connects** -- Test a query if you have a database
5. **Auth works** (if you have it) -- Try logging in

If anything fails, check the Vercel logs:

1. Go to Vercel dashboard > your project > Deployments
2. Click the latest deployment
3. Click "Logs"
4. Look for error messages

## Monitoring and Logs

### View Build Logs

1. Vercel dashboard > Deployments > click a deployment
2. Click "Logs" tab
3. See all build output, errors, warnings

### View Runtime Logs

1. Deployments > your deployment > Functions tab
2. Click a function to see runtime logs

### Monitor Performance

1. Settings > Analytics
2. See page speed, Core Web Vitals, visitor data

Use this to catch performance regressions after deploy.

## Cleanup

### Remove Old Deployments

Vercel keeps old deployments for rollback. To save space:

1. Go to Deployments
2. Click the three dots on old deployments
3. Click "Delete"

This saves storage but you lose the ability to rollback to that version.

### Unused Preview Deployments

Vercel auto-cleans preview deployments for closed PRs. No action needed.

## Next Steps

- Set up a custom domain (optional)
- Add environment variables in the Vercel dashboard
- Enable automatic deployments from your main branch
- Check monitoring to catch any issues post-deploy
