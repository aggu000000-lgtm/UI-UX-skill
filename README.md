# ui-ux-skills

A frontend design skill that escapes AI-default aesthetics through a 5-layer pipeline: intent extraction → aesthetic commitment → slop prevention → technical overdrive → recursive self-audit.

Built for the Crystal ecosystem.

---

## Why this exists

Default AI frontend output converges on the same aesthetic every time: Inter font, purple gradients, three equal-height cards, centered hero. That's the statistical mean of the training data — not a design decision.

This skill breaks that gravity by encoding three things a real senior frontend developer does:

1. **Commit to a specific visual language** — not "modern and clean" but a named, justified aesthetic with exact token values
2. **Engineer with precision** — two-tier OKLCH token system, 8px spatial grid, fluid type scale, spring physics. No magic numbers.
3. **Self-correct before delivering** — a structural + fidelity audit pass catches failures before the user sees them

---

## What the skill enforces

### Aesthetic direction
- Declares one extreme aesthetic (Brutalist Editorial, Retro-Futuristic, Organic Luxury, etc.) before writing a single line
- Full token fingerprint committed upfront: font pair, OKLCH palette, layout rule
- Hard bans: Inter, Roboto, purple-on-white gradients, pure `#000000`/`#FFFFFF`, uniform card grids, navbar-with-logo-left

### Technical standards
- **Color**: OKLCH primitives → semantic tokens, two-tier architecture, dark mode via `[data-theme]` attribute swap only
- **Typography**: `clamp()`-based fluid type scale across 6 steps, no hardcoded `px` font sizes
- **Spacing**: 8px baseline grid, all values on `--space-*` tokens
- **Responsive**: Mobile-first, container queries for components, viewport breakpoints for layout shells only
- **Motion**: Spring physics for interactions, staggered entrance sequences, `prefers-reduced-motion` wrapping
- **3D**: Escalation matrix — Three.js only when semantically justified, never as a default

### Self-audit (mandatory before delivery)
Layer 5A (visual): hierarchy, rhythm, slop detection, token consistency, responsive integrity, emotional register  
Layer 5B (fidelity): structural DOM match, layout accuracy, visual-semantic fidelity, self-healing protocol

---

## Honest ceiling

This skill reliably produces **L3.5→L4** output for single-file artifacts. It prevents the most common failure modes and forces lower-probability, higher-quality aesthetic choices.

It does not produce L5 output. L5 requires real visual judgment — knowing when a composition *feels* wrong even when the tokens are correct. A skill file can teach rules but not when to break them intentionally.

---

## File structure

```
ui-ux-skills/
├── SKILL.md        ← The actual skill (this is what Claude loads)
├── README.md       ← This file
└── references/     ← WIP: library API sources, component contracts, a11y patterns
    └── ...
```

The references folder is actively being built. Once complete, it will ground library-specific code in raw API signatures — reducing hallucination on Radix, Motion, Floating UI, etc.

---

## How to use

### Claude.ai
Upload `SKILL.md` via **Settings → Skills → Upload skill**.

### Claude Code
```bash
cp -r ui-ux-skills/ /your-project/.claude/skills/ui-ux-skills/
```

### Cursor / Windsurf
Paste `SKILL.md` contents into `.cursorrules` or your system prompt file.

### Any agent
Copy `SKILL.md` contents into the system prompt. The skill is plain markdown — works everywhere.

---

## How it relates to Anthropic's `frontend-design` skill

Same SKILL.md. The `ui-ux-skills` wrapper adds installation docs, an honest README, and a references folder for library grounding (in progress). If you already have `frontend-design` loaded, this is additive — not a replacement.

The comparison table in a previous version of this README claimed features the references folder doesn't contain yet. That was wrong. This version doesn't.

---

## Stack coverage

Currently solid on: vanilla HTML/CSS/JS, React (single-file artifacts).

References folder (WIP) will extend grounding to: Next.js App Router, Vue 3, Svelte 5, Tailwind v4, Radix UI, Framer Motion, Floating UI.

---

## License

MIT — use, modify, distribute freely.  
Attribution appreciated, not required.

---

_Built for the Crystal ecosystem._
