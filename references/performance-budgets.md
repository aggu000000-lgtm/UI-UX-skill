# Performance Budgets — Core Web Vitals (2026)

> Performance is not optimization. Performance is a feature. Ship fast by default.

---

## CORE WEB VITALS TARGETS

| Metric | Target (Good) | Needs Improvement | Poor |
|--------|--------------|-------------------|------|
| LCP (Largest Contentful Paint) | < 2.5s | 2.5s - 4.0s | > 4.0s |
| INP (Interaction to Next Paint) | < 200ms | 200ms - 500ms | > 500ms |
| CLS (Cumulative Layout Shift) | < 0.1 | 0.1 - 0.25 | > 0.25 |
| FCP (First Contentful Paint) | < 1.8s | 1.8s - 3.0s | > 3.0s |
| TTFB (Time to First Byte) | < 800ms | 800ms - 1.8s | > 1.8s |

---

## BUDGET LIMITS

| Resource | Budget | Notes |
|----------|--------|-------|
| Total page weight | < 1MB (mobile), < 2MB (desktop) | Compress everything |
| JavaScript bundle | < 170KB gzipped | Code-split, tree-shake |
| CSS bundle | < 50KB gzipped | Purge unused, critical inline |
| Fonts | < 100KB total | 2 families max, woff2 only |
| Images per page | < 500KB total | WebP/AVIF, responsive srcset |
| Third-party scripts | < 100KB | Audit every script's necessity |
| DOM nodes | < 1,500 | Fewer nodes = faster paint |
| DOM depth | < 32 levels | Deep trees block rendering |

---

## IMAGE OPTIMIZATION

### Format Priority
1. **AVIF** — Best compression, best quality. Use when supported.
2. **WebP** — Universal modern support. Fallback from AVIF.
3. **JPEG/PNG** — Legacy fallback only.

### Rules
- Always use `loading="lazy"` for images below the fold
- Always use `decoding="async"` for non-critical images
- Use `fetchpriority="high"` for LCP image (hero, above-the-fold)
- Always provide `srcset` with at least 2-3 sizes
- Use `<picture>` element for format fallbacks (AVIF → WebP → JPEG)
- SVG for icons, logos, illustrations — never rasterize
- Set explicit `width` and `height` on all images to prevent CLS

### Responsive Image Sizes
```
320px viewport  →  640px image (2x)
768px viewport  →  1200px image (1.5x)
1280px viewport →  1920px image (1.5x)
1536px viewport →  2560px image (1.6x)
```

---

## FONT LOADING STRATEGY

### Rules
- Use `font-display: swap` — never block rendering
- Preload critical font files: `<link rel="preload" href="font.woff2" as="font" crossorigin>`
- Limit to 2 font families maximum
- Use variable fonts when available (one file, multiple weights)
- Subset fonts to include only needed characters/glyphs
- System font stack as fallback:
  ```css
  font-family: 'CustomFont', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  ```

---

## JAVASCRIPT OPTIMIZATION

### Rules
- Code-split by route: only load JS for the current view
- Defer non-critical scripts: `defer` or `async`
- Tree-shake: eliminate unused exports
- Lazy load heavy components (charts, editors, maps)
- Use `requestIdleCallback` for non-urgent work
- Avoid layout thrashing: batch DOM reads and writes
- Use CSS for animations, not JS (GPU-accelerated properties: transform, opacity)
- Minimize main thread work: offload to Web Workers when possible

### Critical JS Pattern
```
1. Inline critical JS in <head> (analytics, feature flags)
2. Defer framework bundle
3. Lazy load route-specific code
4. Idle-load non-essential features
```

---

## CSS OPTIMIZATION

### Rules
- Inline critical CSS (above-the-fold styles) in `<style>` tag
- Defer non-critical CSS with `media="print" onload="this.media='all'"`
- Purge unused CSS (Tailwind does this automatically)
- Use CSS containment: `contain: layout style paint` for complex components
- Avoid expensive CSS: `position: fixed`, complex selectors, `@import`
- Use CSS custom properties for theming (no JS required for dark mode toggle)

---

## RENDERING STRATEGY

### Priority Order
1. **Server-side rendering (SSR)** or **static generation (SSG)** for content pages
2. **Streaming SSR** for dynamic content with fast TTFB
3. **Client-side rendering (CSR)** only for app-like dashboards
4. **Partial hydration** — hydrate only interactive components

### Resource Hints
```html
<!-- Preconnect to critical origins -->
<link rel="preconnect" href="https://fonts.googleapis.com" crossorigin>

<!-- Prefetch next likely page -->
<link rel="prefetch" href="/next-page">

<!-- Preload critical resource -->
<link rel="preload" href="/hero.webp" as="image">

<!-- DNS prefetch for third-party -->
<link rel="dns-prefetch" href="https://api.example.com">
```

---

## MONITORING

### Tools
- Lighthouse CI — automated performance audits on every PR
- WebPageTest — real-device testing from global locations
- Chrome DevTools Performance panel — identify bottlenecks
- Core Web Vitals report in Google Search Console — real-user data
- Sentry Performance — production error + performance tracking

### Continuous Checks
- Run Lighthouse on every PR merge
- Alert when any CWV metric degrades by 10%+
- Track bundle size changes with webpack-bundle-analyzer or similar
- Monitor real-user metrics (RUM) in production
