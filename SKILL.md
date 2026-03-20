---
name: frontend-design
description: Create transcendental, award-caliber frontend interfaces that escape "AI slop" through a 5-layer design engineering system. Use this skill whenever the user asks to build ANY web UI — components, pages, apps, dashboards, landing pages, portfolios, posters, React/HTML/CSS layouts — or asks to "style", "beautify", "redesign", or "make it look good". Also trigger when the user shares a design reference, mentions Awwwards/FWA-quality output, or is building anything user-facing. This skill MUST be used even for "simple" UI requests — a button, a card, a form — because aesthetic intent must be set from the first line of code. Never skip this skill for frontend work.
---

# Frontend Design — L5 Autonomous UI Generation

The goal is not "functional and clean." The goal is **amazement** — interfaces that make users ask *"who made this?"*

This skill targets **Level 5 frontend autonomy**: the AI does not wait for pixel-by-pixel direction. It extracts intent, commits to a design system, builds with mathematical precision, and recursively self-corrects against its own visual output before delivering. A human should only need to say *what* — never *how*.

This skill operationalizes **five layers + one recursive correction pass** that must be executed **in order** before writing a single line of code.

---

## Layer 0 — Context Engineering (Intent Extraction)

Before aesthetic decisions, extract the full intent signal from the request. This is the "tacit knowledge transfer" step — surface what the user hasn't said but means.

Ask internally (never out loud unless truly ambiguous):
- **Who** is the user of this interface? (skeptical buyer / power user / first-time visitor)
- **What emotional shift** must occur in the first 3 seconds? (trust / excitement / calm / urgency)
- **What is the single conversion action** everything serves?
- **What domain register** applies? (fintech → trust, gaming → kinetic energy, wellness → calm, dev tools → precision)
- **Is there an implicit brand** present? (if yes, extract its token fingerprint before committing to Layer 1)

If a brand reference, design file, or existing UI is provided — treat it as a **semantic token source**. Before generating, mentally extract:
```
brand.color.primary     → [value]
brand.color.surface     → [value]
brand.typography.display → [font family + weight]
brand.spacing.base      → [value]
brand.motion.default    → [easing + duration]
```
These extracted tokens govern all subsequent layers. A brand token always overrides an aesthetic default.

---

## Layer 1 — Aesthetic Commitment (Conceptual Anchor)

Commit to one **extreme** aesthetic direction. Name it explicitly. This overrides distributional convergence — the model's gravity toward statistical-mean design.

**Each aesthetic entry now includes: font pair · OKLCH palette · one layout rule.**

| Aesthetic | Font Pair | OKLCH Palette | Layout Rule |
|---|---|---|---|
| **Brutalist Editorial** | Bebas Neue (display) + IBM Plex Mono (body) | `oklch(98% 0 0)` bg · `oklch(8% 0 0)` ink · `oklch(55% 0.22 25)` red accent | Strict 12-col grid, zero border-radius, rules as decoration |
| **Swiss International** | Neue Haas Grotesk (all weights) | `oklch(97% 0.005 80)` cream · `oklch(10% 0.01 60)` near-black · `oklch(52% 0.22 25)` red | Mathematical column rhythm, asymmetric gutters, type as structure |
| **Retro-Futuristic** | Orbitron (display) + Share Tech Mono (body) | `oklch(8% 0.01 240)` deep navy · `oklch(72% 0.28 145)` phosphor green · `oklch(65% 0.2 65)` amber | Grid overlay at 6% opacity, scanline pseudo-elements, CRT glow via `text-shadow` |
| **Organic Luxury** | Cormorant Garamond (display) + Satoshi (body) | `oklch(93% 0.015 75)` warm cream · `oklch(28% 0.03 60)` dark sepia · `oklch(58% 0.1 55)` gold | Generous whitespace, single focal image, type floats in space |
| **Maximalist Chaos** | Clash Display (display) + Syne (body) | 4+ OKLCH accents, all high chroma (0.20+) | Overlapping z-layers, broken grid, deliberate overflow |
| **Dark Technical** | JetBrains Mono (all) | `oklch(9% 0.01 240)` · `oklch(65% 0.25 145)` green · `oklch(68% 0.2 65)` amber | Terminal line height (1.6), ruled separators, dense information density |
| **Kinetic Minimalism** | PP Neue Montreal (one weight only) | `oklch(96% 0.005 80)` · `oklch(12% 0.01 60)` · one accent max | One visual element per screen section, all interest comes from motion |
| **Art Deco Geometric** | Playfair Display (display) + Cormorant (body) | `oklch(85% 0.08 75)` gold · `oklch(10% 0.01 60)` black · `oklch(94% 0.01 80)` ivory | Bilateral symmetry, ornamental border system, geometric type lockup |

**Declare the aesthetic + full token fingerprint in a comment at the top of every file:**
```css
/* AESTHETIC: Retro-Futuristic
   --font-display: 'Orbitron'; --font-body: 'Share Tech Mono'
   --brand-bg: oklch(8% 0.01 240); --brand-accent: oklch(72% 0.28 145)
   --brand-surface: oklch(13% 0.015 240); --brand-text: oklch(88% 0.02 145)
   LAYOUT: grid overlay + scanlines + CRT glow on key text */
```

---

## Layer 2 — Negative Constraints (Slop Prevention)

**Absolute bans.** No exceptions. Each ban forces lower-probability, higher-quality latent space regions.

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

**Mobile layout bans:**
- ❌ Desktop layout stacked vertically with no mobile-specific rethink
- ❌ Tap targets under 44px
- ❌ Horizontal scroll on mobile caused by fixed-width elements
- ✅ Instead: mobile-first grid with `container-type: inline-size`, reflow at 640px, thumb-zone-aware CTA placement

**Motion bans:**
- ❌ `transition: all 0.3s ease` on everything
- ❌ Generic fadeIn/slideUp on scroll for every element
- ❌ Bounce easing (`cubic-bezier(0.68,-0.55,0.27,1.55)`) as a default
- ✅ Instead: staggered reveals with intentional delay offsets, spring physics for interactions, one orchestrated entrance sequence

---

## Layer 3 — Technical Overdrive (Creative Coding)

Move from "functional" to "amazing" by reaching beyond standard HTML/CSS. Match technical ambition to the aesthetic direction.

### Color Systems — Use OKLCH + Semantic Token Hierarchy

OKLCH is perceptually uniform. Dark mode inversions, contrast ratios, and tinted neutrals stay mathematically consistent.

**Critical:** tokens must be **semantically named** — expressing *purpose*, not *value*. The AI must know *why* a token exists, not just what hex it holds. This prevents brand fragmentation across components.

```css
/* Tier 1 — Primitive palette (raw OKLCH values, never used directly in components) */
:root {
  --_amber-60: oklch(72% 0.18 65);
  --_neutral-10: oklch(10% 0.015 60);
  --_neutral-16: oklch(16% 0.018 60);
  --_neutral-94: oklch(94% 0.01 60);
  --_neutral-58: oklch(58% 0.02 60);
}

/* Tier 2 — Semantic tokens (these are used in all component styles) */
:root {
  --color-surface-base:     var(--_neutral-10);    /* page background */
  --color-surface-raised:   var(--_neutral-16);    /* cards, modals */
  --color-accent-default:   var(--_amber-60);      /* primary interactive */
  --color-accent-hover:     oklch(from var(--_amber-60) calc(l + 0.08) c h); /* derived */
  --color-text-primary:     var(--_neutral-94);
  --color-text-muted:       var(--_neutral-58);
  --color-button-primary-bg:    var(--color-accent-default);
  --color-button-primary-text:  var(--color-surface-base);
  --color-interactive-focus:    oklch(72% 0.22 250); /* distinct from accent */
}
```

Dark mode is a single token swap at `[data-theme="light"]` — never a duplicated stylesheet.

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

### Responsive Strategy — Container-First

Mobile-first. Always. Breakpoints via container queries where possible, media queries as fallback.

```css
/* Container query approach — components respond to their container, not the viewport */
.card-grid {
  container-type: inline-size;
  display: grid;
  grid-template-columns: 1fr;
  gap: var(--space-l);
}

@container (min-width: 500px) {
  .card-grid { grid-template-columns: repeat(2, 1fr); }
}
@container (min-width: 900px) {
  .card-grid { grid-template-columns: repeat(3, 1fr); }
}

/* Viewport breakpoints for layout shells only */
@media (min-width: 768px)  { /* tablet  */ }
@media (min-width: 1200px) { /* desktop */ }
```

**Mobile layout rules:**
- Tap targets: minimum `44px × 44px`, touch-action controlled
- Thumb zone: primary CTAs in bottom 40% of viewport on mobile
- Reflow test: every component must be verified at 320px, 375px, 768px, 1440px

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
  var(--color-surface-base);

/* Grain overlay */
.grain::after {
  content: ''; position: fixed; inset: 0; pointer-events: none;
  background-image: url("data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' width='200' height='200'><filter id='n'><feTurbulence type='fractalNoise' baseFrequency='0.75' numOctaves='4'/><feColorMatrix type='saturate' values='0'/></filter><rect width='200' height='200' filter='url(%23n)' opacity='0.4'/></svg>");
  opacity: 0.035; mix-blend-mode: overlay;
}
```

### Three.js Hero — Escalation Decision Matrix

**Do not default to Three.js.** Use this matrix to decide scope before writing a single canvas line:

| Signal | Correct Response |
|---|---|
| "Make it look good" / "landing page" / no 3D mention | CSS atmospheric background + GSAP motion. No canvas. |
| "Immersive", "3D", "particles", "interactive background" | Three.js with the recipe below |
| "Dashboard", "settings", "form", "app" | Zero 3D. Motion only on interactions. |
| "Hero section", high-ambition aesthetic declared | Three.js optional — decide based on aesthetic (Retro-Futurism → yes, Swiss International → no) |

**When Three.js is warranted:**
1. Canvas positioned `absolute` over hero section, `pointer-events: none`
2. Camera synced to scroll via GSAP ScrollTrigger (`camera.position.z` lerp)
3. Mouse displacement shader on background texture (`uniform vec2 uMouse`)
4. All Three.js in a single `initScene()` function, called after DOM ready
5. Graceful fallback: if WebGL unavailable, CSS gradient mesh takes over seamlessly

---

## Layer 4 — Compositional Logic (Design Engineering)

Code architecture as intentional as visual architecture.

### Hierarchy-Aware DOM Architecture

The DOM must mirror the visual hierarchy. Nested relationships between components are not accidental — they are structural intent. Build outside-in:

```
Page Shell (layout grid, theme tokens)
  └── Section (semantic landmark, spacing rhythm)
        └── Content Block (typographic hierarchy)
              └── Interactive Unit (button, card, input)
                    └── Micro-detail (icon, badge, indicator)
```

**Rule:** If a component's visual depth exceeds its DOM depth by more than 2 levels, the structure is wrong. Refactor the HTML before styling.

```html
<!-- ❌ Flat DOM, deep visual — everything fights for z-index -->
<div class="page">
  <div class="card-with-header-and-badge-and-cta-all-at-once"></div>
</div>

<!-- ✅ DOM depth matches visual hierarchy -->
<main class="page-shell">
  <section class="feature-section" aria-labelledby="features-heading">
    <article class="feature-card">
      <header class="card-header">
        <span class="card-badge">New</span>
        <h3 class="card-title" id="features-heading">Title</h3>
      </header>
      <p class="card-body">Description</p>
      <footer class="card-actions">
        <button class="btn-primary">CTA</button>
      </footer>
    </article>
  </section>
</main>
```

### Composition over Boolean Props

```jsx
// ❌ Never
<Button isDestructive isLoading isLarge />

// ✅ Always
<Button.Root variant="destructive">
  <Button.Spinner />
  <Button.Label>Delete</Button.Label>
</Button.Root>
```

### CSS Token Discipline
- All design decisions live in `:root` custom properties via the two-tier semantic system (Layer 3) — no magic numbers in component styles
- No inline styles except dynamic runtime values (e.g. `style="--progress: 72%"`)
- Dark mode via `[data-theme]` attribute — never duplicated stylesheets
- Component-scoped tokens cascade from global semantic tokens: `--btn-bg: var(--color-button-primary-bg)`

### Accessibility (Non-Negotiable)
- WCAG AA contrast minimum — OKLCH makes this mathematically verifiable: `L` difference ≥ 40% for text
- `prefers-reduced-motion` wraps all non-essential animation
- Semantic HTML always — aesthetic intent never justifies `<div>` soup
- Focus rings styled to match the aesthetic (not removed): `outline: 2px solid var(--color-interactive-focus); outline-offset: 3px`
- All interactive elements have `aria-label` or visible text — no icon-only buttons without labels

---

## Layer 5A — The Visual Self-Audit (Critique Pass)

Mandatory before delivery. If a box fails — fix it immediately, then re-check.

### Visual Hierarchy
- [ ] User's eye lands on the intended focal point within 2 seconds
- [ ] Clear size/weight/color hierarchy — not everything at equal visual weight
- [ ] Primary CTA is the most visually prominent interactive element
- [ ] **Fix protocol:** if hierarchy fails → increase size contrast ratio by 1.5×, reduce competing elements' weight

### Rhythm & Space
- [ ] Adequate vertical breathing room throughout
- [ ] All spacing values on the 8px grid — no arbitrary pixel values
- [ ] Type scale feels like one coherent system
- [ ] **Fix protocol:** if rhythm fails → audit every `padding`/`margin` value, round to nearest `--space-*` token

### Slop Detection
- [ ] Card/grid layouts have deliberate variation — not cloned templates
- [ ] Copy is context-specific, not placeholder — no "Lorem ipsum", no "Learn More"
- [ ] At least 2 moments of unexpected delight (a hover, a reveal, a texture, an interaction)
- [ ] **Fix protocol:** if slop detected → replace one card with a different size/orientation variant; replace one CTA with domain-specific language

### Brand & Token Consistency
- [ ] Every color in the output traces back to a semantic token from Layer 0/3
- [ ] No raw hex or RGB values in component styles — only `var(--*)` references
- [ ] Font families match the aesthetic declaration comment at top of file
- [ ] **Fix protocol:** grep for `#`, `rgb(`, `hsl(` in component styles — convert every hit to a token

### Responsive Integrity
- [ ] Layout holds at 320px without horizontal scroll
- [ ] Tap targets ≥ 44px on mobile
- [ ] No text below `--step--1` on any viewport
- [ ] **Fix protocol:** if mobile breaks → switch to single-column, increase tap target padding, bump minimum font

### Emotional Resonance
- [ ] Interface feels built for a *specific* user, not a generic persona
- [ ] Correct emotional register for the domain (trust/fintech, excitement/gaming, calm/wellness)
- [ ] Would a designer screenshot and share this?

---

## Layer 5B — Recursive Self-Correction (Fidelity Pass)

After Layer 5A, mentally simulate **rendering the output** and evaluate against these Design2Code-derived fidelity checks. This is the AI equivalent of opening the browser and visually inspecting — executed as structured reasoning before final delivery.

### Structural Fidelity (TreeBLEU equivalent)
- [ ] DOM nesting matches the intended visual hierarchy (ref: Layer 4 architecture rule)
- [ ] No component is visually "orphaned" — every element belongs to a clear parent context
- [ ] Z-index layers are intentional and documented with a comment (e.g. `/* z: modal > overlay > content > base */`)

### Layout Accuracy (Block-Match equivalent)
- [ ] Every major visual block occupies the intended grid columns
- [ ] Text blocks do not overflow their containers at any breakpoint
- [ ] Images/media have explicit `aspect-ratio` set — no layout shift on load

### Visual Semantic Fidelity (CLIP Score equivalent)
- [ ] The aesthetic declared in Layer 1 is *visible in the output* — not just in the tokens
- [ ] Color palette matches the OKLCH values in the Layer 1 commitment comment
- [ ] Motion character matches the aesthetic (Kinetic Minimalism → precise, purposeful; Maximalist Chaos → layered, stacked)

### Self-Healing Protocol
If any fidelity check fails, execute this loop **before delivering**:
1. Identify the failing check
2. Locate the root cause in the code (wrong token? missing CSS rule? wrong DOM structure?)
3. Fix the root cause — never patch symptoms with `!important` or inline overrides
4. Re-run both 5A and 5B checklists on the patched section
5. Deliver only when all checks pass

---

## Execution Sequence

1. **Context Engineering (Layer 0)** — extract intent, user, emotional arc, and brand tokens from the request
2. **Aesthetic Commitment (Layer 1)** — declare one extreme aesthetic with full font + color + layout spec
3. **Negative Constraints (Layer 2)** — mentally ban every default; reach for lower-probability choices
4. **Technical Build (Layer 3)** — two-tier OKLCH tokens, fluid type, 8px grid, spring motion, escalation matrix for 3D
5. **Compositional Architecture (Layer 4)** — hierarchy-aware DOM, composition patterns, responsive + accessibility
6. **Visual Self-Audit (Layer 5A)** — critique checklist, patch failures with fix protocols
7. **Fidelity Pass (Layer 5B)** — structural/layout/semantic fidelity checks, self-healing loop
8. **Deliver** — only after both 5A and 5B fully pass

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
- All colors via semantic OKLCH tokens — zero raw hex in component styles
- DOM hierarchy mirrors visual hierarchy (Layer 4 architecture rule enforced)
- Verified at 320px, 768px, and 1440px before delivery
- **The bar: would this place on Awwwards? If not, run the self-healing protocol until it does.**
