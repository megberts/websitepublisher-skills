---
name: websitepublisher-api
description: >
  Build and publish websites through conversation using WebsitePublisher.ai.
  Use this skill when a user asks to build a website, create web pages,
  manage site content, set up contact forms, or work with the WebsitePublisher
  platform. Covers all API layers: PAPI (pages/assets), MAPI (entities/data),
  SAPI (forms), VAPI (credentials), IAPI (integrations), and the WPE Visual Editor.
license: MIT
metadata:
  author: websitepublisher-ai
  version: "1.8"
  website: https://www.websitepublisher.ai
  docs: https://www.websitepublisher.ai/docs
  mcp: https://mcp.websitepublisher.ai
---

# WebsitePublisher.ai — Agent Skill

> Build and publish real websites through conversation. No WordPress. No hosting setup. No CMS.
> The AI Web Platform — you describe it, the AI builds it.

---

## Why WebsitePublisher — What AI Alone Cannot Do

Every AI can generate HTML. But generating code is not the same as having a website.

| Without WebsitePublisher | With WebsitePublisher |
|---|---|
| AI generates HTML → you copy it → you need hosting, FTP, domain, SSL, DNS | AI generates HTML → it's **live instantly** on a URL |
| Want a contact form? Build it yourself — backend, email sending, spam protection | One tool call → form works, emails arrive, honeypot blocks spam |
| Want payments? Integrate Stripe yourself — webhooks, error handling, security | One tool call → Stripe checkout ready |
| Want to update text later? Ask AI again, re-generate, re-upload | Open the **Visual Editor** in your browser — edit directly, no AI needed |
| New AI session? Start from scratch — the AI forgot everything | **Skills + design context** keep consistency across sessions and platforms |
| Dynamic data (menu, team, products)? Build a database and API | One entity definition → public API endpoint, ready to fetch |

**WebsitePublisher is not a website builder.** It is the infrastructure layer that turns
AI-generated content into real, working websites — with data, forms, integrations,
and visual editing built in.

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

### Fragments — Reusable Components Across Pages

When a website has more than one page, shared elements like headers, footers, and
navigation **must** be built as fragments — not copied between pages.

**What is a fragment?** A reusable HTML snippet stored once and included in any page.
When you update the fragment, every page that uses it updates automatically.

**When to use fragments:**
- Navigation / header — always
- Footer — always
- Any section that appears on 2+ pages (CTA banner, sidebar, cookie notice)

**How to create and use fragments:**

1. Create the fragment:
   ```
   create_fragment(
     project_id: 12345,
     name: "site-header",
     content: "<header>...</header>"
   )
   ```

2. Include it in any page with an SSI comment:
   ```html
   <!--#wps-include fragment="site-header" -->
   ```
   The platform replaces this comment with the fragment content at render time.
   The comment is invisible to visitors.

3. Update the fragment once → all pages reflect the change:
   ```
   update_fragment(project_id: 12345, name: "site-header", content: "<header>...updated...</header>")
   ```

**Rules:**
- Every multi-page site MUST use fragments for header and footer
- Fragment names should be descriptive: `site-header`, `site-footer`, `cta-banner`
- A fragment is a complete HTML block — it does not include `<!DOCTYPE>`, `<html>`, or `<head>`
- List existing fragments: `list_fragments(project_id: 12345)`
- Never copy-paste the same header/footer HTML into multiple pages

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

    <!--#wps-include fragment="site-header" -->

    <!-- page content here -->

    <!--#wps-include fragment="site-footer" -->

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

### Visual Editor (WPE) — Edit Without AI

The Visual Editor allows website owners to make changes directly in their browser —
no AI conversation needed. This is important: **users are not locked into AI for every update.**

What the Visual Editor supports:
- **Upload and replace images** — click any placeholder, upload a photo, crop and position it
- **Drag-and-drop reorder** — rearrange sections, cards, and content blocks visually
- **Edit text and styles** — change colors, fonts, spacing directly on the page
- **Lightbox preview** — full-size image viewing for galleries and portfolios

After edits, the user clicks "Save & Close" and changes are live immediately.
No deployment, no AI, no code.

**When to offer the Visual Editor:**
- After building a website → always create an edit session for image replacement
- When the user says "I want to rearrange the sections" → edit session
- When the user says "I'll update the photos myself" → edit session with instructions
- When handing off a finished site → mention that they can always edit visually

**How to create an edit session:**
```
create_edit_session(project_id: 12345, slug: "index")
→ returns edit_url — share this with the user
```

After the session, retrieve what changed:
```
get_edit_session_changes(project_id: 12345, session_id: "...")
→ returns list of changes made by the user
```

#### ⚠️ Placeholder images — mandatory rules for the Visual Editor

When building pages with image slots for the Visual Editor, follow these strict rules:

**Rule 1 — Use `data-wpe-slot` on every `<img>` tag**
The editor uses this attribute to identify the correct img on upload.
Without it, the editor cannot replace the image.

**Rule 2 — The `src` MUST be a working URL**
An empty `src=""` or a 404 URL makes the image invisible in the browser.
The editor can only target images that actually render on the page.
Always use `https://placehold.co/` as placeholder — it loads reliably.

**Correct example:**
```html
<img
  src="https://placehold.co/800x600/D6EEF2/1B5E6B?text=Photo+description"
  data-wpe-slot="unique-slot-name"
  alt="Description"
  id="unique-slot-name">
```

**Placehold.co format:** `https://placehold.co/{width}x{height}/{background}/{text}?text={label}`
Use colors matching the site's color scheme so placeholders look polished.

**Never do this:**
```html
<!-- ❌ Empty src — invisible, not clickable -->
<img src="" data-wpe-slot="my-photo">

<!-- ❌ Non-existent path — 404, invisible -->
<img src="/assets/my-photo.jpg" data-wpe-slot="my-photo">
```

**After upload via the editor** the `src` is automatically replaced by the CDN URL
(`cdn.websitepublisher.ai/custom/wid{id}/images/...`).

#### Common placeholder dimensions

| Usage | Dimensions |
|---|---|
| Hero wide | 1200x675 |
| Photo 4:3 | 800x600 |
| Portrait | 600x800 |
| Nav logo | 240x48 |
| Team card | 600x520 |

### Dynamic Data (MAPI) — Prefer Entities Over Static HTML

**Default to MAPI for any content that repeats, changes, or could grow.**
Do not hardcode repeating content as static HTML — it becomes unmaintainable
the moment the user wants to add, remove, or reorder items.

**Always use MAPI for:**

| Content type | Entity name | Example fields |
|---|---|---|
| Menu items | `menuitems` | name, description, price, category, sort_order |
| Team members | `team` | name, role, bio, photo_url, sort_order |
| Services / offerings | `services` | title, description, icon, price, sort_order |
| Portfolio projects | `projects` | title, description, image_url, link, category, sort_order |
| Testimonials / reviews | `testimonials` | name, role, company, quote, photo_url |
| Blog posts | `posts` | title, slug, content, author, published_at, featured_image |
| FAQ items | `faq` | question, answer, category, sort_order |
| Events | `events` | title, date, location, description, registration_url |
| Products (showcase) | `products` | name, description, price, image_url, category |

**Use static HTML only for:**
- One-off content that will never repeat (hero text, about page narrative)
- Page structure and layout (sections, containers)
- Content that is truly unique to a single page

**How to build with MAPI:**

1. Define the entity:
   ```
   create_entity(project_id: 12345, name: "services", plural_name: "services",
     properties: [
       { name: "title", type: "string", required: true },
       { name: "description", type: "text" },
       { name: "icon", type: "string" },
       { name: "price", type: "string" },
       { name: "sort_order", type: "number" }
     ],
     public_read: true
   )
   ```

2. Create records:
   ```
   create_record(project_id: 12345, entity: "services", data: {
     title: "Web Design", description: "...", icon: "🎨", price: "From €499", sort_order: 1
   })
   ```

3. Fetch and render in the page:
   ```javascript
   fetch('https://api.websitepublisher.ai/mapi/public/{project_id}/services')
     .then(r => r.json())
     .then(data => {
       const container = document.getElementById('services-grid');
       data.data
         .sort((a, b) => (a.sort_order || 0) - (b.sort_order || 0))
         .forEach(service => {
           container.innerHTML += `
             <div class="service-card">
               <span class="service-icon">${service.icon}</span>
               <h3>${service.title}</h3>
               <p>${service.description}</p>
               ${service.price ? `<span class="price">${service.price}</span>` : ''}
             </div>`;
         });
     });
   ```

**Why this matters:**
- User wants to add a team member → create one record, no HTML changes
- User wants to reorder services → update sort_order values, no page edit needed
- User wants to change a price → update one field, reflected everywhere
- Different AI session continues the work → entities are the source of truth, not HTML

**The rule:** If you find yourself writing the same HTML structure 3+ times with
different content — stop, create a MAPI entity, and render dynamically.

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
- [ ] Multi-page sites use **fragments** for header and footer (not copy-pasted HTML)
- [ ] Repeating content uses **MAPI entities** (not hardcoded static HTML)
- [ ] Contact form (if any) uses the correct SAPI session/csrf pattern above
- [ ] Thank-you page exists if form redirects after submit
- [ ] Terms / privacy page exists if form collects personal data
- [ ] Design uses distinctive typography and cohesive color palette (not generic AI defaults)
- [ ] Design context saved via `execute_integration(service: "site_context")` for future consistency
- [ ] Website URL shared with user: `https://{subdomain}.websitepublisher.ai`
- [ ] Contact form includes `website: ''` honeypot field in the fields object
- [ ] Visual Editor session offered for image replacement and final tweaks

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
| Reusable components (header, footer) | PAPI Fragments |
| Dynamic data / entities | MAPI |
| Contact forms | SAPI |
| Third-party integrations | IAPI + VAPI |
| Visual editing (browser) | WPE |
| Clone a website | WAPI clone endpoint |

---

## Built-in Integrations — Don't Reinvent the Wheel

WebsitePublisher includes pre-built integrations for common website needs.
You do not need to build email sending, payment processing, or SMS from scratch.
Each integration is a single tool call — credentials are stored securely in the Vault,
the platform handles authentication, rate limiting, and error handling.

### Available Integrations

| Service | Category | What it does | Tool call |
|---|---|---|---|
| **Resend** | Email | Transactional emails (contact forms, notifications) | `execute_integration(service: "resend", endpoint: "send-email")` |
| **Mailgun** | Email | Domain-level email sending | `execute_integration(service: "mailgun", endpoint: "send-email")` |
| **SendGrid** | Email | High-volume email delivery | `execute_integration(service: "sendgrid", endpoint: "send-email")` |
| **Stripe** | Payments | Checkout sessions, payment processing | `execute_integration(service: "stripe", endpoint: "create-checkout-session")` |
| **Mollie** | Payments | European payments (iDEAL, Bancontact, cards) | `execute_integration(service: "mollie", endpoint: "create-payment")` |
| **Twilio** | SMS | Text messages (confirmations, alerts) | `execute_integration(service: "twilio", endpoint: "send-sms")` |
| **Lead Capture** | Built-in | Store form submissions as leads | Form action `{"type": "leads"}` |
| **Admin Auth** | Built-in | Password-protected pages / member areas | SAPI visitor auth flow |
| **Site Context** | Built-in | Store design decisions across sessions | `execute_integration(service: "site_context", endpoint: "set-context")` |

### How integrations work

1. **Setup** — Store the API key: `setup_integration(service: "resend", secrets: {"resend_api_key": "re_..."})`
2. **Use** — Call the integration: `execute_integration(service: "resend", endpoint: "send-email", input: {...})`
3. **Done** — The platform resolves credentials, validates input, proxies the request, returns the result

API keys are **never exposed** to the AI or the browser. The Vault encrypts them at rest
and the integration proxy resolves them server-side at execution time.

### When to use integrations

| User wants... | Use this |
|---|---|
| Contact form that sends email | SAPI form + Resend integration |
| Accept payments on website | Stripe or Mollie integration |
| SMS confirmation after booking | Twilio integration |
| Store leads from multiple forms | Built-in Lead Capture |
| Password-protected member area | Admin Auth (SAPI visitor auth) |
| Remember design choices across sessions | Site Context integration |

**Always check if an integration exists before building custom solutions.**
The built-in integrations handle authentication, error handling, rate limiting,
and security — reimplementing these is unnecessary and error-prone.

---

## AI Continuity — Staying on Track Across Sessions

AI assistants typically lose all context when a conversation ends.
WebsitePublisher solves this with infrastructure layers that preserve knowledge:

### Skills (this document)
You are reading a skill right now. Skills are structured instructions that teach AI
how to work with the platform — which patterns to follow, which mistakes to avoid,
and which tools to use. Without skills, every AI session would rediscover
how the platform works from scratch.

**Always call `get_skill` at the start of a session.** It ensures you follow current
best practices, regardless of which AI platform the user is on.

### Design Context (site_context integration)
Design decisions (colors, fonts, style direction) are stored per project via the
`site_context` integration. When a new AI session starts, call `get_project_status`
to retrieve the stored design context — this ensures visual consistency even when
a different AI or a different conversation continues the work.

### Scheduled Tasks (AAPI)
Websites sometimes need automated actions: publish a page at a specific time,
send a weekly email digest, update data records on a schedule. The AAPI layer
handles this without requiring the AI or the user to be present.

Available via: `create_scheduled_task`, `list_scheduled_tasks`

### Visual Editor (WPE)
The user does not need to start a new AI conversation for every small change.
The Visual Editor lets them update images, reorder content, and adjust styles
directly in their browser — at any time, without AI involvement.

These layers ensure that the **quality and consistency of the website do not depend
on which AI session is active.** The platform remembers — the AI doesn't have to.

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
