# Prompt Interpretation Guide

How to decompose vague user prompts into comprehensive design briefs. This is the first step before any code is written.

---

## THE DECOMPOSITION FRAMEWORK

Every user prompt, no matter how sparse, contains enough information to construct a full design brief. The framework extracts six dimensions:

### 1. Intent Extraction

**Question:** What is the user actually trying to achieve?

Strip away the surface request. "Make a landing page" is not the intent. The intent is one of:
- Convert visitors into users (SaaS landing page)
- Build brand awareness (marketing site)
- Showcase work (portfolio)
- Sell a product (ecommerce)
- Tell a story (narrative experience)
- Provide information (documentation, blog)

**How to determine:** Look at industry keywords, product type, and action words in the prompt.

### 2. Mood Identification

**Question:** What feeling should this interface evoke?

Mood is rarely stated explicitly. Extract it from:
- Industry (fintech = trust, music = energy, wellness = calm)
- Word choice ("bold" = confident, "clean" = minimal, "fun" = playful)
- Target audience (enterprise = professional, Gen-Z = energetic)
- Competitor context (if mentioned)

**Mood spectrum:**
| Calm ← → Energetic |
| Minimal ← → Rich |
| Serious ← → Playful |
| Intimate ← → Bold |
| Traditional ← → Experimental |

### 3. Audience Detection

**Question:** Who will use this?

Audience determines density, motion intensity, and interaction complexity:
- **Enterprise/B2B**: Lower motion intensity, higher information density, conservative colors
- **Consumer/B2C**: Higher motion intensity, moderate density, expressive colors
- **Creative professionals**: High motion intensity, variable density, experimental layouts
- **General public**: Moderate motion intensity, clear hierarchy, accessible patterns
- **Technical users**: Low motion intensity, high data density, functional design

### 4. Gap Filling

**Question:** What did the user NOT specify that I need to decide?

Common gaps and how to fill them:

| Gap | Decision Rule |
|-----|--------------|
| No color specified | Derive from mood + industry. Fintech → navy + emerald. Music → dark + warm accent. Wellness → sage + cream. |
| No font specified | Default to Geist + Geist Mono for technical, Satoshi + Inter for general, Cabinet Grotesk for creative. |
| No layout specified | Hero: asymmetric split (not centered). Features: 2-column zigzag (not 3 equal cards). |
| No motion specified | Derive from motion personality system based on mood + audience. |
| No content specified | Use organic, realistic placeholder content. Never use Lorem Ipsum or "John Doe". |
| No interaction specified | Apply standard interaction sequences (button cycle, form cycle, modal cycle) per motion personality. |

### 5. Brief Construction

Assemble the extracted dimensions into a structured brief:

```
VISION: [One sentence describing the intended experience]

MOTION PERSONALITY: [Which personality] — [Why it fits]

COLOR DIRECTION: [Palette approach] — [Emotional reasoning]

LAYOUT STRATEGY: [Structural approach] — [Why it fits the content]

TYPOGRAPHY: [Font pairing] — [Scale approach]

CHOREOGRAPHY PLAN: [Which patterns, where, rhythm]

DOPAMINE MAP: [Where reward moments are placed]
```

### 6. Vision Announcement

Share the brief with the user in 2-3 sentences, then execute.

---

## EXAMPLE DECOMPOSITIONS

### Example 1: "Build a landing page for my AI startup"

**Intent:** Convert visitors into early adopters or sign-ups
**Mood:** Confident, innovative, forward-thinking
**Audience:** Tech-savvy early adopters, potential investors
**Motion Personality:** Snap — quick, decisive, no-nonsense energy matches startup pace
**Color Direction:** Dark base (Zinc-950) with electric blue accent — signals innovation without the AI purple cliché
**Layout Strategy:** Asymmetric hero (text left, visual right), 2-column zigzag features, single CTA focus
**Choreography:** Cascade entry for features, converge for hero, snap interaction cycles throughout
**Dopamine Map:** CTA hover (magnetic pull), feature card reveal (staggered), scroll-triggered metric counter

> "I'm reading this as a confident, conversion-focused launch page. Going with Snap motion for decisive energy — dark base with electric blue accent to signal innovation without the typical AI purple. Hero will be asymmetric with a converge entrance, features in a zigzag pattern with cascade reveals, and I'm placing dopamine moments at the CTA, feature reveals, and a scroll-triggered metrics section."

### Example 2: "I need a portfolio site for my photography"

**Intent:** Showcase work, attract clients, build personal brand
**Mood:** Intimate, artistic, contemplative
**Audience:** Potential clients, gallery curators, other creatives
**Motion Personality:** Whisper — barely-there transitions that let the photography speak
**Color Direction:** Pure white or near-black base, no accent color. The photos ARE the color.
**Layout Strategy:** Full-bleed hero image, masonry gallery, minimal text, generous whitespace
**Choreography:** Fade entries for images (no slides, no scales — just appearance), unfold for project details
**Dopamine Map:** Image hover (subtle zoom + title reveal), project click (smooth expand), scroll velocity affects parallax

> "This should be a gallery, not a website. Whisper motion — the transitions should be so subtle they're almost invisible, letting your work take all the attention. Pure white or near-black base, no competing colors. Full-bleed images with a masonry layout, and the only dopamine moments are the image reveals and hover states."

### Example 3: "Make a dashboard for our analytics tool"

**Intent:** Present complex data clearly, enable quick decisions
**Mood:** Confident, clear, efficient
**Audience:** Data analysts, product managers, executives
**Motion Personality:** Pulse — alive with real-time data, but controlled and professional
**Color Direction:** Light base (Zinc-50) with emerald accent for positive, rose for alerts. Functional color coding.
**Layout Strategy:** Dense grid with border-t dividers (no cards unless elevation is functional), monospace for numbers
**Choreography:** Wave entry for data cards, subtle ambient pulses for live metrics, snap for interactions
**Dopamine Map:** Metric update animations (number counting), alert appearance (gentle pulse), filter apply (satisfying snap)

> "This is a cockpit, not a showcase. Pulse motion personality — the dashboard should feel alive with data but never distracting. Light base with functional color coding, dense grid layout with dividers instead of cards, and dopamine moments at metric updates and filter interactions. Monospace for all numbers."

### Example 4: "Create something cool for our music festival"

**Intent:** Generate excitement, sell tickets, build hype
**Mood:** Energetic, bold, immersive
**Audience:** Music fans, 18-35, mobile-first
**Motion Personality:** Flow — continuous, connected, liquid energy matching music
**Color Direction:** Dark base with warm gradient accents (amber to coral). High energy, high contrast.
**Layout Strategy:** Full-screen hero with kinetic typography, horizontal scroll artist lineup, immersive schedule grid
**Choreography:** Wave for artist cards, flow-linked section transitions, velocity-reactive scroll effects
**Dopamine Map:** Artist card hover (3D tilt + audio preview), ticket CTA (particle burst on click), scroll-driven lineup reveal

> "This needs to feel like the festival before you even buy a ticket. Flow motion — everything connected, everything moving. Dark base with warm amber-to-coral gradients, kinetic typography in the hero, and a horizontal-scrolling artist lineup. The dopamine is everywhere: artist cards that respond to hover with audio previews, a ticket button that celebrates when clicked, and a lineup that reveals as you scroll."

---

## EDGE CASES

### When the prompt is extremely vague
"Make it look good" → Ask ONE clarifying question about the product/industry, then proceed with default assumptions based on the answer.

### When the user contradicts themselves
"I want minimal but also packed with features" → NO — minimal and feature-dense are opposing goals. I'll prioritize clarity with progressive disclosure: show the essentials, reveal complexity on demand.

### When the user requests a known anti-pattern
"Put the nav in the center with a hamburger on desktop" → NO — centered navigation with desktop hamburger breaks established mental models and increases cognitive load. I'll use a standard left or top navigation pattern that users already know.

### When the user says "make it like [specific site]"
Study the referenced site's motion personality, color approach, and layout strategy. Apply those patterns, not a visual copy. The reference informs the brief, it doesn't replace it.

---

## QUICK DECOMPOSITION CHEAT SHEET

| If the user says... | Intent is... | Mood is... | Motion personality is... |
|---------------------|-------------|-----------|------------------------|
| "landing page" | Convert | Confident | Snap |
| "portfolio" | Showcase | Intimate | Whisper |
| "dashboard" | Inform | Clear | Pulse |
| "creative site" | Impress | Bold | Flow |
| "ecommerce" | Sell | Trustworthy | Snap |
| "blog" | Read | Calm | Whisper |
| "app" | Enable | Efficient | Snap |
| "event page" | Excite | Energetic | Flow |
| "nonprofit" | Inspire | Earnest | Breathe |
| "SaaS" | Convert | Professional | Snap |
