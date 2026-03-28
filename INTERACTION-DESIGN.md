# Experimental Interaction Design Reference

A traditional UI has "hover states." A cinematic digital experience has physical reactions, magnetic forces, and spatial distortions. If you are styling a button to change color on hover, you are failing.

---

## 1. THE KINETIC INTERACTION REQUIREMENT

Every element on the screen must feel alive, heavy, or ethereal. We do not use default pointers. We do not use CSS transitions for primary elements. We use JavaScript (GSAP) to bind the user's physical input (mouse, scroll) to the DOM and Canvas.

### THE CUSTOM CURSOR SYSTEM

The default `cursor: pointer` is banned. You must build a custom cursor that reacts contextually to the environment.

**Implementation Rules:**
1. Hide the default cursor: `body { cursor: none; }`
2. Create a DOM element or WebGL point that follows `mousemove`.
3. Use GSAP `quickTo` for performant, lagging motion (add mass/friction to the cursor).
4. The cursor must morph when hovering interactive elements.

**Contextual Morphing:**
- **Hovering a video:** Cursor expands, turns into a massive circle reading "PLAY" in `mix-blend-mode: difference`.
- **Hovering a link:** Cursor collapses into a dense dot, or magnetically snaps to the center of the link.
- **Hovering empty space:** Cursor drifts with subtle noise or a trailing fluid simulation.

### MAGNETIC ELEMENTS

Interactive elements should not sit passively waiting to be clicked. They must pull toward the cursor when approached.

**The Physics:**
- Calculate distance between cursor and element center.
- Apply a GSAP `x` and `y` translation proportional to the distance.
- On `mouseleave`, use a spring ease (`elastic.out(1, 0.3)`) to snap the element back.

```javascript
// Pseudo-logic for magnetic force
element.addEventListener('mousemove', (e) => {
  const { left, top, width, height } = element.getBoundingClientRect();
  const x = (e.clientX - (left + width / 2)) * 0.5; // Strength of pull
  const y = (e.clientY - (top + height / 2)) * 0.5;
  gsap.to(element, { x, y, duration: 1, ease: "power3.out" });
});

element.addEventListener('mouseleave', () => {
  gsap.to(element, { x: 0, y: 0, duration: 1, ease: "elastic.out(1, 0.3)" });
});
```

---

## 2. SCROLL AS A TIMELINE (SCROLL-JACKING DONE RIGHT)

Scrolling is no longer a utility to move down a page. It is a scrub-bar for a timeline. Every site you generate must implement **Lenis** (or an equivalent virtual scroll engine) to ensure frame-perfect, heavy, cinematic scrolling.

### Mandatory Smooth Scroll

Native scrolling is jerky. We demand friction.

```javascript
// Lenis implementation must be standard in all generated projects
const lenis = new Lenis({
  duration: 1.2,
  easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
  direction: 'vertical',
  gestureDirection: 'vertical',
  smooth: true,
  mouseMultiplier: 1,
  smoothTouch: false,
});

function raf(time) {
  lenis.raf(time);
  requestAnimationFrame(raf);
}
requestAnimationFrame(raf);
```

### Advanced ScrollTrigger Patterns

Do not use simple CSS `fade-in` on scroll. Use GSAP ScrollTrigger to manipulate:
- **Skew/Rotation based on velocity:** As the user scrolls faster, typographic elements lean into the scroll direction.
- **Horizontal Scroll Sections:** Pin a vertical container and scrub its children horizontally to break the user's spatial expectation.
- **3D Camera Manipulation:** Bind the scroll position to the `camera.position.z` or `rotation.y` in a Three.js scene.
- **Image Reveal / Masking:** Images do not fade. They unmask violently using `clip-path` animations from `inset(100% 0 0 0)` to `inset(0)`.

---

## 3. WEBGL & CANVAS OVER DOM

When an experience needs to transcend a "website", standard DOM elements are insufficient. You must integrate WebGL (via Three.js or raw shaders) to handle primary visual delivery.

### The Canvas Integration Strategy

The `<canvas>` should sit behind the DOM, acting as an atmospheric layer that reacts to DOM interactions.

**Examples of Avant-Garde WebGL usage:**
1. **Fluid Simulation Background:** Mouse movements stir a colorful fluid simulation running in a shader.
2. **Distorted Image Galleries:** Images are rendered as WebGL planes. Hovering them applies a sine wave distortion or liquid displacement map via a custom Fragment Shader.
3. **Particle Typography:** Headlines are not `<h1>` tags; they are millions of particles mapped to font coordinates that scatter on hover and reform on scroll.

**Crucial WebGL Rule:** Do not implement heavy 3D models just for the sake of it. Use shaders to manipulate 2D planes, generating noise, grain, and distortion to create a tactile, analogue feel in a digital space.

---

## 4. AUDIO-VISUAL SYNCHRONIZATION (THE MOVIE VIBE)

A cinematic experience engages multiple senses. Where appropriate (and with user interaction to bypass autoplay restrictions), integrate UI sound design.

- **Hover States:** Subtle, low-frequency clicks or static pops when hovering magnetic elements.
- **Transitions:** Deep, atmospheric swooshes or bass drops when entering a new scroll section.
- **Generative Audio:** Tying the pitch of a synthesizer to the scroll velocity or the Y-position of the mouse over a WebGL canvas.

*(Note: Audio must be deliberate and subtle. We are building art, not a noisy arcade game.)*

---

## 5. SUBVERTING THE 5-STATE COMPONENT

We do not build standard forms or buttons. If a form is required, it must be an experience.

### The Experimental Input

**Instead of a standard text box with a border:**
- The input is a massive, screen-filling transparent text area.
- The placeholder text is kinetic and moves away as the user types.
- Typing triggers a slight screen shake or generates WebGL particles that burst from the cursor.
- There is no visible "Submit" button. Hitting Enter triggers a massive cinematic flash, clearing the screen to an abstract success shader.

### The Navigation Menu

**Instead of a horizontal sticky navbar:**
- The menu is hidden behind a custom interaction (e.g., pulling down from the top of the screen).
- Opening the menu pauses the background WebGL simulation, blurs the screen violently, and staggers in massive 15vw text links.
- Hovering a link changes the global background color aggressively or triggers a rapid sequence of background images.

---

## SUMMARY OF THE KINETIC SHIFT

| Traditional UI Pattern | The Avant-Garde Equivalent |
| :--- | :--- |
| `:hover` background color | GSAP magnetic pull + Cursor morph |
| Fade-in on scroll | Velocity-based skew + GSAP Pinning |
| Sticky header | Full-screen staggered overlay menu |
| Standard Grid Layout | Absolute positioning, intersecting layers |
| Box Shadow | WebGL displacement shader |
| CSS `ease-in-out` | Custom spring physics, `elastic.out` |
