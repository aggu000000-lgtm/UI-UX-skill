# Typography Scale — Responsive Systems (2026)

> Typography is the interface. Get it right and everything else follows.

---

## TYPE SCALE RATIOS

Choose one ratio and use it consistently across the entire project.

| Scale Name | Ratio | Use Case |
|------------|-------|----------|
| Minor Second | 1.067 | Dense data interfaces, dashboards |
| Major Second | 1.125 | Content-heavy sites, blogs |
| Minor Third | 1.200 | General purpose, SaaS products |
| Major Third | 1.250 | **Default recommendation** — balanced hierarchy |
| Perfect Fourth | 1.333 | Marketing sites, landing pages |
| Augmented Fourth | 1.414 | Bold editorial, magazine layouts |
| Golden Ratio | 1.618 | Dramatic hierarchy, hero sections |

---

## MAJOR THIRD (1.250) — DEFAULT SCALE

Base: 16px

| Level | Size | Line Height | Weight | Usage |
|-------|------|-------------|--------|-------|
| 6xl | 61.0px | 1.1 | 700 | Hero headlines |
| 5xl | 48.8px | 1.1 | 700 | Page titles |
| 4xl | 39.1px | 1.15 | 700 | Section headers |
| 3xl | 31.3px | 1.2 | 600 | Sub-section headers |
| 2xl | 25.0px | 1.25 | 600 | Card titles, modal headers |
| xl | 20.0px | 1.3 | 600 | Component headers |
| lg | 18.0px | 1.4 | 500 | Subtitles, lead text |
| base | 16.0px | 1.5 | 400 | Body text (minimum) |
| sm | 14.0px | 1.5 | 400 | Captions, helper text |
| xs | 12.0px | 1.5 | 400 | Labels, badges, timestamps |

---

## PERFECT FOURTH (1.333) — MARKETING SCALE

Base: 16px

| Level | Size | Line Height | Weight | Usage |
|-------|------|-------------|--------|-------|
| 6xl | 75.9px | 1.05 | 800 | Hero headlines |
| 5xl | 56.9px | 1.1 | 700 | Page titles |
| 4xl | 42.7px | 1.15 | 700 | Section headers |
| 3xl | 32.0px | 1.2 | 600 | Sub-section headers |
| 2xl | 24.0px | 1.25 | 600 | Card titles |
| xl | 18.0px | 1.3 | 500 | Component headers |
| lg | 18.0px | 1.4 | 500 | Subtitles |
| base | 16.0px | 1.5 | 400 | Body text |
| sm | 14.0px | 1.5 | 400 | Captions |
| xs | 12.0px | 1.5 | 400 | Labels |

---

## RESPONSIVE TYPOGRAPHY

### Fluid Type with clamp()

Never use media queries alone for font sizing. Use `clamp()` for smooth scaling.

```css
/* clamp(min, preferred, max) */

:root {
  --text-xs:   clamp(0.75rem, 0.7rem + 0.25vw, 0.875rem);
  --text-sm:   clamp(0.875rem, 0.8rem + 0.35vw, 1rem);
  --text-base: clamp(1rem, 0.95rem + 0.5vw, 1.125rem);
  --text-lg:   clamp(1.125rem, 1rem + 0.75vw, 1.25rem);
  --text-xl:   clamp(1.25rem, 1.1rem + 1vw, 1.5rem);
  --text-2xl:  clamp(1.5rem, 1.2rem + 1.5vw, 2rem);
  --text-3xl:  clamp(1.875rem, 1.4rem + 2.5vw, 2.5rem);
  --text-4xl:  clamp(2.25rem, 1.5rem + 3.5vw, 3.25rem);
  --text-5xl:  clamp(3rem, 1.8rem + 5vw, 4.5rem);
  --text-6xl:  clamp(3.75rem, 2rem + 7vw, 6rem);
}
```

### Fluid Line Height

```css
:root {
  --leading-tight:  clamp(1.1, 1 + 0.5vw, 1.25);
  --leading-snug:   clamp(1.2, 1.1 + 0.5vw, 1.375);
  --leading-normal: clamp(1.4, 1.3 + 0.5vw, 1.5);
  --leading-relaxed: clamp(1.5, 1.4 + 0.5vw, 1.625);
  --leading-loose:  clamp(1.6, 1.5 + 0.5vw, 1.75);
}
```

---

## FONT LOADING

### Preload Critical Fonts

```html
<link rel="preload" href="/fonts/inter-var.woff2" as="font"
      type="font/woff2" crossorigin>
```

### CSS font-display

```css
@font-face {
  font-family: 'Inter';
  src: url('/fonts/inter-var.woff2') format('woff2');
  font-display: swap;
  font-weight: 100 900;
  font-style: normal;
}
```

### System Font Stack (Zero Load Time)

```css
font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI',
  Roboto, 'Helvetica Neue', Arial, 'Noto Sans', sans-serif,
  'Apple Color Emoji', 'Segoe UI Emoji';
```

---

## LINE LENGTH AND READABILITY

| Content Type | Max Width | Characters |
|-------------|-----------|------------|
| Body text | 600-700px | 65-75 chars |
| Documentation | 700-800px | 75-85 chars |
| Code blocks | 100% | N/A (horizontal scroll) |
| Headlines | 100% | N/A |
| Form inputs | 400-500px | N/A |
| Card content | 100% of card | N/A |

---

## FONT PAIRING RULES

### Rule 1: Contrast, Don't Clash
- Pair a serif heading with a sans-serif body (or vice versa)
- Never pair two serifs or two similar sans-serifs

### Rule 2: Same Family, Different Weights
- Use one typeface with multiple weights (e.g., Inter 400 for body, Inter 700 for headings)
- Safest approach, zero pairing risk

### Rule 3: Maximum 2 Typefaces
- One for headings, one for body
- One is better than two
- Three is always too many

### Recommended Pairings (2026)

| Headings | Body | Vibe |
|----------|------|------|
| Inter | Inter | Clean, modern, universal |
| Playfair Display | Source Sans 3 | Editorial, elegant |
| Space Grotesk | Inter | Tech-forward, bold |
| DM Serif Display | DM Sans | Refined, approachable |
| Clash Display | Satoshi | Premium, contemporary |
| Instrument Serif | Instrument Sans | Journalistic, crisp |

---

## ACCESSIBILITY REQUIREMENTS

- Body text minimum 16px — always
- Line height minimum 1.5x for body text
- Never use all-caps for sentences or paragraphs
- Avoid decorative fonts for body text
- Ensure clear letterform distinction (I/l/1, O/0)
- Test on actual phone screens, not just desktop previews
- 15% of global population has dyslexia — readable type is inclusive type
