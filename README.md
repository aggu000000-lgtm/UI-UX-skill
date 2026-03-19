# UI-UX Skills

## What are 'UI-UX Skills'?

With the rise of `skills` in the AI agents community, we are introducing `UI-UX Skills`.  
Anthropic and the community have already created many skills for frontend — this skill is built from the ground up to serve the **full versatility of the frontend coding community**.

- This skill makes the AI completely focus on **UI** (visual design) _and_ **UX** (interaction design, flow, accessibility) with literally `pixel-by-pixel` accuracy. It supercharges the AI to deliver frontend experiences at the level of an **L5 frontend developer**.
- It ships with **references** — open-source raw library sources and L5-level code patterns — so the AI never hallucinates APIs, component APIs, or CSS behavior while coding.
- Built specifically for **Claude Code**, it plugs directly into the agent loop and activates automatically on any frontend task without requiring manual invocation.

---

## Why this skill exists

Most AI frontend output converges on the same aesthetic: purple gradients, Inter font, predictable card layouts. That's L1 output.

An L5 frontend developer does three things differently:

1. **Intentional design direction** — Every project has a clear visual language. Not just "clean" or "modern" but a specific, committed aesthetic POV.
2. **Engineering precision** — No magic numbers, no dead CSS, no `!important` chains. Token systems, component contracts, and layered architecture.
3. **UX as a first-class concern** — Interaction states, micro-animations, accessibility, focus management, keyboard navigation — not afterthoughts.

This skill encodes all three directly into the AI's context.

---

## What's inside

```
ui-ux-skills/
├── SKILL.md                  # Core skill instructions (loads on trigger)
├── README.md                 # This file
├── references/
│   ├── design-tokens.md      # Token-first CSS architecture patterns
│   ├── component-anatomy.md  # L5-level component structure (React + Vanilla)
│   ├── animation-bible.md    # Motion design principles + code patterns
│   ├── accessibility.md      # WCAG 2.2 AA patterns, ARIA, keyboard nav
│   ├── typography.md         # Font pairing system, type scale, variable fonts
│   └── library-sources.md    # Raw API reference for Tailwind, Radix, Motion, etc.
└── assets/
    ├── token-template.css    # Base CSS variables template
    └── component-stubs/      # Stripped starter components per pattern type
```

---

## Skill triggers

This skill activates when you ask Claude Code to do **anything visual or interactive**:

| You say                                  | Skill activates |
| ---------------------------------------- | --------------- |
| `build a landing page for X`             | ✅              |
| `make a dashboard with charts`           | ✅              |
| `create a settings page`                 | ✅              |
| `improve the UX of this form`            | ✅              |
| `refactor this component to look better` | ✅              |
| `add micro-interactions to my navbar`    | ✅              |
| `this UI looks bad, fix it`              | ✅              |
| `build me something beautiful`           | ✅              |

---

## What the skill enforces

### Design

- **No generic aesthetics.** Inter + purple gradient = rejected. Every output commits to a specific, contextually justified visual language.
- **Token-first.** All color, spacing, type, radius, and shadow as CSS variables — never hardcoded.
- **Typography as a design element.** Variable fonts, display/body pairings, tracked uppercase, fluid type scales.
- **Depth and atmosphere.** Gradients, noise textures, blur layers, shadows — used intentionally, not decoratively.

### Engineering

- **Zero dead CSS.** Every rule serves a purpose.
- **Component contracts.** Props/variants are explicit. No implicit behavior.
- **Layered architecture.** Tokens → primitives → components → compositions. No skipping layers.
- **No hallucinated APIs.** The `library-sources.md` reference is loaded before any library code is written, grounding every API call in real docs.

### UX

- **Interaction states are mandatory.** Every interactive element ships with hover, focus, active, disabled, and loading states.
- **Motion with purpose.** Animations communicate state changes — they don't just look cool.
- **Accessibility from line one.** Semantic HTML, ARIA where needed, keyboard navigation, focus traps for modals, color contrast ≥ 4.5:1.
- **Responsive as a default.** Mobile-first unless explicitly told otherwise.

---

## Supported stacks

The skill covers all major frontend environments:

- **Vanilla HTML/CSS/JS** — Single-file and multi-file
- **React** — Hooks, compound components, context patterns
- **Next.js** — App router, RSC, server actions
- **Vue 3** — Composition API, `<script setup>`
- **Svelte 5** — Runes, snippets
- **CSS frameworks** — Tailwind v4, UnoCSS, vanilla tokens

Library integrations with raw-source references:

- **Radix UI / shadcn/ui** — Headless primitives
- **Motion (Framer Motion)** — Animation engine
- **Vaul / cmdk** — Drawer, command palette
- **Floating UI** — Tooltips, popovers, dropdowns
- **Three.js** — 3D UI elements when relevant

---

## Installation

### Via Claude Code (recommended)

```bash
claude mcp install ui-ux-skills
```

Or drag-and-drop the `.skill` file into the Claude Code skills panel.

### Manual

Copy the `ui-ux-skills/` folder into your project's `.claude/skills/` directory:

```bash
cp -r ui-ux-skills/ /your-project/.claude/skills/ui-ux-skills/
```

Claude Code will auto-detect and load it on the next session.

---

## How it differs from `frontend-design` (Anthropic's official skill)

|                     | `frontend-design`      | `ui-ux-skills`                       |
| ------------------- | ---------------------- | ------------------------------------ |
| Aesthetic direction | Bold, varied, creative | Same, but enforced via design system |
| Library grounding   | General guidance       | Raw API sources loaded at runtime    |
| UX coverage         | Minimal                | Full: states, a11y, keyboard, motion |
| Token architecture  | Not specified          | Mandatory token-first system         |
| Component contracts | Not specified          | Explicit variant/prop contracts      |
| Stack coverage      | HTML/CSS/JS, React     | All major stacks                     |
| Reference files     | None                   | 6 domain-specific reference docs     |

Both skills can coexist. `ui-ux-skills` is additive — it layers engineering precision and UX rigor on top of creative direction.

---

## Contributing

Open a PR with:

- A new reference file under `references/` (follow the existing format)
- Updated `SKILL.md` if the new reference needs a loading instruction
- A test prompt + expected output in `tests/`

Library source references should link to the official raw GitHub source, not docs pages — the AI needs API signatures, not prose.

---

## License

MIT — free to use, modify, and distribute.  
Attribution appreciated but not required.

---

_Built for the Crystal ecosystem and the broader Claude Code community._
