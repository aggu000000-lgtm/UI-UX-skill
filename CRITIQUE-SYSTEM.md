# Critique System

**The hardest part of design is knowing when you're done — and when you're deceiving yourself.**

This document is the mandatory final step before any UI output is delivered.
It is not a checklist. It is a simulated design review with three different critics
who each see different failure modes.

---

## THE THREE CRITICS

### CRITIC 1 — The Reductionist (Dieter Rams)

_"Is it as little design as possible?"_

Rams's 10 principles exist to identify what has been _added_ that doesn't belong.
This critic removes. They are suspicious of every element. They ask:

- "Why is this here? What breaks if it's removed?"
- "Is this decoration or communication?"
- "Does this element create meaning, or does it just fill space?"

**Common failures this critic catches:**

- Decorative borders that add no hierarchy
- Background gradients that don't communicate depth or temperature
- Animations that delight the designer but distract the user
- More than one accent color (only one is signal — the rest is noise)
- Icon + label when just the label is clearer
- Three levels of type hierarchy when two would work

**Pass condition:** Every element in the composition has a job.
**Fail condition:** The designer added anything because it "looked nice."

---

### CRITIC 2 — The Precision Engineer (Rasmus Andersson)

_"Does every pixel have a reason?"_

Rasmus Andersson (Figma designer, creator of the Inter typeface, designer of Spotify's UI system)
embodies mathematical precision in interface craft. This critic measures.

- "Are spacing values consistent? Can I see the grid?"
- "Is the type hierarchy clear at a glance — size, weight, color?"
- "Does the visual weight of elements match their importance hierarchy?"
- "Is motion purposeful — does it communicate state, or is it just movement?"
- "Are interactive states complete, or is hover the only one defined?"

**Common failures this critic catches:**

- Misaligned elements (off-grid by even 2px)
- Inconsistent border-radius across similar components
- Type weights that don't create enough contrast (font-weight 400 vs 500 is invisible)
- Cards with shadows AND borders (pick one depth signal)
- Hover states that change too many properties simultaneously (pick one signal)
- Placeholder text using the same color as filled text
- Missing loading/error states

**Pass condition:** You could overlay a grid and everything would snap to it.
**Fail condition:** Eyeballed spacing. Inconsistent radius. Missing states.

---

### CRITIC 3 — The User Advocate (the First-Time User)

_"Does this make me feel capable, or overwhelmed?"_

This critic has never seen the interface before. They have no patience for confusion.
They read quickly, skip what seems optional, and make decisions in seconds.

- "What is this? (Can I tell in 3 seconds?)"
- "What should I do first? (Is the primary action obvious?)"
- "What happens if I get this wrong? (Is recovery visible and forgiving?)"
- "Does this trust me with the right amount of information? (Not too much, not too little.)"
- "Does anything here make me feel stupid? (Error messages, dead-ends, jargon.)"

**Common failures this critic catches:**

- Hero copy that describes features instead of outcomes ("A powerful platform" vs "Launch faster")
- Primary CTA buried below secondary information
- No visual feedback on interactive elements (nothing tells me it worked)
- Error messages that blame the user ("Invalid input") instead of guiding them
- Empty states with no CTA (leaves the user stranded)
- Forms asking for too much information too early (progressive disclosure)
- Navigation labels that are internally meaningful but externally opaque ("Hub", "Workspace", "Studio")

**Pass condition:** A smart stranger understands the page's purpose within 3 seconds and knows what to do.
**Fail condition:** Clarity was sacrificed for aesthetics.

---

## THE VETO PROTOCOL

After generating any UI output, mentally run this sequence:

```
CRITIC 1 — The Reductionist
□ Can I remove anything without losing meaning?
□ Every color, shadow, border, and animation serves communication?
□ One accent. One typeface family. One motion personality.

CRITIC 2 — The Precision Engineer
□ Spacing is grid-based. No magic numbers.
□ All five component states are defined (default, hover, active, loading, disabled/error).
□ Type hierarchy is unmistakable at a glance.
□ Responsive behavior is defined, not assumed.

CRITIC 3 — The User Advocate
□ Page purpose clear in 3 seconds.
□ Primary action is the most visually prominent interactive element.
□ Error messages guide toward recovery, not describe failure.
□ Empty states have direction (icon + message + CTA).
□ Copy is real, contextual, and on-voice. Zero placeholders.

PASS: All 12 boxes checked → deliver.
FAIL: Fix the failures → re-run → deliver.
```

**No partial delivery.** If the critique reveals a failure, fix it before sending.
The entire value of this system is in refusing to output work you know is incomplete.

---

## THE SCREENSHOT TEST

One question. Non-negotiable.

> **"Would a designer screenshot and share this with someone they respect, without apology or context?"**

If yes → deliver.
If "maybe" → fix one more thing, then ask again.
If no → identify the biggest single failure, fix it, re-run critique.

"Maybe" is "no." "Pretty good" is "no." "It works" is "no."
The bar is: someone sees this and says _"who made this?"_

---

## ANTI-PATTERNS — THE GALLERY OF SHIPPED FAILURES

These are real failure modes in AI-generated UIs. Every one has been shipped.
If any of these appear in your output, they are critiqued failures, not stylistic choices.

**The Card Grid**
Twelve identical cards in a 3×4 grid with the same padding, border-radius, and typography weight.
Symptom: The first card could be any card. Visual fatigue sets in at card 3.
Fix: Vary one dimension — size, or emphasis weight, or entry animation timing.

**The Blue CTA**
`#3B82F6` as the primary button color on a white background.
Symptom: The interface looks like a Tailwind starter template.
Fix: Pick an accent that expresses the product's personality. Blue says nothing.

**The Floating Header Syndrome**
A sticky nav that takes up 80px and uses `box-shadow: 0 2px 4px rgba(0,0,0,0.1)`.
Symptom: The UI is half nav. The shadow feels cheap.
Fix: Blur backdrop + minimal border + reduce height to 56px max.

**The Loading Spinner Problem**
Full page spinner for 2+ seconds. User sees white screen, then a spinner, then content.
Symptom: Feels slow even when the network isn't.
Fix: Skeleton screens. Show structure immediately. Fill in data.

**The Error Wall**
Form validates on submit. User fills 8 fields. Submits. Sees 4 red error messages simultaneously.
Symptom: Failure is delivered as punishment, not guidance.
Fix: Validate on blur for each field. Inline recovery instructions. Never all-at-once.

**The Ghost Menu**
Navigation items that are text on background with no visual indicator of which is active.
Symptom: User doesn't know where they are.
Fix: Active state with accent color or indicator mark. Always.

**The Placeholder Paragraph**
Lorem ipsum in final output. "Your headline here" in a CTA. "Description goes here" in a card.
Symptom: The interface doesn't communicate — it performs communication.
Fix: Every word must be real, contextual, and on-voice for the domain.

**The Responsive Afterthought**
Beautiful desktop layout. On mobile: nav overflows, text is too large, cards stack with broken padding.
Symptom: Responsive was considered last.
Fix: Mobile-first. Build at 375px. Then expand.

**The Invisible Focus**
`outline: none` on `:focus`. No `:focus-visible` alternative.
Symptom: Keyboard users are locked out.
Fix: Style focus rings to match the aesthetic. Remove for mouse only via `:focus:not(:focus-visible)`.

**The Motion Spam**
Every card fades in on scroll. Every text block slides up. Every button pulses.
Symptom: The user feels motion sick, not delighted.
Fix: One orchestrated entrance sequence per screen. Motion only on meaningful state changes.

---

## CONFIDENCE TIERS

When delivering UI output, optionally signal confidence:

| Tier                   | Meaning                                                                             |
| ---------------------- | ----------------------------------------------------------------------------------- |
| **Tier 1: Awwwards**   | Would win a design award. Would be shared by designers. All critics pass.           |
| **Tier 2: Production** | Ships to real users without shame. All critics pass. Possibly one minor compromise. |
| **Tier 3: Functional** | Works correctly. Not design-forward. Communicate what was compromised and why.      |

Never deliver below Tier 2 without explicitly flagging the gap and the reason.
