# Color Systems — Palettes, Contrast, Dark Mode (2026)

> Color is not decoration. Color is information architecture.

---

## PALETTE ARCHITECTURE

Every project needs exactly these color groups:

### 1. Primary Color
- The brand color. Used for primary actions, active states, key links.
- Must pass 4.5:1 contrast on white (light mode) and on dark bg (dark mode).
- One color. Not three. One.

### 2. Accent Color
- Used sparingly for highlights, badges, decorative elements.
- Should contrast with the primary color.
- Not required to pass contrast ratios (used decoratively, not for text).

### 3. Neutral Grays (Minimum 9 Steps)
```
--gray-50:   #fafafa   (backgrounds, subtle fills)
--gray-100:  #f5f5f5   (card backgrounds, dividers)
--gray-200:  #e5e5e5   (borders, disabled states)
--gray-300:  #d4d4d4   (placeholder text, inactive icons)
--gray-400:  #a3a3a3   (secondary labels, helper text)
--gray-500:  #737373   (tertiary text)
--gray-600:  #525252   (body text on light bg)
--gray-700:  #404040   (headings on light bg)
--gray-800:  #262626   (primary text on light bg)
--gray-900:  #171717   (near-black, headings)
--gray-950:  #0a0a0a   (pure dark bg)
```

### 4. Semantic Colors
```
--success:  green  (confirmations, completed states)
--warning:  amber  (cautions, pending states)
--error:    red    (failures, required field errors)
--info:     blue   (informational messages, links)
```

Each semantic color needs 3 variants:
- Base (text/icons)
- Light (background fills)
- Dark (hover/active states)

---

## CONTRAST REQUIREMENTS (WCAG 2.1 AA)

| Content Type | Minimum Ratio | Example |
|-------------|--------------|---------|
| Normal text (< 18px or < 14px bold) | 4.5:1 | Body copy, labels, links |
| Large text (≥ 18px or ≥ 14px bold) | 3:1 | Headings, large buttons |
| UI components | 3:1 | Button borders, input borders, icons |
| Focus indicators | 3:1 | Focus ring against any background |

### Quick Contrast Formula
```
Contrast Ratio = (L1 + 0.05) / (L2 + 0.05)
Where L1 is the lighter color's relative luminance
and L2 is the darker color's relative luminance
```

### Relative Luminance Calculation
```
For each RGB channel (R, G, B):
  sRGB = channel / 255
  if sRGB <= 0.03928: linear = sRGB / 12.92
  else: linear = ((sRGB + 0.055) / 1.055) ^ 2.4

L = 0.2126 * R_linear + 0.7152 * G_linear + 0.0722 * B_linear
```

---

## DARK MODE CONSTRUCTION

### Rules
1. Never use pure black (#000000) — it causes smearing on OLED and eye strain
2. Use dark gray backgrounds (#0a0a0a to #1a1a1a)
3. Darken semantic colors for dark mode (saturated colors vibrate on dark)
4. Lighten text colors (white text on dark bg needs less contrast than dark on light)
5. Reduce shadow usage — shadows are invisible on dark backgrounds
6. Use borders and subtle color shifts instead of shadows for depth

### Dark Mode Token Mapping
```css
/* Light mode */
--bg-primary: #ffffff;
--bg-secondary: #f5f5f5;
--text-primary: #171717;
--text-secondary: #525252;
--border: #e5e5e5;

/* Dark mode */
--bg-primary: #0a0a0a;
--bg-secondary: #171717;
--text-primary: #fafafa;
--text-secondary: #a3a3a3;
--border: #262626;
```

### Dark Mode Semantic Colors
```css
/* Darken saturated colors for dark backgrounds */
--error-light:    #fef2f2;    --error-dark:    #991b1b;
--warning-light:  #fffbeb;    --warning-dark:  #92400e;
--success-light:  #f0fdf4;    --success-dark:  #166534;
--info-light:     #eff6ff;    --info-dark:     #1e40af;
```

---

## COLOR BLINDNESS CONSIDERATIONS

8% of men and 0.5% of women have color vision deficiency.

### Rules
- Never use color alone to convey information
- Red/green color blindness is most common — error/success states must have icons or text
- Use patterns, icons, or text labels alongside color
- Test palettes with color blindness simulators

### Safe Semantic Indicators
```
Error:   Red color + ✕ icon + "Error" text label
Success: Green color + ✓ icon + "Success" text label
Warning: Amber color + ⚠ icon + "Warning" text label
Info:    Blue color + ℹ icon + "Info" text label
```

---

## IMPLEMENTATION

### CSS Custom Properties (Design Tokens)
```css
:root {
  /* Primary */
  --color-primary: #2563eb;
  --color-primary-hover: #1d4ed8;
  --color-primary-active: #1e40af;

  /* Accent */
  --color-accent: #8b5cf6;

  /* Neutrals */
  --color-gray-50: #fafafa;
  --color-gray-100: #f5f5f5;
  --color-gray-200: #e5e5e5;
  --color-gray-300: #d4d4d4;
  --color-gray-400: #a3a3a3;
  --color-gray-500: #737373;
  --color-gray-600: #525252;
  --color-gray-700: #404040;
  --color-gray-800: #262626;
  --color-gray-900: #171717;

  /* Semantic */
  --color-success: #16a34a;
  --color-warning: #d97706;
  --color-error: #dc2626;
  --color-info: #2563eb;
}

/* Dark mode */
@media (prefers-color-scheme: dark) {
  :root {
    --color-primary: #3b82f6;
    --color-primary-hover: #60a5fa;
    --color-gray-50: #0a0a0a;
    --color-gray-100: #171717;
    --color-gray-200: #262626;
    --color-gray-800: #e5e5e5;
    --color-gray-900: #fafafa;
  }
}
```

### Tailwind Configuration
```js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: {
          DEFAULT: '#2563eb',
          hover: '#1d4ed8',
          active: '#1e40af',
        },
        gray: {
          50: '#fafafa',
          100: '#f5f5f5',
          // ... full scale
        }
      }
    }
  }
}
```

---

## REFERENCE PALETTES (2026 BENCHMARKS)

| Brand | Primary | Style |
|-------|---------|-------|
| Linear | #5e6ad2 | Deep indigo, minimal, dark-first |
| Stripe | #635bff | Vibrant purple-blue, gradient-heavy |
| Apple | #0071e3 | Clean blue, restrained, premium |
| Vercel | #000000 | Monochrome, white text, stark |
| Spotify | #1db954 | Vibrant green, dark background |
| Notion | #2383e2 | Calm blue, neutral-dominant |
| Figma | #a259ff | Purple accent, light and airy |
