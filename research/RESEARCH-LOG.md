# Research Log — UI/UX Skill

**Date:** March 2026  
**Researcher:** Claude Sonnet 4.6  
**Purpose:** Backing research for the UI/UX Skill design

---

## 1. COMPETITIVE ANALYSIS — TASTE-SKILL (lexnlin)

**Repository:** github.com/Leonxlnx/taste-skill  
**Stars:** 2,400+ | **Forks:** 156+  
**Significance:** Most-starred AI frontend skill in the community as of March 2026.

### What taste-skill does well

- Three-dial configuration system (DESIGN_VARIANCE, MOTION_INTENSITY, VISUAL_DENSITY)
- Stops the most egregious slop patterns (Inter, blue CTAs, generic card grids)
- Multi-skill structure (taste + redesign + output + luxury + minimalist)
- Output-skill concept (forces completion, no placeholders) is genuinely useful

### What taste-skill gets wrong (identified flaws)

**1. Three-dial system is dimensionally flat**
Unitless 1-10 scalars applied universally have no contextual grounding. DESIGN_VARIANCE=8 for a
fintech dashboard and DESIGN_VARIANCE=8 for a portfolio should produce completely different output.
The dials give the illusion of control without capturing design intent.

**2. Anti-pattern approach has diminishing returns**
Taste-skill is primarily a blacklist — DO NOT use Inter, DO NOT use blue, DO NOT use heavy shadows.
This is reactive design. It chases current AI slop patterns, not design principles. As models
improve, the blacklist becomes stale. It treats the symptom, not the disease.

**3. No positive generative framework**
The skill tells models what to avoid, but not what to _become_. Without committing to a specific
aesthetic direction upfront, models avoid the obvious slop and land in the "less bad average."
A named aesthetic commitment (Linear Dark, Editorial Luxury) is more generative than prohibitions.

**4. Zero UX or semantic layer**
Taste-skill covers visual aesthetics entirely. No information architecture, no user flow, no
interaction semantics. A visually stunning page with broken UX is a taste-skill success.

**5. No accessibility enforcement**
WCAG contrast ratios, ARIA, keyboard navigation, focus states — absent entirely.
The minimalist-skill actually bans things that help accessibility (colored backgrounds, heavy shadows).

**6. No responsiveness**
No mobile-first thinking, no responsive breakpoints, no container queries. The skill is
desktop-centric by omission.

**7. "One accent color" oversimplification**
Mature design systems need semantic color roles: primary, destructive, warning, success, info.
A flat ban on multiple accents breaks any app with form validation states.

**8. Stack ambiguity**
Tailwind-specific rules appear in a stack-agnostic skill. `shadow-md` means nothing in vanilla CSS.

**9. No skill coordination protocol**
When combining taste + redesign + output, no conflict resolution mechanism exists.
The skills are additive without composition logic.

---

## 2. MODEL CAPABILITY RESEARCH (Frontier Models, March 2026)

### Claude Sonnet 4.6 / Opus 4.6

- Strong native design intuition per community testing
- Responds powerfully to emotional/narrative framing
- Best model for agentic coding tasks (reliability)
- Frontier performance on frontend generation with less hand-holding than predecessors

### Gemini 2.5 Pro / 3 Pro

- Community reports: highest raw visual quality among current frontier models
- "SVG logic and padding consistency are way better now" (user comparison, 50 test sites)
- Noted as "a level less AI-designed" with better contrast and balance vs older models
- Main failure: "endless thinking loop" on complex tasks (stuck in reasoning before coding)
- Preferred for visual tasks; Claude preferred for agentic/backend tasks (split workflow)

### GPT-5 family

- Community consensus: "textbook AI slop" for UI without heavy direction
- Gemini consistently edges ahead in pure design quality comparisons
- Strong structural code quality; weak native aesthetic judgment
- Most susceptible to: Inter defaults, blue CTAs, generic card grids

### Qwen3 / Qwen3.5 (235B-A22B flagship)

- Competitive with DeepSeek-R1, o1, o3-mini, Gemini 2.5 Pro on coding/math
- Strong coding precision; weak native aesthetic judgment
- No community examples specifically praising Qwen for visual UI quality
- Needs maximum explicit scaffolding for design tasks

---

## 3. STRUCTURAL ANALYSIS — EXISTING SKILLS

### Reviewed: /mnt/skills/user/frontend-design/SKILL.md (our existing skill)

**Strengths:**

- Excellent Layer 3 (technical): OKLCH color system, fluid type scale, 8px grid, spring motion
- Good Layer 2 (anti-slop): Named bans on Inter, blue gradients, uniform card grids
- Layer 5 (critique): Exists as a checklist — useful but not deep enough
- Emotional Narrative Framing section is strong — unique in the field

**Missing:**

- Phase 0: No designer identity activation
- Phase 1: No structured design brief — emotional arc, hierarchy of one, surprise decision
- Phase 3: No interaction states — only happy path is designed
- Micro-copy: Not mentioned
- Responsive: Only partially addressed (fluid type, not layout/breakpoints)
- Accessibility: Mentioned but not specified (one line about WCAG AA)
- Model calibration: Not addressed

---

## 4. DESIGN PRINCIPLES RESEARCH

### Dieter Rams — 10 Principles of Good Design

Primary principle used in critique: "As little design as possible" (principle 10).
Secondary: "Good design is honest" (no decoration masquerading as function).
Application: The Reductionist critic in CRITIQUE-SYSTEM.md.

### WCAG 2.2 Requirements

- AA minimum: 4.5:1 for normal text, 3:1 for large text and UI components
- WCAG 2.5.5: Minimum touch target 44×44px
- WCAG 2.4.11 (2.2 new): Focus appearance requirements

### Container Queries (CSS Containment Level 3)

- Now supported in all major browsers (Chrome 105+, Firefox 110+, Safari 16+)
- Prefer over media queries for component-level responsive behavior
- `container-type: inline-size` is the standard declaration

### OKLCH Color Space

- Perceptually uniform: equal L values appear equally bright
- Enables reliable contrast math (∆L ≥ 40 for AA contrast)
- Dark mode inversions stay mathematically consistent
- Native in CSS since 2022, now universally supported

---

## 5. IDENTIFIED GAPS — WHAT NO EXISTING SKILL ADDRESSES

The fundamental gap in all existing AI frontend skills (including taste-skill and our own):

**They train models to be better coders of UIs, not to think like designers who code.**

Developer mental model: "What components do I need → what should they look like?"
Designer mental model: "What should the user feel → what does that emotion look like spatially → what components manifest that feeling?"

Specific gaps addressed by the new UI/UX Skill:

1. Designer identity activation (Phase 0) — reframes the model's role before any code
2. Structured design brief (Phase 1) — emotion → hierarchy → reference → surprise
3. All five interaction states per component (Phase 3) — not just happy path
4. Micro-copy as design material (INTERACTION-DESIGN.md) — copy voice system
5. Mobile-first responsive philosophy (INTERACTION-DESIGN.md) — breakpoint vocabulary
6. Named critic veto system (CRITIQUE-SYSTEM.md) — three specific critics, three failure modes
7. Model-specific activation (MODEL-CALIBRATION.md) — calibrated per model
8. Design canon vocabulary (DESIGN-SYSTEMS.md) — positive direction, not prohibition
9. Performance as UX (INTERACTION-DESIGN.md) — loading choreography
10. WCAG AA enforcement with mathematical basis (OKLCH ∆L rule)

---

## 6. FILE STRUCTURE DECISION

**Decision: Multi-file with SKILL.md as trigger + 4 reference docs + research folder**

**Why not single file:**

- Taste-skill's single-file approach limits depth; everything has to be sparse to stay readable
- Claude reliably follows multi-file skills when SKILL.md clearly references them
- Different phases require different reference density (Phase 0: sparse; Phase 4: code-heavy)
- Separation allows updating individual domains (e.g., update MODEL-CALIBRATION.md
  when new model releases without touching the core phases)

**File purposes:**

- `SKILL.md` → Trigger file, phase overview, north star principle
- `DESIGN-SYSTEMS.md` → Color, typography, spacing, motion, canons, component starters
- `INTERACTION-DESIGN.md` → States, micro-copy, responsive, accessibility, performance
- `CRITIQUE-SYSTEM.md` → Three critics, veto protocol, anti-patterns gallery
- `MODEL-CALIBRATION.md` → Per-model activation strategies

**Beats taste-skill structure in:**

- Depth per domain (not compressed into single file)
- Positive frameworks (canons) vs. purely negative (blacklists)
- UX coverage (states, copy, responsive, a11y, performance)
- Model-aware guidance
- Philosophical coherence (designer identity as north star)
