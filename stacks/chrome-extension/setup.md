# Chrome Extension Setup

## Manifest v3 (Not v2)

Chrome deprecated Manifest v2 in 2023. Build with v3 only. It's the only version the Web Store accepts now.

---

## Project Structure

Create this folder layout from the start:

```
my-extension/
  manifest.json
  src/
    popup/
      popup.html
      popup.tsx
      popup.css
    background/
      background.ts
    content/
      content.ts
    utils/
      storage.ts
      messaging.ts
  public/
    icons/
      icon-16.png
      icon-48.png
      icon-128.png
  vite.config.ts
  tsconfig.json
  package.json
```

- **manifest.json**: Defines the extension. Lives at root.
- **popup/**: The UI when user clicks the extension icon. Single HTML entry point.
- **background/**: Long-running logic. Service worker (v3). No DOM access.
- **content/**: Injects into web pages. Can read/modify DOM.
- **utils/**: Shared code like storage helpers, message passing.
- **public/icons/**: Required sizes: 16, 48, 128px. PNG format.

---

## Build Tools: Vite + CRXJS or Plasmo

Pick one:

### Option A: Vite + CRXJS (More control)

CRXJS is a Vite plugin that handles manifest, builds the extension, auto-reloads in dev.

```bash
npm create vite@latest my-extension -- --template react-ts
cd my-extension
npm install -D @crxjs/vite-plugin@latest
npm install webextension-polyfill
```

Update `vite.config.ts`:

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { crx } from '@crxjs/vite-plugin'
import manifest from './manifest.json' assert { type: 'json' }

export default defineConfig({
  plugins: [react(), crx({ manifest })],
})
```

### Option B: Plasmo (Simpler scaffolding)

Plasmo is a full framework. It hides Vite config, scaffolds the project, handles everything.

```bash
npm install -g plasmo
plasmo init my-extension
cd my-extension
npm install
```

Plasmo auto-generates manifest.json and handles builds. Less boilerplate, less control.

**Recommendation**: Use Plasmo if this is your first extension. Use CRXJS if you need custom build config.

---

## manifest.json (V3 Skeleton)

Every extension needs this at the root.

```json
{
  "manifest_version": 3,
  "name": "My Extension",
  "version": "1.0.0",
  "description": "What this extension does",
  "permissions": ["storage"],
  "host_permissions": ["https://*/*"],
  "background": {
    "service_worker": "src/background/background.ts"
  },
  "action": {
    "default_popup": "src/popup/popup.html",
    "default_title": "My Extension",
    "default_icons": {
      "16": "public/icons/icon-16.png",
      "48": "public/icons/icon-48.png",
      "128": "public/icons/icon-128.png"
    }
  },
  "content_scripts": [
    {
      "matches": ["https://*/*"],
      "js": ["src/content/content.ts"]
    }
  ]
}
```

**Key fields:**
- `manifest_version`: Always 3.
- `permissions`: What APIs the extension uses (storage, activeTab, etc). Request only what you need.
- `host_permissions`: Which sites the extension can interact with (https://*/*, specific domains, etc).
- `background.service_worker`: Path to the background script. Single file only in v3.
- `content_scripts`: Injects into pages matching `matches` pattern.

---

## Permissions: What Each One Does

Request minimum. The Web Store reviews this.

| Permission | Why you need it | When to use |
|------------|-----------------|------------|
| `storage` | Read/write extension data | Almost always. Use chrome.storage.sync/local. |
| `activeTab` | Access current tab URL and content | When popup needs to interact with active page. |
| `tabs` | List all open tabs, see titles/URLs | Dashboard-like features. Overkill for most. |
| `webRequest` | Monitor or block requests | Analytics, ad blocking. Very limited in v3. |
| `host_permissions` | Access specific domains | Content script runs on these sites. |
| `scripting` | Inject scripts dynamically | Rarely needed. Prefer content_scripts in manifest. |
| `contextMenus` | Add right-click menu items | Only if you need context menu. |
| `alarms` | Schedule tasks | Background timers that survive tab close. |

---

## Exact Setup Commands

### Start with Plasmo (recommended for first-timers)

```bash
npm install -g plasmo
plasmo init my-extension
cd my-extension
npm run dev
```

Open `chrome://extensions` -> toggle Developer Mode -> Load Unpacked -> select dist/ folder. Auto-reloads on save.

### Start with Vite + CRXJS

```bash
npm create vite@latest my-extension -- --template react-ts
cd my-extension
npm install -D @crxjs/vite-plugin@latest
npm install webextension-polyfill
# Update vite.config.ts (see above)
npm run dev
```

Same loading process.

### TypeScript Setup

Both tools scaffold TypeScript by default. If you prefer JavaScript, skip the .ts files and use .js. But TypeScript catches permission errors at compile time -- worth it.

---

## Development

After loading unpacked in Chrome:

1. Make changes to code.
2. Vite/Plasmo auto-rebuilds.
3. Toggle the extension off/on in chrome://extensions or press the reload button.
4. For manifest changes, reload the extension.

Use `console` tab on extension pages:
- Popup: Right-click extension icon -> Inspect popup.
- Background: chrome://extensions -> Details -> Inspect views -> service worker.
- Content: Open DevTools on any page (F12).

---

## Next Steps

- Read structure.md for where to put code.
- Read deploy.md when ready to ship.
- See gotchas.md for CSP, service worker limits, and common traps.
