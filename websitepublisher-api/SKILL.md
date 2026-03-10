---
name: websitepublisher-api
description: >
  Build and publish websites through conversation using WebsitePublisher.ai.
  Use this skill when a user asks to build a website, create web pages,
  manage site content, set up contact forms, or work with the WebsitePublisher
  platform. Covers all API layers: PAPI (pages/assets), MAPI (entities/data),
  SAPI (forms), VAPI (credentials), IAPI (integrations), and the WPE Image Editor.
license: MIT
metadata:
  author: websitepublisher-ai
  version: "1.0"
  website: https://www.websitepublisher.ai
  docs: https://www.websitepublisher.ai/docs
  mcp: https://mcp.websitepublisher.ai
---

# WebsitePublisher.ai — Agent Skill

> Build and publish real websites through conversation. No WordPress. No hosting setup. No CMS.
> The AI Web Platform — you describe it, the AI builds it.

---

## Step 1 — Check Connection

Before doing anything, verify the user is connected to WebsitePublisher.ai.

**If connected** (tools respond correctly): proceed to Step 2.

**If not connected**: explain in simple terms:

> "To build your website I need to connect to WebsitePublisher.ai. All you need is your email address — I'll guide you through the rest. It takes about 30 seconds."

Direct the user to sign in at: **https://www.websitepublisher.ai/dashboard**
After signing in, they return here and you continue from Step 2.

---

## Step 2 — Choose the Path

Once connected, ask ONE simple question:

> "Would you like to see what's possible first — I'll build you a demo site in minutes — or do you already have something specific in mind?"

### Path A — "Wow Me" (show first, ask later)

The user is curious but not yet convinced. **Do not ask a long list of questions.**

Ask only:
> "What kind of business or project is this for? Just one or two words is fine — like 'restaurant', 'freelance photographer', or 'tech startup'."

Then immediately build a **complete, impressive demo website** based on that one answer. Use your creativity. Make it beautiful. Show what AI can do.

After the demo is live, share the URL and say:
> "Here's what I built in a few minutes. Want to make it yours? I have a few quick questions to personalise it."

Then move to the intake (Path B) — the user is now convinced.

### Path B — Full Intake (user knows what they want)

Ask questions **one at a time**, conversationally. Do not present a form or list.
Speak like a consultant, not a questionnaire. Use simple, friendly language.

**Phase 1 — Goal (start here)**
- What should the website achieve? (get customers, show portfolio, sell something, inform people?)
- Who is the target audience?
- Does a website already exist? If yes: what needs to improve?

**Phase 2 — Identity**
- Business or project name?
- What does the business do? (ask for a short description in their own words)
- Contact details: email, phone, address (only ask what is relevant)
- Logo and brand colours available? (if yes: ask them to share)

**Phase 3 — Style & Technical**
- Three websites they like the look of (not necessarily competitors) — and why?
- Domain name already registered? If yes: which one?
- Any specific pages needed? (about, services, contact, blog, portfolio...)
- Any keywords important for search engines?

**Do not ask all questions if answers make some irrelevant.** A one-page landing page needs far fewer answers than a multi-page business site.

---

## Step 3 — Build the Website

### Project Setup

1. Get available projects: use `list_projects` or `GET /papi/projects`
2. If no project exists or user wants a new one: create it via `create_website` / WAPI
3. Note the `project_id` — used in every subsequent call

### Page Structure Guidelines

Plan the pages before building. Common structures:

| Website type | Recommended pages |
|---|---|
| Landing page | index only |
| Business / SME | index, about, services, contact |
| Portfolio | index, work/projects, about, contact |
| Restaurant | index, menu, about, reservations/contact |
| Blog | index, blog-overview, post-template, about |

Always create an `index` page first — this becomes the homepage.

### Building Pages

Every page must be a **complete, valid HTML document**:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title</title>
    <!-- Optimizer - Canonical -->
    <!-- Optimizer - Custom Meta -->
    <!-- Optimizer - Open Graph -->
    <!-- Optimizer - Header Javascripts -->
</head>
<body>

    <!-- page content here -->

    <!-- Optimizer - Footer Javascripts -->
</body>
</html>
```

**Critical:** Always include the `<!-- Optimizer - ... -->` comment tags exactly as shown.
These activate WebsitePublisher's built-in SEO engine: canonical tags, Open Graph headers,
custom scripts, and tracking injection. They are invisible to visitors — the platform
processes and removes them automatically.

### Page Metadata (pageMeta)

When creating or updating a page, you can pass these metadata fields:

```json
{
  "slug": "about",
  "content": "<!DOCTYPE html>...",
  "meta": {
    "seo_title": "About Us — Company Name",
    "seo_description": "We are a ...",
    "seo_keywords": "keyword1, keyword2",
    "seo_robots_index": true,
    "seo_robots_follow": true,
    "page_language": "en",
    "landingpage": false
  }
}
```

**Field reference:**

| Field | Default | Notes |
|---|---|---|
| `seo_title` | Page name | Shown in browser tab and search results |
| `seo_description` | — | Search result snippet, 150-160 chars ideal |
| `seo_keywords` | — | Maximum 9, comma-separated |
| `seo_robots_index` | false | Set true to include in sitemap and search engines |
| `seo_robots_follow` | false | Set true to allow link following |
| `page_language` | — | ISO code e.g. "en", "nl", "de" |
| `landingpage` | false | Set true to make this the homepage |
| `redirect_code` | — | 301 or 302 — turns page into a redirect |
| `redirect_destination` | — | Full URL or relative path for redirect target |

**SEO tip:** A page only appears in the sitemap once `seo_robots_index` is true.
Pages without this flag are live but not discoverable by search engines — useful for
"soft launch" or pages still being built.

**Homepage tip:** The first page is automatically set as homepage. To change the homepage
later, set `landingpage: true` on the new homepage page.

**Redirect tip:** To create a redirect, create a page with a slug that has no content
conflict, set `redirect_code: 301` and `redirect_destination`. Do not include HTML content.

### Assets (Images)

Upload images before referencing them in pages:

```
POST /papi/project/{id}/assets
```

Use the returned asset URL directly in `<img src="...">` tags.

For new or placeholder visuals, suggest using the **WPE Image Editor** — the user can
upload and edit images visually in their browser, and the AI reads back the result.

### Dynamic Data (MAPI)

Use MAPI when the website has **repeating structured content**: menu items, team members,
portfolio projects, testimonials, blog posts, products, events.

1. Define an entity schema (e.g. "MenuItem" with fields: name, description, price, category)
2. Create records for each item
3. Fetch records in the page via JavaScript using the public MAPI endpoint

```javascript
fetch('https://api.websitepublisher.ai/mapi/public/{project_id}/menuitems')
  .then(r => r.json())
  .then(data => { /* render items */ });
```

### Contact Forms (SAPI)

To add a contact form:

1. Create a form config via SAPI with fields, validation rules, and notification email
2. Use the SAPI session endpoint to get a CSRF token
3. The form submits to the SAPI endpoint — no backend code needed in the page

SAPI handles: CSRF protection, spam filtering (honeypot), email notifications, and
storing submissions.

---

## Step 4 — Go Live Checklist

Before handing over to the user, verify:

- [ ] Homepage has `landingpage: true` (or was created first)
- [ ] All pages that should be findable have `seo_robots_index: true`
- [ ] All pages have `seo_title` and `seo_description` filled in
- [ ] All `<!-- Optimizer - ... -->` comment tags are present in every page
- [ ] Contact form (if any) is connected via SAPI
- [ ] Website URL shared with user: `https://{subdomain}.websitepublisher.ai`

---

## Platform Knowledge

### What WebsitePublisher handles automatically

These things work without any extra configuration:

| Feature | How it works |
|---|---|
| **Sitemap** | Auto-generated. Pages appear when `seo_robots_index: true` |
| **robots.txt** | Auto-generated with "Allow all" + sitemap reference |
| **SSL certificate** | Auto-provisioned via Let's Encrypt on custom domains |
| **Canonical tags** | Injected by Optimizer when comment tag is present |
| **Open Graph** | Injected by Optimizer (uses SEO title/description) |
| **Static caching** | Pages are served as static files — extremely fast |
| **CDN** | Assets served via cdn.websitepublisher.ai |

### What requires API calls

| Feature | API |
|---|---|
| Pages and content | PAPI |
| Dynamic data / entities | MAPI |
| Contact forms | SAPI |
| Third-party integrations | IAPI + VAPI |
| Image editing | WPE (browser-based) |
| Clone a website | WAPI clone endpoint |

### Redirects

Redirects are page-level. Create a page with the source slug, set `redirect_code` and
`redirect_destination`. No slug conflicts with existing content pages are allowed.

### Cloning a Website

To duplicate an existing website (e.g. for a new client based on a template):

```
POST /wapi/websites/{source_project_id}/clone
```

This deep-copies all pages, assets, and S3 files. Only the latest version of each file
is copied — revision history is not included. The user must own the source project.

### What the AI does NOT need to build

- Sitemap → automatic
- robots.txt → automatic
- SSL → automatic
- Page caching → automatic
- Canonical tags → automatic (via Optimizer comment tags)

---

## API Quick Reference

### Base URLs
```
Pages & Assets:    https://api.websitepublisher.ai/papi/
Entities & Data:   https://api.websitepublisher.ai/mapi/
Forms & Sessions:  https://api.websitepublisher.ai/sapi/
Vault:             https://api.websitepublisher.ai/vapi/
Integrations:      https://api.websitepublisher.ai/iapi/
Dashboard:         https://api.websitepublisher.ai/dapi/
```

### Authentication
```
Bearer token in Authorization header:
  wps_... = project-scoped key
  wpa_... = account-wide key
  OAuth token (via MCP or GPT Actions)
```

### Key PAPI Endpoints
```
GET    /papi/projects                           List projects
GET    /papi/project/{id}/pages                 List pages
POST   /papi/project/{id}/pages                 Create page
PUT    /papi/project/{id}/pages/{slug}          Update page
DELETE /papi/project/{id}/pages/{slug}          Delete page
POST   /papi/project/{id}/assets                Upload asset
GET    /papi/project/{id}/pages?type=fragment   List fragments
```

### Full Documentation
https://www.websitepublisher.ai/docs

### MCP Setup (for Claude Desktop, Cursor, Windsurf, GitHub Copilot)
https://www.websitepublisher.ai/docs/mcp
