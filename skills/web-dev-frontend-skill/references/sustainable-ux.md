# Sustainable UX Reference

> **2026 STANDARDS**: Sustainable UX is about creating digital products that minimize environmental impact while maintaining quality. Performance optimization directly correlates with energy efficiency. This guide covers patterns for building greener digital experiences.

---

## CORE PRINCIPLES

### The Performance-Environment Connection

1. **Less data = less energy**: Smaller files = less bandwidth = less carbon
2. **Less processing = less power**: Efficient code = less CPU usage = less energy
3. **Less rendering = longer battery**: Optimized UI = less GPU usage = longer device life
4. **Longer device life = less e-waste**: Well-optimized apps work on older devices

### Carbon Awareness

| Action | CO2 Impact (approx.) |
|--------|---------------------|
| 1MB page weight | ~0.04g CO2 |
| Heavy JavaScript (500KB+) | ~0.2g CO2 per page load |
| Auto-playing video | ~0.5g CO2 per page load |
| Per user per day (optimized site) | ~0.1g CO2 |
| Per user per day (heavy site) | ~1g CO2 |

---

## PERFORMANCE OPTIMIZATION

### Image Optimization

```css
/* Modern image formats */
.hero-image {
  /* AVIF first, WebP fallback, PNG/JPG last */
  background-image: 
    url('hero.avif'),
    url('hero.webp'),
    url('hero.jpg');
}

/* Responsive images with art direction */
<picture>
  <source 
    media="(min-width: 1024px)" 
    srcset="hero-1920.avif 1920w, hero-1280.avif 1280w"
    type="image/avif"
  >
  <source 
    media="(min-width: 1024px)" 
    srcset="hero-1920.webp 1920w, hero-1280.webp 1280w"
    type="image/webp"
  >
  <img 
    src="hero-1280.jpg" 
    srcset="hero-1920.jpg 1920w, hero-1280.jpg 1280w, hero-640.jpg 640w"
    sizes="100vw"
    loading="lazy"
    decoding="async"
    alt="Hero image"
  >
</picture>
```

### Lazy Loading Strategy

```html
<!-- Native lazy loading -->
<img src="image.jpg" loading="lazy" alt="...">

<!-- For critical above-fold images, use eager -->
<img src="hero.jpg" loading="eager" fetchpriority="high" alt="...">

<!-- Video lazy loading -->
<video preload="none" poster="poster.jpg">
  <source data-src="video.mp4" type="video/mp4">
</video>
```

### Font Optimization

```css
/* Variable fonts - single file for all weights */
@font-face {
  font-family: 'InterVariable';
  src: url('InterVariable.woff2') format('woff2-variations');
  font-weight: 100 900;
  font-display: swap;
}

/* Subset fonts to used characters */
@font-face {
  font-family: 'HeadingFont';
  src: url('heading-subset.woff2') format('woff2');
  unicode-range: U+0020-007F; /* ASCII only */
}

/* Critical font loading */
<link rel="preload" href="heading.woff2" as="font" crossorigin>
```

---

## JAVASCRIPT EFFICIENCY

### Code Splitting

```javascript
// Dynamic imports for routes
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));

// Lazy load non-critical components
const HeavyChart = lazy(() => import('./components/HeavyChart'));

// Intersection Observer for deferred loading
const observer = new IntersectionObserver(
  (entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        loadHeavyComponent();
        observer.disconnect();
      }
    });
  },
  { rootMargin: '200px' }
);
```

### Efficient State Management

```javascript
// Bad: Re-rendering entire list on any change
const [state, setState] = useState(largeObject);

// Better: Granular state
const [items, setItems] = useState([]);
const [filter, setFilter] = useState('all');
const [sort, setSort] = useState('date');

// Best: Normalized state with selectors
const selectFilteredItems = createSelector(
  (state) => state.items,
  (state) => state.filter,
  (items, filter) => filterItems(items, filter)
);
```

### Debounce & Throttle

```javascript
// Debounce expensive operations
const debouncedSearch = debounce((query) => {
  performSearch(query);
}, 300);

// Throttle scroll handlers
const throttledScroll = throttle(() => {
  updateScrollPosition();
}, 16); // ~60fps

window.addEventListener('scroll', throttledScroll);
```

---

## CSS EFFICIENCY

### Efficient Selectors

```css
/* Bad: Overly specific */
html body main #container .section .item .title

/* Good: Flat specificity */
.item-title

/* Great: BEM with scope */
.card__title { /* specific */ }
```

### Containment for Performance

```css
/* Isolate repaints */
.widget {
  contain: layout style paint;
}

/* Content visibility for off-screen */
.off-screen {
  content-visibility: auto;
  contain-intrinsic-size: 0 500px;
}

/* Reduce style recalculations */
.isolated-component {
  contain: content;
}
```

### Minimal Animations

```css
/* GPU-accelerated only */
.performed-animation {
  transform: translateX(0);
  will-change: transform;
}

/* Respect reduced motion */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## DARK MODE EFFICIENCY

### OLED-Friendly Dark Mode

```css
/* True black for OLED */
@media (prefers-color-scheme: dark) {
  :root {
    --surface-primary: #000000;
    --surface-secondary: #0a0a0a;
    --text-primary: #e5e5e5;
  }
}

/* Traditional dark mode (uses more energy on OLED) */
@media (prefers-color-scheme: dark) {
  :root {
    --surface-primary: #1a1a2e;
    --surface-secondary: #16213e;
    --text-primary: #eaeaea;
  }
}
```

---

## SKELETON LOADING (ENERGY EFFICIENT)

### CSS Skeleton Patterns

```css
/* Simple skeleton */
.skeleton {
  background: linear-gradient(
    90deg,
    var(--bg-tertiary) 25%,
    var(--bg-secondary) 50%,
    var(--bg-tertiary) 75%
  );
  background-size: 200% 100%;
  animation: skeleton-loading 1.5s ease-in-out infinite;
}

@keyframes skeleton-loading {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}

/* Pulse variant (less animation) */
.skeleton-pulse {
  background: var(--bg-tertiary);
  animation: skeleton-pulse 2s ease-in-out infinite;
}

@keyframes skeleton-pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.5; }
}
```

### HTML Pattern

```html
<div class="card">
  <div class="skeleton skeleton-image"></div>
  <div class="card-content">
    <div class="skeleton skeleton-text"></div>
    <div class="skeleton skeleton-text short"></div>
  </div>
</div>
```

---

## RESOURCE EFFICIENCY CHECKLIST

### Pre-Delivery Audit

```markdown
## Sustainable UX Checklist

### Images
- [ ] Use AVIF/WebP formats
- [ ] Implement responsive images
- [ ] Lazy load below-fold images
- [ ] Set explicit width/height
- [ ] Use srcset for density

### Fonts
- [ ] Use variable fonts (single file)
- [ ] Subset fonts to used characters
- [ ] Preload critical fonts
- [ ] Use font-display: swap

### JavaScript
- [ ] Code split routes
- [ ] Lazy load non-critical JS
- [ ] Remove unused code (tree shaking)
- [ ] Debounce/throttle handlers
- [ ] Use efficient data structures

### CSS
- [ ] Minimize CSS bundle
- [ ] Use CSS containment
- [ ] GPU-accelerated animations only
- [ ] Avoid expensive selectors

### Video
- [ ] No autoplay with sound
- [ ] Lazy load videos
- [ ] Provide poster images
- [ ] Offer reduced quality option

### General
- [ ] Enable compression (Brotli)
- [ ] Use CDN for static assets
- [ ] Cache effectively
- [ ] Test on low-end devices
```

---

## TOOLS & RESOURCES

### Performance Measurement
- **Lighthouse**: https://developers.google.com/web/tools/lighthouse
- **WebPageTest**: https://webpagetest.org/
- **GTmetrix**: https://gtmetrix.com/

### Carbon Calculators
- **Website Carbon Calculator**: https://www.websitecarbon.com/
- **EcoGrader**: https://www.ecograder.com/

### Optimization Tools
- **Squoosh**: https://squoosh.app/ (Image compression)
- **Bundlephobia**: https://bundlephobia.com/ (Package size)
- **CSS Containment**: https://caniuse.com/css-containment

---

## ENERGY COMPARISON

| Component | Low Energy | High Energy |
|-----------|------------|-------------|
| Images | AVIF/WebP, <100KB | PNG/JPG, >500KB |
| Fonts | Variable, subset | Multiple weights, full |
| Video | No autoplay, lazy | Autoplay, heavy |
| Animation | CSS transforms | JS animations |
| Rendering | Static SSG | Client hydration |
| Hosting | Green CDN | Standard hosting |

---

**Last Updated**: 2026-04-12
