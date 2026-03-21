# library-sources.md
# Version-pinned API signatures for AI-grounded frontend code
# Every claim traceable to library source. If unverified, marked [UNVERIFIED].
# Load this file before writing any code using Radix, Motion, Floating UI, GSAP, or Three.js.

---

## Quick-reference: What to load per task

| Task | Section to read |
|---|---|
| Modal, drawer, dropdown, tooltip, select | § Radix UI |
| Page transitions, scroll animations, presence | § Motion v12 |
| Popover, tooltip, context menu positioning | § Floating UI |
| Scroll-driven animation, pinning, scrubbing | § GSAP + ScrollTrigger |
| 3D hero, shader backgrounds, WebGL scenes | § Three.js r165+ |

---

## Radix UI Primitives — v1.4.3

### Install
```
npm install radix-ui
```

### Import paths
```ts
// ✅ Preferred — unified package, correct tree-shaking
import { Dialog, Popover, DropdownMenu, Tooltip, Select, Tabs, Accordion } from "radix-ui";

// ✅ Also valid — scoped packages
import * as Dialog      from "@radix-ui/react-dialog";
import * as Popover     from "@radix-ui/react-popover";
import * as Select      from "@radix-ui/react-select";
import * as Tabs        from "@radix-ui/react-tabs";
import * as Accordion   from "@radix-ui/react-accordion";

// ❌ Never — default imports don't exist on Radix
import Dialog from "@radix-ui/react-dialog";
```

### Core API signatures

```ts
// ── Dialog (v1.1.2) ──────────────────────────────────────────────────────────
Dialog.Root: {
  open?: boolean;                          // controlled open state
  defaultOpen?: boolean;                   // uncontrolled initial state
  onOpenChange?: (open: boolean) => void;  // fires on every state change
  modal?: boolean;                         // default: true — blocks outside interaction
}

Dialog.Trigger: {
  asChild?: boolean;  // default: false — merges props onto child (requires forwardRef on child)
}

Dialog.Portal: {
  container?: HTMLElement | null;  // default: document.body — must be mounted before dialog opens
  forceMount?: true;               // keeps DOM alive for exit animations (use with AnimatePresence)
}

Dialog.Overlay: {
  asChild?: boolean;   // default: false
  forceMount?: true;   // same as Portal.forceMount — required for exit animations
}

Dialog.Content: {
  asChild?: boolean;
  forceMount?: true;
  onOpenAutoFocus?:       (event: Event) => void;          // call event.preventDefault() to suppress focus
  onCloseAutoFocus?:      (event: Event) => void;          // focus returns to trigger by default
  onEscapeKeyDown?:       (event: KeyboardEvent) => void;
  onPointerDownOutside?:  (event: PointerDownOutsideEvent) => void;
  onInteractOutside?:     (event: Event) => void;
}

Dialog.Title: {}       // required for screen reader accessibility — always include
Dialog.Description: {} // recommended
Dialog.Close: { asChild?: boolean }

// ── Select (v1.1.2) ──────────────────────────────────────────────────────────
Select.Root: {
  value?: string;
  defaultValue?: string;
  onValueChange?: (value: string) => void;
  dir?: "ltr" | "rtl";   // default: "ltr"
  open?: boolean;
  defaultOpen?: boolean;
  onOpenChange?: (open: boolean) => void;
  disabled?: boolean;
  name?: string;          // for form submission
}

Select.Trigger: { asChild?: boolean }
Select.Value:   { placeholder?: React.ReactNode }

Select.Content: {
  position?: "item-aligned" | "popper";  // default: "item-aligned"
  // popper mode only:
  side?:        "top" | "right" | "bottom" | "left";  // default: "bottom"
  sideOffset?:  number;                                // default: 0
  align?:       "start" | "center" | "end";            // default: "start"
  alignOffset?: number;
}

Select.Item: {
  value:      string;     // required — must be unique within Select.Root
  disabled?:  boolean;    // default: false
  textValue?: string;     // used for typeahead
}
```

### Minimal working example
```tsx
import React from 'react';
import { Dialog } from 'radix-ui';

// forwardRef is REQUIRED when using asChild with custom components
const Btn = React.forwardRef<HTMLButtonElement, React.ButtonHTMLAttributes<HTMLButtonElement>>(
  (props, ref) => <button {...props} ref={ref} />
);

export const ConfirmDialog = () => (
  <Dialog.Root>
    <Dialog.Trigger asChild>
      <Btn>Open</Btn>
    </Dialog.Trigger>
    <Dialog.Portal>
      <Dialog.Overlay className="fixed inset-0 bg-black/50" />
      <Dialog.Content className="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 p-6 bg-white rounded-lg">
        <Dialog.Title>Confirm action</Dialog.Title>
        <Dialog.Description>This cannot be undone.</Dialog.Description>
        <Dialog.Close asChild>
          <Btn>Cancel</Btn>
        </Dialog.Close>
      </Dialog.Content>
    </Dialog.Portal>
  </Dialog.Root>
);
```

### Breaking changes: v0 → v1
- `collisionTolerance` renamed to `collisionPadding` on all Popper-based components
- `offset` on Arrow removed — use `sideOffset` on Content instead
- `allowPinchZoom` now defaults to `true` across all modal components

### Sharp edges
1. **asChild + missing forwardRef** — if the child component doesn't use `React.forwardRef`, Radix cannot inject the DOM ref. Positioning breaks silently, focus trapping breaks silently. No error thrown.
2. **Portal container not mounted** — if `container` prop points to a node that doesn't exist yet when the dialog opens, the portal renders nowhere. Check mount order.
3. **AnimatePresence exit animations** — Radix unmounts immediately on close. To get exit animations, pass `forceMount` to both `Dialog.Overlay` and `Dialog.Content`, then control visibility via Motion.
4. **Nested `<button>` inside Trigger** — renders `<button><button>`, invalid HTML. Use `asChild` or a non-button element.
5. **Missing Dialog.Title** — screen readers cannot name the modal. Always include, or pass `aria-label` to Content.

### Anti-patterns
```tsx
// ❌ Custom component without forwardRef — asChild silently fails
const BadBtn = (props) => <button {...props} />;
<Dialog.Trigger asChild><BadBtn /></Dialog.Trigger>

// ✅ Correct
const GoodBtn = React.forwardRef((props, ref) => <button {...props} ref={ref} />);
<Dialog.Trigger asChild><GoodBtn /></Dialog.Trigger>

// ❌ Overriding internal Radix handlers without composing
<Dialog.Content onPointerDownOutside={() => setOpen(false)} />

// ✅ Compose — call the original handler
<Dialog.Content onPointerDownOutside={(e) => { e.preventDefault(); setOpen(false); }} />
```

---

## Motion (Framer Motion) — v12.38.0

### Install
```
npm install motion
```

### Import paths
```ts
// ✅ Preferred — react-specific entry point
import { motion, AnimatePresence, useScroll, useTransform,
         useMotionValue, LayoutGroup, Reorder } from "motion/react";

// ✅ Also valid — legacy entry, still works
import { motion, AnimatePresence } from "framer-motion";

// ✅ Vanilla JS (no React)
import { animate, scroll } from "motion";

// ❌ Mixing entry points in the same file causes duplicate engine instances
```

### Core API signatures
```ts
// ── motion.* props (subset — full list in Motion docs) ──────────────────────
{
  animate?:   AnimationProps;            // target properties to animate to
  initial?:   AnimationProps | false;    // false disables mount animation
  exit?:      AnimationProps;            // only runs inside AnimatePresence
  layout?:    boolean | "position" | "size" | "x" | "y";
  layoutId?:  string;                    // shared element transitions across components
  transition?: Transition;
  style?:     MotionStyle;               // accepts MotionValues directly
  variants?:  Variants;
  whileHover?:    AnimationProps;
  whileTap?:      AnimationProps;
  whileFocus?:    AnimationProps;
  whileInView?:   AnimationProps;
  onAnimationComplete?: (definition: AnimationDefinition) => void;
}

// ── AnimatePresence ──────────────────────────────────────────────────────────
AnimatePresence: {
  mode?:        "sync" | "wait" | "popLayout";  // default: "sync"
  initial?:     boolean;                         // default: true — false skips mount animation
  onExitComplete?: () => void;
  // children MUST have unique key props — no key = no exit animation
}

// ── useScroll ────────────────────────────────────────────────────────────────
useScroll(options?: {
  container?: RefObject<Element>;   // default: window
  target?:    RefObject<Element>;   // element to track
  offset?:    ScrollOffset;         // see sharp edges below — this is hallucinated constantly
  axis?:      "x" | "y";            // default: "y"
}): {
  scrollX:         MotionValue<number>;
  scrollY:         MotionValue<number>;
  scrollXProgress: MotionValue<number>;  // 0–1
  scrollYProgress: MotionValue<number>;  // 0–1
}

// ── useTransform ─────────────────────────────────────────────────────────────
useTransform(
  value:   MotionValue,
  input:   number[],   // e.g. [0, 0.5, 1]
  output:  any[],      // e.g. [0, 1, 0] or ["#000", "#fff"]
  options?: { clamp?: boolean; ease?: EasingFunction | EasingFunction[] }
): MotionValue

// ── Reorder ──────────────────────────────────────────────────────────────────
Reorder.Group: {
  values:     any[];                      // source array — required
  onReorder:  (newValues: any[]) => void; // required — update state here
  axis?:      "x" | "y";                 // default: "y"
  as?:        keyof JSX.IntrinsicElements;
}
Reorder.Item: {
  value: any;  // required — must match an item in Reorder.Group values
}
```

### Minimal working example
```tsx
import { motion, AnimatePresence, useScroll, useTransform } from "motion/react";
import { useState } from "react";

export const ScrollFade = () => {
  const [visible, setVisible] = useState(true);
  const { scrollYProgress } = useScroll({
    offset: ["start start", "end start"]  // ← exact syntax required
  });
  const opacity = useTransform(scrollYProgress, [0, 0.5], [1, 0]);

  return (
    <div style={{ height: "200vh" }}>
      <motion.section style={{ opacity, position: "sticky", top: 0 }}>
        <AnimatePresence mode="wait">
          {visible && (
            <motion.div
              key="card"                           // ← required for exit animation
              initial={{ opacity: 0, y: 40 }}
              animate={{ opacity: 1, y: 0 }}
              exit={{ opacity: 0, y: -40 }}
              onClick={() => setVisible(false)}
            >
              Click to exit
            </motion.div>
          )}
        </AnimatePresence>
      </motion.section>
    </div>
  );
};
```

### Breaking changes: v10 → v11 → v12
- **v11**: velocity calculation is now async (per-frame, not per-update). Unit tests reading `.getVelocity()` synchronously will get 0 — await a frame first.
- **v11**: `import from "framer-motion"` still works but `"motion/react"` is the canonical path.
- **v12**: WAAPI acceleration enabled by default for `opacity` and `transform`. Manual `will-change` is now redundant — Motion sets it automatically.

### Sharp edges
1. **useScroll offset** — does NOT accept raw numbers. Must be string pairs: `"start end"` means "when the target's start hits the container's end." Format: `["target-edge container-edge", "target-edge container-edge"]`. Using `[0, 1]` does nothing useful.
2. **Missing `key` in AnimatePresence** — React can't track the exiting element, exit animation is skipped entirely. Always add unique `key` to direct children of AnimatePresence.
3. **`layout` + manual transforms** — the projection engine overwrites `transform` styles. Don't combine `style={{ transform: "..." }}` with `layout` prop.
4. **`layout` scale distortion** — children of a `layout` element appear stretched during resize animations. Fix: make immediate children also `motion` components with `layout`.
5. **onUpdate with `auto`** — `onUpdate` receives the string `"auto"` when animating height/width to auto, not a pixel value. Use `getComputedStyle` inside `requestAnimationFrame` if you need the resolved value.

### Anti-patterns
```tsx
// ❌ AnimatePresence child with no key
<AnimatePresence>
  {isVisible && <motion.div exit={{ opacity: 0 }}>...</motion.div>}
</AnimatePresence>

// ✅ Correct
<AnimatePresence>
  {isVisible && <motion.div key="item" exit={{ opacity: 0 }}>...</motion.div>}
</AnimatePresence>

// ❌ useScroll with raw number offset — silently ignored
const { scrollYProgress } = useScroll({ offset: [0, 1] });

// ✅ Correct string-pair syntax
const { scrollYProgress } = useScroll({ offset: ["start end", "end start"] });

// ❌ Manual will-change with Motion v12
style={{ willChange: "transform" }} // redundant, can cause double-compositing

// ✅ Let Motion handle it — remove manual will-change entirely
```

---

## Floating UI — v0.27.19

### Install
```
npm install @floating-ui/react
```

### Import paths
```ts
import {
  useFloating,
  autoUpdate,
  offset,
  flip,
  shift,
  arrow,
  size,
  hide,
  autoPlacement,
  FloatingArrow,
  FloatingPortal,
  FloatingFocusManager,
  useInteractions,
  useHover,
  useFocus,
  useClick,
  useDismiss,
  useRole,
} from "@floating-ui/react";
```

### Core API signatures
```ts
// ── useFloating ──────────────────────────────────────────────────────────────
useFloating(options?: {
  open?:            boolean;
  onOpenChange?:    (open: boolean) => void;
  placement?:       Placement;              // default: "bottom"
  strategy?:        "absolute" | "fixed";   // default: "absolute"
  middleware?:      Middleware[];           // ORDER MATTERS — see sharp edges
  whileElementsMounted?: (reference, floating, update) => () => void;
  // ✅ pass autoUpdate here — handles cleanup automatically
}): {
  x:              number;
  y:              number;
  placement:      Placement;               // may differ from requested (after flip)
  strategy:       "absolute" | "fixed";
  middlewareData: MiddlewareData;
  refs: {
    setReference:  (node: Element | null) => void;
    setFloating:   (node: HTMLElement | null) => void;
    reference:     React.MutableRefObject<Element | null>;
    floating:      React.MutableRefObject<HTMLElement | null>;
  };
  floatingStyles: CSSProperties;           // apply directly to floating element
  context:        FloatingContext;         // pass to useInteractions + FocusManager
  update:         () => void;
}

// ── Middleware pipeline — MUST be in this order ───────────────────────────────
middleware: [
  offset(value),        // 1st — shifts element away from reference
  flip(options?),       // 2nd — flips to opposite side on collision
  shift(options?),      // 3rd — slides along axis to stay in view
  size(options?),       // 4th — resizes floating element to fit
  arrow({ element }),   // 5th — positions arrow (needs ref to DOM node)
  hide(options?),       // 6th — hides when reference is out of view
]
// Deviation from this order = incorrect coordinates

// ── MiddlewareData shapes ─────────────────────────────────────────────────────
middlewareData: {
  arrow?:  { x?: number; y?: number; centerOffset: number };
  hide?:   { referenceHidden: boolean; escaped: boolean };
  flip?:   { index: number; overflows: Array<{ side: Side; amount: number }> };
  offset?: { x: number; y: number };
  shift?:  { x: number; y: number };
}

// ── FloatingFocusManager ──────────────────────────────────────────────────────
FloatingFocusManager: {
  context:         FloatingContext;  // required
  modal?:          boolean;          // default: true — false for tooltips/menus
  initialFocus?:   number | RefObject<HTMLElement>;
  returnFocus?:    boolean;          // default: true
  closeOnFocusOut?: boolean;         // default: true
}

// ── Interaction hooks ─────────────────────────────────────────────────────────
useHover(context, options?: { delay?: number | { open?: number; close?: number }; move?: boolean })
useFocus(context, options?: { keyboardOnly?: boolean })
useClick(context, options?: { toggle?: boolean })
useDismiss(context, options?: { outsidePress?: boolean; escapeKey?: boolean })
useRole(context, options?: { role?: "dialog" | "tooltip" | "menu" | "listbox" | "grid" | "tree" })

useInteractions(interactions: ReturnType<typeof useHover | useFocus | ...>[]):
  { getReferenceProps, getFloatingProps, getItemProps }
```

### Minimal working example
```tsx
import { useState, useRef } from "react";
import {
  useFloating, autoUpdate, offset, flip, shift, arrow,
  FloatingArrow, FloatingPortal, FloatingFocusManager,
  useInteractions, useHover, useFocus,
} from "@floating-ui/react";

export const Tooltip = ({ label, children }) => {
  const [open, setOpen] = useState(false);
  const arrowRef = useRef(null);

  const { refs, floatingStyles, context, middlewareData, placement } = useFloating({
    open,
    onOpenChange: setOpen,
    whileElementsMounted: autoUpdate,
    middleware: [
      offset(8),
      flip(),
      shift({ padding: 8 }),
      arrow({ element: arrowRef }),
    ],
  });

  const { getReferenceProps, getFloatingProps } = useInteractions([
    useHover(context, { delay: { open: 300 } }),
    useFocus(context),
  ]);

  return (
    <>
      <span ref={refs.setReference} {...getReferenceProps()}>{children}</span>
      <FloatingPortal>
        {open && (
          <FloatingFocusManager context={context} modal={false}>
            <div
              ref={refs.setFloating}
              style={floatingStyles}
              {...getFloatingProps()}
            >
              {label}
              <FloatingArrow ref={arrowRef} context={context} />
            </div>
          </FloatingFocusManager>
        )}
      </FloatingPortal>
    </>
  );
};
```

### Sharp edges
1. **Middleware order is not optional** — `arrow()` before `flip()` means the arrow is calculated for the wrong side. `shift()` before `flip()` means shift runs before knowing the final placement. Always: offset → flip → shift → size → arrow → hide.
2. **Arrow falsy check bug** — `if (middlewareData.arrow.x)` fails when `x === 0`. Always use `middlewareData.arrow.x != null`.
3. **Arrow axis** — only apply the axis relevant to the placement. For top/bottom placement, use `x` from middlewareData.arrow; for left/right, use `y`. Applying both makes the arrow jump to wrong corners.
4. **Missing `max-content`** — floating element must have `width: max-content` in CSS, otherwise it wraps and measurement returns wrong dimensions.
5. **Reactive middleware without memo** — `middleware={[offset(val)]}` inline recreates the array every render → infinite re-render loop. Memoize with `useMemo` if options depend on state.
6. **`display: none` measurement** — computing position of a hidden element returns zero dimensions. Render before positioning, use `visibility: hidden` instead.

### Anti-patterns
```tsx
// ❌ Falsy check on arrow coordinate — 0 is valid
if (middlewareData.arrow?.x) { ... }

// ✅ Correct null check
if (middlewareData.arrow?.x != null) { ... }

// ❌ Inline middleware array — infinite re-render if any option is reactive
useFloating({ middleware: [offset(8), flip(), shift()] })

// ✅ Memoize middleware
const middleware = useMemo(() => [offset(8), flip(), shift()], []);
useFloating({ middleware });

// ❌ Wrong middleware order
middleware: [arrow({ element: arrowRef }), flip(), offset(8)]

// ✅ Correct order
middleware: [offset(8), flip(), shift(), arrow({ element: arrowRef })]
```

---

## GSAP + ScrollTrigger — v3.12.5

### Install
```
npm install gsap @gsap/react
```

### Import paths
```ts
import { gsap }          from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { useGSAP }       from "@gsap/react";

// Registration — must happen before any ScrollTrigger usage
gsap.registerPlugin(ScrollTrigger, useGSAP);
```

### Core API signatures
```ts
// ── useGSAP (React integration) ──────────────────────────────────────────────
useGSAP(
  setup: (context: gsap.Context) => void | (() => void),
  dependencies?: { scope?: React.RefObject<Element>; dependencies?: any[] }
): { contextSafe: <T extends Function>(fn: T) => T }
// contextSafe wraps event handlers so animations created inside are tracked for cleanup

// ── gsap core ────────────────────────────────────────────────────────────────
gsap.to(target, vars):        Tween
gsap.from(target, vars):      Tween
gsap.fromTo(target, from, to): Tween
gsap.timeline(vars?):         Timeline
gsap.set(target, vars):       void   // immediate property set, no animation
gsap.context(fn, scope?):     gsap.Context

// ── ScrollTrigger.create / inline scrollTrigger vars ─────────────────────────
scrollTrigger: {
  trigger:            Element | string;          // element that activates the trigger
  start:              string | number | Function; // e.g. "top center", "top 80%", () => window.innerHeight
  end:                string | number | Function; // e.g. "bottom top", "+=500"
  scrub:              boolean | number;           // true = frame-perfect; number = smoothing lag in seconds
  pin:                boolean | string | Element; // pins element during scroll
  pinSpacing:         boolean;                   // default: true — adds spacer to prevent layout collapse
  markers:            boolean;                   // debug only — remove before production
  invalidateOnRefresh: boolean;                  // re-calculates on window resize — use with dynamic layouts
  refreshPriority:    number;                    // controls order when multiple triggers refresh
  onEnter:            (self: ScrollTrigger) => void;
  onLeave:            (self: ScrollTrigger) => void;
  onEnterBack:        (self: ScrollTrigger) => void;
  onLeaveBack:        (self: ScrollTrigger) => void;
  onUpdate:           (self: ScrollTrigger) => void;
}
```

### Minimal working example
```tsx
import { useRef } from "react";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { useGSAP } from "@gsap/react";

gsap.registerPlugin(ScrollTrigger, useGSAP);

export const PinnedHero = () => {
  const container = useRef(null);
  const card = useRef(null);

  const { contextSafe } = useGSAP(() => {
    gsap.timeline({
      scrollTrigger: {
        trigger: container.current,
        start: "top top",
        end: "+=600",
        scrub: 1,
        pin: true,
        invalidateOnRefresh: true,
      }
    })
    .to(card.current, { scale: 0.8, opacity: 0 });
  }, { scope: container });

  // contextSafe required for event-handler animations — tracked for cleanup
  const handleClick = contextSafe(() => {
    gsap.to(card.current, { rotation: 360, duration: 0.6 });
  });

  return (
    <div ref={container} style={{ height: "100vh" }}>
      <div ref={card} onClick={handleClick} style={{ width: 200, height: 200, background: "steelblue" }} />
    </div>
  );
};
```

### Sharp edges
1. **Never apply `scrollTrigger` to a tween inside a timeline** — apply it to the timeline itself. Tween-level triggers inside timelines cause conflicting playheads.
2. **`pin` + layout collapse** — GSAP injects a pin-spacer div to hold the space. If pinned element has `position: absolute` or negative margins, spacer calculates wrong height → layout jump. Wrap pinned content in a clean unstyled `<div>` and pin that.
3. **Dynamic content + stale triggers** — if DOM height changes after init (accordion opens, image loads), ScrollTrigger positions are stale. Call `ScrollTrigger.refresh()` after the change. Never call it on every render.
4. **Event handlers without `contextSafe`** — animations triggered by click/hover outside of `useGSAP`'s callback are not tracked for cleanup → memory leaks + StrictMode double-fire bugs. Always wrap with `contextSafe`.
5. **`autoAlpha` vs `opacity`** — `opacity: 0` makes element invisible but still interactive (blocks clicks). `autoAlpha: 0` also sets `visibility: hidden`. Use `autoAlpha` for hidden initial states.
6. **Hard-coded pixel `start` values** — `start: "top 400px"` breaks on window resize. Use `start: () => window.innerHeight * 0.8` for responsive triggers. Pair with `invalidateOnRefresh: true`.
7. **LCP element animation** — never animate the largest contentful paint element. It causes CLS (cumulative layout shift) and tanks Core Web Vitals. Animate below-the-fold content only.

### Anti-patterns
```tsx
// ❌ scrollTrigger on a tween inside a timeline
gsap.timeline().to(el, { x: 100, scrollTrigger: { trigger: el } })

// ✅ scrollTrigger on the timeline
gsap.timeline({ scrollTrigger: { trigger: el, scrub: 1 } }).to(el, { x: 100 })

// ❌ Event handler animation outside contextSafe
const handleClick = () => gsap.to(el, { scale: 1.2 }); // not tracked, leaks on unmount

// ✅ Wrapped with contextSafe
const handleClick = contextSafe(() => gsap.to(el.current, { scale: 1.2 }));

// ❌ Hard-coded start value
scrollTrigger: { start: "top 400px" }

// ✅ Function-based for responsiveness
scrollTrigger: { start: () => `top ${window.innerHeight * 0.8}px`, invalidateOnRefresh: true }
```

---

## Three.js — r165+

### Install
```
npm install three @types/three
```

### Import paths
```ts
import * as THREE from "three";
// Named imports also valid:
import { WebGLRenderer, Scene, PerspectiveCamera, ShaderMaterial,
         Mesh, TorusKnotGeometry, TextureLoader, Raycaster,
         Vector2, Clock, SRGBColorSpace } from "three";
```

### Critical removals — r150+ (frequently hallucinated as still valid)

```ts
// ❌ REMOVED in r150 — will throw at runtime
texture.encoding = THREE.sRGBEncoding;
renderer.physicallyCorrectLights = true;

// ✅ Correct replacements
texture.colorSpace = THREE.SRGBColorSpace;
// physicallyCorrectLights is now the default — remove the line entirely
// For r150–r162: renderer.useLegacyLights = false; (removed in r163)

// ❌ REMOVED in r159 — no WebGL1 skinned mesh support
// SkinnedMesh with WebGL1 renderer will not render

// ❌ DEPRECATED in r183
const clock = new THREE.Clock();  // still works but flagged
// ✅ Use instead:
const timer = new THREE.Timer();
```

### Core API signatures
```ts
// ── WebGLRenderer ────────────────────────────────────────────────────────────
new THREE.WebGLRenderer({
  canvas?:           HTMLCanvasElement;
  antialias?:        boolean;            // true for hero scenes
  alpha?:            boolean;            // true to see DOM background through canvas
  powerPreference?:  "high-performance" | "low-power" | "default";
})

renderer.setSize(width, height, updateStyle?: boolean);
// ⚠️ Pass updateStyle: false inside ResizeObserver to prevent infinite resize loop

renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
// ❌ Never: renderer.setPixelRatio(window.devicePixelRatio) — uncapped DPR on retina = 4× fill rate

// ── ShaderMaterial ───────────────────────────────────────────────────────────
new THREE.ShaderMaterial({
  uniforms: {
    uTime:  { value: 0 },          // float
    uMouse: { value: new THREE.Vector2(0, 0) },  // vec2
  },
  vertexShader:   string,
  fragmentShader: string,
  transparent?:   boolean,
  side?:          THREE.FrontSide | THREE.BackSide | THREE.DoubleSide,
})

// ⚠️ Uniform update — must set .value, never overwrite the uniform object
material.uniforms.uTime.value = performance.now() / 1000;
// ❌ Never: material.uniforms.uTime = performance.now() / 1000;

// ── Mouse → NDC conversion for Raycaster ─────────────────────────────────────
// Normalized Device Coordinates: x and y must be in range [-1, 1]
mouse.x =  (event.clientX / window.innerWidth)  * 2 - 1;
mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

raycaster.setFromCamera(mouse, camera);
const intersects = raycaster.intersectObjects(scene.children);
```

### Memory disposal — required on unmount
```ts
// Every geometry, material, and texture created must be explicitly disposed
// JS garbage collector does NOT free GPU memory automatically

geometry.dispose();
material.dispose();
texture.dispose();
renderTarget.dispose();
renderer.dispose();

// For scenes with many objects:
scene.traverse((obj) => {
  if (obj instanceof THREE.Mesh) {
    obj.geometry.dispose();
    if (Array.isArray(obj.material)) {
      obj.material.forEach(m => m.dispose());
    } else {
      obj.material.dispose();
    }
  }
});
```

### Minimal working example
```tsx
import { useEffect, useRef } from "react";
import * as THREE from "three";

export const ShaderHero = () => {
  const mountRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    if (!mountRef.current) return;

    const scene    = new THREE.Scene();
    const camera   = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 100);
    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });

    camera.position.z = 3;
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    mountRef.current.appendChild(renderer.domElement);

    const geometry = new THREE.TorusKnotGeometry(1, 0.3, 100, 16);
    const material = new THREE.ShaderMaterial({
      uniforms: { uTime: { value: 0 } },
      vertexShader: `
        varying vec2 vUv;
        void main() {
          vUv = uv;
          gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
        }
      `,
      fragmentShader: `
        uniform float uTime;
        varying vec2 vUv;
        void main() {
          vec3 col = 0.5 + 0.5 * cos(uTime + vUv.xyx + vec3(0.0, 2.0, 4.0));
          gl_FragColor = vec4(col, 1.0);
        }
      `,
    });

    const mesh = new THREE.Mesh(geometry, material);
    scene.add(mesh);

    const onResize = () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight, false); // false = no style feedback loop
    };
    window.addEventListener("resize", onResize);

    let rafId: number;
    const animate = (t: number) => {
      material.uniforms.uTime.value = t * 0.001;
      mesh.rotation.y += 0.005;
      renderer.render(scene, camera);
      rafId = requestAnimationFrame(animate);
    };
    rafId = requestAnimationFrame(animate);

    return () => {
      cancelAnimationFrame(rafId);
      window.removeEventListener("resize", onResize);
      geometry.dispose();
      material.dispose();
      renderer.dispose();
      mountRef.current?.removeChild(renderer.domElement);
    };
  }, []);

  return <div ref={mountRef} style={{ position: "absolute", inset: 0, pointerEvents: "none" }} />;
};
```

### Sharp edges
1. **`texture.encoding` still used** — the single most common Three.js hallucination. `sRGBEncoding` was removed in r150. Always use `texture.colorSpace = THREE.SRGBColorSpace`.
2. **Uncapped `devicePixelRatio`** — on 5K displays this is 3–4×, meaning 16× more pixels to fill. Always `Math.min(window.devicePixelRatio, 2)`.
3. **`renderer.setSize()` inside ResizeObserver without `false`** — the canvas CSS dimensions update → triggers ResizeObserver → resize fires again → infinite loop + browser crash. Always pass `false` as third arg in observers.
4. **Uniform object overwrite** — `material.uniforms.uTime = 1.0` destroys the uniform descriptor. Only ever set `.value`.
5. **Missing disposal** — GPU VRAM is not freed by GC. Every mesh added must be disposed on component unmount. Use the `scene.traverse` pattern for complex scenes.
6. **`physicallyCorrectLights`** — still hallucinated frequently. Removed. PBR is the default. Delete the line.

### Anti-patterns
```ts
// ❌ Removed API — runtime error in r150+
texture.encoding = THREE.sRGBEncoding;
renderer.physicallyCorrectLights = true;

// ✅ Correct
texture.colorSpace = THREE.SRGBColorSpace;
// (remove physicallyCorrectLights entirely)

// ❌ Uncapped pixel ratio
renderer.setPixelRatio(window.devicePixelRatio);

// ✅ Capped
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

// ❌ Uniform object overwrite — destroys the uniform
material.uniforms.uTime = performance.now();

// ✅ Set .value only
material.uniforms.uTime.value = performance.now() / 1000;

// ❌ setSize inside ResizeObserver without false — infinite loop
observer = new ResizeObserver(() => renderer.setSize(w, h));

// ✅ Pass false to suppress style update
observer = new ResizeObserver(() => renderer.setSize(w, h, false));
```
