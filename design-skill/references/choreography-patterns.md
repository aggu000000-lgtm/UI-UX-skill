# Choreography Patterns

Complete specifications for every choreography pattern used in the design-skill. Each pattern includes timing, easing, and implementation guidance.

---

## ENTRY PATTERNS

### Cascade

Sequential waterfall reveal. Elements appear one after another, creating a sense of ordered arrival.

**Spec:**
- Stagger delay: `index × 80ms` (Snap/Personality) or `index × 120ms` (Whisper)
- Entry animation: translateY(20px) + opacity 0 → translateY(0) + opacity 1
- Easing: per motion personality
- Parent: must be a single animation container (not separate triggered animations)

**Implementation (Framer Motion):**
```jsx
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: { staggerChildren: 0.08, delayChildren: 0.1 }
  }
};
const item = {
  hidden: { opacity: 0, y: 20 },
  show: { opacity: 1, y: 0 }
};
```

**Implementation (CSS):**
```css
.cascade-item {
  opacity: 0;
  transform: translateY(20px);
  animation: cascadeIn 0.4s ease-out forwards;
}
.cascade-item:nth-child(1) { animation-delay: 0.1s; }
.cascade-item:nth-child(2) { animation-delay: 0.18s; }
.cascade-item:nth-child(3) { animation-delay: 0.26s; }
.cascade-item:nth-child(n) { animation-delay: calc(0.1s + (var(--index, 0) * 0.08s)); }

@keyframes cascadeIn {
  to { opacity: 1; transform: translateY(0); }
}
```

**Use when:** Lists, feature grids, testimonial cards, navigation items, any repeated element group
**Avoid when:** Single hero element, emergency/urgent content, above-the-fold critical content (too slow)

---

### Converge

Elements move from edges toward a central point. Creates a sense of gathering, focus, or assembly.

**Spec:**
- Outer elements travel 40-60px, inner elements travel 15-25px
- All elements arrive simultaneously or with minimal stagger (50ms)
- Entry animation: from respective edges + opacity 0 → center position + opacity 1
- Works best with 3-5 elements

**Implementation (Framer Motion):**
```jsx
const convergeItems = [
  { initial: { x: -60, opacity: 0 }, animate: { x: 0, opacity: 1 } },   // left
  { initial: { y: -40, opacity: 0 }, animate: { y: 0, opacity: 1 } },   // top
  { initial: { x: 60, opacity: 0 }, animate: { x: 0, opacity: 1 } },    // right
  { initial: { y: 40, opacity: 0 }, animate: { y: 0, opacity: 1 } },    // bottom
];
```

**Use when:** Hero sections, feature reveals, centered compositions, product showcases
**Avoid when:** Dense data displays, mobile-first narrow layouts

---

### Diverge

Elements explode outward from a single origin point. Creates energy, expansion, revelation.

**Spec:**
- Origin: center of container or user's click point
- Travel distance: 20-80px depending on container size
- Entry animation: scale(0.5) + opacity 0 → scale(1) + opacity 1, with positional offset
- Stagger: 60-100ms between elements

**Implementation (Framer Motion):**
```jsx
const divergeItem = {
  hidden: { scale: 0.5, opacity: 0, x: 0, y: 0 },
  show: (i) => ({
    scale: 1,
    opacity: 1,
    x: Math.cos((i / total) * Math.PI * 2) * 60,
    y: Math.sin((i / total) * Math.PI * 2) * 60,
    transition: { delay: i * 0.08, type: "spring", stiffness: 120, damping: 14 }
  })
};
```

**Use when:** Modal opens, menu expansions, detail reveals, radial navigation
**Avoid when:** Content-heavy pages, accessibility-critical flows

---

### Wave

Sinusoidal timing offset across a row or grid. Creates a flowing, organic rhythm.

**Spec:**
- Delay formula: `sin(index × 0.5) × maxDelay`
- Max delay: 200-400ms
- Creates a natural wave effect — not linear, not random
- Works on any grid or flex layout

**Implementation (CSS):**
```css
.wave-item {
  opacity: 0;
  animation: waveIn 0.5s ease-out forwards;
  animation-delay: calc(sin(var(--index) * 0.5) * 0.3s + 0.3s);
}
```

**Implementation (Framer Motion):**
```jsx
const waveItem = {
  hidden: { opacity: 0, y: 30 },
  show: (i) => ({
    opacity: 1,
    y: 0,
    transition: {
      delay: Math.sin(i * 0.5) * 0.3 + 0.3,
      duration: 0.5,
      ease: "easeOut"
    }
  })
};
```

**Use when:** Horizontal galleries, team grids, logo walls, portfolio thumbnails
**Avoid when:** Sequential content that needs ordered reading

---

### Stack

Elements layer on top of each other with offset. Creates depth, hierarchy, and physical presence.

**Spec:**
- Z-index offset: each element +1 z-index
- Y-offset: each element shifted -8px from previous
- Scale: each element 0.98× of previous (subtle depth)
- Entry: bottom element first, then layers upward

**Implementation:**
```css
.stack-item {
  position: relative;
}
.stack-item:nth-child(1) { z-index: 1; transform: translateY(16px) scale(0.96); }
.stack-item:nth-child(2) { z-index: 2; transform: translateY(8px) scale(0.98); }
.stack-item:nth-child(3) { z-index: 3; transform: translateY(0) scale(1); }
```

**Use when:** Card stacks, testimonial sequences, step indicators, document layers
**Avoid when:** Equal-hierarchy content, data tables

---

### Unfold

Content reveals like origami opening. Sequential panel reveals with shared edges.

**Spec:**
- Panels unfold from a hinge point (top, left, or center)
- Each panel reveals after the previous completes
- transform-origin at the hinge edge
- Duration per panel: 300-400ms

**Implementation (Framer Motion):**
```jsx
const unfoldPanel = {
  hidden: { scaleY: 0, opacity: 0 },
  show: {
    scaleY: 1,
    opacity: 1,
    transition: { duration: 0.35, ease: "easeOut" }
  }
};
// Set transform-origin via style: { transformOrigin: "top" }
```

**Use when:** Accordion sections, detail expansions, FAQ items, step-by-step reveals
**Avoid when:** All content needs to be visible simultaneously

---

## EXIT PATTERNS

### Universal Exit Rules

1. **Exit duration = 0.75 × entry duration**
   - Entry 400ms → Exit 300ms
   - Entry 300ms → Exit 225ms
   - Entry 600ms → Exit 450ms

2. **Exit direction = opposite of entry OR complementary motion**
   - Entry: slide up → Exit: slide down
   - Entry: scale in → Exit: scale out
   - Entry: fade in → Exit: fade out (same, but faster)

3. **Exit easing = same personality curve, slightly tighter**
   - Add 10% more tension to the easing curve

### Exit by Pattern

| Entry Pattern | Exit Pattern | Duration Multiplier |
|--------------|--------------|-------------------|
| Cascade | Reverse Cascade (bottom-to-top) | 0.75× |
| Converge | Diverge (scatter outward) | 0.75× |
| Diverge | Converge (collapse inward) | 0.75× |
| Wave | Reverse Wave | 0.75× |
| Stack | Unstack (top-to-bottom removal) | 0.75× |
| Unfold | Fold (reverse hinge) | 0.75× |

---

## SCROLL CHOREOGRAPHY

### Pin and Reveal

Section pins to viewport while content reveals inside it.

**Spec:**
- Pin duration: 1-2 scroll heights
- Content choreography: follows motion personality
- Unpin: smooth release, content settles into natural position

**Implementation (GSAP ScrollTrigger):**
```js
gsap.to(".pin-section", {
  scrollTrigger: {
    trigger: ".pin-section",
    start: "top top",
    end: "+=200%",
    pin: true,
    scrub: 1
  }
});
```

**Use when:** Feature deep-dives, product showcases, storytelling sections
**Avoid when:** Quick-scan content, mobile-first narrow pages

### Progress-Linked

Animation progress tied directly to scroll position.

**Spec:**
- 0% scroll = animation start state
- 100% scroll = animation end state
- Intermediate values = interpolated state
- Must be reversible (scrolling back undoes the animation)

**Implementation:**
```js
// Scroll position drives animation progress
const progress = scrollY / (scrollHeight - viewportHeight);
// Apply progress to transform values
element.style.transform = `translateY(${progress * -100}px)`;
element.style.opacity = progress;
```

**Use when:** Hero transitions, data visualizations, story sequences, product reveals
**Avoid when:** Content that must be accessible without scrolling

### Velocity-Reactive

Motion intensity scales with scroll speed.

**Spec:**
- Measure scroll delta per frame
- Map velocity to animation intensity (0-1 range)
- Decay: when scrolling stops, intensity returns to baseline over 300ms
- Maximum intensity cap to prevent disorientation

**Implementation:**
```js
let velocity = 0;
let lastScrollY = 0;

window.addEventListener("scroll", () => {
  velocity = Math.abs(scrollY - lastScrollY);
  lastScrollY = scrollY;
  const intensity = Math.min(velocity / 100, 1);
  // Apply intensity to animation properties
});

// Decay when not scrolling
setInterval(() => {
  velocity *= 0.9;
}, 50);
```

**Use when:** Immersive narratives, portfolio showcases, experimental sites
**Avoid when:** Task-focused interfaces, forms, data entry

---

## INTERACTION SEQUENCES

### Button Press Cycle

Four-phase interaction that feels physically real.

| Phase | Action | Duration | Transform |
|-------|--------|----------|-----------|
| Hover | Anticipation | 150ms | scale(1.02) + shadow increase |
| Press | Compression | 100ms | scale(0.96) + shadow reduce |
| Release | Return | 150ms | scale(1.0) + shadow restore |
| Complete | Celebration | 200ms | scale(1.05) → scale(1.0) |

**Implementation (Framer Motion):**
```jsx
<motion.button
  whileHover={{ scale: 1.02, boxShadow: "0 8px 25px rgba(0,0,0,0.15)" }}
  whileTap={{ scale: 0.96, boxShadow: "0 2px 8px rgba(0,0,0,0.1)" }}
  transition={{ type: "spring", stiffness: 400, damping: 17 }}
/>
```

### Form Submit Cycle

Multi-step feedback loop that communicates status clearly.

| Phase | Action | Duration | Visual |
|-------|--------|----------|--------|
| Validate | Field check | 200ms | Green pulse or shake |
| Loading | Button morph | 300ms | Button → progress indicator |
| Success | Confirmation | 400ms | Checkmark draw + color shift |
| Error | Rejection | 300ms | Shake + red reveal + message slide |

### Modal Cycle

Coordinated backdrop and content animation.

| Phase | Element | Duration | Animation |
|-------|---------|----------|-----------|
| Open | Backdrop | 200ms | opacity 0 → 0.5 |
| Open | Content | 300ms | scale(0.95) → scale(1), opacity 0 → 1 |
| Close | Content | 225ms | scale(1) → scale(0.95), opacity 1 → 0 |
| Close | Backdrop | 150ms | opacity 0.5 → 0 |

**Note:** Content exits before backdrop on close. This prevents the awkward "content disappearing into nothing" feeling.

---

## PERFORMANCE RULES FOR CHOREOGRAPHY

1. **Animate only transform and opacity** — never animate top, left, width, height
2. **Use will-change sparingly** — only on elements actively animating, remove after
3. **GPU-accelerate with transform: translateZ(0) or translate3d()** for complex scenes
4. **Defer non-critical animations** — entry animations wait for LCP element to load
5. **Respect prefers-reduced-motion** — replace all animations with opacity-only transitions
6. **Batch DOM reads and writes** — never interleave getComputedStyle with style changes
7. **Use requestAnimationFrame** for any JS-driven animation
8. **Limit concurrent animations** — maximum 15 simultaneously animated elements per viewport
