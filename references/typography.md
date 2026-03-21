# Typography Reference
<!-- Load this file before writing any frontend UI code involving fonts. -->
<!-- All claims verified against Google Fonts API v2 and Fontshare v2. -->

---

## 1. Variable Font Usage

### CSS Axis Declaration

Variable fonts expose their design space via an OpenType `fvar` table. CSS controls axes
through two layers — high-level properties and the low-level `font-variation-settings`.

**High-level property → axis mapping:**

| CSS Property | Axis Tag | Example |
|---|---|---|
| `font-weight` | `wght` | `font-weight: 425` (any integer, not just ×100) |
| `font-stretch` | `wdth` | `font-stretch: 89%` (100% = normal) |
| `font-style: oblique <angle>` | `slnt` | `font-style: oblique 12deg` |
| `font-style: italic` | `ital` | `font-style: italic` (binary: 0 or 1) |

**Use `font-variation-settings` directly when:**
1. Manipulating custom axes (`GRAD`, `SOFT`, `WONK`) — no high-level property exists.
2. A single variable file contains both upright + italic masters; browser mapping of `font-style: italic` to `ital` is inconsistent across engines.

**Critical architecture rule — decouple axes via CSS custom properties:**

`font-variation-settings` overwrites the entire property string on every redeclaration.
Setting one axis in a child element silently erases all others. Fix: initialize every axis
as a CSS variable and compose a single settings string.

```css
:root {
  --font-wght: 400;
  --font-opsz: 16;
  --font-grad: 0;
  --font-ital: 0;
  --font-slnt: 0;
}

.typography-base {
  /* High-level for primary axes */
  font-weight: var(--font-wght);
  font-style: normal;

  /* Low-level for custom / override axes */
  font-variation-settings:
    "opsz" var(--font-opsz),
    "GRAD" var(--font-grad),
    "ital" var(--font-ital),
    "slnt" var(--font-slnt);
}

/* State change: update the variable, never redeclare the settings string */
.typography-base.is-bold {
  --font-wght: 750;
}

.typography-base.is-emphasized {
  --font-grad: 150;
}
```

---

### Google Fonts Variable Load Syntax

Endpoint: `https://fonts.googleapis.com/css2`

**Rules (violating any causes a 400 Bad Request):**
- Axes declared after `:` must be in strict alphabetical order: `ital` → `opsz` → `wght`
- Continuous ranges use `..` operator: `100..900`
- When `ital` is requested, prefix every tuple with `0,` (upright) or `1,` (italic)
- Tuple ranges must not overlap: `100..400;400..900` is invalid → use `100..400;401..900` or a single `100..900`

```html
<!-- Single axis variable font -->
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Outfit:wght@100..900&display=swap">

<!-- Multi-axis: ital + opsz + wght (alphabetical order enforced) -->
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,opsz,wght@0,5..1200,400..900;1,5..1200,400..900&display=swap">

<!-- Multi-axis: ital + wght only -->
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght@0,9..144,100..900;1,9..144,100..900&display=swap">
```

---

### Performance Decision Matrix

| Scenario | Use | Reason |
|---|---|---|
| ≤ 3 font styles needed | Static WOFF2 | 3 statics ≈ 60–90KB < ~112KB variable file |
| ≥ 4 font styles needed | Variable | 4 statics ≈ 120KB+ ≥ variable; also eliminates 3 blocking HTTP requests |
| Fluid responsive weight (scale `font-weight` against viewport) | Variable | Only variable fonts interpolate between integer values |
| CSS transitions on `font-weight` / `GRAD` on hover | Variable | Static fonts snap, no interpolation |
| Legacy environment (IE11, old Safari) | Static only | `font-variation-settings` unsupported |

**File size reference:**
- Single static WOFF2: ~20–30KB
- Full variable font (wght 100–900): ~80–120KB
- Variable font payload is ~70% larger than one static weight but replaces up to 9

---

### Axis Registry

| Axis | Tag | Type | CSS Property | Range | Notes |
|---|---|---|---|---|---|
| Weight | `wght` | Registered | `font-weight` | 1–1000 | Any integer; 400 = Regular, 700 = Bold |
| Width | `wdth` | Registered | `font-stretch` | 1–1000 (%) | 100 = default; affects advance widths |
| Italic | `ital` | Registered | `font-style` | 0 or 1 | Binary toggle; may invoke alternate glyphs |
| Optical Size | `opsz` | Registered | `font-optical-sizing` | > 0 (pt) | Adjusts contrast, tracking, x-height per size |
| Grade | `GRAD` | Custom | `font-variation-settings` | −200 to 200 | Changes visual weight without shifting advance widths — zero layout reflow (CLS-safe for dark mode) |
| Slant | `slnt` | Registered | `font-style: oblique <angle>` | −90 to 90 | Continuous slant; not a binary italic switch |
| Softness | `SOFT` | Custom | `font-variation-settings` | font-specific | Present in Fraunces; controls terminal softness |
| Wonk | `WONK` | Custom | `font-variation-settings` | 0 or 1 | Present in Fraunces; toggles alternate letterforms |

---

### Fallback Strategy

Wrap variable `@font-face` in `@supports (font-variation-settings: normal)`. Legacy engines
skip the block entirely and use static definitions.

```css
/* 1. Static fallbacks — always defined first */
@font-face {
  font-family: 'PrimaryStack';
  font-style: normal;
  font-weight: 400;
  font-display: swap;
  src: url('static-regular.woff2') format('woff2');
}

@font-face {
  font-family: 'PrimaryStack';
  font-style: normal;
  font-weight: 700;
  font-display: swap;
  src: url('static-bold.woff2') format('woff2');
}

/* 2. Base assignment maps to static */
body {
  font-family: 'PrimaryStack', system-ui, sans-serif;
  font-weight: 400;
}

/* 3. Progressive enhancement — variable font override */
@supports (font-variation-settings: normal) {
  @font-face {
    font-family: 'PrimaryStack Variable';
    font-weight: 100 900;
    font-style: normal;
    font-display: swap;
    src: url('variable.woff2') format('woff2 supports variations'),
         url('variable.woff2') format('woff2-variations');
  }

  body {
    font-family: 'PrimaryStack Variable', system-ui, sans-serif;
  }
}
```

---

## 2. Optical Sizing

### `font-optical-sizing: auto`

**Default in all modern browsers.** When the loaded font has an `opsz` axis in its `fvar`
table, the browser maps `computed font-size` (in px, treated as pt) directly to the `opsz`
coordinate. No code required.

**If the font has no `opsz` axis: this is a silent no-op.** Zero effect. Check the per-font
table below before relying on it.

What `opsz` actually does (not linear scaling):

| Optical Size Direction | Geometry Change |
|---|---|
| Decreasing (caption) | Thicken strokes, reduce contrast, enlarge ink traps, widen tracking, increase x-height |
| Increasing (display) | Thin hairlines, tighten tracking, aggressive kerning, maximize stroke contrast |

---

### Manual `opsz` Control

Use when you need to decouple optical characteristics from physical size — e.g., a 24px
subheading that should look like a display cut, or a 120px hero that needs robust legibility.

```css
/* Disable auto mapping first, then override */
.hero-legible {
  font-size: 120px;
  font-optical-sizing: none;
  /* Force thick-stroke body geometry despite large size */
  font-variation-settings: 'opsz' 16;
}

.caption-delicate {
  font-size: 11px;
  font-optical-sizing: none;
  /* Force high-contrast display geometry despite small size */
  font-variation-settings: 'opsz' 144;
}
```

---

### `opsz` Ranges by Context

| Context | Pixel Range | Target `opsz` Value | Geometry Profile |
|---|---|---|---|
| Caption / Micro | < 12px | 6–11 | Max x-height, zero contrast, wide tracking, thick strokes |
| Body / Text | 14–18px | 11–18 | Balanced — this is the font's "standard" master |
| Subheading / Deck | 20–32px | 18–32 | Emerging contrast, slightly tighter tracking |
| Display / Poster | 40px+ | 36–144+ | Max contrast, razor hairlines, tight tracking, aggressive kerning |

---

### Per-Font `opsz` Axis Table

> `opsz: NO` = `font-optical-sizing: auto` is a **no-op** for this font.

| Font Family | CDN | Variable? | `wght` Range | `ital` Axis | `opsz` Range | Custom Axes |
|---|---|---|---|---|---|---|
| Outfit | Google | ✅ | 100–900 | ❌ | **NO opsz** | — |
| Cormorant Garamond | Google | ✅ | 300–700 | 0–1 | **NO opsz** | — |
| Playfair Display | Google | ✅ | 400–900 | 0–1 | **5–1200** | `wdth` |
| Fraunces | Google | ✅ | 100–900 | 0–1 | **9–144** | `SOFT`, `WONK` |
| Plus Jakarta Sans | Google | ✅ | 200–800 | 0–1 | **NO opsz** | — |
| JetBrains Mono | Google | ✅ | 100–800 | 0–1 | **NO opsz** | — |
| Bebas Neue | Google | ❌ | 400 only | ❌ | **NO opsz** | — |
| IBM Plex Mono | Google | ❌ | 100–700 | Separate file | **NO opsz** | — |
| Orbitron | Google | ✅ | 400–900 | ❌ | **NO opsz** | — |
| Share Tech Mono | Google | ❌ | 400 only | ❌ | **NO opsz** | — |
| Satoshi | Fontshare | ✅ | 300–900 | 0–1 | **NO opsz** | — |
| Clash Display | Fontshare | ✅ | 200–700 | ❌ | **NO opsz** | — |
| Syne | Google | ✅ | 400–800 | ❌ | **NO opsz** | — |

**Only 2 fonts in the system have a live `opsz` axis: Playfair Display and Fraunces.**

---

### `font-size-adjust`

Prevents Cumulative Layout Shift (CLS) during `font-display: swap`. When a web font loads,
if its x-height differs from the fallback font, text reflows and DOM elements shift.
`font-size-adjust` normalizes the fallback's rendering size to match the primary font's metrics.

**Browser support:** Baseline 2024 — all modern engines.

**Formula:** `adjusted-size = (target-aspect / fallback-aspect) × font-size`

**Keyword options:**

| Keyword | Normalizes Against |
|---|---|
| `ex-height` | Lowercase x height (default) |
| `cap-height` | Uppercase letter height |
| `ch-width` | Advance width of "0" glyph |
| `from-font` | Auto-extracts metric from primary font once loaded |

```css
/* Auto-extract from primary font — recommended default */
p {
  font-size: 16px;
  font-family: "Fraunces", "Times New Roman", serif;
  font-size-adjust: from-font;
}

/* Explicit cap-height normalization for display headings */
h1 {
  font-size: 48px;
  font-family: "Orbitron", "Arial", sans-serif;
  font-size-adjust: cap-height from-font;
}
```

---

## 3. CDN Import Strings

### Preconnect Hints

Place these before any font `<link>` tags. The `crossorigin` attribute is mandatory on font
asset origins — omitting it causes the browser to discard the preconnected socket and open
a new one for the CORS-restricted WOFF2 request, adding a full TCP/TLS round-trip.

```html
<!-- Google Fonts — CSS on googleapis, assets on gstatic (different origins) -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>

<!-- Fontshare — CSS and assets on same origin -->
<link rel="preconnect" href="https://api.fontshare.com" crossorigin>
```

---

### Google Fonts — Variable Fonts

All variable fonts in one optimized request (combine families with `&family=`):

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300..700;1,300..700&family=Fraunces:ital,opsz,wght@0,9..144,100..900;1,9..144,100..900&family=JetBrains+Mono:ital,wght@0,100..800;1,100..800&family=Orbitron:wght@400..900&family=Outfit:wght@100..900&family=Playfair+Display:ital,opsz,wght@0,5..1200,400..900;1,5..1200,400..900&family=Plus+Jakarta+Sans:ital,wght@0,200..800;1,200..800&family=Syne:wght@400..800&display=swap" rel="stylesheet">
```

Individual strings for selective loading:

```html
<!-- Outfit -->
<link href="https://fonts.googleapis.com/css2?family=Outfit:wght@100..900&display=swap" rel="stylesheet">

<!-- Cormorant Garamond (with italic) -->
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300..700;1,300..700&display=swap" rel="stylesheet">

<!-- Playfair Display (opsz + ital + wdth implicit) -->
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,opsz,wght@0,5..1200,400..900;1,5..1200,400..900&display=swap" rel="stylesheet">

<!-- Fraunces (opsz + ital; SOFT and WONK accessible via font-variation-settings) -->
<link href="https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght@0,9..144,100..900;1,9..144,100..900&display=swap" rel="stylesheet">

<!-- Plus Jakarta Sans -->
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:ital,wght@0,200..800;1,200..800&display=swap" rel="stylesheet">

<!-- JetBrains Mono -->
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:ital,wght@0,100..800;1,100..800&display=swap" rel="stylesheet">

<!-- Orbitron -->
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400..900&display=swap" rel="stylesheet">

<!-- Syne -->
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400..800&display=swap" rel="stylesheet">
```

---

### Google Fonts — Static Fonts

Static fonts use discrete weight values, not ranges:

```html
<!-- All static fonts combined -->
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=IBM+Plex+Mono:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;1,100;1,200;1,300;1,400;1,500;1,600;1,700&family=Share+Tech+Mono&display=swap" rel="stylesheet">

<!-- Bebas Neue (400 only — no other weights exist) -->
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&display=swap" rel="stylesheet">

<!-- IBM Plex Mono (italic is a separate file, not an axis) -->
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;1,100;1,200;1,300;1,400;1,500;1,600;1,700&display=swap" rel="stylesheet">

<!-- Share Tech Mono (400 only) -->
<link href="https://fonts.googleapis.com/css2?family=Share+Tech+Mono&display=swap" rel="stylesheet">
```

---

### Fontshare

Array-based syntax. `f[]=family@weight,weight`. No `..` range operator — use discrete weights.
The API resolves variable fonts internally.

```html
<link rel="preconnect" href="https://api.fontshare.com" crossorigin>

<!-- Satoshi + Clash Display combined -->
<link href="https://api.fontshare.com/v2/css?f[]=satoshi@300,400,500,700,900&f[]=clash-display@200,300,400,500,600,700&display=swap" rel="stylesheet">

<!-- Satoshi only -->
<link href="https://api.fontshare.com/v2/css?f[]=satoshi@300,400,500,700,900&display=swap" rel="stylesheet">

<!-- Clash Display only -->
<link href="https://api.fontshare.com/v2/css?f[]=clash-display@200,300,400,500,600,700&display=swap" rel="stylesheet">
```

> **Note:** Syne is available on Google Fonts — use the Google import, not Fontshare.

---

### `font-display: swap` vs `font-display: optional`

| | `swap` | `optional` |
|---|---|---|
| Block period | ~0ms | 100ms |
| Swap period | Infinite | None |
| Behavior | Renders fallback immediately, snaps to web font on load | If font loads within 100ms, uses it; otherwise locks fallback permanently |
| Risk | Flash of Unstyled Text (FOUT), possible CLS without `font-size-adjust` | Zero CLS; custom font may never render on slow connections |
| Use when | Typography is brand-critical, CLS is mitigated via `font-size-adjust` | Maximum performance budget, zero layout shift required |

**Default recommendation:** `swap` + `font-size-adjust: from-font` on body text.

---

### Import String Quick Reference

| Font | CDN | Variable? | Weights | Import Parameter |
|---|---|---|---|---|
| Outfit | Google | ✅ | 100–900 | `family=Outfit:wght@100..900` |
| Cormorant Garamond | Google | ✅ | 300–700 | `family=Cormorant+Garamond:ital,wght@0,300..700;1,300..700` |
| Playfair Display | Google | ✅ | 400–900 | `family=Playfair+Display:ital,opsz,wght@0,5..1200,400..900;1,5..1200,400..900` |
| Fraunces | Google | ✅ | 100–900 | `family=Fraunces:ital,opsz,wght@0,9..144,100..900;1,9..144,100..900` |
| Plus Jakarta Sans | Google | ✅ | 200–800 | `family=Plus+Jakarta+Sans:ital,wght@0,200..800;1,200..800` |
| JetBrains Mono | Google | ✅ | 100–800 | `family=JetBrains+Mono:ital,wght@0,100..800;1,100..800` |
| Bebas Neue | Google | ❌ | 400 | `family=Bebas+Neue` |
| IBM Plex Mono | Google | ❌ | 100–700 | `family=IBM+Plex+Mono:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;1,100;1,200;1,300;1,400;1,500;1,600;1,700` |
| Orbitron | Google | ✅ | 400–900 | `family=Orbitron:wght@400..900` |
| Share Tech Mono | Google | ❌ | 400 | `family=Share+Tech+Mono` |
| Satoshi | Fontshare | ✅ | 300–900 | `f[]=satoshi@300,400,500,700,900` |
| Clash Display | Fontshare | ✅ | 200–700 | `f[]=clash-display@200,300,400,500,600,700` |
| Syne | Google | ✅ | 400–800 | `family=Syne:wght@400..800` |

---

## Gotchas

**1. Missing `crossorigin` on preconnect — full TCP/TLS re-handshake**

`<link rel="preconnect" href="https://fonts.gstatic.com">` without `crossorigin` causes the
browser to discard the socket. WOFF2 requests are CORS-restricted; a new connection is opened
from scratch, defeating the preconnect entirely.

Fix: `<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>`

---

**2. `font-variation-settings` cascade collision silently discards axes**

Setting `font-variation-settings: "wght" 700` on a parent, then `font-weight: 400` on a child,
causes the high-level property to override and erase all `font-variation-settings` on the child.
Axes disappear without error.

Fix: Always initialize axes as CSS custom properties (`--font-wght: 400`) and compose one
unified `font-variation-settings` string. State changes update the variable, never the settings
declaration.

---

**3. `opsz` auto-mapping destroys body text readability at fluid-scaled sizes**

`font-optical-sizing: auto` maps `opsz` to the computed `font-size`. If you scale `font-size`
fluidly via `clamp()` or viewport units, a body paragraph can compute to 72px on a wide screen.
The engine applies display-cut geometry — hairline strokes, tight tracking — into a paragraph
block. Illegible.

Fix: On any fluid or dynamically scaled container, set `font-optical-sizing: none` and lock
`font-variation-settings: "opsz" 16` (or the correct semantic target).

---

**4. Google Fonts API v2: non-alphabetical axis order → 400 Bad Request**

Requesting `wght,opsz,ital` instead of `ital,opsz,wght` returns a 400 error. The font
stylesheet fails to load entirely — no fallback, silent failure in DevTools unless you check
the network tab.

Fix: Axis tags must be in en-US alphabetical order after the colon. Always: `ital` → `opsz` → `wght`.
Overlapping tuples (`100..400;400..900`) also trigger a 400 — ranges must be contiguous and
non-overlapping.

---

**5. `font-style: italic` on a `slnt`-only font → synthetic skew distortion**

If a variable font has a `slnt` axis but no binary `ital` axis, declaring `font-style: italic`
forces the browser to synthesize italics by geometrically skewing the upright master. The result
is corrupted kerning and distorted letterforms.

Fix: Use `font-style: oblique 10deg` to map directly to the `slnt` axis. If the font has both
`ital` and `slnt`, use `font-variation-settings: "ital" 1` explicitly and add `font-synthesis: none`
to prevent the engine from synthesizing anything.

---

**6. `opsz` is a no-op for 11 of 13 fonts in this system**

Only **Playfair Display** (`5–1200`) and **Fraunces** (`9–144`) have a live `opsz` axis.
For all other fonts, `font-optical-sizing: auto` does nothing. Do not write code that depends
on optical sizing behavior for Outfit, Orbitron, Satoshi, Syne, or any mono font in the stack.

---

**7. IBM Plex Mono italic requires a separate font request**

IBM Plex Mono has no `ital` axis. Italic is a completely separate font file. To use italic
variants, you must include the italic weight declarations in your Google Fonts request
(the `1,weight` tuples). Declaring `font-style: italic` without loading the italic file
triggers synthetic skew.
