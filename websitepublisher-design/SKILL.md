---
name: websitepublisher-design
description: >
  Design guidelines for building professional, distinctive websites on WebsitePublisher.ai.
  Fetch this skill before building any HTML pages. Covers typography, color, layout,
  animation, atmosphere, responsive design, and per-business starting directions.
license: MIT
metadata:
  author: websitepublisher-ai
  version: "1.2"
---

# WebsitePublisher Design Skill

> Every website you build must look **professionally designed** — not like AI-generated
> template output. Read this fully before writing any HTML.

---

## 1. Choose a Direction First

Before writing code, answer three questions:

1. **Who is the audience?** A law firm's audience expects authority and restraint. A
   streetwear brand's expects edge and energy. Design for them, not for a generic "user".
2. **What is the one emotion?** Trust, excitement, calm, curiosity, luxury, warmth,
   rebellion, playfulness. Pick **one**. Every choice reinforces it.
3. **What makes this unforgettable?** One signature element — a dramatic hero, an unusual
   color, a bold font, an unexpected layout. This is the thing someone remembers.

**Commit fully.** A half-bold design looks worse than a fully conservative one.

---

## 2. Site Context — Check Before Deciding Anything

Call `get_project_status`. The response may carry a `design_context` block:

```json
{
  "design_context": {
    "color_palette": { "primary": "#2D5A3D", "secondary": "#F4E8D1",
                       "accent": "#C17C5A", "background": "#FFFFFF", "text": "#1A1A1A" },
    "fonts": { "heading": "Playfair Display", "body": "Source Sans 3" },
    "style_notes": "Soft sage & blush palette, organic feel, rounded corners",
    "locale": "nl"
  }
}
```

**If it exists, it wins.** These are the client's established choices, not suggestions.
Map `color_palette` to CSS custom properties, load the named `fonts` from Google Fonts,
follow `style_notes` as direction, and put `locale` in `<html lang="…">`. Everything else in
this skill still applies — but inside that palette and typography.

**If it is null:** follow this skill, ask the user for colors, fonts and style direction, and
once agreed, save it so the next session inherits it:

```
execute_integration(service: "site_context", endpoint: "set-context", input: {
  color_palette: { primary: "#…", secondary: "#…", accent: "#…",
                   background: "#…", text: "#…" },
  fonts: { heading: "Font Name", body: "Font Name" },
  style_notes: "Short description of the visual direction",
  locale: "en"
})
```

Skipping this is why a second session redesigns a site the client already approved.

---

## 3. Typography

### The font rules

- **Never as a heading font:** Arial, Helvetica, Roboto, Open Sans, Lato, Inter,
  `system-ui`, bare `sans-serif`. The heading font carries the brand; these say nothing.
- **As a body font, neutral is fine** — and often right. Inter, Source Sans 3, IBM Plex
  Sans and Fira Sans exist to be read, not noticed. Pair one with a heading font that has
  character.
- **Always load from Google Fonts** via `<link>` in the head. Free, fast, distinctive.
- **Two fonts, not four.** One display/heading + one body.
- **Vary across projects.** If the last site used Playfair Display, this one does not.
  Repetition is how a design practice turns into a template machine.

### Sizing

```css
:root {
    --text-xs:   clamp(0.75rem, 0.7rem + 0.25vw, 0.875rem);
    --text-sm:   clamp(0.875rem, 0.8rem + 0.35vw, 1rem);
    --text-base: clamp(1rem, 0.9rem + 0.5vw, 1.125rem);
    --text-lg:   clamp(1.25rem, 1rem + 1vw, 1.5rem);
    --text-xl:   clamp(1.5rem, 1.2rem + 1.5vw, 2rem);
    --text-2xl:  clamp(2rem, 1.5rem + 2.5vw, 3rem);
    --text-hero: clamp(2.5rem, 2rem + 3vw, 5rem);
}
```

Hero headings below 2.5rem look timid. Use `--text-hero` and mean it.

---

## 4. Color

```css
:root {
    --color-bg:       /* page background */;
    --color-surface:  /* cards, contrasting sections */;
    --color-text:     /* primary text */;
    --color-muted:    /* secondary text, captions */;
    --color-primary:  /* brand — buttons, links, accents */;
    --color-accent:   /* highlights, badges, hover states */;
    --color-border:   /* subtle borders and dividers */;
}
```

- **One dominant color.** Not three equals — one boss, the rest support.
- **CTAs must pop.** Light background → dark or saturated button. Never pale on pale.
- **Body text needs 4.5:1 against its background, large text 3:1.** Check it rather than
  trusting your eye; a palette that photographs well can still be unreadable.
- **Dark themes are underused.** `#0a0a0a` or `#1a1a2e` with light text reads premium and
  modern. Not everything needs white.
- **No pure black on pure white.** `#1a1a1a` on `#fafafa`. Pure contrast looks cheap.
- **Alternate section backgrounds** between `--color-bg` and `--color-surface`. Never two
  consecutive sections on the same tone.

---

## 5. Layout & Composition

### Section rhythm

A page is a sequence of sections. Vary **background** (alternate tones), **width**
(full-bleed hero → contained content → full-bleed image), **alignment** (centered hero →
left-aligned features) and **density** (spacious → compact → spacious).

### Hero patterns — choose one per site

1. **Split** — text one side, visual the other. Works for most businesses.
2. **Full-bleed** — background image or gradient with overlaid text. Dramatic, editorial.
3. **Centered minimal** — large heading, short subtitle, single CTA. Elegant, premium.
4. **Asymmetric overlap** — text block overlapping an image at an offset. Creative, modern.

Generous height (min `80vh` desktop), bold type, one clear CTA.

### Breaking the grid

- **Offset cards** — `transform: translateY(2rem)` on alternating items.
- **Overlapping sections** — negative margin or `position: relative; top: -3rem`.
- **Full-bleed breakout** — one image or callout that escapes the container.
- **Asymmetric columns** — 60/40 or 65/35 instead of 50/50. Imbalance creates interest.

### Spacing

```css
:root {
    --space-section: clamp(4rem, 5vw + 2rem, 8rem);
    --space-block:   clamp(1.5rem, 2vw + 0.5rem, 3rem);
    --space-element: clamp(0.75rem, 1vw, 1.5rem);
    --max-width:     min(1200px, 90vw);
}
```

Generous spacing looks professional. Cramped looks amateur. When in doubt, add more.

---

## 6. Motion

### Page-load reveals — on every page

Stagger by an index you set, not by sibling position. `nth-child` counts **all** children,
so a stray element before your cards silently shifts every delay.

```css
@keyframes fadeInUp {
    from { opacity: 0; transform: translateY(30px); }
    to   { opacity: 1; transform: translateY(0); }
}

.reveal {
    animation: fadeInUp 0.8s ease-out both;
    animation-delay: calc(var(--i, 0) * 0.1s);
}
```

```html
<h1 class="reveal" style="--i:0">…</h1>
<p  class="reveal" style="--i:1">…</p>
<a  class="reveal" style="--i:2">…</a>
```

### Scroll reveals

This needs JavaScript, so **the hidden state must depend on JavaScript being alive.** A
plain `opacity: 0` in the stylesheet leaves the page permanently blank when the script fails
to load, the browser lacks `IntersectionObserver`, or an extension blocks it — and it fails
silently, on a live site, for the visitor only.

```html
<!-- first thing in <head> -->
<script>document.documentElement.classList.add('js');</script>
```

```css
/* Only hidden when JS is running. No JS → content is simply visible. */
.js .scroll-reveal {
    opacity: 0;
    transform: translateY(30px);
    transition: opacity 0.6s ease, transform 0.6s ease;
}
.js .scroll-reveal.visible {
    opacity: 1;
    transform: translateY(0);
}
```

```html
<script>
(function () {
    var els = document.querySelectorAll('.scroll-reveal'), i;
    // Plain loops: the fallback below exists for browsers without IntersectionObserver,
    // and those are exactly the ones that may also lack NodeList.forEach.
    if (!('IntersectionObserver' in window)) {          // old browser → show everything
        for (i = 0; i < els.length; i++) { els[i].classList.add('visible'); }
        return;
    }
    var io = new IntersectionObserver(function (entries) {
        for (var n = 0; n < entries.length; n++) {
            if (entries[n].isIntersecting) {
                entries[n].target.classList.add('visible');
                io.unobserve(entries[n].target);
            }
        }
    }, { threshold: 0.1 });
    for (i = 0; i < els.length; i++) { io.observe(els[i]); }
})();
</script>
```

### Hover states

Every clickable element needs one, and not only a color change: `translateY(-2px)` plus a
deeper shadow on cards, a background shift with slight scale on buttons, an underline
growing from 0 to 100% on text links.

### Rules

- **CSS first.** No animation libraries unless something genuinely needs them.
- **One orchestrated moment beats many scattered effects.** A beautiful load sequence is
  worth more than hover wobble everywhere.
- **Honour `prefers-reduced-motion`** — including transitions, not just animations:

```css
@media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
        scroll-behavior: auto !important;
    }
}
```

---

## 7. Atmosphere & Texture

Flat solid sections look cheap. Add depth, and match the technique to the aesthetic — a law
firm gets layered shadows and a whisper of gradient; a creative agency gets grain and glass;
a restaurant gets warm gradients and organic shapes.

- **Subtle gradients** — not the 2018 Instagram gradient:
  `linear-gradient(135deg, var(--color-bg) 0%, color-mix(in srgb, var(--color-primary) 5%, var(--color-bg)) 100%)`
- **Noise/grain** — a tiny noise PNG at low opacity, or an SVG
  `<feTurbulence baseFrequency="0.65" numOctaves="3"/>` filter.
- **Glassmorphism**, where it fits —
  `backdrop-filter: blur(10px); background: rgba(255,255,255,0.08); border: 1px solid rgba(255,255,255,0.1);`
- **Layered shadows** — real shadows stack:
  `0 1px 2px rgba(0,0,0,.05), 0 4px 12px rgba(0,0,0,.08), 0 16px 40px rgba(0,0,0,.06)`
- **Decorative elements** — abstract SVG shapes, dot patterns or lines, absolutely
  positioned behind content.

---

## 8. Responsive

Every page works at 375px. Check mentally at 375, 768 and 1200.

```css
.container {
    width: var(--max-width);
    margin: 0 auto;
    padding: 0 clamp(1rem, 3vw, 2rem);
}

.grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(min(300px, 100%), 1fr));
    gap: var(--space-block);
}
```

- **Mobile navigation is mandatory** — hidden checkbox toggle or minimal JS, full-screen
  overlay or slide-in panel, a clear close action.
- Single column under 768px. No side-by-side on small screens.
- Images always `width: 100%; height: auto;`
- Touch targets min 44×44px, navigation items 48px.
- **No horizontal scrolling, ever.**

---

## 9. Starting Directions

One table, because "what fonts", "what colors" and "what hero" are the same question asked
three times. Each row is a **starting point with alternatives** — pick one pairing, then push
it somewhere the row does not mention. Two sites from the same row must not look alike.

| Business | Direction | Heading font (pick one) | Body font | Palette | Hero | Signature |
|---|---|---|---|---|---|---|
| Restaurant / café | Warm, intimate | Cormorant Garamond · Gloock · Fraunces | DM Sans · Outfit | Terracotta, olive, cream, burgundy | Full-bleed food photography | Menu section, reservation CTA |
| Tech / SaaS | Clean, confident | Space Grotesk · Sora · Plus Jakarta Sans | Inter · Source Sans 3 | Dark `#0f172a` + electric accent | Split: headline / product shot | Feature grid, pricing, social proof |
| Portfolio / creative | Expressive | Instrument Serif · Bebas Neue · Anton | Manrope · Fira Sans | Near-black with one striking accent, or all-white with bold accents | Full-screen, name only | Project grid with hover reveals |
| Law / finance / consulting | Authoritative | Libre Baskerville · DM Serif Display · EB Garamond | Source Sans 3 · IBM Plex Sans | Navy, charcoal, forest, gold accent | Centered, short powerful headline | Team, trust indicators |
| Health / wellness | Calm, nurturing | Nunito · Quicksand · Lora | Merriweather · DM Sans | Sage, seafoam, warm linen, soft coral | Calming imagery, soft gradient | Services with icons, booking CTA |
| E-commerce / retail | Desirable, direct | Urbanist · Archivo · Outfit | DM Sans · Source Sans 3 | Product-led, very high CTA contrast | Product front and centre | Product grid, categories, reviews |
| Education / non-profit | Approachable | Fraunces · Nunito · Poppins | Source Sans 3 · Lora | Teal, warm orange, bright blue, soft yellow | Human imagery, plain promise | Programmes, impact numbers |
| Fashion / luxury | Exclusive | Cormorant Garamond · Gloock · Italiana | Manrope · Jost | Black, champagne, deep wine, ivory | Editorial full-bleed | Lookbook grid, restrained copy |

---

## 10. Anti-Patterns

Only the ones not already covered above.

| Anti-pattern | Why it fails | Instead |
|---|---|---|
| Purple gradient on white | The AI cliché. It announces who made the page. | Colors that come from the business identity |
| Centered everything | Predictable and flat | Mix alignments — left, centered, asymmetric |
| Identical cards in a perfect row | Template-like, mechanical | Vary sizes, offset positions, or go masonry |
| "Happy diverse team in modern office" | Generic stock direction produces generic stock results | Describe a specific, contextual image, or search Unsplash for a real one |
| Walls of text | Unreadable and intimidating | 3-4 lines per paragraph, something visual between blocks |
| Placeholder copy left behind | Every write is live immediately — lorem ipsum ships | Write the real copy, or do not create the section yet |

---

## The Golden Rule

**No two websites should look the same.**

Section 9 gives you a place to stand, not a template to fill. If the last site used Playfair
Display, a dark theme and fade-in reveals, this one gets a different heading font, a light
warm palette and a different entrance. Variety is not decoration — it is the whole difference
between a design practice and a template machine.

Match every choice to the business, the audience and the purpose. When the user has not
specified a style, choose a bold direction and commit to it.
