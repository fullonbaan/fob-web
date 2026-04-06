# courses.fullonbaan.com — Setup Guide

## Overview

| What | From | To |
|------|------|----|
| Subdomain | `courses.fullonbaan.com` | Vercel training site |
| Old URL redirect | `fob-ln-training.vercel.app` | `courses.fullonbaan.com` (301) |
| /training/ fix | `fullonbaan.com/training/` | ✅ Already fixed (index.html exists) |

---

## Step 1 — Add `courses.fullonbaan.com` in Vercel

1. Go to [vercel.com/dashboard](https://vercel.com/dashboard)
2. Open the **fob-ln-training** project
3. Click **Settings → Domains**
4. Click **Add** and enter: `courses.fullonbaan.com`
5. Vercel will show you the DNS record to add (see Step 2)
6. Vercel will also ask about **www redirect** — choose "Redirect to `courses.fullonbaan.com`" (no www needed for subdomains)

---

## Step 2 — Add DNS Records at Your Domain Registrar

Log in to wherever you manage `fullonbaan.com` DNS (GoDaddy, Cloudflare, Namecheap, etc.).

Add this record:

| Type | Name | Value | TTL |
|------|------|-------|-----|
| CNAME | `courses` | `cname.vercel-dns.com` | 3600 (1 hour) |

> **Note:** If you're on Cloudflare, set the proxy to **DNS only (grey cloud)** — do NOT use the orange cloud proxy for Vercel-hosted sites.

Also add for `training` subdomain redirect (optional — redirects training.fullonbaan.com → courses):

| Type | Name | Value | TTL |
|------|------|-------|-----|
| CNAME | `training` | `cname.vercel-dns.com` | 3600 |

Then add `training.fullonbaan.com` in Vercel too, and set it to **redirect to `courses.fullonbaan.com`** with 301 in the Vercel Domains settings.

---

## Step 3 — Add vercel.json to fob-ln-training repo

Copy the file `fob-ln-training-vercel.json` (in this folder) to your fob-ln-training GitHub repo as **`vercel.json`** (in the project root).

This config redirects all `fob-ln-training.vercel.app` traffic → `courses.fullonbaan.com` with HTTP 301.

```json
{
  "redirects": [
    {
      "source": "/:path*",
      "has": [{ "type": "host", "value": "fob-ln-training.vercel.app" }],
      "destination": "https://courses.fullonbaan.com/:path*",
      "permanent": true
    }
  ]
}
```

**What this preserves:**
- `fob-ln-training.vercel.app/modules.html` → `courses.fullonbaan.com/modules.html`
- `fob-ln-training.vercel.app/pricing.html` → `courses.fullonbaan.com/pricing.html`
- `fob-ln-training.vercel.app/faq.html` → `courses.fullonbaan.com/faq.html`
- All deep links and bookmarks remain valid via 301

---

## Step 4 — Update Canonical URLs in fob-ln-training site

Once `courses.fullonbaan.com` is live, update the `<head>` of each page in the training site:

```html
<!-- index.html -->
<link rel="canonical" href="https://courses.fullonbaan.com/">

<!-- modules.html -->
<link rel="canonical" href="https://courses.fullonbaan.com/modules.html">

<!-- pricing.html -->
<link rel="canonical" href="https://courses.fullonbaan.com/pricing.html">

<!-- faq.html -->
<link rel="canonical" href="https://courses.fullonbaan.com/faq.html">
```

---

## Step 5 — Update sitemap.xml

Add the training subdomain URLs to the sitemap at `fullonbaan.com/sitemap.xml` OR create a separate `sitemap.xml` for the courses site and submit it independently to Google Search Console.

**Separate courses sitemap** (add as `sitemap.xml` in fob-ln-training project root):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://courses.fullonbaan.com/</loc>
    <lastmod>2026-04-06</lastmod>
    <changefreq>weekly</changefreq>
    <priority>0.95</priority>
  </url>
  <url>
    <loc>https://courses.fullonbaan.com/modules.html</loc>
    <lastmod>2026-04-06</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.9</priority>
  </url>
  <url>
    <loc>https://courses.fullonbaan.com/pricing.html</loc>
    <lastmod>2026-04-06</lastmod>
    <changefreq>weekly</changefreq>
    <priority>0.85</priority>
  </url>
  <url>
    <loc>https://courses.fullonbaan.com/why-us.html</loc>
    <lastmod>2026-04-06</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
  <url>
    <loc>https://courses.fullonbaan.com/testimonials.html</loc>
    <lastmod>2026-04-06</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.75</priority>
  </url>
  <url>
    <loc>https://courses.fullonbaan.com/faq.html</loc>
    <lastmod>2026-04-06</lastmod>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
</urlset>
```

---

## Step 6 — Google Search Console

### Add courses.fullonbaan.com as a property
1. Go to [search.google.com/search-console](https://search.google.com/search-console)
2. Click **Add property → URL prefix**
3. Enter `https://courses.fullonbaan.com/`
4. Verify via **HTML tag** method (add `<meta name="google-site-verification" content="...">` to training site index.html)

### Submit sitemap
1. In GSC, select the `courses.fullonbaan.com` property
2. Go to **Sitemaps** (left sidebar)
3. Enter `sitemap.xml` and click **Submit**

---

## Step 7 — Update internal links on fullonbaan.com

Update any links in the main site that currently point to `fob-ln-training.vercel.app` to use `courses.fullonbaan.com` instead.

---

## Timeline

| Step | Time to complete |
|------|-----------------|
| Add Vercel custom domain | ~5 minutes |
| DNS propagation | 5 min – 48 hours (usually <1 hour on most registrars) |
| vercel.json redirect live | Instant after deploy |
| GSC indexing | 1–14 days |
