# UI/UX Skill — Designer-First Frontend Generation

> Stop generating UIs. Start designing them.

A complete skill system that makes AI generate frontend interfaces the way a senior product
designer would — with emotional intent, systematic design decisions, and production-grade
execution. Works for beginners who want stunning results and professionals who demand precision.

---

## What This Is

An AI skill system (a collection of `SKILL.md` files) that rewires how language models approach
frontend generation. Instead of the typical pattern — _"write CSS that makes this component look
good"_ — this skill forces a designer's cognitive sequence:

1. **Who is this for and how should they feel?**
2. **What is the single most important action on this screen?**
3. **What design canon captures this product's personality?**
4. **How do all five states of every component behave?**
5. **Does this pass three different critics' scrutiny?**

The result: interfaces that get screenshotted, not just shipped.

---

## File Structure

```
ui-ux-skill/
├── README.md                 ← You are here
├── SKILL.md                  ← Main trigger file — the 6-phase system
├── DESIGN-SYSTEMS.md         ← Color (OKLCH), typography, spacing, motion, design canons
├── INTERACTION-DESIGN.md     ← Component states, micro-copy, responsive, accessibility
├── CRITIQUE-SYSTEM.md        ← Three critics, veto protocol, anti-pattern gallery
├── MODEL-CALIBRATION.md      ← Per-model activation (Claude, Gemini, GPT, Qwen)
└── research/
    └── RESEARCH-LOG.md       ← Full research backing this skill
```

---

## How to Use

### Option A: Full skill (any AI editor)

Copy the entire `ui-ux-skill/` folder into your project and reference it:

```
@SKILL.md — follow the full 6-phase system
```

### Option B: Cursor / Windsurf

Add to `.cursorrules` or project rules:

```
Read and strictly follow ui-ux-skill/SKILL.md for all frontend work.
Reference DESIGN-SYSTEMS.md for tokens, INTERACTION-DESIGN.md for component patterns.
```

### Option C: Claude Projects

Add `SKILL.md` as a project document. Reference other files as needed per task type.

### Option D: Just SKILL.md

If you only want the phases, `SKILL.md` alone is a significant upgrade. The supplementary
files are consulted when depth is needed — the phases are the core system.

---

## The Six Phases

| Phase | Name                | What it produces                                                    |
| ----- | ------------------- | ------------------------------------------------------------------- |
| 0     | Designer Identity   | Role declaration + creative studio frame                            |
| 1     | Design Brief        | Emotional arc, hierarchy of one, canon reference, surprise decision |
| 2     | Design System       | OKLCH tokens, fluid type scale, motion personality, copy voice      |
| 3     | Component Design    | All 5 states per component, real copy                               |
| 4     | Technical Execution | Production-grade code, accessible, responsive, performant           |
| 5     | Critique Pass       | Three named critics, screenshot test, confidence tier               |

---

## Why This Beats taste-skill

| Dimension           | taste-skill                | ui-ux-skill                                                |
| ------------------- | -------------------------- | ---------------------------------------------------------- |
| Core approach       | Blacklist (what NOT to do) | Positive framework (what TO become)                        |
| Design brief        | None                       | Required — emotional arc + hierarchy + reference           |
| UX coverage         | Visual only                | States, copy, responsive, a11y, performance                |
| Aesthetic direction | 3 dials (1-10)             | 6 named canons with design philosophy                      |
| Interaction states  | Not mentioned              | Enforced — all 5 per component                             |
| Micro-copy          | Not mentioned              | First-class design material with voice system              |
| Accessibility       | Mentions WCAG              | OKLCH math, ARIA patterns, keyboard nav                    |
| Responsiveness      | Not mentioned              | Mobile-first philosophy + container queries                |
| Model calibration   | Not mentioned              | Per-model activation for Claude/Gemini/GPT/Qwen            |
| Critique system     | Checklist                  | Three named critics, named failure modes, confidence tiers |
| File structure      | 5 flat files               | 5 deep reference files + research folder                   |

---

## Design Philosophy

**A developer makes things work and then makes them look good.**  
**A designer makes things feel right and then makes them work.**  
**This skill makes the AI the designer.**

The insight this skill is built on: every existing AI frontend skill (including taste-skill and
most system prompts) trains models to be _better coders of UIs_. None of them reframe the model's
cognitive identity. The designer's mental model — "what should the user feel → what does that
emotion look like spatially → what components manifest that feeling" — is entirely absent from
the prompt engineering field.

This skill fixes that at the root.

---

## Research

Full research backing available in `research/RESEARCH-LOG.md`. Covers: taste-skill analysis,
frontier model capability benchmarks (Claude 4.6, Gemini 3, GPT-5, Qwen3), existing skill gap
analysis, and design principles citations (WCAG 2.2, Rams, OKLCH, container queries).
