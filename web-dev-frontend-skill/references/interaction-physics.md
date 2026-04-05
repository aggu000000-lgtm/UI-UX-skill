# Interaction Physics — Easing, Animations, Motion Design (2026)

> Motion is not decoration. Motion is spatial information and state confirmation.

---

## ANIMATION DURATION GUIDELINES

| Interaction Type | Duration | Reason |
|-----------------|----------|--------|
| Micro-interactions (button press, toggle) | 150-200ms | Fast feedback, no perceived delay |
| State transitions (hover, focus) | 150-250ms | Immediate but smooth |
| Component enter/exit (modals, dropdowns) | 200-300ms | Visible but not slow |
| Page transitions | 300-500ms | Context preservation |
| Complex animations (carousel, parallax) | 500-800ms | Smooth, cinematic |
| Skeleton loading pulse | 1.5-2s (loop) | Perceived loading speed |

**Never exceed 500ms for user-triggered interactions.** Users perceive anything slower as lag.

---

## EASING CURVES

### Standard Easing Functions

| Curve | CSS Value | Use Case |
|-------|-----------|----------|
| Linear | `linear` | Color transitions, opacity only |
| Ease Out | `cubic-bezier(0.16, 1, 0.3, 1)` | Elements entering (from off-screen, fade in) |
| Ease In | `cubic-bezier(0.7, 0, 0.84, 0)` | Elements exiting (slide out, fade out) |
| Ease In-Out | `cubic-bezier(0.65, 0, 0.35, 1)` | Morphing, size changes, color + transform |
| Spring | `cubic-bezier(0.34, 1.56, 0.64, 1)` | Bouncy enter, playful interactions |
| Snappy | `cubic-bezier(0.2, 0, 0, 1)` | Linear-style apps (Linear, Vercel) |

### Linear-Style Snappy Curve
```css
/* The curve that makes Linear feel fast */
--ease-snappy: cubic-bezier(0.2, 0, 0, 1);

/* Used for: sidebar transitions, panel resizing, list reordering */
```

### Apple-Style Smooth Curve
```css
/* The curve that makes Apple feel premium */
--ease-smooth: cubic-bezier(0.25, 0.1, 0.25, 1);

/* Used for: page transitions, hero animations, scroll reveals */
```

---

## CSS TRANSITIONS

### Standard Transition Pattern
```css
.button {
  transition: background-color 150ms var(--ease-snappy),
              color 150ms var(--ease-snappy),
              transform 150ms var(--ease-snappy),
              box-shadow 150ms var(--ease-snappy);
}

.button:hover {
  background-color: var(--color-primary-hover);
  transform: translateY(-1px);
}

.button:active {
  transform: translateY(0);
}
```

### GPU-Accelerated Properties
Only animate these properties for 60fps animations:
- `transform` (translate, scale, rotate, skew)
- `opacity`
- `filter` (blur, brightness)

**Never animate:** `width`, `height`, `top`, `left`, `margin`, `padding` — these trigger layout recalculation.

### will-change Hint
```css
.animated-element {
  will-change: transform, opacity;
}
```
Use sparingly. Overuse wastes GPU memory.

---

## SPRING PHYSICS

### Spring Configuration
```
Mass: 1
Stiffness: 180
Damping: 12
```

This produces:
- Natural overshoot
- Quick settle (~400ms)
- No oscillation beyond 2 bounces

### CSS Spring Approximation
```css
@keyframes spring {
  0%   { transform: scale(0.9); }
  50%  { transform: scale(1.03); }
  75%  { transform: scale(0.99); }
  100% { transform: scale(1); }
}

.spring-enter {
  animation: spring 400ms cubic-bezier(0.34, 1.56, 0.64, 1);
}
```

---

## SCROLL ANIMATIONS

### Intersection Observer Pattern
```js
const observer = new IntersectionObserver(
  (entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('animate-in');
        observer.unobserve(entry.target);
      }
    });
  },
  { threshold: 0.1, rootMargin: '0px 0px -50px 0px' }
);

document.querySelectorAll('.scroll-animate').forEach(el => {
  observer.observe(el);
});
```

### Scroll-Driven Animations (2026 Native)
```css
/* Native scroll-driven animations — no JS needed */
.hero-image {
  animation: fade-in linear;
  animation-timeline: view();
  animation-range: entry 0% entry 40%;
}
```

---

## GESTURE HANDLING

### Touch Gestures
| Gesture | Action | Feedback |
|---------|--------|----------|
| Tap | Activate/click | 150ms scale down + color shift |
| Long press | Context menu | 300ms scale up + shadow |
| Swipe left/right | Navigate/delete | Follow finger, snap on release |
| Pull down | Refresh | Elastic resistance, spinner on release |
| Pinch | Zoom | Scale follows fingers, max/min bounds |

### Touch Feedback
```css
.touch-feedback {
  -webkit-tap-highlight-color: transparent;
  touch-action: manipulation;
}

.touch-feedback:active {
  transform: scale(0.97);
  transition: transform 100ms ease-out;
}
```

---

## LOADING STATES

### Skeleton Loading
```css
.skeleton {
  background: linear-gradient(
    90deg,
    var(--gray-200) 25%,
    var(--gray-100) 50%,
    var(--gray-200) 75%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
  border-radius: 4px;
}

@keyframes shimmer {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
```

### Progress Indicators
- Determinate: use progress bar when percentage is known
- Indeterminate: use spinner when duration is unknown
- Never block interaction during loading — show partial state

---

## PREFERS-REDUCED-MOTION

### Mandatory Implementation
```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

### Information Preservation
When animations are disabled, the information they convey must still be communicated:
- Scroll reveals: content is visible by default
- State transitions: use color/border changes, not movement
- Loading states: show text "Loading..." alongside or instead of spinner
- Page transitions: instant navigation, no fade

---

## MICRO-INTERACTIONS

### Button Press
```css
.btn:active {
  transform: scale(0.97);
  transition: transform 100ms ease-out;
}
```

### Link Hover
```css
.link {
  position: relative;
}

.link::after {
  content: '';
  position: absolute;
  bottom: -2px;
  left: 0;
  width: 0;
  height: 1px;
  background: currentColor;
  transition: width 200ms var(--ease-snappy);
}

.link:hover::after {
  width: 100%;
}
```

### Card Hover Lift
```css
.card {
  transition: transform 200ms var(--ease-snappy),
              box-shadow 200ms var(--ease-snappy);
}

.card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.1);
}
```

### Toggle Switch
```css
.toggle {
  transition: background-color 200ms var(--ease-snappy);
}

.toggle-thumb {
  transition: transform 200ms var(--ease-spring);
}
```

---

## PERFORMANCE RULES

1. Animate only `transform` and `opacity` for 60fps
2. Use `will-change` sparingly — only on elements currently animating
3. Remove `will-change` after animation completes
4. Avoid `box-shadow` animations — use opacity on a pseudo-element instead
5. Avoid animating `filter: blur()` on large elements — use on small elements only
6. Use CSS animations over JS animations when possible
7. For complex choreography, use FLIP technique (First, Last, Invert, Play)
