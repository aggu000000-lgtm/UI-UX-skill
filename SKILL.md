---
name: ui-ux-skills
description: >
  Create transcendental, award-caliber frontend interfaces that escape "AI slop" through a 6-layer design engineering system. Use this skill whenever the user asks to build ANY web UI — components, pages, apps, dashboards, landing pages, portfolios, posters, React/HTML/CSS layouts — or asks to "style", "beautify", "redesign", or "make it look good". Also trigger when the user shares a design reference, mentions Awwwards/FWA-quality output, or is building anything user-facing. This skill MUST be used even for "simple" UI requests — a button, a card, a form — because aesthetic intent must be set from the first line of code. Never skip this skill for frontend work. Before writing code on library-heavy tasks (Radix, Motion, Floating UI), load the relevant reference file from references/.
---

# ui-ux-skills — L4 Autonomous UI Generation

The goal is not "functional and clean." The goal is **amazement** — interfaces that make users ask *"who made this?"*

This skill operationalizes **six layers + one recursive correction pass** that must be executed **in order** before writing a single line of code.

> **Reference files** — load before writing library-specific code:
> - `references/design-tokens.md` — two-tier OKLCH token architecture
> - `references/typography.md` — CDN-verified font sources + pairing system
> - `references/animation-bible.md` — motion principles + code patterns
> - `references/accessibility.md` — WCAG 2.2 AA, ARIA, keyboard nav
> - `references/library-sources.md` — raw API signatures for Radix, Motion, Floating UI
> - `references/component-anatomy.md` — L4-level component structure patterns

---

## Layer 0 — Context Engineering (Intent Extraction)

Before aesthetic decisions, extract the full intent signal. Surface what the user hasn't said but means.

Ask internally (never out loud unless genuinely ambiguous):
- **Who** is the user of this interface? (skeptical buyer / power user / first-time visitor)
- **What emotional shift** must occur in the first 3 seconds? (trust / excitement / calm / urgency)
- **What is the single conversion action** everything else serves?
- **What domain register** applies? (fintech → trust, gaming → kinetic energy, wellness → calm, dev tools → precision)
- **Single-file or multi-file context?** If multi-file — read existing token files before committing to a palette. Never introduce a second design system into an existing codebase.

If a brand reference, design file, or existing UI is provided — extract its token fingerprint before Layer 1:
```
brand.color.primary      → [value]
brand.color.surface      → [value]
brand.typography.display → [font family + weight]
brand.spacing.base       → [value]
brand.motion.default     → [easing + duration]
```
A brand token **always** overrides an aesthetic default. Never blow up an existing design system to satisfy a Layer 1 aesthetic preference.

---

## Layer 1 — Aesthetic Commitment (Conceptual Anchor)

Commit to one **extreme** aesthetic direction. Name it explicitly. This overrides distributional convergence — the model's gravity toward statistical-mean design.

**All fonts below are CDN-verified.** Google Fonts = free. Fontshare = free. No font in this table requires a paid license or will silently fall back.

| Aesthetic | Font Pair | OKLCH Palette | Layout Rule | CDN |
|---|---|---|---|---|
| **Brutalist Editorial** | Bebas Neue (display) + IBM Plex Mono (body) | `oklch(98% 0 0)` bg · `oklch(8% 0 0)` ink · `oklch(55% 0.22 25)` red | 12-col grid, zero border-radius, rules as decoration | Google Fonts |
| **Swiss International** | Outfit (all weights) | `oklch(97% 0.005 80)` cream · `oklch(10% 0.01 60)` near-black · `oklch(52% 0.22 25)` red | Mathematical column rhythm, asymmetric gutters, type as structure | Google Fonts |
| **Retro-Futuristic** | Orbitron (display) + Share Tech Mono (body) | `oklch(8% 0.01 240)` deep navy · `oklch(72% 0.28 145)` phosphor green · `oklch(65% 0.2 65)` amber | Grid overlay at 6% opacity, scanline pseudo-elements, CRT glow via `text-shadow` | Google Fonts |
| **Organic Luxury** | Cormorant Garamond (display) + Satoshi (body) | `oklch(93% 0.015 75)` warm cream · `oklch(28% 0.03 60)` dark sepia · `oklch(58% 0.1 55)` gold | Generous whitespace, single focal image, type floats in space | Google Fonts + Fontshare |
| **Maximalist Chaos** | Clash Display (display) + Syne (body) | 4+ OKLCH accents, all chroma 0.20+ | Overlapping z-layers, broken grid, deliberate overflow | Fontshare + Google Fonts |
| **Dark Technical** | JetBrains Mono (all) | `oklch(9% 0.01 240)` · `oklch(65% 0.25 145)` green · `oklch(68% 0.2 65)` amber | Terminal line height (1.6), ruled separators, dense information density | Google Fonts |
| **Kinetic Minimalism** | Plus Jakarta Sans (one weight only) | `oklch(96% 0.005 80)` · `oklch(12% 0.01 60)` · one accent max | One visual element per section, all interest from motion | Google Fonts |
| **Art Deco Geometric** | Playfair Display (display) + Fraunces (body) | `oklch(85% 0.08 75)` gold · `oklch(10% 0.01 60)` black · `oklch(94% 0.01 80)` ivory | Bilateral symmetry, ornamental border system, geometric type lockup | Google Fonts |

**Fontshare CDN import syntax** (for Satoshi, Clash Display, Cabinet Grotesk):
```html
<link href="https://api.fontshare.com/v2/css?f[]=satoshi@700,500,400&f[]=clash-display@600,700&display=swap" rel="stylesheet">
```

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

**Absolute bans. No exceptions.** Each ban forces lower-probability, higher-quality output.

**Typography bans:**
- ❌ Inter, Roboto, Arial, system-ui, -apple-system, Space Grotesk, DM Sans
- ✅ Instead: any font from the Layer 1 table — all CDN-verified, all free

**Color bans:**
- ❌ Purple gradients on white (`#7C3AED` → `#FFFFFF`)
- ❌ Pure black `#000000` or pure white `#FFFFFF` as base surfaces
- ❌ Generic blue CTAs (`#3B82F6`, `#2563EB`)
- ❌ Raw hex or `rgb()` values in component styles — semantic tokens only
- ✅ Instead: OKLCH tinted neutrals, warm near-blacks, earthy creams, unexpected accent hues

**Layout bans:**
- ❌ Uniform card grids (3-column equal-height cards, identical padding)
- ❌ Centered hero → 3-feature-icons → CTA template
- ❌ Navbar: logo-left, links-center, CTA-right (unless explicitly required)
- ✅ Instead: asymmetric compositions, broken grid, overlapping layers, diagonal flow

**Mobile layout bans:**
- ❌ Desktop layout stacked vertically with no mobile-specific rethink
- ❌ Tap targets under 44px
- ❌ Horizontal scroll caused by fixed-width elements
- ✅ Instead: mobile-first grid, `container-type: inline-size`, thumb-zone CTAs in bottom 40%

**Motion bans:**
- ❌ `transition: all 0.3s ease` on everything
- ❌ Generic fadeIn/slideUp on every scroll element
- ❌ Bounce easing as a default
- ✅ Instead: staggered reveals with intentional delays, spring physics, one orchestrated entrance sequence

---

## Layer 3 — Technical Overdrive (Creative Coding)

### Color Systems — Two-Tier OKLCH Token Architecture

Single-tier tokens (direct hex → component) break down at scale. Use two tiers: primitives that are never referenced in components, and semantic tokens that map intent to value.

```css
/* ── Tier 1: Primitive palette (never used directly in components) ── */
:root {
  --_amber-60:    oklch(72% 0.18 65);
  --_neutral-10:  oklch(10% 0.015 60);
  --_neutral-16:  oklch(16% 0.018 60);
  --_neutral-94:  oklch(94% 0.01 60);
  --_neutral-58:  oklch(58% 0.02 60);
}

/* ── Tier 2: Semantic tokens (ONLY these in component styles) ── */
:root {
  --color-surface-base:          var(--_neutral-10);
  --color-surface-raised:        var(--_neutral-16);
  --color-accent-default:        var(--_amber-60);
  --color-accent-hover:          oklch(from var(--_amber-60) calc(l + 0.08) c h);
  --color-text-primary:          var(--_neutral-94);
  --color-text-muted:            var(--_neutral-58);
  --color-button-primary-bg:     var(--color-accent-default);
  --color-button-primary-text:   var(--color-surface-base);
  --color-interactive-focus:     oklch(72% 0.22 250);
  --color-border-subtle:         oklch(from var(--_neutral-16) l c h / 0.6);
}
```

Dark mode = a single token swap at `[data-theme="light"]`. **Never** a duplicated stylesheet.

> See `references/design-tokens.md` for multi-file token architecture and component-scoped token patterns.

### Typography — Fluid Type Scale

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

> See `references/typography.md` for variable font usage, optical sizing, and full CDN import strings.

### Spatial Design — 8px Baseline Grid

```css
:root {
  --space-xs: 4px;   --space-s:   8px;
  --space-m:  16px;  --space-l:   24px;
  --space-xl: 40px;  --space-2xl: 64px;
  --space-3xl: 96px; --space-4xl: 128px;
}
```

### Responsive Strategy — Container-First

Mobile-first. Always. Container queries for components; media queries for layout shells only.

```css
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

/* Viewport breakpoints — layout shells only */
@media (min-width: 768px)  { /* tablet  */ }
@media (min-width: 1200px) { /* desktop */ }
```

**Mobile rules — non-negotiable:**
- Tap targets minimum `44px × 44px`
- Primary CTAs in bottom 40% of viewport on mobile (thumb zone)
- Every component verified at 320px, 375px, 768px, 1440px

### Motion — Spring Physics for Interactions

```css
/* Tactile spring */
transition: transform 400ms cubic-bezier(0.34, 1.56, 0.64, 1),
            box-shadow 300ms cubic-bezier(0.25, 0.46, 0.45, 0.94);
```

```js
// Staggered entrance — one orchestrated sequence
gsap.from('.hero-word', {
  y: 80, opacity: 0, duration: 0.9,
  ease: 'power4.out',
  stagger: { each: 0.08, from: 'start' }
});
```

> See `references/animation-bible.md` for scroll-triggered sequences, exit animations, and layout transitions.

### Atmospheric Backgrounds (CSS-only)

```css
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

### Three.js — Escalation Decision Matrix

Do **not** default to Three.js. Run this matrix before writing a single canvas line.

| Signal | Correct Response |
|---|---|
| "Make it look good" / "landing page" / no 3D mention | CSS atmospheric background + GSAP. No canvas. |
| "Immersive", "3D", "particles", "interactive background" | Three.js with the recipe below |
| "Dashboard", "settings", "form", "app" | Zero 3D. Motion on interactions only. |
| Retro-Futuristic or Maximalist Chaos aesthetic declared | Three.js optional — decide based on complexity budget |
| Swiss International, Organic Luxury, or Dark Technical | No 3D. These aesthetics conflict with it. |

**When Three.js is warranted:**
1. Canvas `absolute` over hero, `pointer-events: none`
2. Camera synced to scroll via GSAP ScrollTrigger
3. Mouse displacement shader: `uniform vec2 uMouse`
4. All Three.js in a single `initScene()`, called after DOM ready
5. Graceful fallback: if WebGL unavailable, CSS gradient mesh takes over seamlessly

> See `references/library-sources.md` for Three.js r165+ API signatures.

---

## Layer 4 — Compositional Logic (Design Engineering)

### Hierarchy-Aware DOM Architecture

The DOM must mirror the visual hierarchy. Build outside-in:

```
Page Shell (layout grid, theme tokens)
  └── Section (semantic landmark, spacing rhythm)
        └── Content Block (typographic hierarchy)
              └── Interactive Unit (button, card, input)
                    └── Micro-detail (icon, badge, indicator)
```

If a component's visual depth exceeds its DOM depth by more than 2 levels — the structure is wrong. Refactor the HTML before styling.

```html
<!-- ❌ Flat DOM, deep visual — everything fights for z-index -->
<div class="card-with-header-and-badge-and-cta"></div>

<!-- ✅ DOM depth matches visual hierarchy -->
<article class="feature-card">
  <header class="card-header">
    <span class="card-badge">New</span>
    <h3 class="card-title">Title</h3>
  </header>
  <p class="card-body">Description</p>
  <footer class="card-actions">
    <button class="btn-primary">CTA</button>
  </footer>
</article>
```

### Multi-File Token Discipline

When working in a multi-file codebase:
1. **Read before writing** — check for an existing `tokens.css` / `variables.css` / design system file before declaring new tokens
2. **Never duplicate** — extend existing tokens with new semantic names; don't redefine primitives
3. **Token import order** — primitives → semantic → component-scoped. Never reverse.
4. Component-scoped tokens cascade from global semantic: `--btn-bg: var(--color-button-primary-bg)`

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

> See `references/component-anatomy.md` for compound component patterns and variant contracts.

### Interaction States — Mandatory

Every interactive element ships with all states. No exceptions.

```css
.btn {
  /* base */         background: var(--color-button-primary-bg);
  /* hover */        &:hover  { background: var(--color-accent-hover); }
  /* active */       &:active { transform: scale(0.97); }
  /* focus */        &:focus-visible { outline: 2px solid var(--color-interactive-focus); outline-offset: 3px; }
  /* disabled */     &:disabled { opacity: 0.4; cursor: not-allowed; }
  /* loading */      &[aria-busy="true"] { /* spinner state */ }
}
```

### Accessibility — Non-Negotiable

- WCAG AA minimum — OKLCH `L` difference ≥ 40% for body text
- `prefers-reduced-motion` wraps all non-essential animation
- Semantic HTML always — no `<div>` soup for aesthetic reasons
- Focus rings match the aesthetic — styled, never removed
- No icon-only buttons without `aria-label`

> See `references/accessibility.md` for ARIA patterns, keyboard navigation, and focus trap implementation.

---

## Layer 5A — Visual Self-Audit (Critique Pass)

Mandatory before delivery. Each failed check has a fix protocol — apply it immediately.

### Visual Hierarchy
- [ ] Eye lands on intended focal point within 2 seconds
- [ ] Clear size/weight/color hierarchy — nothing at equal visual weight
- [ ] Primary CTA is most prominent interactive element
- **Fix:** increase size contrast by 1.5×, reduce competing elements' weight

### Rhythm & Space
- [ ] Adequate vertical breathing room throughout
- [ ] All spacing on 8px grid — zero arbitrary pixel values
- [ ] Type scale feels like one coherent system
- **Fix:** audit every `padding`/`margin`, round to nearest `--space-*` token

### Slop Detection
- [ ] Card/grid layouts have deliberate variation — not cloned templates
- [ ] Copy is context-specific — no "Lorem ipsum", no "Learn More"
- [ ] At least 2 moments of unexpected delight (hover, reveal, texture, interaction)
- **Fix:** replace one card with a different size/orientation variant; replace generic CTAs with domain-specific language

### Token Consistency
- [ ] Every color traces back to a semantic token
- [ ] Zero raw hex, `rgb()`, or `hsl()` in component styles
- [ ] Font families match the aesthetic declaration comment
- **Fix:** grep for `#`, `rgb(`, `hsl(` in component styles — convert every hit to a `var(--*)` reference

### Responsive Integrity
- [ ] Layout holds at 320px without horizontal scroll
- [ ] Tap targets ≥ 44px on mobile
- [ ] No text below `--step--1` on any viewport
- **Fix:** switch to single-column, increase tap target padding, bump minimum font step

### Emotional Resonance
- [ ] Interface feels built for a *specific* user, not a generic persona
- [ ] Correct emotional register for the domain
- [ ] Would a designer screenshot and share this?

---

## Layer 5B — Fidelity Pass (Structural Self-Correction)

After 5A, mentally simulate rendering and run these checks. This is the AI equivalent of opening the browser.

### Structural Fidelity
- [ ] DOM nesting matches intended visual hierarchy (Layer 4 architecture rule)
- [ ] No component is visually "orphaned" — every element belongs to a clear parent
- [ ] Z-index layers are intentional and commented: `/* z: modal > overlay > content > base */`

### Layout Accuracy
- [ ] Every major visual block occupies the intended grid columns
- [ ] Text blocks don't overflow containers at any breakpoint
- [ ] Images/media have explicit `aspect-ratio` — no layout shift on load

### Visual Semantic Fidelity
- [ ] The aesthetic declared in Layer 1 is *visible* in the output — not just in comments
- [ ] OKLCH values in the output match the Layer 1 commitment
- [ ] Motion character matches the aesthetic

### Self-Healing Protocol
If any check fails:
1. Identify the failing check
2. Find root cause in code (wrong token? missing rule? wrong DOM structure?)
3. Fix root cause — never patch with `!important` or inline overrides
4. Re-run 5A and 5B on the patched section
5. Deliver only when all checks pass

---

## Execution Sequence

1. **Layer 0** — extract intent, user, emotional arc, brand tokens; check for existing design system if multi-file
2. **Layer 1** — declare one extreme aesthetic with CDN-verified fonts + full token fingerprint
3. **Layer 2** — ban every default; reach for lower-probability choices
4. **Layer 3** — two-tier OKLCH tokens, fluid type, 8px grid, spring motion, Three.js escalation check
5. **Layer 4** — hierarchy-aware DOM, multi-file token discipline, composition patterns, all interaction states, accessibility
6. **Layer 5A** — visual critique, apply fix protocols
7. **Layer 5B** — structural/fidelity checks, self-healing loop
8. **Deliver** — only after both passes fully clear

---

## Emotional Narrative Framing

For any non-trivial interface, frame design intent as a user journey before touching code:

> *"A [first-time visitor / returning user / skeptical buyer] lands here.  
> They feel [overwhelmed / curious / skeptical].  
> Within 3 seconds they must feel [trusted / excited / calm].  
> The one action I'm designing toward is [X].  
> Every layout decision, color choice, and motion beat serves that arc."*

---

## Output Contract

- Production-grade code only — no pseudocode, no `/* TODO */`, no placeholders
- Single-file HTML: fully self-contained, all fonts via CDN-verified sources from Layer 1 table
- Multi-file: read existing tokens first, never introduce a second design system
- React: default export, zero required props, all defaults baked in
- All animations wrapped in `prefers-reduced-motion` fallback
- All colors via semantic OKLCH tokens — zero raw hex in component styles
- All interactive elements ship with hover, focus, active, disabled, loading states
- DOM hierarchy mirrors visual hierarchy (Layer 4 rule enforced)
- Verified at 320px, 768px, 1440px before delivery
- **The bar: would this place on Awwwards? If not, run the self-healing protocol until it does.**
