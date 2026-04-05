# Design Tokens — Token Architecture and Component API (2026)

> Tokens are the single source of truth. Components consume tokens. Tokens define the system.

---

## TOKEN LAYERS

Design tokens operate at three levels:

| Layer | Purpose | Example |
|-------|---------|---------|
| Primitive | Raw values (hex, px, numbers) | `--blue-500: #3b82f6` |
| Semantic | Meaning-based aliases | `--color-primary: var(--blue-500)` |
| Component | Component-specific overrides | `--button-bg: var(--color-primary)` |

---

## COMPLETE TOKEN SYSTEM

### Colors
```css
:root {
  /* Primitive colors */
  --blue-50: #eff6ff;
  --blue-100: #dbeafe;
  --blue-200: #bfdbfe;
  --blue-300: #93c5fd;
  --blue-400: #60a5fa;
  --blue-500: #3b82f6;
  --blue-600: #2563eb;
  --blue-700: #1d4ed8;
  --blue-800: #1e40af;
  --blue-900: #1e3a8a;

  --gray-50: #fafafa;
  --gray-100: #f5f5f5;
  --gray-200: #e5e5e5;
  --gray-300: #d4d4d4;
  --gray-400: #a3a3a3;
  --gray-500: #737373;
  --gray-600: #525252;
  --gray-700: #404040;
  --gray-800: #262626;
  --gray-900: #171717;
  --gray-950: #0a0a0a;

  /* Semantic colors */
  --color-primary: var(--blue-600);
  --color-primary-hover: var(--blue-700);
  --color-primary-active: var(--blue-800);
  --color-primary-subtle: var(--blue-50);

  --color-success: #16a34a;
  --color-success-bg: #f0fdf4;
  --color-warning: #d97706;
  --color-warning-bg: #fffbeb;
  --color-error: #dc2626;
  --color-error-bg: #fef2f2;
  --color-info: #2563eb;
  --color-info-bg: #eff6ff;

  /* Surface colors */
  --color-bg: #ffffff;
  --color-bg-secondary: var(--gray-50);
  --color-bg-elevated: #ffffff;
  --color-bg-overlay: rgba(0, 0, 0, 0.5);

  /* Text colors */
  --color-text: var(--gray-900);
  --color-text-secondary: var(--gray-600);
  --color-text-tertiary: var(--gray-500);
  --color-text-inverse: #ffffff;
  --color-text-link: var(--color-primary);

  /* Border colors */
  --color-border: var(--gray-200);
  --color-border-strong: var(--gray-300);
  --color-border-focus: var(--color-primary);
}
```

### Spacing
```css
:root {
  --space-1:  0.25rem;  /* 4px */
  --space-2:  0.5rem;   /* 8px */
  --space-3:  0.75rem;  /* 12px */
  --space-4:  1rem;     /* 16px */
  --space-5:  1.25rem;  /* 20px */
  --space-6:  1.5rem;   /* 24px */
  --space-8:  2rem;     /* 32px */
  --space-10: 2.5rem;   /* 40px */
  --space-12: 3rem;     /* 48px */
  --space-16: 4rem;     /* 64px */
  --space-20: 5rem;     /* 80px */
  --space-24: 6rem;     /* 96px */
}
```

### Typography
```css
:root {
  /* Font families */
  --font-sans: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  --font-mono: 'JetBrains Mono', 'Fira Code', 'Cascadia Code', monospace;

  /* Font sizes */
  --text-xs:   0.75rem;   /* 12px */
  --text-sm:   0.875rem;  /* 14px */
  --text-base: 1rem;      /* 16px */
  --text-lg:   1.125rem;  /* 18px */
  --text-xl:   1.25rem;   /* 20px */
  --text-2xl:  1.5rem;    /* 24px */
  --text-3xl:  1.875rem;  /* 30px */
  --text-4xl:  2.25rem;   /* 36px */
  --text-5xl:  3rem;      /* 48px */

  /* Line heights */
  --leading-tight: 1.25;
  --leading-snug: 1.375;
  --leading-normal: 1.5;
  --leading-relaxed: 1.625;
  --leading-loose: 2;

  /* Font weights */
  --font-normal: 400;
  --font-medium: 500;
  --font-semibold: 600;
  --font-bold: 700;
}
```

### Borders and Radii
```css
:root {
  --radius-sm: 0.25rem;   /* 4px */
  --radius-md: 0.375rem;  /* 6px */
  --radius-lg: 0.5rem;    /* 8px */
  --radius-xl: 0.75rem;   /* 12px */
  --radius-2xl: 1rem;     /* 16px */
  --radius-full: 9999px;

  --border-width: 1px;
  --border-width-strong: 2px;
}
```

### Shadows
```css
:root {
  --shadow-xs: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.1), 0 1px 2px rgba(0, 0, 0, 0.06);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.07), 0 2px 4px rgba(0, 0, 0, 0.06);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1), 0 4px 6px rgba(0, 0, 0, 0.05);
  --shadow-xl: 0 20px 25px rgba(0, 0, 0, 0.1), 0 8px 10px rgba(0, 0, 0, 0.05);
  --shadow-2xl: 0 25px 50px rgba(0, 0, 0, 0.25);
  --shadow-inner: inset 0 2px 4px rgba(0, 0, 0, 0.06);
}
```

### Z-Index Scale
```css
:root {
  --z-base:     0;
  --z-dropdown: 100;
  --z-sticky:   200;
  --z-overlay:  300;
  --z-modal:    400;
  --z-popover:  500;
  --z-tooltip:  600;
  --z-toast:    700;
}
```

### Transitions
```css
:root {
  --duration-fast: 150ms;
  --duration-normal: 200ms;
  --duration-slow: 300ms;
  --duration-slower: 500ms;

  --ease-snappy: cubic-bezier(0.2, 0, 0, 1);
  --ease-smooth: cubic-bezier(0.25, 0.1, 0.25, 1);
  --ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1);
}
```

---

## DARK MODE TOKEN OVERRIDES

```css
@media (prefers-color-scheme: dark) {
  :root {
    --color-bg: var(--gray-950);
    --color-bg-secondary: var(--gray-900);
    --color-bg-elevated: var(--gray-800);

    --color-text: var(--gray-50);
    --color-text-secondary: var(--gray-400);
    --color-text-tertiary: var(--gray-500);

    --color-border: var(--gray-800);
    --color-border-strong: var(--gray-700);

    --color-primary: var(--blue-400);
    --color-primary-hover: var(--blue-300);
    --color-primary-subtle: rgba(59, 130, 246, 0.15);

    --shadow-xs: 0 1px 2px rgba(0, 0, 0, 0.3);
    --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.4), 0 1px 2px rgba(0, 0, 0, 0.3);
    --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.4), 0 2px 4px rgba(0, 0, 0, 0.3);
    --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.5), 0 4px 6px rgba(0, 0, 0, 0.3);
  }
}
```

---

## COMPONENT API DESIGN

### Standard Component Props
```
size:       "xs" | "sm" | "md" | "lg" | "xl"
variant:    "primary" | "secondary" | "ghost" | "danger"
state:      "default" | "hover" | "focus" | "active" | "disabled" | "loading"
intent:     "neutral" | "info" | "success" | "warning" | "error"
```

### Button Component Token Usage
```css
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--space-2);
  padding: var(--space-2) var(--space-4);
  font-size: var(--text-sm);
  font-weight: var(--font-medium);
  line-height: var(--leading-normal);
  border-radius: var(--radius-md);
  border: var(--border-width) solid transparent;
  cursor: pointer;
  transition:
    background-color var(--duration-fast) var(--ease-snappy),
    color var(--duration-fast) var(--ease-snappy),
    border-color var(--duration-fast) var(--ease-snappy),
    box-shadow var(--duration-fast) var(--ease-snappy);
}

.btn-primary {
  background-color: var(--color-primary);
  color: var(--color-text-inverse);
  border-color: var(--color-primary);
}

.btn-primary:hover {
  background-color: var(--color-primary-hover);
  border-color: var(--color-primary-hover);
}

.btn-primary:focus-visible {
  outline: 2px solid var(--color-border-focus);
  outline-offset: 2px;
}

.btn-primary:active {
  background-color: var(--color-primary-active);
}

.btn-primary:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  pointer-events: none;
}

/* Sizes */
.btn-sm  { padding: var(--space-1) var(--space-3); font-size: var(--text-xs); }
.btn-md  { padding: var(--space-2) var(--space-4); font-size: var(--text-sm); }
.btn-lg  { padding: var(--space-3) var(--space-6); font-size: var(--text-base); }
.btn-xl  { padding: var(--space-4) var(--space-8); font-size: var(--text-lg); }
```

---

## TOKEN NAMING CONVENTIONS

### Do
```css
--color-primary         /* Semantic — describes purpose */
--color-bg              /* Semantic — describes function */
--blue-500              /* Primitive — describes value */
--space-4               /* Scale — describes size */
--text-base             /* Scale — describes size */
```

### Don't
```css
--blue                  /* Too vague */
--main-color            /* Meaningless */
--big-padding           /* Subjective */
--color-text-dark       /* Assumes light mode only */
--sidebar-width         /* Too specific — breaks reusability */
```

---

## TOKEN VALIDATION CHECKLIST

- [ ] All colors have light and dark mode values
- [ ] All semantic colors reference primitive colors
- [ ] Spacing follows a consistent scale (4px or 8px base)
- [ ] Typography scale uses a mathematical ratio
- [ ] Z-index values use a scale of 100 (no arbitrary numbers)
- [ ] Shadow values work on both light and dark backgrounds
- [ ] No hardcoded values in components — all use tokens
- [ ] Dark mode overrides cover all surface, text, and border tokens
