# Stack Components

Each stack component is a self-contained set of reference docs. They follow a consistent structure so the Build skill can load any of them interchangeably.

---

## Stack Selection

During Shape, Shipwright asks: "What kind of thing are you building?" Based on the answer, it selects the right stack component.

| Builder says | Stack selected | Why |
|---|---|---|
| "a web app" / "SaaS" / "dashboard" | nextjs | Full-stack, SSR, easy deploy |
| "a Chrome extension" | chrome-extension | Browser-native, Manifest v3 |
| "a Telegram bot" / "Discord bot" | telegram-bot | Lightweight, event-driven |
| "a CLI tool" / "a script" | cli-tool | Node.js, npm-distributable |
| "a Slack app" | slack-app | Bolt framework, workspace integration |
| "a landing page" / "waitlist" | landing-page | Simple, fast, Stripe-ready |

The Build skill reads `stacks/{stack}/setup.md` before scaffolding. The methodology (tickets, worktrees, commits) stays the same. Only the setup instructions change.

New stacks can be added as simple markdown files. No skill rewrites needed.

---

## Component Structure

Every stack has 4 files:

```
stacks/{stack}/
  setup.md       # Init sequence, common pitfalls
  structure.md   # Recommended project structure
  deploy.md      # Deployment instructions
  gotchas.md     # Version-specific issues, traps
```

---

## Next.js (`stacks/nextjs/`)

**setup.md -- Key rules:**
1. `create-next-app` creates a subdirectory by default. Always use `.` as path argument or create the target dir first.
2. Must init Next.js FIRST, then add .shipwright/ after.
3. Correct init:
   ```
   mkdir project-name && cd project-name
   npx create-next-app@latest . --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"
   ```
4. App Router (not Pages Router)
5. `src/` directory -- keeps app code separate from config
6. Server Components by default -- don't add "use client" unless needed
7. Metadata API for SEO -- `export const metadata` in layout/page files
8. Route handlers for APIs -- `app/api/`, not a separate Express server

**deploy.md:**
- `vercel --prod` or push to GitHub with Vercel auto-deploy
- Environment variables via Vercel dashboard or CLI
- Custom domain setup
- Preview deployments for PRs

---

## Chrome Extension (`stacks/chrome-extension/`)

**setup.md:**
1. Manifest v3 (not v2 -- deprecated)
2. Project structure: `manifest.json`, `popup/`, `background/`, `content/`
3. TypeScript + bundler (Vite with CRXJS or Plasmo framework)
4. Permissions: request minimum needed, explain each in the listing

**gotchas.md:**
- Content Security Policy blocks inline scripts
- Service workers (background) have no DOM access
- Chrome Web Store review takes 1-3 business days
- Manifest v3 limitations: no persistent background pages, limited webRequest

**deploy.md:**
- Build and zip
- Chrome Web Store Developer Dashboard
- $5 one-time registration fee
- Screenshots and promotional images required

---

## Telegram Bot (`stacks/telegram-bot/`)

**setup.md:**
1. grammY framework (recommended) or Telegraf
2. Get bot token from @BotFather
3. Webhook mode for production, polling for development
4. Project structure: `src/bot.ts`, `src/commands/`, `src/handlers/`

**deploy.md:**
- Railway or Fly.io (free tier available)
- Set webhook URL after deploy
- Environment: BOT_TOKEN as secret

---

## CLI Tool (`stacks/cli-tool/`)

**setup.md:**
1. Node.js with Commander.js or oclif
2. Project structure: `src/commands/`, `src/utils/`, `bin/`
3. TypeScript recommended
4. Add `"bin"` field to package.json

**deploy.md:**
- `npm publish` to npm registry
- Or distribute via GitHub Releases
- Add to Homebrew tap (optional)

---

## Slack App (`stacks/slack-app/`)

**setup.md:**
1. Bolt.js framework
2. Socket Mode for development, HTTP for production
3. Create app at api.slack.com/apps
4. Scopes: request minimum needed

**deploy.md:**
- Railway/Heroku/Fly.io
- Set OAuth redirect URLs
- Submit to Slack App Directory (optional)

---

## Landing Page (`stacks/landing-page/`)

**setup.md:**
1. Astro or plain HTML + Tailwind
2. Stripe integration via Checkout Sessions
3. Structure: hero, problem, solution, pricing, CTA, footer

**deploy.md:**
- Vercel or Netlify (one-click)
- Custom domain
- Stripe webhook for payment confirmation

---

## Adding a New Stack

Create a folder under `stacks/` with the 4 standard files. The Build skill auto-discovers stacks by reading `stacks/{stack}/setup.md`. No plugin code changes needed.

Template for a new stack:
```
stacks/my-stack/
  setup.md       # "How to initialize this from scratch"
  structure.md   # "Recommended folder structure and patterns"
  deploy.md      # "How to get this live"
  gotchas.md     # "Things that will waste an hour if you don't know"
```
