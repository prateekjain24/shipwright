# SEO Checklist for Web Projects

Basic SEO so your project is discoverable. This is for web stacks only (Next.js, landing pages). Skip this for Chrome extensions.

---

## Meta Tags (Page Head)

### Title Tag
- **What:** The text that appears in browser tab and search results
- **Length:** 50-60 characters (anything longer gets cut off)
- **Where:** In your `<head>` element
- **Example:** `<title>Habit Tracker for Parents -- Build Daily Routines</title>`
- **How to verify:** Open your deployed URL in a browser, check the tab title

### Meta Description
- **What:** The summary snippet shown below your title in search results
- **Length:** 150-160 characters (gets cut off if longer)
- **Where:** In your `<head>` element: `<meta name="description" content="..."/>`
- **Example:** `<meta name="description" content="Free habit tracker for busy parents. Track your kids' daily routines. See progress at a glance."/>`
- **How to verify:** Use a tool like Seobility preview or just imagine how it looks in Google results

---

## Open Graph Tags (Social Sharing)

When someone shares your link on Twitter, Facebook, or Slack, OG tags control what preview they see.

| Tag | Purpose | Example |
|-----|---------|---------|
| `og:title` | Title when shared | "Habit Tracker for Parents" |
| `og:description` | Description when shared | "Free habit tracker for busy parents." |
| `og:image` | Image shown in preview | `https://yoursite.com/og-image.png` (1200x630px) |
| `og:url` | Canonical URL of the page | `https://yoursite.com` |
| `og:type` | Type of content | "website" |

### Where to put them
In your `<head>` element:
```html
<meta property="og:title" content="Habit Tracker for Parents"/>
<meta property="og:description" content="Free habit tracker for busy parents."/>
<meta property="og:image" content="https://yoursite.com/og-image.png"/>
<meta property="og:url" content="https://yoursite.com"/>
<meta property="og:type" content="website"/>
```

### How to verify
Paste your URL into Twitter's Card Validator (https://cards-dev.twitter.com/validator) or Facebook's Sharing Debugger. You'll see exactly what preview shows up.

---

## Sitemap

A sitemap tells search engines what pages exist on your site.

### For Next.js projects
Next.js can auto-generate this. Add to `next.config.js`:
```javascript
async function generateSitemap() {
  return [
    { url: 'https://yoursite.com/', changefreq: 'daily', priority: 1.0 },
    { url: 'https://yoursite.com/about', changefreq: 'monthly', priority: 0.8 },
  ]
}
```

### For simple landing pages
Create a `sitemap.xml` file at the root:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://yoursite.com/</loc>
    <changefreq>daily</changefreq>
    <priority>1.0</priority>
  </url>
</urlset>
```

### How to verify
Go to `https://yoursite.com/sitemap.xml` in your browser. If you see XML, you're good.

---

## Robots.txt

Tells search engines which pages to crawl and which to skip.

### For most projects
Create `public/robots.txt` at the root:
```
User-agent: *
Allow: /
Disallow: /admin
Sitemap: https://yoursite.com/sitemap.xml
```

### What this does
- Allows all bots to crawl everything (except /admin)
- Points to your sitemap

### How to verify
Go to `https://yoursite.com/robots.txt` in your browser. You should see the text.

---

## Structured Data (Schema)

Optional, but helps search engines understand what your page is about. Most valuable for:
- Landing pages that describe a product or service
- Blog posts
- Events

### Simple example: Product
Add this to your `<head>`:
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org/",
  "@type": "Product",
  "name": "Habit Tracker for Parents",
  "description": "Free habit tracker for busy parents",
  "image": "https://yoursite.com/og-image.png",
  "offers": {
    "@type": "Offer",
    "price": "0",
    "priceCurrency": "USD"
  }
}
</script>
```

### How to verify
Paste your URL into Google's Rich Result Test (https://search.google.com/test/rich-results). It will tell you if the schema is valid.

---

## Quick Verification Checklist

Before you call Ship done:

- [ ] Page title is 50-60 characters and includes the main keyword
- [ ] Meta description exists and is 150-160 characters
- [ ] OG tags are set (title, description, image, URL)
- [ ] Sitemap.xml exists and is valid (visit `/sitemap.xml`)
- [ ] Robots.txt exists (visit `/robots.txt`)
- [ ] Test social sharing: paste your URL into Twitter Card Validator
- [ ] OG image is at least 1200x630px (bigger is fine, but under 2MB)

---

## What This Gets You

These basics don't guarantee ranking. Google still needs to crawl you, index you, and find you relevant. But they do:

1. Make your project shareable -- when someone shares on Twitter, it looks good
2. Tell search engines what your project is about
3. Set you up for growth later -- once you have traffic, these tags matter more
4. Cost you 15 minutes of work now vs hours of work debugging mystery rankings later

For a side project MVP, this is enough to start.
