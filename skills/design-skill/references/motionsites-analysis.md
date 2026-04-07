# MotionSites.ai — Complete Analysis & Reference

> **CRITICAL**: Every pattern, timing value, and choreography principle in this document must be treated as MANDATORY reference when building animated hero sections. This is not optional guidance — these are proven production patterns from a platform specifically designed to create stunning animated websites via AI prompts.

## 1. Platform Overview

**MotionSites.ai** is a platform by Viktor Oddy (@viktoroddy) that provides premium hero section prompts for creating stunning, animated websites using AI coding tools. The platform bridges the gap between design intent and AI code generation by providing highly specific, production-ready prompt templates.

**Key Sites Analyzed:**
- NOVA Space Systems (Landing Page — Premium)
- AKOR Security (Landing Page — Premium)
- Nexus IT Solutions (Hero Section — Premium)
- Bionova (Hero Section)

**Core Philosophy:** MotionSites prompts work because they are SPECIFIC about motion choreography, timing, easing, and visual hierarchy. Generic prompts like "add animations" produce generic CSS transitions. MotionSites prompts describe the exact feel, timing, and behavior of every animated element.

---

## 2. Prompt Structure Anatomy

Every effective MotionSites prompt follows this structure:

```
[PROJECT CONTEXT] + [VISUAL LAYOUT] + [MOTION CHOREOGRAPHY] + [TIMING VALUES] + [EASING CURVES] + [INTERACTION BEHAVIOR] + [COLOR/TYPOGRAPHY] + [TECHNICAL CONSTRAINTS]
```

### 2.1 Project Context
- Brand name and industry
- Target audience and emotional tone
- Primary conversion goal

### 2.2 Visual Layout
- Hero section structure (split, centered, full-bleed, etc.)
- Content hierarchy (headline, subheadline, CTA, visual element)
- Grid/flexbox arrangement specifics

### 2.3 Motion Choreography
- Stagger sequence (which elements animate first/second/third)
- Direction of each animation (slide up, fade in, scale, rotate)
- Relationship between elements (do they animate independently or as a group?)

### 2.4 Timing Values
- Duration per element (typically 0.6s - 1.2s for hero elements)
- Stagger delay between elements (typically 0.1s - 0.3s)
- Total choreography duration (typically 1.5s - 3s for full hero reveal)

### 2.5 Easing Curves
- Custom cubic-bezier values (not just ease-in-out)
- Different easing for different element types
- Spring physics for interactive elements

### 2.6 Interaction Behavior
- Hover states for CTAs and interactive elements
- Scroll-triggered secondary animations
- Mouse-follow or parallax effects

### 2.7 Color & Typography
- Specific font choices (see 1000-underrated-google-fonts.csv)
- Color palette with exact values
- Typography scale and hierarchy

### 2.8 Technical Constraints
- Framework/library preferences (GSAP, Framer Motion, CSS only)
- Performance budgets (no layout thrashing, GPU-accelerated properties)
- Responsive behavior requirements

---

## 3. Animation Patterns Catalog

### 3.1 Hero Reveal Patterns

#### Pattern A: Staggered Rise (Most Common)
```
Elements rise from below with staggered delays
- Headline: translateY(40px) → 0, opacity 0 → 1, 0.8s, cubic-bezier(0.16, 1, 0.3, 1)
- Subheadline: translateY(30px) → 0, opacity 0 → 1, 0.7s, delay 0.15s
- CTA: translateY(20px) → 0, opacity 0 → 1, 0.6s, delay 0.3s
- Visual element: scale(0.95) → 1, opacity 0 → 1, 1.0s, delay 0.1s
```

#### Pattern B: Split Reveal
```
Left content and right visual reveal independently
- Left column: slide from left, 0.8s, cubic-bezier(0.22, 1, 0.36, 1)
- Right column: slide from right, 0.8s, delay 0.2s
- Overline tag: fade in first, 0.4s
- CTA group: fade up last, 0.6s, delay 0.5s
```

#### Pattern C: Cinematic Fade
```
Slow, dramatic reveal like a film opening
- Background: slow zoom 1.05 → 1.0, 2.0s
- Headline: fade + slight rise, 1.2s, delay 0.5s
- Subheadline: fade, 1.0s, delay 0.8s
- CTA: fade + scale, 0.8s, delay 1.2s
```

#### Pattern D: Typewriter + Reveal
```
Headline types in character by character
- Headline: char-by-char reveal, 0.03s per char, stagger 0.02s
- Cursor blink during typing
- After headline: subheadline fades up, 0.6s, delay 0.3s after typing
- CTA slides up, 0.5s, delay 0.5s after subheadline
```

#### Pattern E: Parallax Layers
```
Multiple depth layers move at different speeds
- Background layer: slowest parallax (0.2x scroll speed)
- Mid layer: medium parallax (0.5x scroll speed)
- Foreground content: fastest parallax (0.8x scroll speed)
- Each layer has independent entrance animation
```

### 3.2 Scroll-Driven Patterns

#### Pattern A: Progressive Disclosure
```
Content reveals as user scrolls
- Section enters viewport → elements fade up with stagger
- Images scale from 0.9 to 1.0 on scroll
- Text reveals line by line on scroll progress
- Progress indicator shows scroll position
```

#### Pattern B: Sticky Storytelling
```
Elements stick and release at narrative beats
- Hero text sticks while background transitions
- Each scroll section has its own sticky phase
- Release triggers next section entrance
- Smooth transitions between sticky phases
```

#### Pattern C: Horizontal Scroll Section
```
Vertical scroll triggers horizontal movement
- Container translates horizontally based on scroll progress
- Cards slide in from right as section enters
- Progress bar shows horizontal position
- Snap points at each card
```

### 3.3 Micro-Interaction Patterns

#### Pattern A: Magnetic Button
```
CTA buttons attract cursor within radius
- Cursor enters 50px radius → button moves toward cursor
- Movement proportional to cursor distance from center
- Easing: spring physics (stiffness 200, damping 20)
- On click: scale down 0.95, then back to 1.0
```

#### Pattern B: Text Scramble
```
Hover triggers character scramble effect
- Each character cycles through random chars before settling
- Duration: 0.3s total, 0.02s per character
- Characters resolve left to right
- Easing: ease-out for each character
```

#### Pattern C: Image Reveal
```
Hover reveals image with creative transition
- Clip-path animation (circle expand, wipe, etc.)
- Duration: 0.6s - 0.8s
- Easing: cubic-bezier(0.16, 1, 0.3, 1)
- Overlay text fades in during reveal
```

---

## 4. Timing & Easing Reference

### 4.1 Standard Timing Values

| Element Type | Duration | Stagger Delay | Total Window |
|---|---|---|---|
| Overline/Tag | 0.4s | 0s | 0.4s |
| Headline | 0.8s | 0.1s | 0.9s |
| Subheadline | 0.6s | 0.25s | 1.15s |
| CTA Button | 0.5s | 0.4s | 1.3s |
| Hero Image | 1.0s | 0.05s | 1.05s |
| Background | 1.5s | 0s | 1.5s |
| Decorative Elements | 0.6s | 0.15s | 1.2s |

### 4.2 Easing Curves (Custom cubic-bezier)

| Feel | cubic-bezier | Use Case |
|---|---|---|
| Smooth rise | (0.16, 1, 0.3, 1) | Hero element entrance |
| Gentle settle | (0.22, 1, 0.36, 1) | Content slide-in |
| Quick snap | (0.25, 0.46, 0.45, 0.94) | Button hover |
| Elastic bounce | (0.68, -0.55, 0.265, 1.55) | Playful elements |
| Natural decel | (0.33, 1, 0.68, 1) | Scroll-driven reveals |
| Soft start | (0.55, 0.085, 0.68, 0.53) | Gentle fades |
| Sharp entrance | (0.77, 0, 0.175, 1) | Dramatic reveals |
| Spring physics | (0.34, 1.56, 0.64, 1) | Interactive elements |

### 4.3 Choreography Rules

1. **Never animate all elements simultaneously** — always stagger
2. **Top-to-bottom reading order** — headline first, then subheadline, then CTA
3. **Background before content** — let the stage be set before actors appear
4. **Faster for smaller elements** — micro-interactions should be quick (0.2s-0.4s)
5. **Slower for larger elements** — hero backgrounds can take 1.5s-2s
6. **Delay proportional to distance** — elements further from viewport center get more delay
7. **Total hero reveal under 3 seconds** — respect user attention span

---

## 5. Visual Hierarchy in Hero Sections

### 5.1 NOVA Space Systems Pattern
- **Overline tag** (small, uppercase, letter-spaced) → animates first
- **Massive headline** (display font, 72px-120px) → animates second with dramatic entrance
- **Subheadline** (body font, 18px-24px) → animates third with gentle fade
- **CTA group** (primary + secondary button) → animates fourth with slide-up
- **Hero visual** (3D element, image, or illustration) → animates in parallel with headline

### 5.2 AKOR Security Pattern
- **Dark background** with subtle gradient or texture
- **Headline** with gradient text or glow effect
- **Trust indicators** (logos, stats) → animate after CTA
- **Security-focused visual** (shield, lock, abstract pattern) → subtle breathing animation
- **Navigation** → minimal, fades in last

### 5.3 Nexus IT Solutions Pattern
- **Split layout** (content left, visual right)
- **Content column** → staggered reveal top to bottom
- **Visual column** → scale + fade entrance
- **Stats/metrics** → count-up animation after main content
- **Client logos** → infinite horizontal scroll marquee

### 5.4 Bionova Pattern
- **Full-bleed background** with overlay
- **Centered content** → dramatic fade + rise
- **Scroll indicator** → bouncing animation, infinite loop
- **Navigation** → transparent, becomes solid on scroll

---

## 6. Motion Design Principles

### 6.1 The 12 Principles Applied to Web

1. **Squash & Stretch** — Buttons compress on click (scale 0.95)
2. **Anticipation** — Elements pull back slightly before moving forward
3. **Staging** — One focal point per animation phase
4. **Straight Ahead vs Pose to Pose** — Use keyframes for web, not frame-by-frame
5. **Follow Through** — Elements continue moving slightly past their target
6. **Slow In Slow Out** — Always use easing, never linear
7. **Arc** — Elements move in arcs, not straight lines (use translateX + translateY together)
8. **Secondary Action** — Background elements animate subtly while main content animates
9. **Timing** — Duration communicates weight and importance
10. **Exaggeration** — Slightly exaggerate for impact, but keep it subtle
11. **Solid Drawing** — Maintain 3D awareness in 2D space (perspective, depth)
12. **Appeal** — Every animation should feel intentional and delightful

### 6.2 Human-Feel Motion Rules

- **Add imperfection** — Slight variation in stagger timing (0.12s, 0.18s, 0.25s instead of uniform 0.15s)
- **Natural rhythms** — Breathing animations should use 3-4 second cycles (human breathing rate)
- **Organic easing** — Use custom cubic-bezier values that mimic natural acceleration
- **Weight awareness** — Heavier elements move slower, lighter elements move faster
- **Energy conservation** — Elements should feel like they have momentum, not teleport
- **Context sensitivity** — Animation speed adjusts to user behavior (fast scroll = faster animations)

---

## 7. Prompt Templates

### 7.1 Basic Hero Template
```
Create a hero section for [BRAND] — a [INDUSTRY] company targeting [AUDIENCE].

Layout: [LAYOUT TYPE] with [CONTENT ARRANGEMENT]

Typography:
- Headline: [FONT NAME], [SIZE]px, [WEIGHT]
- Subheadline: [FONT NAME], [SIZE]px, [WEIGHT]
- CTA: [FONT NAME], [SIZE]px, [WEIGHT]

Animation choreography (page load):
1. [ELEMENT 1] animates with [ANIMATION TYPE], duration [X]s, easing [EASING]
2. [ELEMENT 2] animates with [ANIMATION TYPE], duration [X]s, delay [Y]s
3. [ELEMENT 3] animates with [ANIMATION TYPE], duration [X]s, delay [Y]s

Hover states:
- CTA: [HOVER EFFECT]
- Links: [HOVER EFFECT]

Color palette: [COLORS]
Background: [BACKGROUND TYPE]
```

### 7.2 Advanced Hero Template
```
Build a cinematic hero section for [BRAND].

Visual concept: [DESCRIBE THE VISUAL METAPHOR]

Structure:
- Navigation: [NAV STYLE]
- Hero container: [CONTAINER STYLE]
- Content zone: [CONTENT STYLE]
- Visual zone: [VISUAL STYLE]

Motion choreography:
- Background: [BACKGROUND ANIMATION]
- Overline: [OVERLINE ANIMATION]
- Headline: [HEADLINE ANIMATION] with [EASING], [DURATION]s
- Subheadline: [SUBHEADLINE ANIMATION] with [EASING], [DURATION]s, delay [X]s
- CTA group: [CTA ANIMATION] with [EASING], [DURATION]s, delay [X]s
- Visual element: [VISUAL ANIMATION] with [EASING], [DURATION]s

Scroll behavior:
- Parallax: [PARALLAX DETAILS]
- Secondary animations: [SCROLL ANIMATIONS]
- Section transitions: [TRANSITION DETAILS]

Technical:
- Framework: [GSAP/Framer Motion/CSS]
- Performance: GPU-accelerated only, no layout thrashing
- Responsive: [RESPONSIVE BEHAVIOR]
```

---

## 8. Integration with Human Design Elements

### 8.1 Connecting to 1000-human-made-design-elements.csv

When using MotionSites patterns, ALWAYS incorporate human-made design elements:

- **Typography**: Use hand-drawn or imperfect typography elements (IDs 1-20 from CSV)
- **Texture**: Add paper grain, film grain, or canvas textures (IDs 21-40)
- **Color**: Use hand-mixed color variations, not perfect gradients (IDs 41-60)
- **Layout**: Break the grid with organic whitespace (IDs 61-80)
- **Micro-interactions**: Add breathing animations, hesitant hovers (IDs 81-100)

### 8.2 Font Selection from 1000-underrated-google-fonts.csv

NEVER default to Inter, Poppins, Roboto, or Montserrat. Instead:

- **Headlines**: Instrument Serif, Fraunces, Bricolage Grotesque, Young Serif
- **Body**: Instrument Sans, Literata, Newsreader, Source Serif 4
- **Display**: Aboreto, Red Rose, Space Grotesk, Syne
- **Mono**: JetBrains Mono, Space Mono, IBM Plex Mono

### 8.3 Anti-AI Detection Checklist

Before declaring a hero section complete, verify:
- [ ] Stagger timing has natural variation (not uniform intervals)
- [ ] Easing curves are custom cubic-bezier (not ease-in-out)
- [ ] At least one element has a hand-drawn or imperfect quality
- [ ] Color palette includes slight imperfections or variations
- [ ] Typography uses underrated fonts from the CSV
- [ ] Motion has weight awareness (heavy = slow, light = fast)
- [ ] At least one micro-interaction feels human (breathing, hesitant hover)
- [ ] No element animates in perfect isolation (secondary actions exist)
- [ ] Total hero reveal feels natural, not mechanical

---

## 9. Premium vs Free Pattern Differences

### 9.1 Free Patterns
- Basic staggered reveals
- Standard easing curves
- Single animation phase
- Limited interaction states
- Generic visual elements

### 9.2 Premium Patterns
- Multi-phase choreography (entrance + secondary + scroll)
- Custom easing curves per element type
- Mouse-follow and parallax effects
- Rich hover states with secondary animations
- Unique visual metaphors (3D elements, abstract shapes)
- Scroll-driven narrative flow
- Performance-optimized with GPU acceleration
- Responsive choreography (different animations per breakpoint)

---

## 10. Implementation Guide

### 10.1 With GSAP
```javascript
// Hero reveal choreography
const tl = gsap.timeline({ defaults: { ease: "power3.out" } });

tl.from(".overline", { y: 20, opacity: 0, duration: 0.4 })
  .from(".headline", { y: 40, opacity: 0, duration: 0.8 }, "-=0.2")
  .from(".subheadline", { y: 30, opacity: 0, duration: 0.6 }, "-=0.4")
  .from(".cta-group", { y: 20, opacity: 0, duration: 0.5 }, "-=0.3")
  .from(".hero-visual", { scale: 0.95, opacity: 0, duration: 1.0 }, "-=0.8");
```

### 10.2 With Framer Motion
```jsx
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: { staggerChildren: 0.15, delayChildren: 0.2 }
  }
};

const item = {
  hidden: { y: 30, opacity: 0 },
  show: { y: 0, opacity: 1, transition: { duration: 0.6, ease: [0.16, 1, 0.3, 1] } }
};
```

### 10.3 With CSS Only
```css
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(30px); }
  to { opacity: 1; transform: translateY(0); }
}

.hero-element {
  animation: fadeUp 0.6s cubic-bezier(0.16, 1, 0.3, 1) forwards;
  opacity: 0;
}

.hero-element:nth-child(1) { animation-delay: 0.1s; }
.hero-element:nth-child(2) { animation-delay: 0.25s; }
.hero-element:nth-child(3) { animation-delay: 0.4s; }
```

---

## 11. Sources & References

- MotionSites.ai — Primary platform analysis
- motionsites.ai/guide — Usage guide and walkthrough
- @viktoroddy (Threads/Instagram) — Creator's social presence
- uimotionprompts.com — Complementary UI motion prompt resource
- 1000-human-made-design-elements.csv — Human design element reference
- 1000-underrated-google-fonts.csv — Font selection reference

---

## 12. MANDATORY COMPLIANCE

When building any animated hero section, you MUST:

1. **Reference this document** for choreography patterns and timing values
2. **Cross-reference** with 1000-human-made-design-elements.csv for human quality signals
3. **Select fonts** from 1000-underrated-google-fonts.csv, never AI defaults
4. **Apply human-feel motion** rules — natural variation, organic easing, weight awareness
5. **Verify against** the anti-AI detection checklist before delivery
6. **Use the prompt templates** as starting points, then customize for the specific project
7. **Ensure every animation** serves a purpose — no decoration without function
8. **Test at multiple breakpoints** — choreography must adapt to screen size
9. **Performance first** — GPU-accelerated properties only, no layout thrashing
10. **Accessibility** — respect prefers-reduced-motion, provide non-animated alternatives
