# WCAG 3.0 Reference Guide

> **2026 STANDARDS**: WCAG 3.0 is in active development (Working Draft March 2026). This reference prepares you for the transition while maintaining WCAG 2.2 AA compliance as the current legal requirement.

---

## CURRENT COMPLIANCE REQUIREMENTS

### Legal Baseline (2026)
- **ADA Title II (US)**: WCAG 2.1 Level AA required by April 24, 2026
- **European Accessibility Act**: WCAG 2.1 AA required from June 2025
- **WCAG 3.0**: Not yet mandatory, but early adoption recommended for future-proofing

### WCAG 2.2 AA (Current Requirement)
Continue using WCAG 2.2 Level AA as your compliance baseline. See `accessibility-checklist.md` for complete WCAG 2.2 AA checklist with all Success Criteria.

**Key WCAG 2.2 AA Requirements:**
- All SC 2.1 through 2.5 must be met
- 2.4.11 Focus Not Obscured (Minimum) — NEW in 2.2
- 2.4.12 Focus Not Obscured (Enhanced) — NEW in 2.2
- 2.5.7 Dragging Movements — NEW in 2.2
- 2.5.8 Target Size (Minimum) — NEW in 2.2

---

## WCAG 3.0 KEY CHANGES

### New Conformance Model

WCAG 3.0 replaces binary pass/fail with a **Bronze/Silver/Gold** scoring system:

| Level | Score | Description |
|-------|-------|-------------|
| **Bronze** | 0-50% | Minimum accessibility. Equivalent to WCAG 2.x Level A |
| **Silver** | 51-80% | Enhanced accessibility with additional requirements |
| **Gold** | 81-100% | Highest level of accessibility achievement |

**Conformance Strategy:**
- Aim for **Silver** level as your target (equivalent to WCAG 2.x AA+)
- Use Bronze as minimum viable accessibility
- Pursue Gold for mission-critical public services

### Functional Needs Categories

WCAG 3.0 organizes requirements by **functional needs** rather than technology-specific SC:

1. **Perceivable**
   - Text Alternatives
   - Audio/Video Alternatives
   - Adaptable Content
   - Distinguishable

2. **Operable**
   - Keyboard Accessible
   - Enough Time
   - Seizure-Safe
   - Navigable
   - Input Modalities

3. **Understandable**
   - Readable
   - Predictable
   - Input Assistance

4. **Robust**
   - Compatible

### New Testing Approach

- **Conformance Assertions**: Test outcomes tied to specific user needs
- **Methods**: Practical testing procedures with pass/fail assertions
- **Resilience Testing**: Content tested across assistive technologies
- **Scoring**: Each assertion contributes to overall Bronze/Silver/Gold score

---

## PREPARING FOR WCAG 3.0

### Immediate Actions

1. **Audit Color Contrast with APCA**
   ```css
   /* Current: 4.5:1 ratio for normal text */
   /* WCAG 3.0: Uses APCA (Advanced Perceptual Contrast Algorithm) */
   
   /* APCA considers: */
   /* - Font weight (lighter = needs more contrast) */
   /* - Font size (smaller = needs more contrast) */
   /* - Ambient light conditions */
   /* - Background color luminance */
   
   /* Tools: https://www.myndex.com/APCA/ */
   /* Target: Lc 45+ for body text, Lc 60+ for UI elements */
   ```

2. **Review Target Sizes**
   ```css
   /* WCAG 2.2 AA requires 24x24px minimum */
   /* WCAG 3.0 may require 44x44px for all targets */
   
   .touch-target {
     min-width: 44px;
     min-height: 44px;
     padding: 12px; /* Ensures 44px hit area */
   }
   ```

3. **Test with Screen Readers**
   - NVDA + Firefox (Windows)
   - VoiceOver + Safari (macOS/iOS)
   - JAWS + Chrome (Windows)
   - TalkBack + Chrome (Android)

4. **Implement Focus Visibility**
   ```css
   /* WCAG 3.0 emphasizes focus visibility */
   :focus-visible {
     outline: 2px solid currentColor;
     outline-offset: 2px;
   }
   
   /* Ensure 3:1 contrast ratio for focus indicators */
   ```

### Component Audit Checklist

```markdown
- [ ] All interactive elements keyboard accessible
- [ ] Focus visible on all interactive elements (3:1 minimum contrast)
- [ ] No focus traps without escape mechanism
- [ ] All form inputs have visible labels
- [ ] Error messages specific and field-connected
- [ ] Time limits have extension mechanisms
- [ ] Content reflows at 400% zoom
- [ ] Touch targets minimum 44x44px (WCAG 3.0 target)
- [ ] Text spacing overrideable
- [ ] Animations respect prefers-reduced-motion
- [ ] Images have appropriate alt text
- [ ] Videos have captions
- [ ] Audio has transcript option
- [ ] Color not sole means of conveying information
- [ ] Heading hierarchy logical and complete
- [ ] Skip navigation link present
- [ ] Page titles descriptive and unique
```

---

## WCAG 3.0 NEW REQUIREMENTS

### Predicted Additions

Based on the current Working Draft (March 2026):

1. **Extended Color Contrast**
   - Adopts APCA or similar algorithm
   - Considers font weight, size, ambient light
   - More contextual than WCAG 2.x contrast ratios

2. **Enhanced Touch Interaction**
   - Multi-touch gesture alternatives required
   - Cancelable touch actions
   - Larger minimum touch targets (44x44px)

3. **Cognitive Accessibility**
   - Clear, consistent navigation patterns
   - Predictable operation models
   - Input error prevention mechanisms
   - Reading level considerations

4. **Emotional Safety**
   - Avoid triggering content without warnings
   - Respect user preferences for motion/content
   - Consider psychological impact

5. **Contextual Help**
   - Context-sensitive assistance available
   - Process indicators for multi-step flows
   - Clear error recovery paths

6. **Reduced Motion Enhancements**
   - `prefers-reduced-motion` support mandatory
   - Alternative static states for animations
   - User control over motion intensity

---

## IMPLEMENTATION TIMELINE

| Phase | Timeline | Action | Target |
|-------|----------|--------|--------|
| **Now** | 2026 Q1-Q2 | Maintain WCAG 2.2 AA | Current legal compliance |
| **Near** | 2026 Q3-Q4 | Start accessibility-first workflow | Proactive preparation |
| **Early** | 2027 | Begin WCAG 3.0 pilot projects | Early adoption testing |
| **Full** | 2028+ | Full WCAG 3.0 Silver compliance | Legal readiness |

### Migration Path

1. **Phase 1 (Now)**: Achieve and maintain WCAG 2.2 AA compliance
2. **Phase 2 (2026)**: Implement APCA contrast testing, increase touch targets
3. **Phase 3 (2027)**: Adopt functional needs testing approach
4. **Phase 4 (2028)**: Target WCAG 3.0 Silver conformance

---

## RESOURCES

### Official W3C Resources
- **W3C WCAG 3.0**: https://www.w3.org/TR/wcag-3.0/
- **WCAG 3.0 Explainer**: https://www.w3.org/TR/wcag-3.0-explainer/
- **WCAG 3.0 Methods**: https://www.w3.org/TR/wcag-3.0-methods/

### Testing Tools
- **TheWCAG.com WCAG 3.0 Guide**: https://www.thewcag.com/wcag-3-0
- **APCA Calculator**: https://www.myndex.com/APCA/
- **axe DevTools**: Supports WCAG 2.2, preparing for 3.0
- **WAVE**: Web accessibility evaluation tool

### Related Documents
- `accessibility-checklist.md` — Complete WCAG 2.2 AA checklist
- `ethical-privacy-ux.md` — Privacy and ethical design considerations

---

## QUICK REFERENCE: WCAG 2.2 vs 3.0

| Aspect | WCAG 2.2 | WCAG 3.0 |
|--------|----------|----------|
| Structure | Success Criteria (SC) | Guidelines + Assertions |
| Conformance | Pass/Fail (A/AA/AAA) | Bronze/Silver/Gold scoring |
| Color Contrast | 4.5:1 ratio (fixed) | APCA (contextual algorithm) |
| Testing | Manual + Automated checks | Methods + Assertions + Scoring |
| Scope | Web content | Apps, VR, XR, IoT, tools |
| Touch Targets | 24x24px (AA), 44x44px (AAA) | 44x44px (expected minimum) |
| Cognitive Load | Limited coverage | Expanded requirements |
| Emotional Safety | Not addressed | New consideration |

---

## WCAG 3.0 SCORING EXAMPLE

### Sample Component: Navigation Menu

| Assertion | Method | Outcome | Points |
|-----------|--------|---------|--------|
| Keyboard accessible | Tab through all items | Pass | +10 |
| Focus visible | Check focus indicator contrast | Pass | +10 |
| Screen reader announces items | Test with NVDA/VoiceOver | Pass | +10 |
| Skip link present | Verify skip navigation | Pass | +5 |
| Current page indicated | Check visual + programmatic | Pass | +5 |
| **Total** | | | **40/50 (Silver)** |

---

**Last Updated**: 2026-04-12  
**Status**: WCAG 3.0 Working Draft (March 2026)  
**Compliance Target**: WCAG 2.2 AA now → WCAG 3.0 Silver by 2028
