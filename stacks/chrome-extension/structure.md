# Chrome Extension Structure

## Recommended Folder Layout

```
my-extension/
├── manifest.json              (at root, defines the extension)
├── src/
│   ├── popup/
│   │   ├── popup.html         (entry point for extension popup)
│   │   ├── popup.tsx          (React component or JS)
│   │   └── popup.css          (styles for popup only)
│   ├── background/
│   │   └── background.ts      (service worker, single file)
│   ├── content/
│   │   └── content.ts         (injected into web pages)
│   └── utils/
│       ├── storage.ts         (chrome.storage helpers)
│       ├── messaging.ts       (send/receive messages between scripts)
│       └── constants.ts       (shared values)
├── public/
│   └── icons/
│       ├── icon-16.png        (required sizes)
│       ├── icon-48.png
│       └── icon-128.png
├── vite.config.ts
├── tsconfig.json
└── package.json
```

---

## What Goes Where

### popup.html and popup.tsx

The UI that opens when user clicks your extension icon.

**popup.html:**
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8" />
  <title>My Extension</title>
</head>
<body>
  <div id="root"></div>
  <script type="module" src="./popup.tsx"></script>
</body>
</html>
```

**popup.tsx:**
```typescript
import React from 'react'
import ReactDOM from 'react-dom/client'
import Popup from './Popup'

ReactDOM.createRoot(document.getElementById('root')!).render(<Popup />)
```

**What it can do:**
- React, Vue, Svelte -- any framework.
- Access `chrome.storage` to read saved data.
- Send messages to background script.
- Trigger actions on click.

**What it can't do:**
- Run indefinitely. Popup closes when user clicks away.
- Access content script directly. Use background as middleman.
- Access `chrome.tabs` without `activeTab` permission.

**Lifecycle**: Opens when clicked, closes when unfocused. Don't store state here that needs to survive.

---

### background/background.ts

The service worker. Runs in the background, no DOM, survives tab close.

```typescript
// Listen for messages from popup or content script
chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
  if (message.type === 'DO_SOMETHING') {
    // Handle the message
    sendResponse({ result: 'done' })
  }
})

// Run on install
chrome.runtime.onInstalled.addListener(() => {
  console.log('Extension installed')
})

// Listen for tab updates
chrome.tabs.onUpdated.addListener((tabId, changeInfo) => {
  if (changeInfo.status === 'complete') {
    // Do something when a tab finishes loading
  }
})
```

**What it can do:**
- Listen for messages from popup and content scripts.
- Access `chrome.tabs`, `chrome.alarms`, `chrome.storage`.
- Run timers and background jobs.
- Track events across tabs.
- Wake up on specific triggers (tab update, alarm, message).

**What it can't do:**
- Access the DOM. No `document`, no `window.fetch` (use chrome.fetch).
- Run continuously in v3. Gets unloaded after 30 seconds of inactivity (Chrome 116+). Any event, API call, or message resets this timer. Use alarms for repeated tasks.
- Keep state in memory. Use `chrome.storage` for persistence.

**Messaging pattern:**
```typescript
// In background
chrome.runtime.onMessage.addListener((msg, sender, sendResponse) => {
  if (msg.type === 'GET_DATA') {
    sendResponse({ data: 'here' })
  }
})

// In popup
chrome.runtime.sendMessage({ type: 'GET_DATA' }, (response) => {
  console.log(response.data)
})
```

---

### content/content.ts

Injected into web pages. Can read and modify the DOM. Runs in page context but with limited access.

```typescript
// Listen for messages from popup/background
chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
  if (message.type === 'MODIFY_PAGE') {
    document.body.style.backgroundColor = 'blue'
    sendResponse({ modified: true })
  }
})

// Read the page
const title = document.title
const links = document.querySelectorAll('a')

// Send data to background
chrome.runtime.sendMessage({
  type: 'PAGE_DATA',
  title,
  linkCount: links.length,
})
```

**What it can do:**
- Read and modify the DOM.
- Listen to user events (click, input, etc).
- Send messages to background.
- Access `window.location`, `document.title`, etc.

**What it can't do:**
- Access `chrome.*` APIs directly (only messaging).
- Run on pages like chrome://, chrome-extension://, etc.
- Block page scripts or read their variables.
- XHR to cross-origin sites without CORS.

**Injection:**
Declare in manifest.json:
```json
"content_scripts": [
  {
    "matches": ["https://*/*"],
    "js": ["src/content/content.ts"],
    "run_at": "document_start"
  }
]
```

- `"matches"`: Patterns where content script runs.
- `"run_at"`: `document_start` (early), `document_end` (late), `document_idle` (default).

---

### utils/storage.ts

Helper to use `chrome.storage` cleanly.

```typescript
// Wrapper for chrome.storage.local
export const storage = {
  async get(key: string) {
    const result = await chrome.storage.local.get(key)
    return result[key]
  },
  async set(key: string, value: any) {
    await chrome.storage.local.set({ [key]: value })
  },
  async remove(key: string) {
    await chrome.storage.local.remove(key)
  },
}

// Usage everywhere:
const saved = await storage.get('myData')
await storage.set('myData', { foo: 'bar' })
```

Use `chrome.storage.local` for extension data, `chrome.storage.sync` if you want it synced across user's Chrome devices.

---

### utils/messaging.ts

Helper to send messages between scripts.

```typescript
// Send a message, wait for response
export async function sendToBackground(message: any) {
  return new Promise((resolve) => {
    chrome.runtime.sendMessage(message, resolve)
  })
}

// In popup
const response = await sendToBackground({ type: 'DO_WORK' })
console.log(response)
```

---

## How manifest.json Connects Everything

The manifest tells Chrome which files are which.

```json
{
  "manifest_version": 3,
  "name": "My Extension",
  "background": {
    "service_worker": "src/background/background.ts"
  },
  "action": {
    "default_popup": "src/popup/popup.html"
  },
  "content_scripts": [
    {
      "matches": ["https://*/*"],
      "js": ["src/content/content.ts"]
    }
  ],
  "permissions": ["storage"],
  "host_permissions": ["https://*/*"]
}
```

**Connections:**
1. User clicks extension icon -- opens the popup (popup.html).
2. Popup loads popup.tsx and can send messages to background.
3. Content script runs on every HTTPS page (matches rule).
4. Background listens for messages from popup or content script.
5. Content script and background communicate via chrome.runtime.sendMessage.
6. All scripts can read/write chrome.storage.

**Data flow:**
```
Page (DOM)
  ↓
Content Script (reads page)
  ↓ (sendMessage)
Background (handles logic)
  ↓ (sendMessage)
Popup (UI)
```

---

## Communication Patterns

### Popup ↔ Background

Most common. Popup sends request, background handles it.

```typescript
// In popup
chrome.runtime.sendMessage({ type: 'FETCH_DATA' }, (response) => {
  console.log(response)
})

// In background
chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
  if (message.type === 'FETCH_DATA') {
    sendResponse({ data: [1, 2, 3] })
  }
})
```

### Content Script ↔ Background

Content script injects into pages. Background does the work.

```typescript
// In content script
chrome.runtime.sendMessage({ type: 'LOG_EVENT', page: 'homepage' }, (response) => {
  console.log('Logged:', response)
})

// In background
chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
  if (message.type === 'LOG_EVENT') {
    // Log the event, maybe save to storage
    sendResponse({ success: true })
  }
})
```

### Popup ↔ Content Script

Not direct. Route through background.

```typescript
// In popup, send to background with tabId
const [tab] = await chrome.tabs.query({ active: true, currentWindow: true })
chrome.tabs.sendMessage(tab.id, { type: 'HIGHLIGHT' })

// In content script, listen
chrome.runtime.onMessage.addListener((message) => {
  if (message.type === 'HIGHLIGHT') {
    document.body.style.border = '5px solid red'
  }
})
```

---

## File Naming

- Service worker file: `background.ts` (single file, required).
- Content script: `content.ts` (or split into multiple files, all listed in manifest).
- Popup: `popup.html` + `popup.tsx` (or .jsx, .js).
- Utilities: camelCase. `storage.ts`, `messaging.ts`, `api.ts`.
- React components: PascalCase. `Popup.tsx`, `Settings.tsx`.

---

## TypeScript Tips

Use `webextension-polyfill` for proper types:

```bash
npm install webextension-polyfill
npm install -D @types/webextension-polyfill
```

Then in your files:

```typescript
import browser from 'webextension-polyfill'

// Now you get types on chrome.* APIs
browser.storage.local.get('key')
browser.tabs.query({ active: true })
```

Without it, `chrome.*` is untyped.
