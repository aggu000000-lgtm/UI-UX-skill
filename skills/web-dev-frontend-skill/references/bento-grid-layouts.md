# Bento Grid Layouts Reference

> **2026 STANDARDS**: Bento grids have become the dominant layout pattern. They use asymmetric, card-based arrangements to create natural visual hierarchy. This guide covers implementation patterns and CSS techniques.

---

## WHAT IS A BENTO GRID?

Named after Japanese lunch boxes (bento), these layouts arrange content in asymmetric cards of varying sizes within a grid. Key characteristics:

- **Asymmetric balance**: Different card sizes create visual interest
- **Natural hierarchy**: Larger cards draw attention first
- **Breathing room**: Generous gaps between cards
- **Responsive flexibility**: Cards reflow based on container size

---

## CSS IMPLEMENTATION

### Basic Bento Grid (CSS Grid)

```css
.bento-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: 200px;
  gap: 1.5rem;
  padding: 1.5rem;
}

/* Card sizes */
.bento-item--small { grid-column: span 1; grid-row: span 1; }
.bento-item--medium { grid-column: span 2; grid-row: span 1; }
.bento-item--large { grid-column: span 2; grid-row: span 2; }
.bento-item--wide { grid-column: span 4; grid-row: span 1; }
.bento-item--tall { grid-column: span 1; grid-row: span 2; }
```

### Subgrid Integration (2026)

```css
.parent-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(3, minmax(150px, auto));
  gap: 1.5rem;
}

.card {
  /* Inherit parent grid tracks */
  grid-column: span 2;
  display: grid;
  grid-row: span 2;
  grid-template-rows: subgrid;
}

.card-header { /* aligns with parent row 1 */ }
.card-content { /* aligns with parent row 2 */ }
.card-footer { /* aligns with parent row 3 */ }
```

### CSS Grid Level 3 Masonry (Emerging)

```css
.bento-masonry {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: masonry;
  gap: 1.5rem;
  align-tracks: start; /* or end, center, stretch */
}
```

### Flexbox Fallback

```css
.bento-flex {
  display: flex;
  flex-wrap: wrap;
  gap: 1.5rem;
  padding: 1.5rem;
}

.bento-card {
  flex: 1 1 calc(25% - 1.5rem); /* 4 columns base */
  min-width: 250px;
}

.bento-card--featured {
  flex: 2 1 calc(50% - 1.5rem); /* 2x width */
}

.bento-card--wide {
  flex: 4 1 100%; /* Full width */
}
```

---

## RESPONSIVE BENTO PATTERNS

### Mobile-First Reflow

```css
.bento-grid {
  /* Mobile: single column */
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.bento-card {
  min-height: 200px;
}

@media (min-width: 640px) {
  .bento-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    grid-auto-rows: 180px;
    gap: 1rem;
  }
}

@media (min-width: 1024px) {
  .bento-grid {
    grid-template-columns: repeat(4, 1fr);
    grid-auto-rows: 200px;
    gap: 1.5rem;
  }
  
  .bento-card--hero {
    grid-column: span 2;
    grid-row: span 2;
  }
}
```

### Container Query Responsive

```css
.dashboard-grid {
  container-type: inline-size;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
}

@container (min-width: 600px) {
  .dashboard-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

@container (min-width: 900px) {
  .dashboard-grid {
    grid-template-columns: repeat(4, 1fr);
  }
  
  .metric-card--featured {
    grid-column: span 2;
    grid-row: span 2;
  }
}
```

---

## ANIMATED BENTO CARDS

### Hover Lift Effect

```css
.bento-card {
  transition: transform 300ms ease-out, box-shadow 300ms ease-out;
  transform-origin: center bottom;
}

.bento-card:hover {
  transform: translateY(-8px) scale(1.02);
  box-shadow: 
    0 20px 40px -12px rgba(0, 0, 0, 0.15),
    0 0 0 1px rgba(0, 0, 0, 0.05);
}
```

### Staggered Entry Animation

```css
@keyframes bento-enter {
  from {
    opacity: 0;
    transform: translateY(30px) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.bento-card {
  animation: bento-enter 600ms ease-out backwards;
}

.bento-card:nth-child(1) { animation-delay: 0ms; }
.bento-card:nth-child(2) { animation-delay: 100ms; }
.bento-card:nth-child(3) { animation-delay: 200ms; }
.bento-card:nth-child(4) { animation-delay: 300ms; }

/* Or use animation-timeline: view() for scroll-triggered */
```

### Content Reveal on Hover

```css
.bento-card {
  overflow: hidden;
  position: relative;
}

.bento-card-content {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 1.5rem;
  background: linear-gradient(to top, rgba(0,0,0,0.8), transparent);
  transform: translateY(100%);
  transition: transform 400ms ease-out;
}

.bento-card:hover .bento-card-content {
  transform: translateY(0);
}
```

---

## CONTENT TYPES FOR BENTO

### Dashboard/Analytics Layout

```
+------------------+--------+--------+
|    Revenue       | Users  | Orders |
|    (2x2)        |  (1x1) |  (1x1) |
|                  +--------+--------+
|                  |  Avg   |  Conv  |
|                  |  Order |  Rate  |
+------------------+--------+--------+
|     Recent Orders Table (4x1)      |
+------------------------------------+
```

### Landing Page Layout

```
+----------+-------------------------+
|  Hero    |                         |
|  Text    |      Hero Image         |
|  (1x2)   |        (2x2)           |
+----------+                         |
|  Stats   |                         |
+----------+-------------------------+
|  Feature 1  |  Feature 2  |  F3    |
|    (1x1)     |    (1x1)    |  (1x1)|
+-----------------------------------+
```

### Portfolio/Showcase Layout

```
+-----+--------+---------------------+
|     |        |                     |
|  P  |   P    |                     |
|  1  |   2    |        P 3         |
|     |        |       (2x2)         |
+-----+--------+                     |
|     |        |                     |
|  P4 |   P5   |                     |
|     |        |                     |
+-----+--------+---------------------+
```

---

## DESIGN TOKENS FOR BENTO

```css
:root {
  /* Spacing */
  --bento-gap-sm: 0.75rem;
  --bento-gap-md: 1rem;
  --bento-gap-lg: 1.5rem;
  
  /* Border radius */
  --bento-radius-sm: 0.75rem;
  --bento-radius-md: 1rem;
  --bento-radius-lg: 1.5rem;
  
  /* Shadows */
  --bento-shadow-sm: 0 1px 3px rgba(0,0,0,0.08);
  --bento-shadow-md: 0 4px 12px rgba(0,0,0,0.1);
  --bento-shadow-lg: 0 12px 32px rgba(0,0,0,0.12);
  
  /* Card sizes */
  --bento-col: 1fr;
  --bento-row-height: 200px;
}
```

---

## ACCESSIBILITY

### Keyboard Navigation

```css
.bento-card:focus-visible {
  outline: 2px solid var(--focus-color, #3b82f6);
  outline-offset: 2px;
  z-index: 10;
}

/* Ensure logical tab order */
.bento-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
}
```

### Reduced Motion

```css
@media (prefers-reduced-motion: reduce) {
  .bento-card {
    animation: none;
    transition: none;
  }
  
  .bento-card:hover {
    transform: none;
    box-shadow: var(--bento-shadow-md);
  }
}
```

### Screen Reader Content

```html
<section aria-label="Dashboard metrics" class="bento-grid">
  <article class="bento-card" aria-labelledby="revenue-title">
    <h3 id="revenue-title">Monthly Revenue</h3>
    <p>$124,500</p>
  </article>
  <!-- More cards -->
</section>
```

---

## TOOLS & RESOURCES

- **Layout Patterns (Google)**: https://web.dev/patterns/layout/
- **CSS Grid Generator**: https://cssgrid-generator.netlify.app/
- **Bento UI Examples**: https://bento-mvp.vercel.app/

---

**Last Updated**: 2026-04-12
