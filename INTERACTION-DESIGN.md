# Interaction Design Reference

The difference between a UI that works and a UI that feels right is entirely in the details
covered in this document. Developers skip these. Designers don't.

---

## 1. THE FIVE-STATE REQUIREMENT

**Every interactive element needs all five states designed before you code the happy path.**
This is the single biggest gap in AI-generated UIs. State blindness makes interfaces feel broken.

### State Map by Component Type

#### BUTTONS

| State          | Visual Signal                                            | Copy                                       |
| -------------- | -------------------------------------------------------- | ------------------------------------------ |
| Default        | Filled background, readable label                        | Action verb: "Create project" not "Submit" |
| Hover          | Slightly lighter/darker bg, subtle lift, cursor: pointer | Same                                       |
| Active/Pressed | Slightly darker, scale(0.97), tactile                    | Same                                       |
| Loading        | Spinner replaces label, same width (no layout shift)     | Consider: "Creating..."                    |
| Disabled       | 40% opacity, cursor: not-allowed, no hover effects       | Same — never remove label                  |

#### INPUTS / FORMS

| State    | Visual Signal                      | Helper text                              |
| -------- | ---------------------------------- | ---------------------------------------- |
| Default  | Subtle border, placeholder visible | Hint: what goes here                     |
| Focus    | Accent border + glow ring          | Guide: format example                    |
| Filled   | Same as default, text visible      | Nothing, or "✓ Looks good"               |
| Error    | Red border, error icon             | Specific message: "Email must contain @" |
| Success  | Green border, check icon           | "Saved" / "Verified"                     |
| Disabled | Reduced opacity, no interaction    | Sometimes: why it's disabled             |

#### CARDS / LIST ITEMS

| State    | Visual Signal                                    |
| -------- | ------------------------------------------------ |
| Default  | Background fill, standard border                 |
| Hover    | Elevated border, slight lift — only if clickable |
| Selected | Accent border or accent fill, check indicator    |
| Loading  | Skeleton (see below)                             |
| Empty    | Dashed border, centered icon + CTA               |
| Error    | Red/warning tint, error message inline           |

#### EMPTY STATES — Design these as a first-class screen

An empty state is often the first thing a new user sees. It is also a conversion opportunity.
Structure: Icon (illustrative, not generic) → Headline (what's missing) → Body (why it matters)
→ CTA (the action that fills it).

```html
<div class="empty-state">
  <div class="empty-icon"><!-- custom SVG, never a generic box --></div>
  <h3>No projects yet</h3>
  <p>
    Projects organize your work into focused spaces. Start with your biggest
    priority.
  </p>
  <button class="btn btn-primary">Create your first project</button>
</div>
```

#### SKELETON SCREENS

Show structure before content. Never show spinners for more than 400ms of expected load.

```css
.skeleton {
  background: linear-gradient(
    90deg,
    var(--surface-1) 25%,
    var(--surface-2) 50%,
    var(--surface-1) 75%
  );
  background-size: 200% 100%;
  animation: shimmer 1.4s infinite;
  border-radius: 4px;
}
@keyframes shimmer {
  0% {
    background-position: 200% 0;
  }
  100% {
    background-position: -200% 0;
  }
}

/* Skeleton shapes — match exact dimensions of real content */
.skeleton-text {
  height: 1em;
  margin-bottom: 0.5em;
}
.skeleton-title {
  height: 1.5em;
  width: 60%;
}
.skeleton-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
}
.skeleton-button {
  height: 40px;
  width: 120px;
  border-radius: 8px;
}
```

---

## 2. MICRO-COPY ARCHITECTURE

Copy is a design material with equal weight to color and typography.
**The words in a UI are part of the design, not afterthoughts.**

### The Copy Voice System

Before writing any UI copy, define the voice in 3 adjectives. Then apply it everywhere.

| Voice                       | Example CTA   | Example Error                                      | Example Placeholder                   |
| --------------------------- | ------------- | -------------------------------------------------- | ------------------------------------- |
| Confident + Clear + Direct  | "Get started" | "Password too short. Try 8+ characters."           | "Enter your workspace name"           |
| Warm + Human + Encouraging  | "Let's begin" | "Almost there — your password needs 8 characters." | "What should we call your workspace?" |
| Technical + Precise + Dry   | "Initialize"  | "ERR: password_min_length (req: 8)"                | "workspace_name"                      |
| Playful + Friendly + Casual | "Let's go →"  | "Oops! Passwords need to be at least 8 chars."     | "Your future workspace name..."       |

### Copy Rules

**Labels and CTAs:**

- Action verbs, specific: "Save changes" not "Submit", "Create project" not "OK"
- First-person where it adds clarity: "View my dashboard" vs "View dashboard"
- Never: "Click here", "Learn more", "Submit", "Go", "Yes/No" (for confirmation dialogs)

**Error messages — the WHAT + HOW formula:**

- ❌ "Invalid email address"
- ✅ "That email doesn't look right. Try: name@example.com"
- ❌ "Error: required field"
- ✅ "Add a title so your team knows what this is about."

**Success messages — confirm + advance:**

- ❌ "Success!"
- ✅ "Project created. Ready to add your first task?"

**Placeholders — show format, not generic prompts:**

- ❌ "Enter text here"
- ✅ "e.g. Q4 Marketing Campaign"

**Tooltips — answer why, not what:**

- ❌ "Premium feature"
- ✅ "Upgrade to share with your whole team"

**Loading messages — be specific:**

- ❌ "Loading..."
- ✅ "Fetching your projects..." / "Almost there..." / "Connecting to workspace..."

**Confirmation dialogs — name the consequence:**

- ❌ "Are you sure you want to delete?"
- ✅ "Delete 'Q4 Campaign'? This removes all 14 tasks permanently."

---

## 3. RESPONSIVE PHILOSOPHY

**Mobile-first is not a breakpoint strategy. It is a design philosophy.**
It means: design for constraint first. Let space expand what you've already built.
Do not design for desktop and then "make it work" on mobile.

### The Three Questions Before Any Layout

1. What is the most important action at 375px? (This is your layout foundation.)
2. What collapses first as space shrinks? (Sidebars, labels, secondary nav.)
3. What never moves? (Primary CTA, brand mark, critical data.)

### Breakpoint Vocabulary

```css
/* Mobile-first breakpoints */
/* xs: 0px — 375px   → phone portrait (design start) */
/* sm: 376px — 640px → phone landscape / small tablet */
/* md: 641px — 768px → tablet portrait */
/* lg: 769px — 1024px → tablet landscape / laptop */
/* xl: 1025px — 1280px → desktop */
/* 2xl: 1281px+     → wide desktop */

:root {
  --bp-sm: 640px;
  --bp-md: 768px;
  --bp-lg: 1024px;
  --bp-xl: 1280px;
}
```

### Container Queries — Use Over Media Queries Where Possible

```css
/* Container queries respond to the component's container, not the viewport.
   This makes components portable — they adapt wherever they're placed. */
.card-container {
  container-type: inline-size;
  container-name: card;
}

@container card (min-width: 400px) {
  .card-body {
    display: grid;
    grid-template-columns: 1fr 2fr;
  }
}
```

### Layout Collapse Patterns

```css
/* Pattern: Holy Grail → Single Column */
.layout {
  display: grid;
  grid-template-columns: 1fr; /* mobile: stack */
  gap: var(--space-5);
}
@media (min-width: 768px) {
  .layout {
    grid-template-columns: 200px 1fr; /* tablet: sidebar + content */
  }
}
@media (min-width: 1024px) {
  .layout {
    grid-template-columns: 240px 1fr 200px; /* desktop: sidebar + content + aside */
  }
}

/* Pattern: Navigation collapse */
.nav-items {
  display: none; /* hidden on mobile */
}
@media (min-width: 768px) {
  .nav-items {
    display: flex;
  }
  .nav-toggle {
    display: none;
  }
}

/* Pattern: Text sizing — already handled by clamp() in DESIGN-SYSTEMS.md */
```

### Touch Targets — Non-negotiable

```css
/* Minimum 44×44px touch target (Apple HIG, Google Material, WCAG 2.5.5) */
.btn,
a,
[role="button"],
input[type="checkbox"],
input[type="radio"] {
  min-height: 44px;
  min-width: 44px;
}
/* For small visual elements (icon buttons), use padding to expand hit area */
.icon-btn {
  padding: var(--space-3);
  /* visual: 24px icon → touch: 24px + 12px×2 = 48px ✓ */
}
```

### Scroll and Overflow

```css
/* Never let content overflow horizontally — it breaks trust */
html,
body {
  overflow-x: hidden;
}

/* Smooth scrolling — but respect preferences */
@media (prefers-reduced-motion: no-preference) {
  html {
    scroll-behavior: smooth;
  }
}

/* Momentum scrolling on mobile containers */
.scrollable {
  -webkit-overflow-scrolling: touch;
}
```

---

## 4. ACCESSIBILITY — NON-NEGOTIABLE

**Accessibility is not a feature. It is the baseline.**
WCAG AA is the minimum. Design for it from line one, not as a patch.

### Contrast Requirements (WCAG AA)

| Text type                        | Minimum ratio  |
| -------------------------------- | -------------- |
| Normal text (< 18px)             | 4.5:1          |
| Large text (≥ 18px / 14px bold)  | 3:1            |
| UI components & focus indicators | 3:1            |
| Decorative elements              | No requirement |

**OKLCH math for contrast:** L channel is perceptually uniform.
To guarantee contrast, keep ∆L between text and background ≥ 40 percentage points for normal text.

### Semantic HTML — The Foundation

```html
<!-- ❌ Never -->
<div class="button" onclick="handleClick()">Submit</div>
<div class="nav">
  <div>Home</div>
  <div>About</div>
</div>
<div class="heading">Main Title</div>

<!-- ✅ Always -->
<button type="button" onclick="handleClick()">Submit</button>
<nav aria-label="Main navigation">
  <ul>
    <li><a href="/">Home</a></li>
  </ul>
</nav>
<h1>Main Title</h1>
```

### Focus Management

```css
/* Focus visible — style it, never remove it */
:focus-visible {
  outline: 2px solid var(--accent);
  outline-offset: 3px;
  border-radius: 4px;
}
/* Remove for mouse users only */
:focus:not(:focus-visible) {
  outline: none;
}
```

### ARIA — When Semantic HTML Isn't Enough

```html
<!-- Icon buttons — always label -->
<button aria-label="Close dialog">
  <svg aria-hidden="true"><!-- icon --></svg>
</button>

<!-- Loading states -->
<button aria-busy="true" aria-live="polite">
  <span aria-hidden="true"><!-- spinner --></span>
  <span class="sr-only">Creating project...</span>
</button>

<!-- Error messages — associate with input -->
<input id="email" aria-describedby="email-error" aria-invalid="true" />
<span id="email-error" role="alert">Email must include @</span>

<!-- Modal dialogs -->
<div role="dialog" aria-modal="true" aria-labelledby="dialog-title">
  <h2 id="dialog-title">Confirm deletion</h2>
</div>

<!-- Screen reader only utility class -->
.sr-only { position: absolute; width: 1px; height: 1px; padding: 0; margin:
-1px; overflow: hidden; clip: rect(0,0,0,0); white-space: nowrap; border: 0; }
```

### Keyboard Navigation

Every interactive element must be:

1. **Focusable** — in logical tab order, no `tabindex > 0`
2. **Operable** — works with Enter and/or Space
3. **Escapable** — modals/drawers close on Escape
4. **Skippable** — "Skip to content" link for keyboard users on long nav

```html
<!-- Skip link — first element in body -->
<a href="#main-content" class="skip-link">Skip to main content</a>
<style>
  .skip-link {
    position: absolute;
    left: -9999px;
    top: auto;
    z-index: 9999;
    padding: var(--space-3) var(--space-5);
    background: var(--accent);
    color: var(--text-inv);
  }
  .skip-link:focus {
    left: var(--space-4);
    top: var(--space-4);
  }
</style>
```

---

## 5. PERFORMANCE AS UX

**Performance is a design decision. Perceived performance is a design skill.**

### Perceived Performance Techniques

```html
<!-- Preload critical fonts -->
<link
  rel="preload"
  href="/fonts/display.woff2"
  as="font"
  type="font/woff2"
  crossorigin
/>

<!-- Lazy load images below fold -->
<img loading="lazy" decoding="async" src="hero.webp" alt="..." />

<!-- Critical CSS inline, rest deferred -->
<style>
  /* above-fold styles only */
</style>
<link rel="stylesheet" href="app.css" media="print" onload="this.media='all'" />
```

### Loading State Choreography

```
0ms     → Show skeleton (match exact layout of content)
300ms   → If still loading: animate skeleton shimmer
800ms   → If still loading: show "almost there" message
2000ms  → Show error state with retry option
```

### Animation Performance

```css
/* GPU-accelerated properties only in animations */
/* ✅ Safe: transform, opacity, filter */
/* ❌ Avoid: width, height, top, left, background-color in animations */

.animated {
  will-change: transform, opacity; /* hint the browser — use sparingly */
  transform: translateZ(0); /* GPU layer promotion */
}
```

---

## 6. COMPONENT PATTERNS — QUICK REFERENCE

### Navigation

```css
/* Sticky nav with blur backdrop */
.nav {
  position: sticky;
  top: 0;
  z-index: 100;
  background: oklch(from var(--surface-0) l c h / 0.85);
  backdrop-filter: blur(12px) saturate(1.2);
  border-bottom: 1px solid var(--surface-3);
  transition: box-shadow var(--dur-medium) var(--ease-out);
}
.nav.scrolled {
  box-shadow: 0 4px 24px oklch(0% 0 0 / 0.2);
}
```

### Modal / Dialog

```css
/* Backdrop */
.modal-backdrop {
  position: fixed;
  inset: 0;
  z-index: 200;
  background: oklch(0% 0 0 / 0.6);
  backdrop-filter: blur(4px);
  display: flex;
  align-items: center;
  justify-content: center;
  animation: fadeIn var(--dur-short) var(--ease-out);
}
/* Dialog */
.modal {
  background: var(--surface-1);
  border: 1px solid var(--surface-3);
  border-radius: 16px;
  padding: var(--space-7);
  max-width: min(540px, calc(100vw - var(--space-8)));
  animation: slideUp var(--dur-medium) var(--ease-spring);
}
@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(16px) scale(0.97);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}
```

### Toast Notifications

```css
.toast-container {
  position: fixed;
  bottom: var(--space-6);
  right: var(--space-6);
  z-index: 300;
  display: flex;
  flex-direction: column;
  gap: var(--space-3);
}
.toast {
  background: var(--surface-2);
  border: 1px solid var(--surface-3);
  border-radius: 10px;
  padding: var(--space-4) var(--space-5);
  min-width: 280px;
  animation: toastIn var(--dur-medium) var(--ease-spring);
}
@keyframes toastIn {
  from {
    opacity: 0;
    transform: translateX(20px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}
```
