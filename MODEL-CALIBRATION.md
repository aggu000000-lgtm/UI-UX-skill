# Model Calibration for the Avant-Garde Director

AI models are heavily fine-tuned to be "helpful, honest, and harmless." In frontend generation, "helpful" translates to "boring, accessible, and strictly gridded."

To get models to output cinematic, Awwwards-winning code (GSAP, Three.js, Lenis, raw WebGL), you have to break their conditioning.

This document outlines how different frontier models respond to the `avant-garde-director-skill` and the specific prompting techniques required to extract digital art instead of SaaS dashboards.

---

## 1. CLAUDE 3.5 SONNET

**The Best Creative Coder**
Claude 3.5 Sonnet is currently the most capable model for executing complex GSAP timelines, managing Three.js scenes, and understanding the nuances of kinetic typography. It naturally grasps "subversive design" when prompted.

**Calibration Notes:**
- **The Relapse Problem:** Mid-way through a complex file, Claude might revert to writing standard CSS hover effects (`transition: all 0.3s ease`). You must aggressively enforce the `INTERACTION-DESIGN.md` rules.
- **The Prompt Hack:** Add this to your prompt: *"Do not use CSS transitions for primary elements. I want to see GSAP `quickTo` and `ScrollTrigger` velocity-based scaling."*
- **Canvas Generation:** Claude is excellent at writing raw WebGL fragment shaders for noise, grain, and fluid distortion. Ask for "a custom GLSL shader inside a Three.js plane."

---

## 2. GPT-4o

**The Logical Physicist**
GPT-4o is excellent at the mathematics behind custom cursors, magnetic buttons, and Lenis scroll integration. However, its aesthetic taste defaults much closer to Bootstrap than Claude does.

**Calibration Notes:**
- **The Aesthetic Deficit:** GPT-4o will build a perfect GSAP timeline but apply it to a boring, perfectly rounded white card. You must explicitly demand "Brutalist palettes, massive 15vw typography, and overlapping Z-indexes."
- **The Boilerplate Bug:** It frequently forgets to register GSAP plugins (`gsap.registerPlugin(ScrollTrigger)`). Remind it in the prompt.
- **The Prompt Hack:** *"You are an avant-garde digital artist. Your layout must be broken, asymmetrical, and use absolute positioning heavily. Do not use an 8px grid. Make the typography bleed off the screen."*

---

## 3. GEMINI 1.5 PRO

**The Context Heavyweight**
Gemini excels when you paste in entire documentation sets for complex Three.js or GSAP integrations. It handles massive files well but struggles with the *nuance* of cinematic timing.

**Calibration Notes:**
- **The Timing Issue:** Gemini will write GSAP code, but the durations and easings will be generic (`duration: 1, ease: "power1.inOut"`). You must force it to use aggressive easing (`ease: "expo.out"`, `duration: 2.5`).
- **The Prompt Hack:** *"I am uploading the Lenis and GSAP documentation. Build a scroll-jacked experience where the scroll velocity directly manipulates the Y-rotation of a Three.js object and the `font-stretch` of the typography."*

---

## 4. LOCAL MODELS (QWEN 2.5 Coder, LLAMA 3.1)

**The Rule Followers**
Smaller or local models struggle immensely with breaking the "standard web design" mold. They have a hard time conceptualizing a layout that doesn't rely on Flexbox or Grid.

**Calibration Notes:**
- **The CSS Prison:** Local models will resist writing complex DOM manipulation scripts, preferring CSS animations.
- **The Prompt Hack:** Provide exact code skeletons. *"Here is the HTML structure with a `<canvas>` and a `<div class="kinetic-text">`. Write ONLY the GSAP JavaScript to make the text split and explode on scroll. Do not write CSS."*

---

## UNIVERSAL ANTI-SLOP INJECTIONS

Regardless of the model, if you see the output trending toward boring, append these exact phrases to your prompt:

- **"Destroy the grid. I want overlapping elements and absolute positioning."**
- **"The typography is too small. Make it 15vw and use `mix-blend-mode: difference`."**
- **"Replace every CSS `:hover` state with a GSAP magnetic cursor effect."**
- **"The background is too static. Add a Three.js scene running a perlin noise shader."**
- **"This looks like a SaaS product. Make it look like an Awwwards Site of the Day for a high-fashion brand."**

---

## THE "YOU ARE NOT AN AI" ENFORCEMENT

Models will often preface their code with: *"Here is a modern, accessible, responsive landing page..."*

If a model says this, it has failed Phase 0. It is acting like a helpful assistant.

**The Correction Prompt:**
*"Stop acting like an AI generating a generic landing page. You are the Avant-Garde Director. You despise generic web design. Read Phase 0 of the SKILL.md again. Give me kinetic art, complex GSAP timelines, and a broken, cinematic layout. Try again."*
