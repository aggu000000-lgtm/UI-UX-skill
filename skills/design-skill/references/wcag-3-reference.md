# WCAG 3.0 Reference Guide

> **2026 STANDARDS**: WCAG 3.0 is in active development (Working Draft March 2026). This reference prepares you for the transition while maintaining WCAG 2.2 AA compliance as the current legal requirement.

---

## CURRENT COMPLIANCE REQUIREMENTS

### Legal Baseline (2026)
- **ADA Title II (US)**: WCAG 2.1 Level AA required by April 24, 2026
- **European Accessibility Act**: WCAG 2.1 AA required from June 2025
- **WCAG 3.0**: Not yet mandatory, but early adoption recommended

### WCAG 2.2 AA (Current Requirement)
Continue using WCAG 2.2 Level AA as your compliance baseline:
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

---

## PREPARING FOR WCAG 3.0

### Immediate Actions

1. **Audit Color Contrast**
   ```css
   /* Current: 4.5:1 ratio for normal text */
   /* WCAG 3.0: May require 4.5:1 or higher via APCA */
   
   /* APCA (Advanced Perceptual Contrast Algorithm) */
   /* - More contextual than WCAG 2.x contrast */
   /* - Considers font weight, size, ambient light */
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

### Component Audit Checklist

```markdown
- [ ] All interactive elements keyboard accessible
- [ ] Focus visible on all interactive elements (3:1 minimum)
- [ ] No focus traps without escape mechanism
- [ ] All form inputs have visible labels
- [ ] Error messages specific and field-connected
- [ ] Time limits have extension mechanisms
- [ ] Content reflows at 400% zoom
- [ ] Touch targets minimum 44x44px
- [ ] Text spacing overrideable
- [ ] Animations respect prefers-reduced-motion
- [ ] Images have appropriate alt text
- [ ] Videos have captions
- [ ] Audio has transcript option
```

---

## WCAG 3.0 NEW REQUIREMENTS

### Predicted Additions

Based on the current Working Draft (March 2026):

1. **Extended Color Contrast**
   - May adopt APCA or similar algorithm
   - Considers more visual factors

2. **Enhanced Touch Interaction**
   - Multi-touch gesture alternatives
   - Cancelable touch actions

3. **Cognitive Accessibility**
   - Clear, consistent navigation
   - Predictable operation
   - Input error prevention

4. **Emotional Safety**
   - Avoid triggering content
   - Respect user preferences

5. **Contextual Help**
   - Context-sensitive assistance
   - Process indicators

---

## IMPLEMENTATION TIMELINE

| Phase | Action | Target |
|-------|--------|--------|
| **Now** | Maintain WCAG 2.2 AA | Current compliance |
| **2026** | Start accessibility-first workflow | Proactive preparation |
| **2027** | Begin WCAG 3.0 pilot projects | Early adoption |
| **2028+** | Full WCAG 3.0 compliance | Legal readiness |

---

## RESOURCES

- **W3C WCAG 3.0**: https://www.w3.org/TR/wcag-3.0/
- **WCAG 3.0 Explainer**: https://www.w3.org/TR/wcag-3.0-explainer/
- **TheWCAG.com WCAG 3.0 Guide**: https://www.thewcag.com/wcag-3-0

---

## QUICK REFERENCE: WCAG 2.2 vs 3.0

| Aspect | WCAG 2.2 | WCAG 3.0 |
|--------|----------|----------|
| Structure | Success Criteria | Guidelines + Assertions |
| Conformance | Pass/Fail | Bronze/Silver/Gold |
| Color | 4.5:1 ratio | May use APCA |
| Testing | Manual + Automated | Methods + Assertions |
| Scope | Web content | Apps, VR, XR, tools |

---

**Last Updated**: 2026-04-12
