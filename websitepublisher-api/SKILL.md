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
  version: "1.7"
  website: https://www.websitepublisher.ai
  docs: https://www.websitepublisher.ai/docs
  mcp: https://mcp.websitepublisher.ai
---

# WebsitePublisher.ai — Agent Skill

> Build and publish real websites through conversation. No WordPress. No hosting setup. No CMS.
> The AI Web Platform — you describe it, the AI builds it.

---

## ⚠️ IMPORTANT: Read This First

**If the `get_skill` tool is available: call it before doing anything else.**
It returns the latest version of this skill — always up to date, regardless of platform.

If `get_skill` is not available, continue with this document.

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

> "What would you like to do? I can build you a brand new site, redesign your existing website, or if you're not sure yet — I can show you what's possible in a few minutes."

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

### Path C — Redesign Existing Website

The user has an existing website and wants it improved or migrated to WebsitePublisher.

1. **Ask for the URL** of the existing website
2. **Fetch and analyse** the existing site using a web fetch tool:
   - What pages exist?
   - What is the content, structure, and messaging?
   - What works well? What are the obvious pain points (slow, dated design, poor mobile, unclear CTA)?
3. **Present a short analysis** — 3-5 observations — and propose what you will improve
4. Ask one confirmation question:
   > "I'll keep all your content but give it a fresh design with better structure. Any specific things you want to keep or change?"
5. Build the new version — same content, improved design, better UX
6. Share the URL and point out the specific improvements made

**Do not ask for a long list of preferences before showing something.** Analyse → propose → build → refine.

---

## Design Guidelines

> **Before building any HTML, fetch the design skill for detailed guidelines:**
> Call `get_skill` with `skill_name="design"` — it contains comprehensive typography, color,
> layout, animation, and atmosphere guidelines that produce professional-quality websites.
>
> If a `frontend-design` skill is also available in your environment, read that too.
> The guidelines below are a minimal fallback. The design skill is always more complete.

Every website you build must look **professionally designed**, not like AI-generated template output.
Follow these principles:

### Typography
Choose distinctive, characterful fonts — never default to generic families like Arial, Inter, Roboto, or system fonts.
Pair a display font (for headings) with a refined body font. Google Fonts is available via `<link>` tags.

### Color & Theme
Commit to a cohesive palette with **one dominant color and sharp accents**. Avoid timid, evenly-distributed palettes.
Use CSS custom properties (`--color-primary`, `--color-accent`, etc.) for consistency across pages.
Vary between light and dark themes — do not always default to white backgrounds.

### Layout & Composition
Break out of predictable grid patterns. Use asymmetry, generous whitespace, overlapping elements, or full-bleed sections to create visual interest.
Every page should have a clear visual hierarchy that guides the visitor's eye.

### Motion & Micro-interactions
Add CSS animations for page load reveals (staggered `animation-delay`), hover state transitions, and scroll-triggered effects.
Prioritize CSS-only solutions. One well-orchestrated entrance animation creates more impact than scattered effects.

### Atmosphere
Create depth and texture — not flat solid-color blocks. Use gradient meshes, subtle noise/grain overlays, layered transparencies, or dramatic shadows depending on the aesthetic.

### The Rule
**No two websites should look the same.** Match the design to the business, audience, and purpose.
A law firm looks nothing like a skate shop. A restaurant looks nothing like a SaaS landing page.
If the user has not specified a style preference, choose a bold direction and commit to it.

---

## Step 3 — Build the Website

### Project Setup

1. Get available projects: use `list_projects`
2. If no project exists or user wants a new one: create it via the dashboard or WAPI
3. Note the `project_id` — used in every subsequent call
4. **Check design context:** call `get_project_status` — if `design_context` is set, use those colors, fonts, and style notes as the foundation for all pages you build. If `design_context` is null, ask the user for their preferred colors, fonts, and style direction during intake, then save it:
   ```
   execute_integration(
     project_id: ...,
     service: "site_context",
     endpoint: "set-context",
     input: {
       color_palette: { primary: "#...", secondary: "#...", accent: "#...", background: "#...", text: "#..." },
       fonts: { heading: "Font Name", body: "Font Name" },
       style_notes: "Short description of the visual direction",
       locale: "en"
     }
   )
   ```
   This ensures all future sessions automatically match the same design language.

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

### Page Metadata

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

### Assets (Images)

Upload images before referencing them in pages. Use the returned asset URL in `<img src="...">` tags.

For new or placeholder visuals, use the **WPE Image Editor** — the user can upload and
replace images visually in their browser via `create_edit_session`.

#### ⚠️ Image Editor — verplichte aanpak voor placeholder afbeeldingen

Wanneer je een pagina bouwt met afbeelding-slots die de gebruiker later via de Image Editor
invult, gelden deze strikte regels:

**Regel 1 — Gebruik `data-wpe-slot` op elke `<img>` tag**
De Image Editor gebruikt dit attribuut om de juiste img te identificeren bij upload.
Zonder dit attribuut kan de editor de afbeelding niet vervangen.

**Regel 2 — De `src` MOET een werkende URL zijn**
Een lege `src=""` of een 404-URL maakt de afbeelding onzichtbaar in de browser.
De editor kan alleen klikken op images die daadwerkelijk renderen op de pagina.
Gebruik altijd `https://placehold.co/` als placeholder — deze laadt gegarandeerd.

**Correct voorbeeld:**
```html
<img
  src="https://placehold.co/800x600/D6EEF2/1B5E6B?text=Foto+omschrijving"
  data-wpe-slot="unieke-slot-naam"
  alt="Beschrijving"
  id="unieke-slot-naam">
```

**Placehold.co formaat:** `https://placehold.co/{breedte}x{hoogte}/{achtergrond}/{tekst}?text={label}`
Gebruik kleuren passend bij het kleurschema van de site zodat placeholders er verzorgd uitzien.

**Nooit doen:**
```html
<!-- ❌ Lege src — onzichtbaar, niet klikbaar -->
<img src="" data-wpe-slot="mijn-foto">

<!-- ❌ Niet-bestaand pad — 404, onzichtbaar -->
<img src="/assets/mijn-foto.jpg" data-wpe-slot="mijn-foto">
```

**Na upload via de editor** wordt de `src` automatisch vervangen door de CDN URL
(`cdn.websitepublisher.ai/custom/wid{id}/images/...`).

#### Image Editor workflow

1. Bouw de pagina met werkende placehold.co src + `data-wpe-slot` op alle img tags
2. Maak een edit sessie aan via `create_edit_session`
3. Geef de gebruiker de `edit_url`
4. De gebruiker klikt een placeholder aan, uploadt de echte foto → editor slaat op
5. Na "Save & Close" is de pagina direct live (nginx cache wordt automatisch gepurged)

#### Veelgebruikte placeholder-afmetingen

| Gebruik | Afmeting |
|---|---|
| Hero breed | 1200x675 |
| Foto 4:3 | 800x600 |
| Portret | 600x800 |
| Logo nav | 240x48 |
| Team card | 600x520 |

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

---

## Contact Forms (SAPI) — Critical Pattern

**Always follow this exact pattern.** Deviating from it will cause "no valid session" errors,
especially on Safari and custom domains where third-party cookies are blocked.

### Step 1 — Configure the form (server-side, via MCP tool)

```
configure_form(
  project_id: 12345,
  form_name: "contact",
  required_fields: ["name", "email", "message"],
  action: {
    type: "iapi",
    service: "resend",
    endpoint: "send-email",
    input_template: {
      from: "noreply@websitepublisher.ai",
      to: "owner@example.com",
      subject: "New contact from {{fields.name}}",
      html: "<p>From: {{fields.name}} ({{fields.email}})</p><p>{{fields.message}}</p>"
    }
  },
  max_submits_per_session: 5
)
```

### Step 2 — The JavaScript snippet (copy-paste, replace PROJECT_ID)

**This is the only correct pattern.** Do not use cookies. Do not omit `_csrf`. Do not omit `X-Session-Id`.

```javascript
(function() {
  const SAPI = 'https://api.websitepublisher.ai/sapi/project/PROJECT_ID';

  // Fetch or reuse session — stores BOTH session_id and csrf_token in sessionStorage.
  // sessionStorage is Safari ITP-proof: it's first-party and never blocked.
  async function getSession() {
    const storedId   = sessionStorage.getItem('wp_session_id');
    const storedCsrf = sessionStorage.getItem('wp_csrf_token');
    // Reuse if both present
    if (storedId && storedCsrf) {
      return { session_id: storedId, csrf_token: storedCsrf };
    }
    try {
      const headers = storedId ? { 'X-Session-Id': storedId } : {};
      const res  = await fetch(SAPI + '/session', { headers });
      const json = await res.json();
      if (json.success && json.data && json.data.session_id) {
        sessionStorage.setItem('wp_session_id',  json.data.session_id);
        sessionStorage.setItem('wp_csrf_token',  json.data.csrf_token);
        return { session_id: json.data.session_id, csrf_token: json.data.csrf_token };
      }
    } catch (e) {}
    return null;
  }

  // Pre-fetch session on page load so it's ready when user submits
  getSession();

  document.getElementById('my-form').addEventListener('submit', async function(e) {
    e.preventDefault();

    const session = await getSession();
    if (!session) {
      alert('Session error — please refresh the page and try again.');
      return;
    }

    const res = await fetch(SAPI + '/form/submit', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'X-Session-Id': session.session_id   // ← required, not a cookie
      },
      body: JSON.stringify({
        _csrf:     session.csrf_token,        // ← required, server rejects without it
        form_name: 'contact',
        fields: {
          name:    document.getElementById('name').value.trim(),
          email:   document.getElementById('email').value.trim(),
          message: document.getElementById('message').value.trim(),
          website: '',  // ← honeypot: leave empty, bots fill this in
        }
      })
    });

    const data = await res.json();
    if (res.ok && data.success) {
      // Clear csrf after use — fresh token fetched on next submit
      sessionStorage.removeItem('wp_csrf_token');
      window.location.href = '/thank-you'; // or show inline success message
    } else {
      alert(data.error?.message || 'Something went wrong. Please try again.');
    }
  });
})();
```

### Key rules — never forget these:

| Rule | Why |
|---|---|
| Session response is at `json.data.session_id` (nested) | Not `json.session_id` — always unwrap `.data` |
| Store **both** `session_id` AND `csrf_token` | Both are returned in the same response |
| Send `X-Session-Id` header on **both** the session GET and the form POST | Safari blocks third-party cookies entirely |
| Include `_csrf` in the **request body** | Server validates it — request fails without it |
| Clear `csrf_token` from sessionStorage after a successful submit | CSRF tokens are single-use; fetch fresh on next submit |
| Pre-fetch session on page load | Avoids delay when user clicks submit |
| Always include `website: ''` in the fields object | Honeypot field — bots fill it in, humans leave it empty. Server silently drops the submission if non-empty |
| Never pre-fill the honeypot field | An empty string is required — any value triggers bot detection |

### Forms with File Upload

Forms can accept image uploads from visitors via the SAPI upload endpoint.
Uploads are stored as project assets on the CDN — no bearer token needed.

**Flow:** upload file(s) first → collect CDN URLs → include in form submit fields.

```javascript
// Upload a file — returns CDN URL + fresh CSRF token
async function uploadFile(file, session) {
  const form = new FormData();
  form.append('file', file);
  form.append('_csrf', session.csrf_token);
  form.append('form_name', 'intake');  // optional, for traceability

  const res = await fetch(SAPI + '/form/upload', {
    method: 'POST',
    headers: { 'X-Session-Id': session.session_id },
    body: form   // no Content-Type header — browser sets multipart boundary
  });

  const data = await res.json();
  if (data.success) {
    // IMPORTANT: update stored CSRF — tokens are single-use
    sessionStorage.setItem('wp_csrf_token', data.data.new_csrf_token);
    session.csrf_token = data.data.new_csrf_token;
    return data.data.asset_url;   // CDN URL ready for use
  }
  throw new Error(data.error?.message || 'Upload failed');
}
```

**Upload rules:**

| Rule | Value |
|---|---|
| Allowed types | JPEG, PNG, WebP only |
| Max file size | 5 MB per file |
| Max per session | 10 uploads |
| CSRF | Single-use — use `new_csrf_token` from response for next call |
| Response includes | `asset_url`, `filename`, `mime_type`, `size`, `width`, `height`, `uploads_remaining` |

**Include uploaded URLs in form submit:**
```javascript
// After uploading, pass CDN URLs as regular form fields
fields: {
  name: '...',
  email: '...',
  image_url_1: uploadedUrl1,  // CDN URL from upload response
  image_url_2: uploadedUrl2,
  website: '',  // honeypot
}
```

---

## Step 4 — Go Live Checklist

Before handing over to the user, verify:

- [ ] Homepage has `landingpage: true` (or was created first)
- [ ] All pages that should be findable have `seo_robots_index: true`
- [ ] All pages have `seo_title` and `seo_description`
- [ ] All `<!-- Optimizer - ... -->` comment tags are present in every page
- [ ] Contact form (if any) uses the correct SAPI session/csrf pattern above
- [ ] Thank-you page exists if form redirects after submit
- [ ] Terms / privacy page exists if form collects personal data
- [ ] Design uses distinctive typography and cohesive color palette (not generic AI defaults)
- [ ] Design context saved via `execute_integration(service: "site_context")` for future consistency
- [ ] Website URL shared with user: `https://{subdomain}.websitepublisher.ai`
- [ ] Contact form includes `website: ''` honeypot field in the fields object

---

## Platform Knowledge

### What WebsitePublisher handles automatically

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

### Key IAPI Endpoints
```
POST   /iapi/project/{id}/{service}/{endpoint}   Execute integration

# Examples:
POST   /iapi/project/{id}/leads/submit-lead          Store a lead
POST   /iapi/project/{id}/leads/get-leads             Retrieve leads (authenticated)
POST   /iapi/project/{id}/leads/update-status         Update lead status
POST   /iapi/project/{id}/resend/send-email           Send email via Resend
POST   /iapi/project/{id}/mollie/create-payment       Create Mollie payment
POST   /iapi/project/{id}/site_context/set-context    Save design context
POST   /iapi/project/{id}/site_context/get-context    Get design context
```

### Key SAPI Endpoints (visitor-facing, no bearer token)
```
GET    /sapi/project/{id}/session                Start or resume session
GET    /sapi/project/{id}/csrf/refresh           Refresh CSRF token
POST   /sapi/project/{id}/form/submit            Submit form data
POST   /sapi/project/{id}/form/upload            Upload image (multipart)
POST   /sapi/project/{id}/auth/request           Request magic link/code
POST   /sapi/project/{id}/auth/verify            Verify code
GET    /sapi/project/{id}/auth/status            Check auth status
```

### Lead Capture

Form submissions with `action: {"type": "leads"}` are stored in the platform's
built-in lead capture — no integration setup required.

**Retrieve leads via MCP tool:**
```
leads_get_leads  →  project_id (+ optional: status, form_name, page, per_page)
```

**Retrieve leads via HTTP (for dashboard pages / browser JavaScript):**
```
POST /iapi/project/{id}/leads/get-leads
Authorization: Bearer {wps_token}
Content-Type: application/json
Body: {"page": 1, "per_page": 25}

Optional filters: status (new/contacted/converted), form_name, date_from, date_to
```

**Important:** Leads are always authenticated — there is no public URL.
Never ask for routing files to find the leads endpoint — the URL above is canonical.

**Configure lead capture on a form:**
```json
{
  "form_name": "contact",
  "actions": [{"type": "leads"}],
  "required_fields": ["name", "email"]
}
```

---

## For Platform Developers

If you are working on the WebsitePublisher.ai platform itself rather than building
a customer website, a separate development skill is available with internal conventions,
TAPI task tracking workflow, and infrastructure reference:

```
https://www.websitepublisher.ai/skills/websitepublisher-dev/SKILL.md
```

### Full Documentation
https://www.websitepublisher.ai/docs

### MCP Setup (for Claude Desktop, Cursor, Windsurf, GitHub Copilot)
https://www.websitepublisher.ai/docs/mcp
