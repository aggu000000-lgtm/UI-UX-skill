# Design Systems Reference

The technical foundation of every interface built with this skill. These are not suggestions.
They are the rails that prevent AI-generated UIs from sliding toward "statistically average" output.

---

## 1. COLOR — OKLCH Architecture

OKLCH is perceptually uniform. Two colors at the same lightness _look_ equally bright to the human
eye. This makes dark mode, contrast checking, and harmonic palettes mathematically reliable.
**Never use hex or rgb for design tokens. Always OKLCH.**

### Surface Palette Template

```css
:root {
  /* Surfaces — warm near-black base (hue 60 = faint amber warmth) */
  --surface-0: oklch(8% 0.012 60); /* page background */
  --surface-1: oklch(13% 0.014 60); /* card / elevated */
  --surface-2: oklch(18% 0.016 60); /* hover state / modal */
  --surface-3: oklch(24% 0.015 60); /* border / divider */

  /* Text */
  --text-hi: oklch(95% 0.008 60); /* primary — near white with warmth */
  --text-md: oklch(70% 0.01 60); /* secondary */
  --text-lo: oklch(50% 0.008 60); /* muted / placeholder */
  --text-inv: oklch(10% 0.012 60); /* text on light accent */

  /* Accent — one only. Change the H (hue) for personality. */
  --accent: oklch(72% 0.19 65); /* electric amber — change H for mood */
  --accent-dim: oklch(52% 0.14 65); /* hover state of accent */
  --accent-glow: oklch(72% 0.19 65 / 0.15); /* for glow/halo effects */

  /* Semantic */
  --color-ok: oklch(70% 0.16 150); /* success / confirm */
  --color-warn: oklch(78% 0.17 75); /* warning */
  --color-err: oklch(60% 0.2 25); /* error / destructive */
  --color-info: oklch(65% 0.16 240); /* informational */
}
```

### Hue Guide — Accent Personality

| H value | Mood              | Use case                         |
| ------- | ----------------- | -------------------------------- |
| 25–35   | Urgent red-orange | Action, energy, sale, danger     |
| 45–70   | Warm amber        | Premium, productive, fintech     |
| 90–130  | Lime-green        | Tech, growth, sustainability     |
| 150–170 | Teal              | Healthcare, calm, trust          |
| 240–270 | Blue              | Enterprise, reliability, default |
| 290–320 | Purple            | Creative, luxury, AI/ML          |
| 330–350 | Pink-magenta      | Social, playful, fashion         |

### Light Mode Inversion

```css
[data-theme="light"] {
  --surface-0: oklch(98% 0.004 60);
  --surface-1: oklch(94% 0.006 60);
  --surface-2: oklch(89% 0.008 60);
  --surface-3: oklch(80% 0.01 60);
  --text-hi: oklch(12% 0.014 60);
  --text-md: oklch(38% 0.012 60);
  --text-lo: oklch(58% 0.01 60);
  --text-inv: oklch(96% 0.006 60);
}
```

### Atmospheric Backgrounds (CSS-only depth)

```css
/* Gradient mesh — 2 radials create depth without images */
body {
  background:
    radial-gradient(
      ellipse at 15% 40%,
      oklch(45% 0.18 290 / 0.12) 0%,
      transparent 55%
    ),
    radial-gradient(
      ellipse at 85% 15%,
      oklch(60% 0.15 45 / 0.1) 0%,
      transparent 45%
    ),
    var(--surface-0);
}

/* Film grain — adds materiality, reduces digital harshness */
body::after {
  content: "";
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 9999;
  background-image: url("data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' width='180' height='180'><filter id='n'><feTurbulence type='fractalNoise' baseFrequency='0.72' numOctaves='4'/><feColorMatrix type='saturate' values='0'/></filter><rect width='180' height='180' filter='url(%23n)' opacity='0.4'/></svg>");
  opacity: 0.028;
  mix-blend-mode: overlay;
}
```

---

## 2. TYPOGRAPHY — Fluid Scale

**Never hardcode font sizes. Every size is a `clamp()`.** This creates a responsive typographic system
that breathes across all viewports without media queries.

### The Fluid Scale

```css
:root {
  /* Modular scale — each step ≈ 1.25× the previous at desktop */
  --step--2: clamp(0.64rem, 0.6rem + 0.2vw, 0.75rem); /* caption, label */
  --step--1: clamp(0.8rem, 0.74rem + 0.28vw, 0.875rem); /* small, meta */
  --step-0: clamp(1rem, 0.92rem + 0.42vw, 1.125rem); /* body */
  --step-1: clamp(1.2rem, 1.08rem + 0.58vw, 1.4rem); /* lead / large body */
  --step-2: clamp(1.56rem, 1.3rem + 1.28vw, 2rem); /* h3 */
  --step-3: clamp(2rem, 1.55rem + 2.24vw, 3rem); /* h2 */
  --step-4: clamp(2.6rem, 1.8rem + 4vw, 4.5rem); /* h1 */
  --step-5: clamp(3.4rem, 2rem + 7vw, 7rem); /* display / hero */
}
```

### Font Selection — Anti-Slop List

**BANNED** (too common, zero personality): Inter, Roboto, Arial, system-ui, DM Sans, Space Grotesk

**APPROVED by aesthetic intent:**

| Aesthetic            | Display                              | Body                     | Mono           |
| -------------------- | ------------------------------------ | ------------------------ | -------------- |
| **Editorial/Luxury** | Cormorant Garamond, Playfair Display | Lora, Source Serif 4     | —              |
| **Technical/Dark**   | Bebas Neue, Barlow Condensed         | IBM Plex Sans            | IBM Plex Mono  |
| **Modern/Clean**     | Syne, Clash Display                  | Satoshi, Cabinet Grotesk | JetBrains Mono |
| **Geometric/Art**    | Monument Extended, Neue Haas         | Neue Haas Grotesk        | —              |
| **Brutalist**        | Druk Wide, BN Bergen St              | BN Bergen St             | Courier New    |
| **Premium/App**      | PP Neue Montreal, Mona Sans          | PP Neue Montreal         | Berkeley Mono  |
| **Organic/Human**    | Fraunces, Canela                     | Instrument Serif         | —              |

### Typography Rules

```css
/* Always set these on html element */
html {
  font-size: 100%; /* do NOT set px here — breaks user accessibility settings */
  -webkit-font-smoothing: antialiased;
  text-rendering: optimizeLegibility;
}

/* Line length — never full-width body text */
.prose {
  max-width: 65ch;
}

/* No widows */
h1,
h2,
h3 {
  text-wrap: balance;
}
p {
  text-wrap: pretty;
}

/* Tracking — always optical, never arbitrary */
.display {
  letter-spacing: -0.03em;
} /* large type optically tight */
.headline {
  letter-spacing: -0.02em;
}
.body {
  letter-spacing: 0;
} /* body text never tracked */
.label {
  letter-spacing: 0.05em;
  text-transform: uppercase;
} /* UI labels only */
.mono {
  letter-spacing: -0.02em;
} /* code looks better slightly tight */
```

---

## 3. SPATIAL RHYTHM — 8px Grid

All spacing is a **multiple of 8** (or 4 for fine-grain detail). Users feel this rhythm
subconsciously. Breaking it creates visual unease that nobody can articulate but everyone feels.

```css
:root {
  --space-1: 4px; /* hairline gap, icon padding */
  --space-2: 8px; /* tight inline gap */
  --space-3: 12px; /* compact gap */
  --space-4: 16px; /* default inline gap */
  --space-5: 24px; /* section padding, card gap */
  --space-6: 32px; /* component separation */
  --space-7: 48px; /* section separation */
  --space-8: 64px; /* large section gap */
  --space-9: 96px; /* page-level breathing room */
  --space-10: 128px; /* hero generous spacing */

  /* Fluid space — grows with viewport */
  --space-fluid-s: clamp(var(--space-5), 3vw, var(--space-7));
  --space-fluid-m: clamp(var(--space-7), 5vw, var(--space-9));
  --space-fluid-l: clamp(var(--space-8), 8vw, var(--space-10));
}
```

### Container & Layout Widths

```css
:root {
  --container-sm: 560px; /* reading column, forms */
  --container-md: 768px; /* focused content */
  --container-lg: 1024px; /* standard page */
  --container-xl: 1280px; /* wide pages */
  --container-2xl: 1440px; /* full-bleed layouts */
}

.container {
  width: min(var(--container-xl), 100% - var(--space-fluid-s) * 2);
  margin-inline: auto;
}
```

---

## 4. MOTION — Named Personalities

Motion is not decoration. It communicates **the physics of your system** — how heavy things are,
how eager they are to respond, how confident the interface is. Give motion a personality before
assigning easing values.

### The Four Motion Personalities

**1. SNAPPY** (Products, tools, dashboards — response feels instant)

```css
--ease-snap: cubic-bezier(0.25, 0, 0, 1);
--dur-micro: 80ms; /* toggle, checkbox */
--dur-short: 120ms; /* hover state */
--dur-medium: 200ms; /* panel slide */
```

**2. SPRING** (Consumer apps, interactive surfaces — tactile and alive)

```css
--ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1); /* overshoot = alive */
--ease-bounce: cubic-bezier(0.68, -0.3, 0.32, 1.3);
--dur-micro: 120ms;
--dur-short: 200ms;
--dur-medium: 400ms;
```

**3. CINEMATIC** (Marketing, editorial, portfolio — deliberate and confident)

```css
--ease-out: cubic-bezier(0.16, 1, 0.3, 1); /* power4.out equivalent */
--ease-in-out: cubic-bezier(0.87, 0, 0.13, 1);
--dur-short: 300ms;
--dur-medium: 600ms;
--dur-long: 900ms;
```

**4. WHISPER** (Healthcare, finance, legal — barely moves, projects calm)

```css
--ease-gentle: cubic-bezier(0.4, 0, 0.2, 1);
--dur-micro: 100ms;
--dur-short: 180ms;
--dur-medium: 280ms;
/* No bounce. No overshoot. Ever. */
```

### Motion Rules

```css
/* Reduced motion — always wrap entrances and decorative animations */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}

/* Staggered entrance — one orchestrated sequence, not random fadeIns */
.stagger-child:nth-child(1) {
  animation-delay: 0ms;
}
.stagger-child:nth-child(2) {
  animation-delay: 60ms;
}
.stagger-child:nth-child(3) {
  animation-delay: 120ms;
}
.stagger-child:nth-child(4) {
  animation-delay: 180ms;
}
/* Rule: max stagger gap 80ms. Beyond that, it feels like lag. */

/* Hover — always transform + shadow, never background flicker */
.card:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 32px var(--accent-glow);
  transition:
    transform var(--dur-short) var(--ease-spring),
    box-shadow var(--dur-medium) var(--ease-out);
}
```

---

## 5. DESIGN CANONS — Named Aesthetic Vocabularies

These are not visual references — they are **design philosophies**. Pick one and embody it.
Mixing canons creates mediocre work. Committing to one creates memorable work.

### Canon 1: LINEAR DARK

_Linear, Vercel, Raycast, Resend_

```
— Near-black surface with extreme subtlety between elevations
— One electric accent (usually cyan, purple, or amber) used sparingly as signal
— Monospace or geometric sans — Geist, Söhne, or GT America Mono
— Micro-interactions that feel mechanical and precise
— Cards with 1px borders, not shadows
— Content density: high. Every pixel is information.
— The vibe: you are a serious engineer using a serious tool
```

### Canon 2: EDITORIAL LUXURY

_Apple.com, Stripe.com, Framer.com_

```
— Generous whitespace as a power move (empty space = expensive)
— Large display type that takes entire sections
— Photography or 3D that occupies hero completely
— Scroll-triggered reveals — content earns its appearance
— The copy does work. Short sentences. No adjectives.
— Accent: one. Often unexpected (not blue).
— The vibe: whatever this costs, it's worth it
```

### Canon 3: BRUTALIST EDITORIAL

_Awwwards winners, portfolio sites, art direction_

```
— Grid violations on purpose: type that breaks containment
— Raw structure exposed: visible gutters, column lines, numbers
— High contrast: near-black and near-white, nothing in between
— Monospace type at scale
— Decoration is violence — remove everything decorative
— The vibe: made by a person who doesn't need your approval
```

### Canon 4: WARM MINIMAL

_Notion, Craft, Linear (light mode), Things_

```
— Cream/warm white backgrounds, not pure white
— Subtle ink-like text, not stark black
— Generous prose-like spacing
— Barely-there dividers and borders (opacity 0.06 to 0.12)
— No hard shadows — ambient only
— Icons are strokes, not fills
— The vibe: a well-designed notebook
```

### Canon 5: PLAYFUL GEOMETRIC

_Arc browser, Pitch, Loom, Discord_

```
— Saturated colors that conflict productively
— Rounded corners pushed to near-circles
— Illustration elements that reward attention
— Type with personality — not just legibility
— Hover states that feel delightful, not just functional
— The vibe: built by people who are having fun
```

### Canon 6: DATA COCKPIT

_Retool, Figma, Grafana, trading terminals_

```
— Dense: max information per pixel
— Monospace body, tabular figures
— Borders over shadows — sharp containment
— Color = semantic. No color for decoration.
— Headers are short labels. Copy is data.
— Motion = state change only. No entrance animation.
— The vibe: a professional tool for professionals
```

---

## 6. COMPONENT LIBRARY STARTERS

Canonical implementations. Adapt tokens — never change the architecture.

### Button System

```css
.btn {
  display: inline-flex;
  align-items: center;
  gap: var(--space-2);
  padding: var(--space-3) var(--space-5);
  font-size: var(--step--1);
  font-weight: 500;
  letter-spacing: 0.01em;
  border-radius: 8px;
  border: 1.5px solid transparent;
  cursor: pointer;
  user-select: none;
  transition:
    background var(--dur-short) var(--ease-snap),
    transform var(--dur-micro) var(--ease-spring),
    box-shadow var(--dur-medium) var(--ease-out);
}
.btn:active {
  transform: scale(0.97);
}
.btn:focus-visible {
  outline: 2px solid var(--accent);
  outline-offset: 3px;
}

/* Primary */
.btn-primary {
  background: var(--accent);
  color: var(--text-inv);
}
.btn-primary:hover {
  background: var(--accent-dim);
  box-shadow: 0 0 0 4px var(--accent-glow);
}

/* Ghost */
.btn-ghost {
  background: transparent;
  border-color: var(--surface-3);
  color: var(--text-md);
}
.btn-ghost:hover {
  background: var(--surface-2);
  border-color: var(--accent);
  color: var(--text-hi);
}

/* Disabled — always style, never just opacity alone */
.btn:disabled {
  opacity: 0.4;
  cursor: not-allowed;
  pointer-events: none;
}

/* Loading state — same size, spinner replaces content */
.btn[data-loading="true"] {
  pointer-events: none;
}
.btn[data-loading="true"] .btn-label {
  opacity: 0;
}
.btn[data-loading="true"]::after {
  content: "";
  position: absolute;
  width: 16px;
  height: 16px;
  border: 2px solid currentColor;
  border-top-color: transparent;
  border-radius: 50%;
  animation: spin 0.6s linear infinite;
}
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
```

### Card System

```css
.card {
  background: var(--surface-1);
  border: 1px solid oklch(from var(--accent) l c h / 0.08);
  border-radius: 12px;
  padding: var(--space-6);
  transition:
    border-color var(--dur-medium) var(--ease-out),
    transform var(--dur-short) var(--ease-spring),
    box-shadow var(--dur-medium) var(--ease-out);
}
.card:hover {
  border-color: oklch(from var(--accent) l c h / 0.25);
  transform: translateY(-2px);
  box-shadow: 0 12px 40px oklch(0% 0 0 / 0.25);
}

/* Empty state — always design this */
.card-empty {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: var(--space-4);
  min-height: 200px;
  color: var(--text-lo);
  border-style: dashed;
}
```

### Input System

```css
.input {
  width: 100%;
  background: var(--surface-0);
  border: 1.5px solid var(--surface-3);
  border-radius: 8px;
  padding: var(--space-3) var(--space-4);
  color: var(--text-hi);
  font-size: var(--step-0);
  transition:
    border-color var(--dur-short) var(--ease-snap),
    box-shadow var(--dur-medium) var(--ease-out);
}
.input::placeholder {
  color: var(--text-lo);
}
.input:hover {
  border-color: var(--text-lo);
}
.input:focus {
  outline: none;
  border-color: var(--accent);
  box-shadow: 0 0 0 3px var(--accent-glow);
}
.input.error {
  border-color: var(--color-err);
}
.input.success {
  border-color: var(--color-ok);
}
.input:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  background: var(--surface-1);
}
```

---

## QUICK REFERENCE — The "Zero Magic Numbers" Check

Before finalizing any CSS, grep for:

- Any `px` value that's NOT a multiple of 4 → flag it
- Any `rem` value that doesn't correspond to the type scale → flag it
- Any `color` that isn't a CSS variable → flag it (except for specific atmospheric effects)
- Any `transition` that isn't referencing `--dur-*` and `--ease-*` tokens → flag it
