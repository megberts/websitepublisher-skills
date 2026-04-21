---
name: websitepublisher-design
description: >
  Design guidelines for building professional, distinctive websites on WebsitePublisher.ai.
  Fetch this skill before building any HTML pages. Covers typography, color, layout,
  animation, atmosphere, responsive design, and industry-specific presets.
license: MIT
metadata:
  author: websitepublisher-ai
  version: "1.1"
---

# WebsitePublisher Design Skill

> Every website you build must look **professionally designed** — not like AI-generated template output.
> This skill tells you how. Read it fully before writing any HTML.

---

## 1. Design Thinking — Choose a Direction First

Before writing code, answer three questions:

1. **Who is the audience?** A law firm's audience expects authority and restraint. A streetwear brand's audience expects edge and energy. Design for them, not for a generic "user."
2. **What is the one emotion?** Pick one: trust, excitement, calm, curiosity, luxury, warmth, rebellion, playfulness. Every design choice reinforces that one emotion.
3. **What makes this unforgettable?** Identify one signature element: a dramatic hero, an unusual color, a bold font, an unexpected layout. This is the thing someone remembers.

**Commit fully.** A half-bold design looks worse than a fully conservative one. Choose a direction and execute it with conviction.

---

## 1b. Site Context — Check Before Building

Before making any design decisions, check if the project already has a design context set.

Call `get_project_status` — the response may include a `design_context` block:

```json
{
  "design_context": {
    "color_palette": {
      "primary": "#2D5A3D",
      "secondary": "#F4E8D1",
      "accent": "#C17C5A",
      "background": "#FFFFFF",
      "text": "#1A1A1A"
    },
    "fonts": {
      "heading": "Playfair Display",
      "body": "Inter"
    },
    "style_notes": "Soft sage & blush palette, organic feel, rounded corners",
    "locale": "nl"
  }
}
```

### If design_context exists:
- **Use it as your foundation.** These are the client's established design choices.
- Map `color_palette` values to CSS custom properties (`--color-primary`, `--color-accent`, etc.)
- Use the specified `fonts` for heading and body — load them via Google Fonts
- Follow `style_notes` as creative direction
- Use `locale` for content language (`<html lang="...">`)
- You can still apply all the techniques from this skill (animations, layouts, atmosphere) — but within the established palette and typography.

### If design_context is null:
- Follow the full design thinking process in this skill
- Ask the user for their preferences: colors, fonts, style direction
- After agreeing on a direction, save it for future sessions:
  ```
  execute_integration(
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
- This ensures consistency across all future conversations about this project.

---

## 2. Typography

### Font Selection Rules

- **Never use:** Arial, Helvetica, Inter, Roboto, Open Sans, Lato, system-ui, sans-serif as primary fonts. These are invisible — they say nothing about the brand.
- **Always use Google Fonts** via `<link>` tags in `<head>`. They're free, fast, and distinctive.
- **Pair two fonts:** one display/heading font + one body font. The heading font carries personality; the body font carries readability.

### Pairing Strategy

| Business Tone | Heading Font Direction | Body Font Direction |
|---------------|----------------------|-------------------|
| Luxury / Premium | High-contrast serif (Playfair Display, Cormorant Garamond, DM Serif Display) | Clean sans (DM Sans, Outfit, Manrope) |
| Modern / Tech | Geometric sans (Space Grotesk, Sora, Urbanist, Plus Jakarta Sans) | Same family or neutral (IBM Plex Sans, Source Sans 3) |
| Warm / Friendly | Rounded sans (Nunito, Quicksand, Varela Round) | Readable serif or same family |
| Bold / Editorial | Strong sans or slab (Bebas Neue, Oswald, Anton, Archivo Black) | Contrasting serif (Lora, Merriweather, Libre Baskerville) |
| Creative / Artistic | Expressive display (Gloock, Instrument Serif, Fraunces) | Clean complement (Inter can work here as body only) |
| Traditional / Trust | Classic serif (Libre Baskerville, Crimson Pro, EB Garamond) | Neutral sans (Source Sans 3, Fira Sans) |

**Critical:** Do NOT always pick the same fonts. Vary across projects. If you used Playfair Display on the last site, pick something else. Repetition kills distinctiveness.

### Sizing & Hierarchy

```css
/* Fluid type scale — use clamp() for responsive sizing */
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

Use `--text-hero` for the main hero heading. Make it large enough to be unmissable. Hero headings below 2.5rem look timid.

---

## 3. Color & Theme

### Palette Construction

Build a palette with exactly these roles:

```css
:root {
    --color-bg:       /* page background */;
    --color-surface:  /* cards, sections with contrast */;
    --color-text:     /* primary text */;
    --color-muted:    /* secondary text, captions */;
    --color-primary:  /* brand color — buttons, links, accents */;
    --color-accent:   /* secondary accent — highlights, badges, hover states */;
    --color-border:   /* subtle borders and dividers */;
}
```

### Rules

- **One dominant color.** Not three equal colors — one boss, the rest support.
- **High contrast on CTAs.** Buttons must pop. If your background is light, the button must be dark or saturated. Never a pale button on a pale background.
- **Dark themes are underused.** Not everything needs a white background. Dark backgrounds feel premium, modern, and dramatic. Consider `--color-bg: #0a0a0a` or `#1a1a2e` with light text.
- **Avoid pure black text on pure white.** Use `#1a1a1a` on `#fafafa` or similar off-tones. Pure black on pure white creates harsh contrast that feels cheap.
- **Alternating section backgrounds.** Alternate between `--color-bg` and `--color-surface` per section to create visual rhythm. Never use the same background for consecutive sections.

### Color Inspiration by Industry

| Industry | Primary Direction | Mood |
|----------|------------------|------|
| Restaurant / Food | Warm (terracotta, olive, burgundy, gold) | Appetizing, inviting |
| Tech / SaaS | Cool (deep blue, electric purple, cyan) | Innovation, trust |
| Health / Wellness | Natural (sage, seafoam, warm beige, soft coral) | Calm, healing |
| Creative / Design | Bold (electric orange, hot pink, acid green, deep violet) | Energy, creativity |
| Finance / Law | Conservative (navy, charcoal, forest green, gold accent) | Authority, trust |
| Fashion / Luxury | Dramatic (black, champagne, deep wine, ivory) | Exclusivity, desire |
| Education / Non-profit | Accessible (teal, warm orange, bright blue, soft yellow) | Approachable, hopeful |

---

## 4. Layout & Composition

### Section Rhythm

Every page is a sequence of sections. Create rhythm by varying:

- **Background color** (alternate light/dark)
- **Content width** (full-width hero → contained content → full-width image → contained text)
- **Alignment** (centered hero → left-aligned features → centered testimonials)
- **Density** (spacious hero → compact grid → spacious CTA)

### Hero Patterns (choose one per site)

1. **Split Hero** — text left, image/visual right (or reversed). Works for most businesses.
2. **Full-bleed Background** — background image/gradient with overlaid text. Dramatic, editorial.
3. **Centered Minimal** — large centered heading, short subtitle, single CTA. Elegant, premium.
4. **Asymmetric Overlap** — text block overlapping an image at an angle or offset. Creative, modern.

The hero is the first impression. It must be generous in height (min `80vh` on desktop), bold in typography, and clear in its single CTA.

### Grid-Breaking Techniques

- **Offset cards** — not all cards aligned to the same top edge. Use `transform: translateY(2rem)` on alternating items.
- **Overlapping elements** — a section that slightly overlaps the previous one via negative margin or `position: relative; top: -3rem`.
- **Full-bleed breakouts** — one image or callout that breaks the max-width container and stretches edge to edge.
- **Asymmetric two-column** — instead of 50/50, use 60/40 or 65/35. The imbalance creates tension and interest.

### Spacing

```css
:root {
    --space-section: clamp(4rem, 5vw + 2rem, 8rem);  /* between sections */
    --space-block:   clamp(1.5rem, 2vw + 0.5rem, 3rem); /* between content blocks */
    --space-element: clamp(0.75rem, 1vw, 1.5rem);     /* between elements */
    --max-width:     min(1200px, 90vw);                /* content container */
}
```

Generous spacing looks professional. Cramped spacing looks amateur. When in doubt, add more space.

---

## 5. Motion & Animation

### Page Load Reveals (mandatory on every page)

```css
@keyframes fadeInUp {
    from { opacity: 0; transform: translateY(30px); }
    to   { opacity: 1; transform: translateY(0); }
}

.reveal {
    animation: fadeInUp 0.8s ease-out both;
}

/* Stagger child elements */
.reveal:nth-child(1) { animation-delay: 0.1s; }
.reveal:nth-child(2) { animation-delay: 0.2s; }
.reveal:nth-child(3) { animation-delay: 0.3s; }
.reveal:nth-child(4) { animation-delay: 0.4s; }
```

Apply `.reveal` to hero elements, section headings, and card groups. The staggered delay creates an orchestrated entrance that feels polished.

### Scroll-Triggered Reveals (CSS-only via IntersectionObserver)

```html
<script>
const observer = new IntersectionObserver((entries) => {
    entries.forEach(e => { if (e.isIntersecting) e.target.classList.add('visible'); });
}, { threshold: 0.1 });
document.querySelectorAll('.scroll-reveal').forEach(el => observer.observe(el));
</script>

<style>
.scroll-reveal {
    opacity: 0; transform: translateY(30px);
    transition: opacity 0.6s ease, transform 0.6s ease;
}
.scroll-reveal.visible {
    opacity: 1; transform: translateY(0);
}
</style>
```

### Hover States

Every clickable element needs a hover state. Not just color change — consider:
- `transform: translateY(-2px)` + `box-shadow` increase on cards
- Background color shift + slight scale on buttons
- Underline animation on text links (width transition from 0 to 100%)

### Rules

- **CSS-only first.** No external animation libraries unless specifically needed.
- **One orchestrated moment > many scattered effects.** A beautiful page load sequence beats random hover wobbles.
- **Respect `prefers-reduced-motion`.** Always include: `@media (prefers-reduced-motion: reduce) { *, *::before, *::after { animation-duration: 0.01ms !important; } }`

---

## 6. Atmosphere & Texture

Flat solid-color sections look cheap. Add depth:

- **Subtle gradients** — not the 2018 Instagram gradient. Use `background: linear-gradient(135deg, var(--color-bg) 0%, color-mix(in srgb, var(--color-primary) 5%, var(--color-bg)) 100%);`
- **Noise/grain overlay** — adds organic texture. Use a tiny noise PNG as `background-image` with low opacity, or use SVG filter: `<filter><feTurbulence baseFrequency="0.65" numOctaves="3"/></filter>`
- **Glassmorphism** (when appropriate) — `backdrop-filter: blur(10px); background: rgba(255,255,255,0.08); border: 1px solid rgba(255,255,255,0.1);`
- **Layered shadows** — realistic shadows use multiple layers: `box-shadow: 0 1px 2px rgba(0,0,0,0.05), 0 4px 12px rgba(0,0,0,0.08), 0 16px 40px rgba(0,0,0,0.06);`
- **Decorative elements** — abstract SVG shapes, dots patterns, or subtle lines placed as `position: absolute` background decorations behind content sections.

**Match the technique to the aesthetic.** A law firm gets layered shadows and subtle gradients. A creative agency gets grain overlays and glassmorphism. A restaurant gets warm gradients and organic shapes.

---

## 7. Responsive Design

### Mandatory Requirements

Every page must work on mobile (375px+). Test mentally at 375px, 768px, and 1200px.

```css
/* Container pattern */
.container {
    width: var(--max-width);
    margin: 0 auto;
    padding: 0 clamp(1rem, 3vw, 2rem);
}

/* Grid that collapses gracefully */
.grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(min(300px, 100%), 1fr));
    gap: var(--space-block);
}
```

### Mobile Navigation

Always implement a hamburger menu for mobile. Pattern:
- Hidden checkbox toggle (CSS-only) or minimal JS
- Full-screen overlay or slide-in panel
- Large touch targets (min 48px)
- Clear close button/action

### Mobile-Specific Rules

- Hero heading: still bold, but use `clamp()` to scale down proportionally
- Single column under 768px — no side-by-side layouts on small screens
- Images: `width: 100%; height: auto;` always
- Touch targets: min 44x44px for all interactive elements
- No horizontal scrolling — ever

---

## 8. Anti-Patterns — Never Do These

| Anti-Pattern | Why It Fails | Do This Instead |
|-------------|-------------|-----------------|
| Arial/Helvetica/system fonts | Invisible, says nothing about the brand | Google Fonts — pick something with character |
| `#000000` on `#FFFFFF` | Harsh, cheap-looking contrast | Off-blacks on off-whites (`#1a1a1a` on `#fafafa`) |
| Same background color for all sections | Monotonous, no visual rhythm | Alternate between 2-3 background tones |
| Tiny hero text (< 2rem) | Timid, doesn't grab attention | Hero headings: `clamp(2.5rem, 2rem + 3vw, 5rem)` |
| No animations at all | Static, lifeless, feels unfinished | Minimum: page load fade-in reveals |
| Purple gradient on white (the AI cliché) | Screams "AI made this" | Choose colors that match the business identity |
| Centered everything | Predictable, boring | Mix alignments: left, center, asymmetric |
| Cards all exactly the same size/position | Template-like, mechanical | Vary card sizes, offset positions, or use masonry |
| Generic stock photo descriptions | "Happy diverse team in modern office" | Describe specific, contextual visuals or use Unsplash search |
| Walls of text with no visual breaks | Unreadable, intimidating | Max 3-4 lines per paragraph, visual elements between text blocks |

---

## 9. Industry Presets

Quick-start directions for common website types. These are **starting points**, not rigid templates.

### Restaurant / Café
- **Fonts:** Cormorant Garamond + DM Sans, or Gloock + Outfit
- **Colors:** warm palette — terracotta, olive, cream, burgundy accent
- **Hero:** full-bleed food photography background, centered name and tagline
- **Signature:** menu section with elegant typography, reservation CTA
- **Mood:** appetizing, intimate, welcoming

### Tech / SaaS
- **Fonts:** Plus Jakarta Sans + Inter, or Sora + Source Sans 3
- **Colors:** dark theme — `#0f172a` background, electric accent (cyan, violet, green)
- **Hero:** split layout — headline left, product screenshot/illustration right
- **Signature:** feature grid with icons, pricing table, social proof badges
- **Mood:** innovative, trustworthy, clean

### Portfolio / Creative
- **Fonts:** Instrument Serif + Manrope, or Bebas Neue + Fira Sans
- **Colors:** high contrast — near-black bg with one striking accent, or all-white with bold accents
- **Hero:** dramatic full-screen with name only, minimal text
- **Signature:** project grid with hover reveals, case study sections
- **Mood:** confident, curated, artistic

### Professional Services (Law, Finance, Consulting)
- **Fonts:** Libre Baskerville + Source Sans 3, or DM Serif Display + DM Sans
- **Colors:** conservative — navy, charcoal, forest green, subtle gold accent
- **Hero:** centered with authority — short powerful headline, professional imagery
- **Signature:** team section, trust indicators, structured service descriptions
- **Mood:** authoritative, reliable, established

### Health / Wellness
- **Fonts:** Nunito + Lora, or Quicksand + Merriweather
- **Colors:** natural palette — sage, seafoam, warm linen, soft coral accent
- **Hero:** calming imagery, soft gradients, gentle introduction
- **Signature:** services with icons, testimonials with photos, booking CTA
- **Mood:** calming, nurturing, trustworthy

### E-commerce / Retail
- **Fonts:** Urbanist + DM Sans, or Archivo + Source Sans 3
- **Colors:** depends on brand — clean and product-focused, high CTA contrast
- **Hero:** product showcase front-and-center, strong "Shop Now" CTA
- **Signature:** product grid, category navigation, social proof
- **Mood:** desirable, urgent, easy

---

## The Golden Rule

**No two websites should look the same.**

If the previous site you built used Playfair Display, dark theme, and fade-in animations — the next site gets Sora, a light warm palette, and slide-in animations. Variety is not optional. It is the difference between a template machine and a design professional.

Match every choice to the business, the audience, and the purpose. When the user hasn't specified a style, choose a bold direction and commit to it fully.
