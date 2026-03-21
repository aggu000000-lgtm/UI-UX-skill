# design-tokens.md — Two-Tier OKLCH Token Architecture

> **Load this file before writing any component styles.**  
> Every color, shadow, and motion value in a component must trace back to a token defined here. Zero exceptions.

---

## 1. Architecture Overview

The system operates on a **strict unidirectional cascade** across three tiers. The rule is immutable: **UI components only touch Tier 2 semantic tokens — never Tier 1 primitives.**

```
┌─────────────────────────────────────────────────────────────┐
│                     TIER 1: PRIMITIVES                      │
│  Raw, context-agnostic OKLCH values. The artist's palette.  │
│  Named by what they ARE. NEVER used in component CSS.       │
│  Convention: --_[hue-name]-[lightness-int]                  │
└──────────────────────────┬──────────────────────────────────┘
                           │  mapped via var()
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                      TIER 2: SEMANTIC                       │
│  Contextual roles. Maps intent → primitive value.           │
│  Named by what they MEAN. ONLY layer components touch.      │
│  Convention: --color-[category]-[role]                      │
└──────────────────────────┬──────────────────────────────────┘
                           │  overridden via @scope
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                   TIER 3: COMPONENT-SCOPED                  │
│  Isolated semantic overrides within @scope boundaries.      │
│  Prevents specificity wars. Enables local theme inversion.  │
│  Convention: @scope (.component) { --color-* overrides }    │
└─────────────────────────────────────────────────────────────┘
```

**Import order is non-negotiable:**
```
primitives.css → semantic.css → themes/*.css → components/*.css
```
Reversing this order at any point causes cascade failures that are silent and extremely difficult to trace.

---

## 2. Why OKLCH

### The Fatal Flaw of HSL

HSL is not perceptually uniform. A yellow and a blue both set to `hsl(*, *, 50%)` appear radically different in brightness to the human eye — yellow is luminous, blue is muddy. This is because HSL's lightness axis is a geometric transformation of the sRGB cylinder, not a model of human visual biology.

**OKLCH is built from measured human perception.** Any two colors sharing the same `L` value in OKLCH appear equally bright regardless of hue. This has a critical practical consequence: text-to-background contrast ratios are mathematically predictable. A text token at `L = 16%` against a surface token at `L = 98%` will pass WCAG AA on every theme, every hue, every rebrand — without recalculation.

### The Three Axes

```
oklch( L  C  H  / alpha )
         │  │  │
         │  │  └─ Hue: 0–360° color wheel angle
         │  └──── Chroma: 0 (gray) → ~0.37 (max vivid, display-dependent)
         └─────── Lightness: 0% (black) → 100% (white) — perceptually uniform
```

**Lightness (L)** — Manipulate with `calc()`. Adding `0.08` produces identical perceived brightening across all hues. This is the foundation of automated state derivation.

**Chroma (C)** — Theoretically unbounded; practically capped by display hardware.

| C Value | Perceptual Character | Use Case |
|---|---|---|
| `0` | Pure neutral gray | Backgrounds, disabled states |
| `0.01–0.04` | Barely tinted, professional | Neutral surfaces with warmth |
| `0.05–0.14` | Muted, usable | Secondary accents, borders |
| `0.15–0.20` | Vivid, brand-worthy | Primary accents, CTAs |
| `0.21–0.37` | Maximum saturation | Alert states, P3-only accents |

**Hue (H)** — Perceptually distributed. Key landmarks differ from HSL:

| H° | Color | Use Case |
|---|---|---|
| `25` | Red | Error states |
| `65` | Amber / Yellow | Warning, primary accents |
| `95` | Yellow | Warning background tints |
| `145` | Green | Success states |
| `240` | Blue | Info, interactive focus |
| `290` | Purple | Alternative accent |

### P3 Gamut Access

sRGB (hex, rgb(), hsl()) covers ~35% of human visible color. Display P3 is ~50% larger. OKLCH provides direct CSS access to the entire P3 volume.

When an OKLCH coordinate falls outside a display's gamut, the browser performs **Chroma Reduction**: it holds `L` and `H` constant and decreases `C` until the color fits. This means accessibility (lightness contrast) and hue intent are always preserved — only saturation is reduced on inferior hardware.

### Browser Support + Fallback Pattern

| Engine | OKLCH | P3 Gamut |
|---|---|---|
| Safari (iOS 15.4+) | ✅ | ✅ |
| Chrome/Edge (v111+) | ✅ | ✅ |
| Firefox (v113+) | ✅ | ✅ |
| Legacy / IE11 | ❌ | ❌ |

**Three-layer progressive enhancement:**

```css
/* Layer 1: sRGB hex fallback for legacy parsers */
:root {
  --_amber-60: #f59e0b;
}

/* Layer 2: OKLCH for all modern browsers (sRGB-safe chroma) */
@supports (color: oklch(0% 0 0)) {
  :root {
    --_amber-60: oklch(72% 0.18 65);
  }
}

/* Layer 3: P3-expanded chroma for capable displays */
@media (color-gamut: p3) {
  @supports (color: oklch(0% 0 0)) {
    :root {
      --_amber-60: oklch(72% 0.22 65);
    }
  }
}
```

---

## 3. Tier 1 — Primitive Token Spec

### Naming Convention

```
--_[hue-name]-[lightness-int]

Examples:
  --_amber-60    → oklch(72% 0.18 65)   ← amber hue, ~60% lightness range
  --_neutral-16  → oklch(16% 0.04 255)  ← near-black neutral
  --_red-95      → oklch(95% 0.04 25)   ← very light red tint

The --_ prefix signals "private/internal". Never reference in components.
```

The integer encodes the lightness tier, giving immediate intuition: `--_neutral-98` is near-white, `--_neutral-10` is near-black. No ambiguity in design handoff.

### Full Primitive Palette

```css
@layer design.primitives {
  :root {
    /* ── Neutrals (Cool Blue cast, Hue 255) ── */
    --_neutral-98: oklch(98% 0.01 255);   /* Luminous app background */
    --_neutral-95: oklch(95% 0.01 255);   /* Subtle surface elevation */
    --_neutral-90: oklch(90% 0.02 255);   /* UI element background */
    --_neutral-80: oklch(80% 0.02 255);   /* Borders and dividers */
    --_neutral-60: oklch(60% 0.02 255);   /* Muted typography */
    --_neutral-40: oklch(40% 0.03 255);   /* Secondary typography */
    --_neutral-16: oklch(16% 0.04 255);   /* Primary text / dark surface */
    --_neutral-10: oklch(10% 0.04 255);   /* Maximum depth */

    /* ── Accent Family: Amber (Hue 65) ── */
    --_amber-95: oklch(95% 0.05 65);      /* Subtle accent surface */
    --_amber-60: oklch(72% 0.18 65);      /* Primary interactive stop */
    --_amber-20: oklch(35% 0.12 65);      /* Dark accent text */

    /* ── Success Family: Green (Hue 145) ── */
    --_green-95: oklch(95% 0.06 145);
    --_green-60: oklch(65% 0.16 145);
    --_green-20: oklch(30% 0.10 145);

    /* ── Warning Family: Yellow (Hue 95) ── */
    --_yellow-95: oklch(95% 0.06 95);
    --_yellow-70: oklch(75% 0.15 95);
    --_yellow-20: oklch(35% 0.10 95);

    /* ── Error Family: Red (Hue 25) ── */
    --_red-95:    oklch(95% 0.04 25);
    --_red-60:    oklch(60% 0.18 25);
    --_red-20:    oklch(30% 0.12 25);

    /* ── Absolute Anchors ── */
    --_white: oklch(100% 0 0);
    --_black: oklch(0%   0 0);
  }
}
```

**Hard rule:** `--_*` tokens appear exclusively in `tokens/primitives.css` and `tokens/semantic.css`. If a `--_` token appears in any component file, the architecture has been violated.

---

## 4. Tier 2 — Semantic Token Spec

Every component style references one of these tokens — nothing else. When a rebrand occurs, only the primitive → semantic mappings below change. The components are untouched.

### The 8 Mandatory Semantic Categories

| # | Category | Controls |
|---|---|---|
| 1 | **Surface** | Base canvas, raised cards, overlays, inset wells |
| 2 | **Text** | All typography color including inverse and on-accent |
| 3 | **Accent** | Brand color for primary actions and focal points |
| 4 | **Border** | Containers, dividers, focus rings |
| 5 | **Feedback** | Error / Warning / Success / Info — bg + text + border |
| 6 | **Interactive State** | Hover/active/disabled opacity values |
| 7 | **Shadow** | Tinted OKLCH depth — never flat black |
| 8 | **Motion** | Duration and easing for all transitions |

### Complete Semantic CSS

```css
@layer design.semantic {
  :root {
    /* ── 1. SURFACE ── */
    --color-surface-base:    var(--_neutral-98);  /* Page background */
    --color-surface-raised:  var(--_white);        /* Cards, panels */
    --color-surface-overlay: var(--_white);        /* Modals, drawers */
    --color-surface-inset:   var(--_neutral-95);   /* Input fields, wells */

    /* ── 2. TEXT ── */
    --color-text-primary:    var(--_neutral-16);
    --color-text-secondary:  var(--_neutral-40);
    --color-text-muted:      var(--_neutral-60);
    --color-text-inverse:    var(--_white);
    --color-text-on-accent:  var(--_neutral-10);

    /* ── 3. ACCENT ── */
    --color-accent-default:  var(--_amber-60);
    --color-accent-on-accent: var(--_neutral-10);
    /* Derived states (hover, active, subtle) → See Section 5 */

    /* ── 4. BORDER ── */
    --color-border-subtle:   var(--_neutral-90);
    --color-border-default:  var(--_neutral-80);
    --color-border-strong:   var(--_neutral-60);
    --color-border-focus:    var(--color-accent-default);

    /* ── 5. FEEDBACK ── */
    --color-error-bg:        var(--_red-95);
    --color-error-text:      var(--_red-20);
    --color-error-border:    var(--_red-60);

    --color-success-bg:      var(--_green-95);
    --color-success-text:    var(--_green-20);
    --color-success-border:  var(--_green-60);

    --color-warning-bg:      var(--_yellow-95);
    --color-warning-text:    var(--_yellow-20);
    --color-warning-border:  var(--_yellow-70);

    --color-info-bg:         var(--_neutral-95);
    --color-info-text:       var(--_neutral-16);
    --color-info-border:     var(--_neutral-60);

    /* ── 6. INTERACTIVE STATE (opacity multipliers) ── */
    --opacity-hover:         0.08;
    --opacity-active:        0.12;
    --opacity-disabled:      0.50;

    /* ── 7. SHADOW (tinted, never flat black) ── */
    --shadow-color: oklch(from var(--_neutral-16) l c h / 0.08);
    --shadow-sm:    0 1px 2px 0 var(--shadow-color);
    --shadow-md:    0 4px 6px -1px var(--shadow-color),
                    0 2px 4px -2px var(--shadow-color);
    --shadow-lg:    0 10px 15px -3px var(--shadow-color),
                    0 4px 6px -4px  var(--shadow-color);

    /* ── 8. MOTION ── */
    --motion-duration-fast:   150ms;
    --motion-duration-base:   250ms;
    --motion-duration-slow:   400ms;
    --motion-easing-linear:   linear;
    --motion-easing-out:      cubic-bezier(0, 0, 0.2, 1);
    --motion-easing-spring:   cubic-bezier(0.175, 0.885, 0.32, 1.275);
  }
}
```

---

## 5. Derived Tokens — Relative Color Syntax

Never manually define hover or active variants. They are derived programmatically from the base semantic token. Because OKLCH is perceptually uniform, `calc(l + 0.08)` produces the exact same perceived lightening on amber, blue, or red — zero per-hue adjustment needed.

### Primary Pattern (CSS Color 5 RCS)

```css
@supports (color: oklch(from red l c h)) {
  :root {
    /* Lightness up → lighter / brighter */
    --color-accent-hover:    oklch(from var(--color-accent-default) calc(l + 0.08) c h);

    /* Lightness down → darker / pressed */
    --color-accent-active:   oklch(from var(--color-accent-default) calc(l - 0.05) c h);

    /* Alpha channel → transparent tint for badge backgrounds */
    --color-accent-subtle:   oklch(from var(--color-accent-default) l c h / 0.15);

    /* Chroma → 0 + opacity reduction → visually neutral disabled */
    --color-accent-disabled: oklch(from var(--color-accent-default) l 0 h / var(--opacity-disabled));
  }
}
```

**Never modify the H (hue) channel for state derivation.** Shifting hue on hover turns amber buttons green. Only manipulate `L` (brightness) or `/` (opacity).

**Never clamp L to exactly 0% or 100%.** This destroys the underlying chroma and hue vector, making reverse manipulation impossible. Use `98%` as the brightness ceiling and `10%` as the depth floor.

### color-mix() Fallback Pattern

Declare fallbacks first (broad compatibility), let `@supports` override with the precise RCS version:

```css
/* Fallback — runs everywhere, uses color-mix() in oklch interpolation space */
:root {
  --color-accent-hover:    color-mix(in oklch, var(--color-accent-default), var(--_white) 15%);
  --color-accent-active:   color-mix(in oklch, var(--color-accent-default), var(--_black) 10%);
  --color-accent-subtle:   color-mix(in oklch, var(--color-accent-default) 15%, transparent);
}

/* Override with high-precision RCS where supported */
@supports (color: oklch(from red l c h)) {
  :root {
    --color-accent-hover:    oklch(from var(--color-accent-default) calc(l + 0.08) c h);
    --color-accent-active:   oklch(from var(--color-accent-default) calc(l - 0.05) c h);
    --color-accent-subtle:   oklch(from var(--color-accent-default) l c h / 0.15);
  }
}
```

**Why `in oklch` for color-mix()?** Specifying the oklch interpolation space prevents the muddying artifacts of sRGB mixing, where blending two vivid complementary colors produces a dull gray midpoint instead of a perceptually smooth transition.

---

## 6. Dark Mode — Single Swap Pattern

**The rule: only semantic tokens change during a theme swap. Primitives never change.**

`--_neutral-98` is physically bright — that fact doesn't change per theme. The semantic layer simply remaps which primitive a role points to.

```css
/* ── Default: Dark Mode ── */
:root {
  --color-surface-base:    var(--_neutral-10);
  --color-surface-raised:  var(--_neutral-16);
  --color-surface-overlay: var(--_neutral-16);
  --color-surface-inset:   var(--_neutral-16);

  --color-text-primary:    var(--_neutral-98);
  --color-text-secondary:  var(--_neutral-80);
  --color-text-muted:      var(--_neutral-60);

  --color-border-subtle:   var(--_neutral-16);
  --color-border-default:  var(--_neutral-40);

  --color-accent-default:  var(--_amber-60);
  --color-accent-on-accent: var(--_neutral-10);
}

/* ── Single Swap: Light Mode ── */
[data-theme="light"] {
  --color-surface-base:    var(--_neutral-98);
  --color-surface-raised:  var(--_white);
  --color-surface-overlay: var(--_white);
  --color-surface-inset:   var(--_neutral-95);

  --color-text-primary:    var(--_neutral-16);
  --color-text-secondary:  var(--_neutral-40);
  --color-text-muted:      var(--_neutral-60);

  --color-border-subtle:   var(--_neutral-90);
  --color-border-default:  var(--_neutral-80);

  --color-accent-default:  var(--_amber-60);
  --color-accent-on-accent: var(--_white);
}

/* ── OS-level: zero-JS auto-adaptation ── */
@media (prefers-color-scheme: light) {
  /* Only fires if user hasn't explicitly toggled via [data-theme] */
  :root:not([data-theme="dark"]) {
    --color-surface-base:    var(--_neutral-98);
    --color-surface-raised:  var(--_white);
    --color-surface-overlay: var(--_white);
    --color-text-primary:    var(--_neutral-16);
    --color-text-secondary:  var(--_neutral-40);
    --color-border-subtle:   var(--_neutral-90);
    --color-border-default:  var(--_neutral-80);
    --color-accent-default:  var(--_amber-60);
    --color-accent-on-accent: var(--_white);
  }
}
```

**Critical behavior:** All derived tokens (RCS hover/active states) automatically re-ingest the newly swapped `--color-accent-default`. There is zero need to redeclare derived states per theme.

---

## 7. Component-Scoped Tokens

For component-level thematic isolation (e.g., an inverted promotional card on a light page), use CSS `@scope` — not BEM modifiers, not class chains.

### Cascade Resolution Order

```
1. :root semantic definition           → global baseline
2. @scope (.component) { :scope {} }   → local override environment
3. .component { property: var(--*) }   → consumes nearest available token
```

### Standard Component (semantic consumption)

```css
.card {
  background-color: var(--color-surface-raised);
  color:            var(--color-text-primary);
  border:           1px solid var(--color-border-subtle);
  box-shadow:       var(--shadow-md);
  padding:          1.5rem;
  border-radius:    8px;
}
```

### Scoped Override (local theme inversion)

```css
/* Every element nested inside .card.is-highlighted automatically reads
   these local semantic values — no modifier classes on children needed */
@scope (.card.is-highlighted) {
  :scope {
    --color-surface-raised:  var(--color-accent-subtle);
    --color-text-primary:    var(--color-accent-default);
    --color-border-subtle:   var(--color-accent-default);
  }
}
```

**When to use `@scope` vs BEM modifiers:**

| Scenario | Use |
|---|---|
| Nested children must inherit the override automatically | `@scope` |
| Single element's property changes | BEM modifier class |
| Full "inverted" or "contextual theme" zone needed | `@scope` |
| One-off padding/size variant | BEM modifier class |

---

## 8. Multi-File Token Architecture

### File Structure

```
src/styles/
└── tokens/
│   ├── primitives.css       ← Tier 1. All --_* tokens. Imported first.
│   ├── semantic.css         ← Tier 2. All --color-* tokens. Imports primitives.
│   └── themes/
│       ├── dark.css         ← :root default semantic mappings (dark-first)
│       └── light.css        ← [data-theme="light"] semantic overrides
└── components/
    ├── button.css           ← Consumes semantic tokens only
    └── card.css             ← Consumes semantic tokens only
```

### Root Pipeline Entrypoint (`index.css`)

```css
/* 1. Declare layer precedence — non-negotiable order */
@layer design.primitives, design.semantic, design.components;

/* 2. Core token infrastructure */
@import url('./tokens/primitives.css') layer(design.primitives);
@import url('./tokens/semantic.css')   layer(design.semantic);

/* 3. Theme state overrides (still inside design.semantic) */
@import url('./tokens/themes/dark.css')  layer(design.semantic);
@import url('./tokens/themes/light.css') layer(design.semantic);

/* 4. UI components (consume the preceding infrastructure) */
@import url('./components/button.css') layer(design.components);
@import url('./components/card.css')   layer(design.components);
```

The `@layer` declaration ensures that even an `#id.class.specific` component selector cannot override a foundational token definition — the layer order enforces precedence independently of specificity arithmetic.

---

## 9. DTCG JSON Format (Round-Trip with Design Tools)

### Valid Schema

```json
{
  "color": {
    "accent": {
      "default": {
        "$type": "color",
        "$value": "oklch(72% 0.18 65)",
        "$description": "Primary interactive accent — amber. Maps to --_amber-60."
      },
      "subtle": {
        "$type": "color",
        "$value": "{color.accent.default}",
        "$description": "Alias: transparent tint of default accent for badge backgrounds."
      }
    },
    "surface": {
      "base": {
        "$type": "color",
        "$value": "{color.neutral.98}",
        "$description": "Application canvas background."
      }
    }
  },
  "shadow": {
    "sm": {
      "$type": "shadow",
      "$value": {
        "color": "oklch(from oklch(16% 0.04 255) l c h / 0.08)",
        "offsetX": "0px",
        "offsetY": "1px",
        "blur": "2px",
        "spread": "0px"
      }
    }
  },
  "motion": {
    "duration": {
      "base": {
        "$type": "duration",
        "$value": "250ms",
        "$description": "Standard transition duration."
      }
    }
  }
}
```

### Style Dictionary v4 Transform Config

```javascript
import StyleDictionary from 'style-dictionary';
import { register } from '@tokens-studio/sd-transforms';

register(StyleDictionary);

const sd = new StyleDictionary({
  source: ['tokens/**/*.json'],
  platforms: {
    css: {
      transformGroup: 'css',
      buildPath: 'build/css/',
      transforms: [
        'shadow/css/shorthand'   // Flattens composite shadow objects → CSS shorthand
      ],
      files: [{
        destination: 'tokens.css',
        format: 'css/variables',
        options: {
          outputReferences: true  // Preserves var() chains, not flattened values
        }
      }]
    }
  }
});

await sd.buildAllPlatforms();
```

**Key v4 behavior:** Style Dictionary v4 resolves token type via the DTCG `$type` property directly — not legacy CTI attribute matching. Always include `$type` on every token.

---

## 10. Audit Checklist

Run this before committing any component file or executing UI generation.

- [ ] **Semantic-only** — Every color in component CSS traces to a `--color-*` semantic token. Direct `--_*` primitives are categorically absent from components.
- [ ] **No raw values** — Zero `#hex`, `rgb()`, `hsl()`, or bare `oklch()` literals in component files. Every value is a `var(--*)` reference.
- [ ] **Primitive isolation** — `--_*` tokens appear exclusively in `tokens/primitives.css` and `tokens/semantic.css`.
- [ ] **Dark mode fidelity** — All surface and text tokens verified visually and mathematically under `[data-theme="light"]`.
- [ ] **Derived states** — Hover, active, subtle, disabled use RCS `oklch(from var(...) calc(l ± n) c h)` with `color-mix()` fallback declared first.
- [ ] **H channel untouched** — No state derivation modifies the hue. Only `L` and `/` alpha are manipulated.
- [ ] **L boundaries respected** — No derived token clamps `L` to exactly `0%` or `100%`. Ceiling is `98%`, floor is `10%`.
- [ ] **Focus ring** — `--color-border-focus` confirmed at ≥ 3:1 contrast against all adjacent surfaces.
- [ ] **Shadow source** — All shadows use `--shadow-sm/md/lg` which inherit from `--shadow-color` (OKLCH-tinted). Zero `rgba(0,0,0,x)` instances.
- [ ] **Motion tokens** — All `transition` declarations reference `--motion-duration-*` and `--motion-easing-*`. Zero hardcoded `0.3s ease`.
- [ ] **Layer order intact** — `index.css` declares `@layer design.primitives, design.semantic, design.components` before any `@import`.

---

## 11. Common Mistakes + Fixes

| Mistake | Why It Breaks | Fix |
|---|---|---|
| Using `--_amber-60` directly in `button.css` | Bypasses semantic abstraction. Dark mode swap and rebrand leave the button stuck to a hardcoded physical value. | Remap to `--color-accent-default`. |
| Using `hsl()` or `#hex` for new UI features | HSL breaks perceptual uniformity on state transitions, violating WCAG contrast during hover. | Convert to OKLCH equivalent via a perceptual color picker. |
| Defining `--btn-bg`, `--btn-text` per component | Token explosion. Hundreds of hyper-specific custom properties that design teams cannot govern. | Consume global semantic tokens directly unless a `@scope` override is architecturally required. |
| Omitting the `color-mix()` fallback | RCS is unsupported in slightly older environments; hover states render broken or invisible. | Always declare `color-mix()` variant first, then override with `@supports (color: oklch(from red l c h))`. |
| Modifying the H channel on hover | Amber button turns green on hover instead of lightening. Hue is brand identity — it must not shift on interaction. | Only manipulate `L` (lightness) or `/` (alpha) for state derivation. |
| `rgba(0,0,0,0.5)` for shadows | Flat black shadows mud over colored surfaces and disappear on dark backgrounds. | Use `--shadow-color` derived from `--_neutral-16` via OKLCH alpha syntax. |
| Dark mode overrides written inside component files | Thematic logic scattered across hundreds of files. Rebrands require hunting through every component. | All theme logic lives exclusively in `tokens/themes/dark.css` and `tokens/themes/light.css`. |
| Duplicating primitive declarations per theme | Violates DRY. Desynchronizes over time. Two sources of truth for the same value. | Primitives defined once. Themes only swap semantic token mappings. |
| Using class chains for component overrides (`.card.highlight .btn`) | Specificity wars. Source-order-dependent overrides that break unpredictably. | Use `@scope (.card.is-highlighted) { :scope { --color-* overrides } }`. |
| Clamping `L` to exactly `0%` or `100%` in RCS | Obliterates chroma and hue vector. Reverse manipulation becomes impossible; color loses its identity. | Use `98%` as brightness ceiling, `10%` as depth floor. |
