# Motion Timing Scale

Mathematical timing system for all animations. No arbitrary duration values. Everything derives from this scale.

---

## THE TIMING SCALE

Base unit: **100ms** — the smallest perceptible animation duration for UI interactions.

| Token | Duration | Purpose | Example |
|-------|----------|---------|---------|
| `t-micro` | 100ms | Instant feedback | Button press compression, toggle flip |
| `t-small` | 150ms | Quick state changes | Hover states, icon transitions |
| `t-medium` | 200ms | Standard interactions | Button hover, form focus, tooltip appear |
| `t-large` | 300ms | Component transitions | Modal open, menu expand, panel slide |
| `t-xl` | 400ms | Section transitions | Page fades, hero reveals, card entries |
| `t-xxl` | 600ms | Luxury moments | Hero entrance, premium product reveals |
| `t-xxxl` | 900ms | Cinematic reveals | Full page transitions, scroll narratives |

**Rule:** Never use values outside this scale. No 247ms, no 350ms, no 523ms. If a duration feels wrong, change the choreography pattern, not the number.

---

## EASING CURVES BY MOTION PERSONALITY

Each motion personality uses specific easing curves. These are not interchangeable.

### WHISPER — Gentle, Uninterrupted

| Context | Easing Curve | CSS Equivalent |
|---------|-------------|----------------|
| Entry | cubic-bezier(0.25, 0.1, 0.25, 1) | ease |
| Exit | cubic-bezier(0.25, 0.1, 0.25, 1) | ease |
| Scroll-linked | cubic-bezier(0.25, 0.1, 0.25, 1) | ease |
| Hover | cubic-bezier(0.25, 0.1, 0.25, 1) | ease |

**Characteristics:** Smooth, uninterrupted, no overshoot. The animation glides into place.

### BREATHE — Organic, Living

| Context | Easing Curve | CSS Equivalent |
|---------|-------------|----------------|
| Entry | cubic-bezier(0.34, 1.56, 0.64, 1) | custom (gentle overshoot) |
| Exit | cubic-bezier(0.34, 1.56, 0.64, 1) | custom (gentle overshoot) |
| Ambient pulse | cubic-bezier(0.4, 0, 0.6, 1) | ease-in-out |
| Hover | cubic-bezier(0.34, 1.56, 0.64, 1) | custom (gentle overshoot) |

**Characteristics:** Slight overshoot creates organic, breathing quality. Elements settle naturally.

### SNAP — Quick, Elastic

| Context | Easing Curve | CSS Equivalent |
|---------|-------------|----------------|
| Entry | cubic-bezier(0.34, 1.56, 0.64, 1) | custom (spring overshoot) |
| Exit | cubic-bezier(0.34, 1.56, 0.64, 1) | custom (spring overshoot) |
| Press | cubic-bezier(0.2, 0.8, 0.2, 1) | custom (quick response) |
| Hover | cubic-bezier(0.34, 1.56, 0.64, 1) | custom (spring overshoot) |

**Characteristics:** Pronounced overshoot creates elastic snap. Fast response, satisfying return.

### FLOW — Continuous, Liquid

| Context | Easing Curve | CSS Equivalent |
|---------|-------------|----------------|
| Entry | cubic-bezier(0.4, 0, 0.2, 1) | material standard |
| Exit | cubic-bezier(0.4, 0, 0.2, 1) | material standard |
| Scroll-linked | cubic-bezier(0.4, 0, 0.2, 1) | material standard |
| Morph | cubic-bezier(0.4, 0, 0.2, 1) | material standard |

**Characteristics:** Smooth acceleration and deceleration. No overshoot, no bounce. Liquid continuity.

### PULSE — Rhythmic, Alive

| Context | Easing Curve | CSS Equivalent |
|---------|-------------|----------------|
| Interaction | cubic-bezier(0.4, 0, 0.6, 1) | ease-in-out |
| Ambient pulse | cubic-bezier(0.4, 0, 0.6, 1) | ease-in-out |
| Entry | cubic-bezier(0.34, 1.56, 0.64, 1) | custom (spring overshoot) |
| Exit | cubic-bezier(0.34, 1.56, 0.64, 1) | custom (spring overshoot) |

**Characteristics:** Interactions use spring physics. Ambient motion uses smooth ease-in-out for continuous rhythm.

---

## SPRING PHYSICS PARAMETERS

When using spring-based animations (Framer Motion or equivalent), use these parameters per personality:

### WHISPER
```js
{ type: "spring", stiffness: 80, damping: 20, mass: 1.2 }
```
- Low stiffness = soft, gentle movement
- High mass = slower, more deliberate
- Result: smooth, unhurried motion

### BREATHE
```js
{ type: "spring", stiffness: 100, damping: 16, mass: 1.0 }
```
- Moderate stiffness with low damping = gentle overshoot
- Result: organic, breathing quality

### SNAP
```js
{ type: "spring", stiffness: 400, damping: 17, mass: 0.8 }
```
- High stiffness + low mass = quick, snappy response
- Result: elastic, decisive movement

### FLOW
```js
{ type: "tween", duration: 0.4, ease: [0.4, 0, 0.2, 1] }
```
- No spring — uses tween for smooth, uninterrupted motion
- Result: liquid, continuous flow

### PULSE
```js
// Interactions:
{ type: "spring", stiffness: 200, damping: 18, mass: 0.9 }
// Ambient:
{ repeat: Infinity, duration: 2, ease: "easeInOut" }
```
- Spring for interactions, timed loop for ambient
- Result: alive, rhythmic, responsive

---

## DURATION ADJUSTMENT BY CONTEXT

The base timing scale adjusts based on context:

### Mobile Adjustment
- All durations × 0.85 on screens < 768px
- Reason: smaller screens feel slower with same durations
- Exception: Whisper personality stays at 1.0× (slowness is the point)

### Content Density Adjustment
- Dense interfaces (many elements): reduce stagger by 20%
- Sparse interfaces (few elements): increase stagger by 20%
- Reason: more elements need faster total reveal time

### User Preference
- prefers-reduced-motion: replace all durations with 150ms opacity-only transitions
- High contrast mode: no duration change, ensure visibility of animated states

---

## TIMING RELATIONSHIPS

These relationships must always hold:

1. **Exit = 0.75 × Entry** — things leave faster than they arrive
2. **Hover = 0.5 × Click** — hover anticipation is shorter than action confirmation
3. **Stagger ≤ Entry Duration** — stagger delay should never exceed the animation it staggers
4. **Scroll-linked = no duration** — scroll position drives state directly, no time-based animation
5. **Ambient pulse ≥ 2000ms** — ambient animations must be slow enough to not distract

---

## EASING VISUALIZATION REFERENCE

```
WHISPER:  ────╱╱╱╱╱╱╱╱╱╱╱╱────    (smooth, no overshoot)
BREATHE:  ────╱╱╱╱╱╱╱╱╱╱╱╱╲────    (slight overshoot, settles)
SNAP:     ────╱╱╱╱╱╱╱╱╱╱╱╱╲╱╲──    (pronounced overshoot, elastic)
FLOW:     ────╱╱╱╱╱╱╱╱╱╱╱╱────    (smooth S-curve, no overshoot)
PULSE:    ────╱╱╱╱╱╱╱╱╱╱╱╱╲────    (moderate overshoot, rhythmic)
```

---

## IMPLEMENTATION QUICK REFERENCE

### CSS Custom Properties
```css
:root {
  --t-micro: 100ms;
  --t-small: 150ms;
  --t-medium: 200ms;
  --t-large: 300ms;
  --t-xl: 400ms;
  --t-xxl: 600ms;
  --t-xxxl: 900ms;

  /* Whisper easing */
  --ease-whisper: cubic-bezier(0.25, 0.1, 0.25, 1);

  /* Breathe easing */
  --ease-breathe: cubic-bezier(0.34, 1.56, 0.64, 1);

  /* Snap easing */
  --ease-snap: cubic-bezier(0.34, 1.56, 0.64, 1);

  /* Flow easing */
  --ease-flow: cubic-bezier(0.4, 0, 0.2, 1);

  /* Pulse easing */
  --ease-pulse: cubic-bezier(0.4, 0, 0.6, 1);
}
```

### Tailwind Configuration
```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      transitionDuration: {
        'micro': '100ms',
        'small': '150ms',
        'medium': '200ms',
        'large': '300ms',
        'xl': '400ms',
        'xxl': '600ms',
        'xxxl': '900ms',
      },
      transitionTimingFunction: {
        'whisper': 'cubic-bezier(0.25, 0.1, 0.25, 1)',
        'breathe': 'cubic-bezier(0.34, 1.56, 0.64, 1)',
        'snap': 'cubic-bezier(0.34, 1.56, 0.64, 1)',
        'flow': 'cubic-bezier(0.4, 0, 0.2, 1)',
        'pulse': 'cubic-bezier(0.4, 0, 0.6, 1)',
      },
    },
  },
};
```
