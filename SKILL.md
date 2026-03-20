---
name: ui-ux-skill
description: Create transcendental, award-caliber frontend interfaces that escape "AI slop" through a 5-layer design engineering system. Use this skill whenever the user asks to build ANY web UI — components, pages, apps, dashboards, landing pages, portfolios, posters, React/HTML/CSS layouts — or asks to "style", "beautify", "redesign", or "make it look good". Also trigger when the user shares a design reference, mentions Awwwards/FWA-quality output, or is building anything user-facing. This skill MUST be used even for "simple" UI requests — a button, a card, a form — because aesthetic intent must be set from the first line of code. Never skip this skill for frontend work.
---

# Frontend Design — Transcendental UI Generation

The goal is not "functional and clean." The goal is **amazement** — interfaces that make users ask *"who made this?"*

This skill operationalizes five layers that must be executed **in order** before writing a single line of code.

---

## Layer 1 — Aesthetic Commitment (Conceptual Anchor)

Before any code, commit to one **extreme** aesthetic direction. Name it explicitly. This overrides distributional convergence — the model's gravity toward statistical-mean design.

**Pick one and push it to its logical extreme:**

| Aesthetic | Core Visual DNA |
|---|---|
| **Brutalist Editorial** | Raw grids, exposed structure, high contrast B&W, monospace + slab serif |
| **Swiss International** | Strict grid, Helvetica-adjacent, red/black/white, mathematical rhythm |
| **Retro-Futuristic** | Scanlines, neon on deep navy/black, CRT glow, grid overlays |
| **Organic Luxury** | Cream/stone tones, serif display fonts, generous whitespace, soft shadows |
| **Maximalist Chaos** | Layered textures, clashing type scales, overlapping elements, color overload |
| **Dark Technical** | Terminal aesthetic, monospace everything, green/amber on near-black |
| **Kinetic Minimalism** | Near-empty layouts, single focal point, everything else is motion |
| **Art Deco Geometric** | Gold/black/ivory, symmetry, ornamental borders, geometric type |

**Declare the aesthetic in a code comment at the top of every file.** Example:
```
/* AESTHETIC: Retro-Futuristic — CRT scanlines, phosphor green (#00FF41) on #0A0A0A,
   monospace body + condensed display, grid overlay at 8% opacity */
```

---

## Layer 2 — Negative Constraints (Slop Prevention)

These are **absolute bans**. No exceptions. Each ban forces the model into lower-probability, higher-quality latent space regions.

**Typography bans:**
- ❌ Inter, Roboto, Arial, system-ui, -apple-system, Space Grotesk, DM Sans
- ✅ Instead: Syne, Bebas Neue, Playfair Display, Cormorant, IBM Plex Mono, Neue Haas Grotesk, Editorial New, Migra, PP Neue Montreal, Clash Display, Satoshi

**Color bans:**
- ❌ Purple gradients on white (`#7C3AED` → `#FFFFFF` type combos)
- ❌ Pure black `#000000` or pure white `#FFFFFF` as base surfaces
- ❌ Generic blue CTAs (`#3B82F6`, `#2563EB`)
- ✅ Instead: OKLCH tinted neutrals, warm near-blacks (e.g. `oklch(12% 0.02 60)`), earthy creams, unexpected accent hues

**Layout bans:**
- ❌ Uniform card grids (3-column equal-height cards with identical padding)
- ❌ Centered hero → 3-feature-icons → CTA template
- ❌ Navbar with logo-left, links-center, CTA-right (unless explicitly required)
- ✅ Instead: asymmetric compositions, broken grid, overlapping layers, diagonal flow

**Motion bans:**
- ❌ `transition: all 0.3s ease` on everything
- ❌ Generic fadeIn/slideUp on scroll for every element
- ❌ Bounce easing (`cubic-bezier(0.68,-0.55,0.27,1.55)`) as a default
- ✅ Instead: staggered reveals with intentional delay offsets, spring physics for interactions, one orchestrated entrance sequence

---

## Layer 3 — Technical Overdrive (Creative Coding)

Move from "functional" to "amazing" by reaching beyond standard HTML/CSS. Match technical ambition to the aesthetic direction.

### Color Systems — Use OKLCH Always
OKLCH is perceptually uniform. Dark mode inversions, contrast ratios, and tinted neutrals stay mathematically consistent.

```css
:root {
  --surface-0: oklch(10% 0.015 60);   /* warm near-black base */
  --surface-1: oklch(16% 0.018 60);   /* elevated surface */
  --accent:    oklch(72% 0.18 65);    /* electric amber */
  --text-hi:   oklch(94% 0.01 60);    /* primary text */
  --text-lo:   oklch(58% 0.02 60);    /* muted text */
}
```

### Typography — Fluid Type Scale
Never hardcode font sizes. Use `clamp()` for a responsive hierarchy that breathes.

```css
:root {
  --step--1: clamp(0.75rem,  0.7rem  + 0.25vw, 0.875rem);
  --step-0:  clamp(1rem,     0.9rem  + 0.5vw,  1.125rem);
  --step-1:  clamp(1.25rem,  1.1rem  + 0.75vw, 1.5rem);
  --step-2:  clamp(1.75rem,  1.4rem  + 1.5vw,  2.25rem);
  --step-3:  clamp(2.5rem,   1.8rem  + 3vw,    4rem);
  --step-4:  clamp(3.5rem,   2rem    + 6vw,    7rem);
}
```

### Spatial Design — 8px Baseline Grid
All spacing must be multiples of 8 (or 4 for fine-grain). This creates invisible rhythm users feel but cannot name.

```css
:root {
  --space-xs:  4px;   --space-s:   8px;
  --space-m:   16px;  --space-l:   24px;
  --space-xl:  40px;  --space-2xl: 64px;
  --space-3xl: 96px;  --space-4xl: 128px;
}
```

### Motion — Spring Physics for Micro-Interactions

```css
/* Tactile spring — buttons, cards, interactive surfaces */
transition: transform 400ms cubic-bezier(0.34, 1.56, 0.64, 1),
            box-shadow 300ms cubic-bezier(0.25, 0.46, 0.45, 0.94);
```

```js
// Staggered entrance — one orchestrated sequence, not scattered fades
gsap.from('.hero-word', {
  y: 80, opacity: 0, duration: 0.9,
  ease: 'power4.out',
  stagger: { each: 0.08, from: 'start' }
});
```

### Atmospheric Backgrounds (CSS-only)

```css
/* Gradient mesh */
background:
  radial-gradient(ellipse at 20% 50%, oklch(45% 0.15 280 / 0.3) 0%, transparent 60%),
  radial-gradient(ellipse at 80% 20%, oklch(60% 0.18 40  / 0.25) 0%, transparent 50%),
  var(--surface-0);

/* Grain overlay */
.grain::after {
  content: ''; position: fixed; inset: 0; pointer-events: none;
  background-image: url("data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' width='200' height='200'><filter id='n'><feTurbulence type='fractalNoise' baseFrequency='0.75' numOctaves='4'/><feColorMatrix type='saturate' values='0'/></filter><rect width='200' height='200' filter='url(%23n)' opacity='0.4'/></svg>");
  opacity: 0.035; mix-blend-mode: overlay;
}
```

### Three.js Hero (use for high-ambition projects)
When the aesthetic demands immersive 3D, use this recipe — don't improvise the architecture:
1. Canvas positioned `absolute` over hero section, `pointer-events: none`
2. Camera synced to scroll via GSAP ScrollTrigger (`camera.position.z` lerp)
3. Mouse displacement shader on background texture (`uniform vec2 uMouse`)
4. All Three.js in a single `initScene()` function, called after DOM ready

---

## Layer 4 — Compositional Logic (Design Engineering)

Code architecture as intentional as visual architecture.

**Composition over boolean props:**
```jsx
// ❌ Never
<Button isDestructive isLoading isLarge />

// ✅ Always
<Button.Root variant="destructive">
  <Button.Spinner />
  <Button.Label>Delete</Button.Label>
</Button.Root>
```

**CSS token discipline:**
- All design decisions live in `:root` custom properties — no magic numbers in component styles
- No inline styles except dynamic runtime values
- Dark mode via `[data-theme]` attribute or `prefers-color-scheme` — never duplicated stylesheets

**Accessibility (non-negotiable):**
- WCAG AA contrast minimum — OKLCH makes this mathematically verifiable
- `prefers-reduced-motion` wraps all non-essential animation
- Semantic HTML always — aesthetic intent never justifies `<div>` soup
- Focus rings styled to match the aesthetic (not removed)

---

## Layer 5 — The Critique Pass (Visual Self-Audit)

Mandatory before delivery. If a box fails — fix it.

### Visual Hierarchy
- [ ] User's eye lands on the intended focal point within 2 seconds
- [ ] Clear size/weight/color hierarchy — not everything at equal visual weight
- [ ] Primary CTA is the most visually prominent interactive element

### Rhythm & Space
- [ ] Adequate vertical breathing room throughout
- [ ] All spacing values on the 8px grid — no arbitrary pixel values
- [ ] Type scale feels like one coherent system

### Slop Detection
- [ ] Card/grid layouts have deliberate variation — not cloned templates
- [ ] Copy is context-specific, not placeholder — no "Lorem ipsum", no "Learn More"
- [ ] At least 2 moments of unexpected delight (a hover, a reveal, a texture, an interaction)

### Emotional Resonance
- [ ] Interface feels built for a *specific* user, not a generic persona
- [ ] Correct emotional register for the domain (trust/fintech, excitement/gaming, calm/wellness)
- [ ] Would a designer screenshot and share this?

---

## Execution Sequence

1. **Read** — extract purpose, audience, constraints, aesthetic hints from the request
2. **Layer 1** — declare one extreme aesthetic in a comment, commit fully
3. **Layer 2** — mentally ban every default; reach for lower-probability choices
4. **Layer 3** — build with OKLCH tokens, fluid type, 8px grid, spring motion
5. **Layer 4** — clean architecture, composition patterns, accessibility baked in
6. **Layer 5** — critique checklist, patch failures, then deliver

---

## Emotional Narrative Framing

For any non-trivial interface, frame the design intent as a user journey before touching code:

> *"A [first-time visitor / returning user / skeptical buyer] lands here.  
> They feel [overwhelmed / curious / skeptical].  
> Within 3 seconds they must feel [trusted / excited / calm].  
> The one action I'm designing toward is [X].  
> Every layout decision, color choice, and motion beat serves that arc."*

This is the difference between a website and an experience.

---

## Output Contract

- Working, production-grade code only — no pseudocode, no `/* TODO */`, no placeholders
- Single-file HTML: fully self-contained, fonts via CDN or `@font-face`
- React: default export, zero required props, all defaults baked in
- All animations wrapped in `prefers-reduced-motion` fallback
- **The bar: would this place on Awwwards? If not, fix it before delivering.**
