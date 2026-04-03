# Chrome Extension Gotchas

These are the traps that waste hours. Know them upfront.

---

## Content Security Policy (CSP)

CSP blocks inline scripts. You can't do this:

```html
<!-- ❌ WON'T WORK -->
<script>
  console.log('hello')
</script>

<!-- ❌ WON'T WORK -->
<button onclick="doThing()">Click</button>
```

**Why:** Extension manifests forbid inline scripts for security.

**Fix:** Link external scripts only.

```html
<!-- ✓ WORKS -->
<script src="popup.js"></script>

<!-- ✓ WORKS -->
<button id="btn">Click</button>
<script>
  document.getElementById('btn').addEventListener('click', doThing)
</script>
```

Same goes for inline styles:

```html
<!-- ❌ WON'T WORK -->
<div style="color: red;">Text</div>

<!-- ✓ WORKS -->
<div class="error">Text</div>
<style>
  .error { color: red; }
</style>
```

**For frameworks:** React, Vue, Svelte all generate external scripts. You're fine. CSP is mainly an issue if you're writing raw HTML.

---

## Service Workers Have No DOM

The background service worker (background/background.ts) can't access the DOM. You have no `document`, no `window.fetch`, no `localStorage`.

```typescript
// ❌ WILL CRASH
const el = document.getElementById('thing') // undefined

// ❌ WILL CRASH
const data = localStorage.getItem('key') // undefined

// ❌ WILL CRASH
fetch('/api/thing') // fetch is not available
```

**Why:** Service workers run in a special context, not attached to any page.

**Fix:** Use alternatives.

| Don't | Use Instead |
|-------|-------------|
| `document` | Don't need it. Use content script to access page. |
| `localStorage` | `chrome.storage.local` |
| `fetch` (sometimes) | Works for CORS requests. Use it. |
| `window.setTimeout` | `chrome.alarms` for timers that survive unload |
| Storing state in memory | `chrome.storage` for persistence |

```typescript
// ✓ Use chrome.storage instead
const data = await chrome.storage.local.get('key')
const value = data['key']

// ✓ For repeated tasks, use alarms
chrome.alarms.create('myAlarm', { periodInMinutes: 60 })
chrome.alarms.onAlarm.addListener((alarm) => {
  if (alarm.name === 'myAlarm') {
    // This runs even if background is unloaded
  }
})
```

---

## Manifest v3 Limits

v3 is stricter than v2. Know these limits:

### No Persistent Background Pages

v2 had `background.page`. v3 has `service_worker` which unloads after inactivity (~5 mins).

**Problem:** If your extension relies on a background script running 24/7, it doesn't work.

**Solution:**
- Use `chrome.alarms` to wake the background periodically.
- Use `chrome.runtime.onMessage.addListener` to respond to messages (wakes the worker).
- Design your extension to be event-driven, not always-on.

Example: Timer that pings every hour.

```typescript
// In background
chrome.alarms.create('hourlyPing', { periodInMinutes: 60 })
chrome.alarms.onAlarm.addListener((alarm) => {
  if (alarm.name === 'hourlyPing') {
    console.log('Ping!')
  }
})
```

### Limited webRequest

v2's `webRequest` API let you inspect/block all requests. v3 has `declarativeNetRequest` which is simpler but less powerful.

You can't:
- Inspect request body.
- Modify headers dynamically.
- Block requests based on complex logic.

You can:
- Block domains (static lists).
- Redirect URLs.
- Remove headers (whitelist only).

If you need the old `webRequest`, use the `webRequest` permission carefully. You'll need `<all_urls>` permission and human review will be stricter.

### No Eval

```typescript
// ❌ WON'T WORK in v3
eval('some code')
new Function('some code')()
setTimeout('some code', 1000)
```

CSP forbids `eval`. Don't do it. Refactor to regular functions.

---

## Cross-Origin Issues in Content Scripts

Content scripts run in the page but with isolation. You can read the DOM, but you can't access page scripts or make cross-origin requests directly.

### Problem 1: Page Script Access

```typescript
// In content script
console.log(window.myPageVariable) // undefined
// The page has window.myPageVariable, but content script can't see it
```

Content scripts run in an "isolated world" separate from the page script.

**Fix:** Use `window.postMessage` to communicate with page script.

```typescript
// In content script
window.postMessage({ type: 'REQUEST', data: 'something' }, '*')

window.addEventListener('message', (event) => {
  if (event.source !== window) return
  if (event.data.type === 'RESPONSE') {
    console.log(event.data.result)
  }
})

// In a <script> injected into the page
window.addEventListener('message', (event) => {
  if (event.data.type === 'REQUEST') {
    const result = window.myExpensiveAPI(event.data.data)
    window.postMessage({ type: 'RESPONSE', result }, '*')
  }
})
```

Clunky, but works.

### Problem 2: Cross-Origin XHR/Fetch

Content scripts can't fetch from cross-origin domains without CORS headers.

```typescript
// In content script
fetch('https://api.other-site.com/data') // Blocked by CORS
```

**Fix:** Route through background script which has fewer CORS restrictions.

```typescript
// In content script
chrome.runtime.sendMessage({ type: 'FETCH', url: 'https://api.other-site.com/data' }, (response) => {
  console.log(response.data)
})

// In background
chrome.runtime.onMessage.addListener(async (message, sender, sendResponse) => {
  if (message.type === 'FETCH') {
    const response = await fetch(message.url)
    const data = await response.json()
    sendResponse({ data })
  }
})
```

Background has fewer CORS restrictions because it's not bound to a specific origin.

---

## Storage API Quirks

### chrome.storage vs localStorage

Don't use `localStorage` in extensions (it's per-origin and limited).

Use `chrome.storage` instead:

```typescript
// ✓ Use this
await chrome.storage.local.set({ myKey: 'myValue' })
const result = await chrome.storage.local.get('myKey')
console.log(result.myKey) // 'myValue'

// Removes key
await chrome.storage.local.remove('myKey')

// Clear all
await chrome.storage.local.clear()
```

`chrome.storage.local` is per-extension, synced to the user's Chrome profile.

`chrome.storage.sync` syncs across the user's devices (if they're signed in to Chrome).

### Promises vs Callbacks

`chrome.storage` returns Promises. Always use `await`.

```typescript
// ✓ WORKS
const result = await chrome.storage.local.get('key')

// ❌ OLD STYLE (still works but avoid)
chrome.storage.local.get('key', (result) => {
  console.log(result)
})
```

### Size Limits

- `chrome.storage.local`: 10 MB per extension.
- `chrome.storage.sync`: 100 KB per extension.

Don't store large files. If you need to cache images, use `IndexedDB` instead.

### Change Detection

Listen for storage changes:

```typescript
chrome.storage.onChanged.addListener((changes, areaName) => {
  for (let [key, { oldValue, newValue }] of Object.entries(changes)) {
    console.log(`${key} changed from ${oldValue} to ${newValue}`)
  }
})
```

Useful for keeping popup and background in sync.

---

## Service Worker Unload

The background service worker unloads after ~5 minutes of inactivity. If your extension wakes it (message, alarm, user event), it loads. If nothing happens for 5 mins, it's gone.

**This means:**

```typescript
// ❌ DON'T DO THIS
let count = 0
chrome.runtime.onMessage.addListener((msg) => {
  count++ // count resets to 0 when worker unloads
})

// ✓ DO THIS
chrome.runtime.onMessage.addListener((msg) => {
  chrome.storage.local.get('count', (result) => {
    let count = (result.count || 0) + 1
    chrome.storage.local.set({ count })
  })
})
```

Always persist state to `chrome.storage`.

---

## Common Review Rejection Reasons

The Chrome Web Store reviewers look for these:

| Reason | What triggers it | How to avoid |
|--------|-----------------|--------------|
| Misleading description | Description doesn't match actual behavior | Test that your extension does what you say it does |
| Deceptive permissions | Requests `tabs` but uses `contentScript` | Remove unused permissions |
| No clear privacy policy | Collects data but doesn't disclose it | Explain what data you collect, how long you keep it |
| Broken on load | Background script crashes | Test on chrome://extensions -> Details -> Inspect views |
| Auto-opens tabs/popups | Opens new tabs without user action | Only open new windows when user explicitly requests |
| Phishing/malware patterns | Requests unusual permission combos | Don't request permissions you don't need |
| Unwanted software | Installs other software or modifies browser settings | Don't do this |

If rejected, the email will say why. Fix it and resubmit.

---

## Tabs Permissions Explained

`tabs` and `activeTab` are different:

| Permission | What you can do |
|-----------|-----------------|
| `activeTab` (weak) | Access the current tab only, when user clicks the extension icon. Very restrictive. |
| `tabs` (strong) | Query all tabs, see their URLs, inject scripts into them. Powerful but invasive. |

**Use `activeTab` if possible.** The store reviews it less strictly.

```json
{
  "permissions": ["activeTab"]
}
```

If you need `tabs` to do something, explain why in the permissions section.

---

## Debugging Tips

**Background service worker console:**
```
chrome://extensions -> Your extension -> Details -> Inspect views -> service worker
```

**Popup console:**
```
Right-click extension icon -> Inspect popup
```

**Content script console:**
```
Open any page, press F12, check Console tab (if content script is loaded)
```

**Check if extension is running:**
```
chrome://extensions -> toggle Developer Mode (top right)
```

**Force reload extension:**
```
chrome://extensions -> Find your extension -> Reload button
```

**Check permissions:**
```
chrome://extensions -> Your extension -> Details -> Permissions
```

---

## The Biggest Gotcha

Don't build an extension that depends on running continuously. v3 service workers don't work that way.

If you're tempted to use `setInterval` in the background, stop and use `chrome.alarms` instead. If you need real-time updates, listen to messages or tab events. Let the extension be idle. Wake it on demand.

Extensions are not servers. Design them to be event-driven.
