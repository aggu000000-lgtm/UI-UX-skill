# CSS 2026 Features Reference

> **2026 STANDARDS**: These CSS features are either shipped or in late-stage development. Reference this guide when building modern, performant interfaces.

---

## SHIPPED FEATURES (Widely Supported)

### Container Queries (Level 4+)

```css
/* Establish container */
.card-wrapper {
  container-type: inline-size;
  container-name: card;
}

/* Query container size */
@container card (min-width: 400px) {
  .card {
    flex-direction: row;
  }
}

/* Container query units */
.card-title {
  font-size: clamp(1rem, 4cqi, 2rem);
  /* cqi = 1% of container inline size */
}

/* Style queries (Chrome 111+) */
@container style(--variant: dark) {
  .card {
    background: #1a1a2e;
    color: #eaeaea;
  }
}
```

### CSS Grid Level 3 (Grid Lanes)

```css
/* Masonry-style layout */
.gallery {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  grid-template-rows: masonry;
  gap: 16px;
}

/* Grid Lanes - 1D grid layout */
.grid-lanes {
  display: grid;
  grid-template-rows: repeat(3, auto);
  grid-auto-flow: column;
  gap: 1rem;
}
```

### View Transitions (Level 2)

```css
/* Enable cross-page view transitions */
@view-transition {
  navigation: auto;
}

::view-transition-old(root),
::view-transition-new(root) {
  animation-duration: 300ms;
  animation-timing-function: ease-out;
}

/* Named view transitions */
::view-transition-old(hero),
::view-transition-new(hero) {
  animation-duration: 500ms;
}
```

### Scroll-Driven Animations

```css
/* Element scrolls into view triggers animation */
@keyframes fade-in {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}

.animated-element {
  animation: fade-in linear;
  animation-timeline: view();
  animation-range: entry 0% cover 40%;
}

/* Custom scroll timeline */
.progress-bar {
  animation: grow linear;
  animation-timeline: scroll(root block);
}
```

### Gap Decorations

```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  
  /* Column decorations */
  column-rule: 1px solid #e5e7eb;
  column-rule-style: dashed;
  
  /* Row decorations */
  row-rule: 1px solid #f3f4f6;
  row-rule-style: solid;
}

.card-grid {
  /* Break behavior at intersections */
  column-rule-break: avoid;
  row-rule-break: avoid;
}
```

### Reading Flow Control

```css
/* Control keyboard/reader navigation order */
.flex-nav {
  display: flex;
  flex-direction: row-reverse;
  reading-flow: flex-visual;
}

.grid-layout {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  reading-flow: grid-rows;
}
```

### Relative Color Syntax

```css
/* Create color variants dynamically */
.button-primary {
  --base-blue: #3b82f6;
  background: oklch(from var(--base-blue) calc(l + 0.1) c h);
}

.button-ghost {
  --base-blue: #3b82f6;
  background: color-mix(in oklch, var(--base-blue) 20%, transparent);
}

/* Adjust opacity with oklab */
.card-overlay {
  background: oklab(from var(--card-bg) / 0.5);
}
```

### @scope (Cascading Level 6)

```css
/* Proximity-based styling with bounds */
@scope (.card) to (.card-content) {
  :scope {
    border: 1px solid #ddd;
    border-radius: 12px;
  }
  
  :scope(.card-title) {
    font-weight: 600;
  }
}

/* Without lower bound - only upper */
@scope (.modal) {
  .modal-header { /* styled */ }
  .modal-body { /* styled */ }
}
```

---

## EDITOR'S DRAFT / COMING SOON

### CSS Mixins (Native)

```css
/* Define mixin */
@mixin --center {
  display: flex;
  align-items: center;
  justify-content: center;
}

@mixin --card-base {
  padding: 1rem;
  border-radius: 0.5rem;
  background: white;
}

/* Apply mixin */
.card {
  @apply --center;
  @apply --card-base;
}
```

### Layout State Container Queries

```css
/* Query column position */
.masonry-item {
  container-type: layout-state;
}

@container layout-state(column-position: last) {
  .item-footer {
    border-top: 1px solid currentColor;
  }
}
```

### Conditional Rules (CSS Conditional 5)

```css
/* Has selector */
@when selector(.dark-mode) {
  :root {
    color-scheme: dark;
  }
}

/* Supports container queries */
@supports container-type: layout-state {
  /* Progressive enhancement */
}
```

---

## MODERN CSS PATTERNS FOR 2026

### Fluid Typography with clamp()

```css
h1 {
  font-size: clamp(
    2rem,    /* Minimum */
    5vw + 1rem,  /* Preferred (fluid) */
    4rem     /* Maximum */
  );
}

body {
  font-size: clamp(
    1rem,
    0.5vw + 0.875rem,
    1.125rem
  );
}
```

### Container Query Responsive Images

```css
.responsive-image {
  container-type: inline-size;
}

.responsive-image img {
  width: 100%;
  height: auto;
}

@container (max-width: 400px) {
  .responsive-image img {
    aspect-ratio: 1;
    object-fit: cover;
  }
}
```

### Modern Color System (oklch)

```css
:root {
  /* OKLCH - perceptually uniform */
  --color-primary: oklch(55% 0.2 250);
  --color-secondary: oklch(65% 0.15 180);
  --color-accent: oklch(75% 0.25 45);
  
  /* Easy lightness adjustments */
  --color-primary-hover: oklch(from var(--color-primary) calc(l * 1.1) c h);
  --color-primary-active: oklch(from var(--color-primary) calc(l * 0.9) c h);
}
```

### CSS Nesting (Native)

```css
.card {
  padding: 1rem;
  
  & .card-header {
    margin-bottom: 1rem;
    
    &:hover {
      background: #f5f5f5;
    }
  }
  
  @media (min-width: 768px) {
    padding: 2rem;
  }
}
```

### Logical Properties

```css
.card {
  /* Instead of margin-top, margin-right, etc. */
  margin-block: 1rem;
  margin-inline: auto;
  padding-block: 1.5rem;
  padding-inline: 1rem;
  
  /* Logical borders */
  border-block-end: 1px solid #ddd;
  border-inline-start: 4px solid blue;
}
```

---

## PERFORMANCE OPTIMIZATIONS

### Content Visibility

```css
.off-screen-content {
  content-visibility: auto;
  contain-intrinsic-size: 0 500px; /* Estimate height */
}

/* For below-fold sections */
.article-section {
  content-visibility: auto;
  contain-intrinsic-size: auto 400px;
}
```

### Will Change Optimization

```css
/* Hint browser for upcoming animations */
.sliding-element {
  will-change: transform;
  transform: translateX(0);
  transition: transform 300ms ease-out;
}

.sliding-element:hover {
  transform: translateX(10px);
}
```

### CSS Containment

```css
.widget {
  contain: layout style paint;
  /* layout: contained in layout */
  /* style: contained in style calculations */
  /* paint: cannot overflow */
}

.chat-message {
  contain: content;
  /* Shorthand for layout + paint */
}
```

---

## CSS SNAPSHOT 2026 REFERENCE

### Fully Supported (Recommendation)
- CSS2
- CSS Custom Properties
- CSS Color Level 4
- CSS Fonts Level 3
- CSS Box Model Level 3
- CSS Flexbox Level 1
- CSS Grid Level 1 & 2
- CSS Multi-column Level 1
- CSS Transitions
- CSS Animations Level 1
- CSS Transforms Level 1 & 2

### Stable Implementation
- Container Queries Level 4
- View Transitions Level 1
- CSS Scroll Snap Level 1
- CSS Scrollbars Level 1
- Media Queries Level 4
- CSS Shapes Level 1
- CSS Logical Properties Level 1

### In Development
- CSS Mixins
- Layout State Container Queries
- CSS Grid Level 3
- CSS Gap Decorations
- Scroll-Driven Animations
- Reading Flow Control

---

## BROWSER SUPPORT (2026)

| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| Container Queries | 105+ | 110+ | 16+ | 105+ |
| CSS Nesting | 120+ | 117+ | 17.2+ | 120+ |
| View Transitions | 126+ | — | 18.2+ | 126+ |
| Scroll Animations | 115+ | — | 17.2+ | 115+ |
| OKLCH Colors | 111+ | 113+ | 16.4+ | 111+ |
| @scope | 118+ | — | 17.4+ | 118+ |
| has() Selector | 117+ | 121+ | 15.5+ | 117+ |

---

**Last Updated**: 2026-04-12
