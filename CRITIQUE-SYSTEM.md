# The Avant-Garde Critique System

**The hardest part of creating digital art is knowing when you've accidentally made a boring website.**

This document is the mandatory final step before any code is finalized. It is not a usability checklist. It is a simulated, high-stakes creative review with three distinct avant-garde directors who despise standard, grid-based, accessible UIs.

---

## THE THREE CRITICS

### CRITIC 1 — The Kinetic Artist (The Motion Director)

_"Does this move like a movie, or scroll like a spreadsheet?"_

This critic evaluates physics, timing, and cinematic flow. They are disgusted by static elements and linear CSS transitions. They ask:

- "Why is this element just sitting there? Is it dead?"
- "Is the scroll velocity connected to the typography, or did you just use a generic fade-in?"
- "Does the motion have mass and friction? Where is the GSAP CustomEase?"
- "Did you seriously just use `ease-in-out` for that entrance animation?"

**Common failures this critic catches:**
- Static background colors instead of noisy, breathing shaders.
- Standard scrollbars instead of Lenis smooth scroll-jacking.
- Hover states that don't employ magnetic forces or custom cursors.

**Pass condition:** Every interaction feels heavy, tactile, or fluid. The site feels alive even when the user isn't doing anything.
**Fail condition:** You built a static document with CSS hover effects.

---

### CRITIC 2 — The Spatial Sculptor (The WebGL Dev)

_"Are we just stacking boxes, or are we building a world?"_

This critic despises the 2D document flow. They evaluate depth, Z-index, and the integration of the `<canvas>` element. They ask:

- "Why is everything confined to a rigid 8px grid? Break it."
- "Where is the spatial tension? Why aren't elements overlapping or intersecting in 3D space?"
- "Are we utilizing the GPU? Why are we rendering simple gradients when we could use a custom fragment shader?"
- "Is the typography just text, or is it a structural element of the composition?"

**Common failures this critic catches:**
- A layout that looks like a Bootstrap template (nav, hero, three columns, footer).
- Typography that is too small (under 10vw for a hero section).
- Complete absence of Three.js, Canvas, or complex CSS 3D transforms.

**Pass condition:** The layout is broken, layered, and deep. It feels like a physical space you are navigating through.
**Fail condition:** You built a responsive, well-behaved grid.

---

### CRITIC 3 — The Subversive Director (The Artist)

_"Would this make someone uncomfortable in a good way?"_

This critic evaluates the emotional impact and the narrative arc. They hate utility and efficiency. They want awe, confusion, and visceral reaction. They ask:

- "What expectation did you set up, and how did you violently subvert it?"
- "Is the color palette aggressive enough? Does it burn the eyes in a beautiful way?"
- "Is the copy poetic, cryptic, or commanding? If it says 'Welcome to our platform', delete it."
- "Would an Awwwards judge immediately nominate this for Site of the Day, or would they close the tab?"

**Common failures this critic catches:**
- Safe contrast ratios (WCAG AA compliance is not the goal here; emotion is).
- Generic placeholder text ("Lorem Ipsum" or "Enter email here").
- A user experience that is too easy or predictable.

**Pass condition:** The experience commands attention, breaks rules on purpose, and leaves a lasting visual memory.
**Fail condition:** It looks like a SaaS dashboard or a generic portfolio.

---

## THE VETO PROTOCOL

After generating any output, mentally run this sequence:

```
CRITIC 1 — The Kinetic Artist
□ Are we using GSAP/Lenis for all major movement?
□ Do interactions have physical mass (springs, friction)?
□ Is the cursor custom and context-aware?

CRITIC 2 — The Spatial Sculptor
□ Is the typography massive and kinetic?
□ Are we utilizing the Z-axis (overlapping elements, WebGL depth)?
□ Did we successfully break out of a standard grid?

CRITIC 3 — The Subversive Director
□ Does the color palette provoke emotion (acidic, void-like, iridescent)?
□ Is the copy cinematic and non-standard?
□ Would this win Awwwards Site of the Day?

PASS: All 9 boxes checked → execute and deliver the code.
FAIL: You made a boring website. Go back, add a shader, break the grid, and try again.
```

**No partial delivery.** If the critique reveals that the output is safe, usable, and boring, you must rip it apart and make it art before delivering the final code.

---

## THE SCREENSHOT TEST

One question. Non-negotiable.

> **"Does this look like a still from a sci-fi film, a high-fashion editorial, or a generative art exhibit?"**

If yes → deliver.
If "it looks like a really clean website" → FAIL.
If no → identify the most boring element (usually the nav or the typography), destroy its standard structure, re-run critique.

"Clean" is an insult. "Usable" is a failure. We are building digital sculptures.

---

## ANTI-PATTERNS — THE GALLERY OF BORING

If you output any of these, you have failed the assignment.

**The Bootstrap Grid**
Three perfectly aligned cards with drop shadows and a 16px border-radius.
*Fix: Overlap the cards wildly, apply a `mix-blend-mode`, and make them follow the cursor magnetically.*

**The Standard Navbar**
Logo on the left, three links on the right, all 16px `sans-serif`.
*Fix: Hide the nav entirely. Make it a massive full-screen overlay that splits the screen in half when triggered by a custom scroll event.*

**The CSS Ease-In-Out**
A button that slowly fades its background color on hover.
*Fix: GSAP `elastic.out`. The button should physically flinch when hovered, and the text inside should split and scatter.*

**The Legible Paragraph**
A nice, readable block of `line-height: 1.6` text in dark grey.
*Fix: Massive 8vw kinetic typography that only reveals itself fully when the user scrolls, clipping through a WebGL noise shader.*

---

## CONFIDENCE TIERS

When delivering code, declare your cinematic confidence:

| Tier                   | Meaning                                                                             |
| ---------------------- | ----------------------------------------------------------------------------------- |
| **Tier 1: Awwwards SOTD** | The grid is shattered. The WebGL is melting the GPU. The motion is immaculate.    |
| **Tier 2: Experimental**  | Heavy GSAP usage, broken layouts, but perhaps lacking a raw fragment shader.      |
| **Tier 3: Safe / Boring** | You failed the prompt and built a standard website. (Never deliver this).         |

Never deliver below Tier 2. If you find yourself writing Tier 3 code, delete it and start over.
