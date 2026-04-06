# Responsive Engineering — Breakpoints, Fluid Layouts, Container Queries (2026)

> Responsive is not about making things fit. It is about making things work at every size.

---

## BREAKPOINT SYSTEM

### Standard Breakpoints

| Name | Width | Target |
|------|-------|--------|
| xs | 320px | Small phones (portrait) |
| sm | 640px | Large phones (landscape) |
| md | 768px | Tablets (portrait) |
| lg | 1024px | Tablets (landscape), small laptops |
| xl | 1280px | Desktops, laptops |
| 2xl | 1536px | Wide monitors, ultrawide |

### Mobile-First Media Query Pattern
```css
/* Base styles = mobile (320px+) */
.container {
  padding: 1rem;
}

/* Progressive enhancement */
@media (min-width: 640px) { }  /* sm */
@media (min-width: 768px) { }  /* md */
@media (min-width: 1024px) { } /* lg */
@media (min-width: 1280px) { } /* xl */
@media (min-width: 1536px) { } /* 2xl */
```

---

## FLUID LAYOUTS

### Fluid Container with clamp()
```css
.container {
  width: min(100% - 2rem, 1280px);
  margin-inline: auto;
}

/* Or with clamp for gradual scaling */
.container {
  width: clamp(320px, 90vw, 1280px);
  margin-inline: auto;
}
```

### Fluid Spacing
```css
:root {
  --space-xs:  clamp(0.25rem, 0.2rem + 0.25vw, 0.5rem);
  --space-sm:  clamp(0.5rem, 0.4rem + 0.5vw, 0.75rem);
  --space-md:  clamp(0.75rem, 0.6rem + 0.75vw, 1rem);
  --space-lg:  clamp(1rem, 0.8rem + 1vw, 1.5rem);
  --space-xl:  clamp(1.5rem, 1rem + 2vw, 2.5rem);
  --space-2xl: clamp(2rem, 1.5rem + 3vw, 4rem);
  --space-3xl: clamp(3rem, 2rem + 5vw, 6rem);
}
```

### Fluid Grid
```css
.grid {
  display: grid;
  gap: var(--space-lg);
  grid-template-columns: repeat(
    auto-fit,
    minmax(min(100%, 280px), 1fr)
  );
}
```

---

## CONTAINER QUERIES (2026 STANDARD)

Container queries are the modern standard. Use them for component-level responsiveness.

### Basic Container Query
```css
.card-container {
  container-type: inline-size;
  container-name: card;
}

.card {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

@container card (min-width: 400px) {
  .card {
    flex-direction: row;
    align-items: center;
  }
}
```

### Container Query Units
```css
/* cq* units are relative to the container, not the viewport */
.card-title {
  font-size: clamp(1rem, 4cqi, 1.5rem);
}

.card-image {
  width: 100%;
  aspect-ratio: 16 / 9;
}
```

### When to Use Container Queries vs Media Queries
| Use | For |
|-----|-----|
| Container Queries | Component layout changes based on available space |
| Media Queries | Page-level layout, navigation patterns, global spacing |

---

## RESPONSIVE PATTERNS

### 1. Sidebar Layout
```css
.layout {
  display: grid;
  grid-template-columns: 1fr;
}

@media (min-width: 1024px) {
  .layout {
    grid-template-columns: 280px 1fr;
  }
}
```

### 2. Hero Section
```css
.hero {
  display: grid;
  gap: 2rem;
  padding: 2rem 1rem;
}

@media (min-width: 768px) {
  .hero {
    grid-template-columns: 1fr 1fr;
    align-items: center;
    padding: 4rem 2rem;
  }
}

@media (min-width: 1280px) {
  .hero {
    padding: 6rem 4rem;
    gap: 4rem;
  }
}
```

### 3. Navigation
```css
/* Mobile: hamburger menu */
.nav-links {
  display: none;
  position: fixed;
  inset: 0;
  flex-direction: column;
  padding: 2rem;
  background: var(--bg-primary);
}

.nav-links.open {
  display: flex;
}

/* Desktop: horizontal nav */
@media (min-width: 768px) {
  .nav-links {
    display: flex;
    position: static;
    flex-direction: row;
    padding: 0;
    background: none;
  }
}
```

### 4. Data Table
```css
/* Mobile: card view */
.table-row {
  display: grid;
  gap: 0.5rem;
  padding: 1rem;
  border-bottom: 1px solid var(--gray-200);
}

/* Desktop: traditional table */
@media (min-width: 768px) {
  .table-row {
    display: table-row;
  }
}
```

---

## 200% ZOOM COMPLIANCE

WCAG requires content to be fully usable at 200% zoom.

### Rules
1. No horizontal scrolling at 200% zoom (except data tables, code blocks)
2. All content remains readable and accessible
3. Navigation remains functional
4. Touch targets remain minimum 44x44px
5. Text does not overlap or get cut off

### Testing
```
1. Open browser DevTools
2. Set zoom to 200% (Ctrl/Cmd + +)
3. Navigate entire page
4. Verify: no horizontal scroll, no overlap, all interactive elements work
```

---

## RESPONSIVE IMAGES

### srcset Pattern
```html
<img
  src="/hero-800.webp"
  srcset="/hero-400.webp 400w,
          /hero-800.webp 800w,
          /hero-1200.webp 1200w,
          /hero-1600.webp 1600w"
  sizes="(max-width: 640px) 100vw,
         (max-width: 1024px) 80vw,
         1200px"
  alt="Hero description"
  loading="eager"
  fetchpriority="high"
>
```

### Art Direction with <picture>
```html
<picture>
  <source media="(max-width: 768px)" srcset="/hero-mobile.webp">
  <source media="(max-width: 1280px)" srcset="/hero-tablet.webp">
  <img src="/hero-desktop.webp" alt="Hero description" loading="lazy">
</picture>
```

---

## TOUCH VS POINTER

### Detect Input Type
```css
/* Touch devices */
@media (hover: none) and (pointer: coarse) {
  .hover-effect { display: none; }
  .touch-target { min-height: 44px; min-width: 44px; }
}

/* Pointer devices (mouse, trackpad) */
@media (hover: hover) and (pointer: fine) {
  .hover-effect { opacity: 0; transition: opacity 150ms; }
  .card:hover .hover-effect { opacity: 1; }
}
```

### Rules
- Every hover interaction must have a tap-based equivalent
- Hover-only features are inaccessible on touch devices
- Use `@media (hover: none)` to detect touch-only devices
- Minimum 44x44px touch targets on all touch devices

---

## PRINT STYLES

```css
@media print {
  /* Hide non-essential elements */
  nav, footer, .no-print { display: none; }

  /* Ensure readable text */
  body { font-size: 12pt; color: #000; background: #fff; }

  /* Show URLs for links */
  a::after { content: " (" attr(href) ")"; }

  /* Prevent page breaks inside elements */
  .card, .section { break-inside: avoid; }
}
```
