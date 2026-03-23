# Model Calibration Guide

Different frontier models have different design failure modes and different activation levers.
This document tells you how to calibrate prompts for each model to extract best-quality UI output.
If you're using this skill to generate prompts for other models, adjust accordingly.

---

## CLAUDE (Sonnet 4.6 / Opus 4.6)

**Strength:** Strong native design intuition. Responds powerfully to emotional framing and named
aesthetics. Produces coherent design systems when given a clear identity anchor.
Opus is better for complex, multi-screen systems. Sonnet is faster, still excellent for components.

**Failure modes:**

- Tends toward over-animation when no motion personality is specified
- Can produce "safe" layouts if not pushed toward a specific canon
- Occasionally defaults to Inter/Space Grotesk if font choice isn't enforced

**Activation levers:**

- Emotional narrative framing hits hardest: _"a first-time user who feels overwhelmed should
  leave feeling like an expert"_ → Claude will build toward that arc
- Named canonical references trigger specific aesthetic knowledge: "Linear Dark canon" or
  "Stripe editorial" produces more targeted output than generic instructions
- Explicit role declaration in Phase 0 is high-leverage for Claude specifically

**Optimal prompt structure for Claude:**

```
1. Role declaration (Phase 0) — front-load this
2. Emotional brief (Phase 1) — the user's journey
3. Canon name (Phase 1) — one named reference
4. Technical constraints (Phase 4) — OKLCH, tokens, grid
5. Critique bar (Phase 5) — "Awwwards-worthy or iterate"
```

---

## GEMINI 2.5 PRO / 3 PRO

**Strength:** Strongest raw visual design quality among current frontier models (2026 benchmarks).
Better padding consistency, contrast balance, and layout variation than GPT or older Claude.
Responds well to aesthetic instructions without requiring extensive rule scaffolding.

**Failure modes:**

- "Endless thinking loop" on complex multi-component tasks: generates long reasoning chains
  before producing code; can get stuck planning
- Autonomous/agentic mode is less reliable than Claude
- Occasional over-complexity in component architecture

**Activation levers:**

- Scope containment is critical: break complex tasks into single-component subtasks
- Front-load the aesthetic brief with high specificity: Gemini executes declared direction
  faithfully without much coaxing
- "Think then code" split: ask for a brief design rationale (2-3 sentences), then code.
  This satisfies the reasoning loop and gets to output faster
- Explicit completion signal: "Complete the full implementation. Do not summarize remaining steps."

**Optimal prompt structure for Gemini:**

```
1. Scoped task definition — specific, single-component focus
2. Aesthetic direction — named reference + 3 adjective voice
3. Technical requirements — tokens, states, responsive breakpoints
4. "Complete the full implementation" — prevents truncation
5. Design rationale first — 2-3 sentences then code
```

---

## GPT-4.5 / GPT-5

**Strength:** Strong structural code quality. Good component architecture. Follows explicit rules
reliably. Best for complex application logic that requires coordinated state.

**Failure modes:**

- Native UI output is "textbook AI slop" without heavy direction
- Defaults to: Inter font, `#3B82F6` blue CTAs, generic card grids, box-shadow everywhere
- No native aesthetic intuition — requires explicit positive examples, not just prohibitions
- Copy defaults to generic tech startup voice: "Powerful. Simple. Fast."

**Activation levers:**

- Anti-pattern rules have higher impact on GPT than on Claude/Gemini (blacklists work better here)
- Positive example snippets in the prompt are high-leverage: show it one beautiful CSS block
  and it will pattern-match to that quality
- Explicit font specification is mandatory: if not specified, Inter appears
- Step-by-step micro-instructions work better than holistic design philosophy

**Optimal prompt structure for GPT:**

```
1. Anti-pattern blacklist — explicit bans (font, color, layout)
2. Positive example snippet — one beautiful CSS/HTML block as reference
3. Token specification — exact OKLCH values, exact font CDN link
4. Step-by-step build order — component by component, state by state
5. Completion enforcement — "Do not use placeholders. Write every line."
```

---

## QWEN 3 / QWEN 3.5

**Strength:** Excellent coding precision. Strong at following complex technical specifications.
Competitive with top models on structured implementation tasks.

**Failure modes:**

- No native aesthetic judgment — needs maximum scaffolding for visual quality
- Copy quality is generic; needs explicit voice specification
- Motion/animation defaults to basic transitions; spring physics need explicit implementation
- Responsive behavior is often omitted unless explicitly required

**Activation levers:**

- Full technical spec upfront: exact values, exact properties, exact patterns
- Reference code snippets embedded in the prompt are highest-leverage
- Explicit responsive requirement: "include breakpoints at 375px, 768px, 1280px"
- Copy specification: "write real, contextual copy for a [domain] product. Voice: [3 adjectives]"
- Motion: provide the exact easing function; don't describe the feeling

**Optimal prompt structure for Qwen:**

```
1. Full token specification — exact OKLCH values, exact spacing scale
2. Reference CSS blocks — copy the DESIGN-SYSTEMS.md sections verbatim
3. Component state table — list all 5 states per component explicitly
4. Responsive breakpoint table — specify behavior at each breakpoint
5. Copy examples — one example per copy type (CTA, error, placeholder)
6. Completion contract — "No placeholders. No TODOs. Full implementation."
```

---

## DEEPSEEK R2 / V3

**Strength:** Very strong code quality. Good at complex logic. Fast iteration.

**Failure modes:** Similar to Qwen — needs explicit scaffolding for visual quality.
Motion and animation are consistently underspecified without direction.

**Activation levers:** Same pattern as Qwen. Respond well to structured specification tables.

---

## UNIVERSAL ACTIVATION PRINCIPLES

Regardless of model, these always improve output quality:

**1. The Identity Frame**
_"You are a senior product designer at a design-led studio. You are not a developer adding styles.
You are a designer who writes code as a medium."_

**2. The Reference Anchor**
_"The visual reference is [Linear / Stripe / Vercel / Arc / Notion / Figma]. Not to copy —
to capture the quality of intentionality they embody."_

**3. The Anti-Deadline Pressure**
_"Do not optimize for speed of output. Optimize for quality of result. A slower, better answer
is always preferred to a fast, generic one."_

**4. The Completion Contract**
_"Every interactive element has all five states implemented. Copy is real and contextual.
No placeholders. No TODO comments. This is production code."_

**5. The Critique Threat**
_"Before delivering, critique your output as a senior Figma designer would. If anything
would make them cringe, fix it first."_
