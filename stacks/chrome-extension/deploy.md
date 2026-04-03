# Chrome Extension Deployment

## Build and Package

### Build the Extension

With Vite + CRXJS:
```bash
npm run build
```

Output: `dist/` folder with all files bundled.

With Plasmo:
```bash
npm run build
```

Output: `build/chrome-mv3-prod/` folder.

### Zip for Upload

```bash
cd dist
zip -r ../my-extension.zip .
```

Or on Mac:
```bash
cd dist && ditto -c -k --sequesterRsrc . ../my-extension.zip
```

Result: `my-extension.zip` file ready to upload.

Don't include `node_modules/`, source maps, or `.env` files. The build folder should only have compiled files and assets.

---

## Chrome Web Store: Step by Step

### 1. Register as a Developer ($5 one-time fee)

Go to [Chrome Web Store Developer Dashboard](https://chrome.google.com/webstore/devconsole).

Sign in with your Google account. Pay the $5 registration fee (one-time, non-refundable). Takes minutes.

### 2. Create a New Item

Click "New item" in the dashboard.

Upload your `my-extension.zip` file. Chrome validates it automatically. If there are issues, you'll see errors -- fix and re-upload.

### 3. Fill Out the Store Listing

**Basic info:**
- Name (up to 45 characters)
- Short description (up to 132 characters)
- Detailed description (write like you're explaining to a user, not technical)

**Category:** Pick one (Productivity, Messaging, Shopping, etc).

**Language:** Primary language (usually English).

### 4. Upload Graphics

**Icon** (128x128 PNG): The extension icon users see in the store.

**Screenshots** (at least 1, up to 5, 1280x800 or 640x400):
- Show the extension in action.
- Annotate with text explaining what it does.
- First screenshot is the hero image.

**Promotional tile** (440x280 PNG): Optional, shown in store featured sections.

Avoid text smaller than 10pt -- it won't be readable in the store.

### 5. Privacy and Permissions

The store scans your manifest and asks about permissions.

- **activeTab**: Are you only using this on the user's current tab? Yes? Explain.
- **content_scripts**: Which sites do you inject into? Why?
- **storage**: What data do you store? Is it user data? How long?

Be honest. The review team reads these.

Example for a tab highlighter:
> "We use activeTab permission to highlight important elements on the current page only. We store user preferences (highlight color, themes) in chrome.storage.local. No data is sent off-device."

### 6. Content Rating Questionnaire

Simple questions about your extension's content. Usually: Does it have adult content? Violence? Hate speech? Answer honestly.

### 7. Submit for Review

Click "Submit for review."

**Review timeline:** 1--3 business days. Usually 1--2 days. You'll get email updates.

---

## Common Review Rejections

The store has automated checks, then human reviewers. Here's what gets rejected:

| Issue | Why | How to fix |
|-------|-----|-----------|
| Misleading name/description | "Cloud Storage Pro" but it just opens Google Drive | Make description match what it actually does |
| Broken permissions | Requests `tabs` but never uses it | Remove unused permissions from manifest |
| Data collection not disclosed | Stores user history but doesn't say so | Update privacy section to list what you collect |
| Crashes on load | Background service worker throws error | Test thoroughly before submitting |
| No clear purpose | Generic, unclear what it does | Write a description a 10-year-old would understand |
| Auto-opens popups or spam | Opens new tabs without user action | Don't do this |
| Malware/phishing signs | Suspicious permission use patterns | Don't request permissions you don't need |

---

## Resubmission

If rejected:
1. Read the rejection reason email carefully.
2. Fix the issue (update code, manifest, description, whatever they flagged).
3. Build and zip again.
4. Upload the new zip.
5. Click "Resubmit for review" (not "submit new item" -- same listing).

Usually approved within 1 business day on resubmit.

---

## Unlisted Distribution (For Testing, Not Store Release)

If you don't want to publish to the store, you can share the extension directly:

### Option 1: Share the ZIP

Send `my-extension.zip` to testers. They install via:
1. Go to `chrome://extensions/`
2. Toggle "Developer mode" (top right)
3. Click "Load unpacked"
4. Select the unzipped folder

Works, but clunky. Only for small groups.

### Option 2: Upload to Your Own Site

Host the zip on your website. Testers download and install via "Load unpacked."

### Option 3: Use Chrome Web Store Unlisted

In the Chrome Web Store Developer Dashboard:

1. Upload your zip (same as above).
2. Fill only the required fields (name, description, icon, screenshots).
3. Under "Visibility," choose "Unlisted."
4. Submit for review (still reviewed, takes 1--3 days).
5. Copy the store URL. Only people with the link can install it.

**Advantage over ZIP:** Users get auto-updates when you push new versions.

**Disadvantage:** Still goes through review (just not featured in the store).

---

## Updates and New Versions

After your extension is live:

1. Update the `version` field in manifest.json:
   ```json
   "version": "1.0.1"
   ```

2. Build and zip again:
   ```bash
   npm run build
   cd dist && zip -r ../my-extension.zip .
   ```

3. In the Developer Dashboard, click "Upload updated package" under your item.

4. Upload the zip.

5. Fill out what changed (in a "What's new" field).

6. Submit for review (same 1--3 day timeline).

Users with the extension installed get auto-updated within hours.

---

## Monitoring After Launch

In the Developer Dashboard:

- **Reviews and ratings**: See what users think. Read 1-star reviews first -- they're feedback.
- **Statistics**: How many installs, uninstalls, active users.
- **Abuse reports**: If someone flags your extension, you'll get an email. Respond.

---

## Testing Before Submission

Before uploading to the store:

1. Load unpacked locally (chrome://extensions, Load unpacked, select dist/).
2. Test every feature. Especially:
   - Does the popup open and close?
   - Do permissions work? (Can you read the page? Access tabs?)
   - Does messaging work (popup ↔ background)?
3. Check the background service worker console:
   - chrome://extensions -> Your extension -> Details -> Inspect views -> service worker
   - Look for errors.
4. Check the content script console:
   - Open DevTools on any page. Check for errors.
5. Test on multiple Chrome versions (you can't control this after launch, so test on the stable version).

---

## Troubleshooting

**Extension doesn't load when unpacked:**
- Check manifest.json for syntax errors (invalid JSON).
- Check build output folder exists and has index.html/manifest.json.
- Reload extension (toggle off/on).

**Popup is blank:**
- Check popup.html path matches manifest.
- Check for errors in popup.tsx console (right-click extension icon -> Inspect popup).
- Verify Vite/Plasmo build succeeded.

**Background service worker keeps stopping:**
- v3 service workers unload after inactivity. This is normal.
- If your extension needs to run continuously, use `chrome.alarms` to wake it periodically.
- See gotchas.md.

**Content script not injecting:**
- Check manifest.json `content_scripts` section.
- Verify `matches` pattern matches the page URL.
- Reload the extension and refresh the page.
- Open DevTools console on the page -- check for errors.

**Rejected for unclear permissions:**
- Read the rejection email carefully.
- Update the privacy and permissions description to explain exactly what you're doing.
- Resubmit.
