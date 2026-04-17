# WCAG 2.2 AA Accessibility Checklist

> **COMPLIANCE REQUIREMENT**: WCAG 2.2 Level AA is the current legal baseline for accessibility compliance in 2026. All designs and implementations MUST meet these criteria.

---

## PERCEIVABLE (Principle 1)

### 1.1 Text Alternatives
- [ ] **1.1.1 Non-text Content (A)**: All images, icons, charts, and non-text content have text alternatives
- [ ] Decorative images use empty alt (`alt=""`) or are implemented via CSS
- [ ] Functional images (buttons, links) describe the action, not the image
- [ ] Complex images (charts, diagrams) have extended descriptions nearby
- [ ] CAPTCHA alternatives provided for users who cannot complete visual/ audio challenges

### 1.2 Time-based Media
- [ ] **1.2.1 Audio-only and Video-only (A)**: Alternatives provided for prerecorded media
- [ ] **1.2.2 Captions (AA)**: All prerecorded video content has synchronized captions
- [ ] **1.2.3 Audio Description or Media Alternative (A)**: Audio description or transcript provided
- [ ] **1.2.4 Captions (Live) (AA)**: Live video has real-time captions
- [ ] **1.2.5 Audio Description (AA)**: Prerecorded video has audio description

### 1.3 Adaptable Content
- [ ] **1.3.1 Info and Relationships (A)**: Information structure is programmatically determinable
- [ ] Headings use proper heading levels (h1 → h2 → h3, no skipping)
- [ ] Lists use proper `<ul>`, `<ol>`, `<dl>` markup
- [ ] Tables have proper headers (`<th>`) and scope attributes
- [ ] Forms have labels associated with inputs
- [ ] **1.3.2 Meaningful Sequence (A)**: Content order makes sense when linearized
- [ ] **1.3.3 Sensory Characteristics (A)**: Instructions don't rely solely on shape, size, color, or location
- [ ] **1.3.4 Orientation (AA)**: Content doesn't require specific screen orientation (unless essential)
- [ ] **1.3.5 Identify Input Purpose (AA)**: Form fields have autocomplete attributes where applicable
- [ ] **1.3.6 Identify Purpose (AAA)**: UI components and icons have programmatically determinable purpose

### 1.4 Distinguishable
- [ ] **1.4.1 Use of Color (A)**: Color is not the only means of conveying information
- [ ] Links have underlines or other non-color indicators
- [ ] Error states use icons/text in addition to color
- [ ] **1.4.2 Audio Control (A)**: Audio can be paused/muted independently of system volume
- [ ] **1.4.3 Contrast (Minimum) (AA)**: Text has contrast ratio of at least 4.5:1 (normal) or 3:1 (large)
- [ ] **1.4.4 Resize Text (AA)**: Text can be resized up to 200% without loss of content/functionality
- [ ] **1.4.5 Images of Text (AA)**: Text is used instead of images of text (unless essential)
- [ ] **1.4.6 Contrast (Enhanced) (AAA)**: Text has contrast ratio of at least 7:1 (normal) or 4.5:1 (large)
- [ ] **1.4.7 Low or No Background Audio (AAA)**: Background audio is 20dB quieter than foreground
- [ ] **1.4.8 Visual Presentation (AAA)**: Text spacing, alignment, and width are customizable
- [ ] **1.4.9 Images of Text (No Exception) (AAA)**: No images of text except logos/essential cases
- [ ] **1.4.10 Reflow (AA)**: Content reflows at 400% zoom without horizontal scrolling
- [ ] **1.4.11 Non-text Contrast (AA)**: UI components and graphics have 3:1 contrast ratio
- [ ] **1.4.12 Text Spacing (AA)**: Users can override text spacing without breaking layout
- [ ] **1.4.13 Content on Hover or Focus (AA)**: Additional content on hover/focus is dismissible, hoverable, and persistent

---

## OPERABLE (Principle 2)

### 2.1 Keyboard Accessible
- [ ] **2.1.1 Keyboard (A)**: All functionality available via keyboard
- [ ] **2.1.2 No Keyboard Trap (A)**: Keyboard focus can move away from all components
- [ ] **2.1.3 Keyboard (No Exception) (AAA)**: All functionality available via keyboard (no exceptions)
- [ ] **2.1.4 Character Key Shortcuts (A)**: Keyboard shortcuts can be turned off or remapped

### 2.2 Enough Time
- [ ] **2.2.1 Timing Adjustable (A)**: Time limits can be turned off, adjusted, or extended
- [ ] **2.2.2 Pause, Stop, Hide (A)**: Moving/blinking/auto-updating content can be paused/stopped/hidden
- [ ] **2.2.3 No Timing (AAA)**: No time limits required (except real-time events)
- [ ] **2.2.4 Interruptions (AAA)**: Interruptions can be postponed or suppressed
- [ ] **2.2.5 Re-authenticating (AAA)**: Session extension mechanism provided before data loss
- [ ] **2.2.6 Timeouts (AAA)**: Users warned of duration of inactivity before session expires

### 2.3 Seizures and Physical Reactions
- [ ] **2.3.1 Three Flashes or Below Threshold (A)**: No content flashes more than 3 times per second
- [ ] **2.3.2 Three Flashes (AAA)**: No flashing content at all
- [ ] **2.3.3 Animation from Interactions (AAA)**: Motion triggered by interaction can be disabled

### 2.4 Navigable
- [ ] **2.4.1 Bypass Blocks (A)**: Skip navigation link provided
- [ ] **2.4.2 Page Titled (A)**: Pages have descriptive, unique titles
- [ ] **2.4.3 Focus Order (A)**: Focus order preserves meaning and operability
- [ ] **2.4.4 Link Purpose (In Context) (A)**: Link purpose is clear from link text or context
- [ ] **2.4.5 Multiple Ways (AA)**: Multiple ways to find pages (search, sitemap, TOC)
- [ ] **2.4.6 Headings and Labels (AA)**: Headings and labels are descriptive
- [ ] **2.4.7 Focus Visible (AA)**: Keyboard focus indicator is visible
- [ ] **2.4.8 Location (AAA)**: User's location within site is clear
- [ ] **2.4.9 Link Purpose (Link Only) (AAA)**: Link purpose clear from link text alone
- [ ] **2.4.10 Section Headings (AAA)**: Content organized with section headings
- [ ] **2.4.11 Focus Not Obscured (Minimum) (AA)**: Focused element is at least partially visible
- [ ] **2.4.12 Focus Not Obscured (Enhanced) (AAA)**: Focused element is fully visible
- [ ] **2.4.13 Focus Appearance (AAA)**: Focus indicator has 3:1 contrast and clear outline

### 2.5 Input Modalities
- [ ] **2.5.1 Pointer Gestures (A)**: Path-based gestures have single-pointer alternative
- [ ] **2.5.2 Pointer Cancellation (A)**: Down-event triggers can be undone; up-event completion
- [ ] **2.5.3 Label in Name (A)**: Visible label text matches accessible name
- [ ] **2.5.4 Motion Actuation (A)**: Motion-triggered functions can be disabled or have alternatives
- [ ] **2.5.5 Target Size (AAA)**: Touch targets are at least 44x44 pixels
- [ ] **2.5.6 Concurrent Input Mechanisms (AAA)**: Multiple input methods supported simultaneously
- [ ] **2.5.7 Dragging Movements (AA)**: Dragging operations have non-drag alternatives
- [ ] **2.5.8 Target Size (Minimum) (AA)**: Touch targets are at least 24x24 pixels (with exceptions)

---

## UNDERSTANDABLE (Principle 3)

### 3.1 Readable
- [ ] **3.1.1 Language of Page (A)**: Page language declared in HTML lang attribute
- [ ] **3.1.2 Language of Parts (AA)**: Language changes marked up within content
- [ ] **3.1.3 Unusual Words (AAA)**: Definitions provided for idioms, jargon, unusual words
- [ ] **3.1.4 Abbreviations (AAA)**: Expanded form provided for abbreviations
- [ ] **3.1.5 Reading Level (AAA)**: Content written at lower secondary education level
- [ ] **3.1.6 Pronunciation (AAA)**: Pronunciation clarified for ambiguous words

### 3.2 Predictable
- [ ] **3.2.1 On Focus (A)**: Focus doesn't trigger unexpected context changes
- [ ] **3.2.2 On Input (A)**: Input doesn't trigger unexpected context changes
- [ ] **3.2.3 Consistent Navigation (AA)**: Navigation consistent across pages
- [ ] **3.2.4 Consistent Identification (AA)**: Same-function components identified consistently
- [ ] **3.2.5 Change on Request (AAA)**: Context changes only on user request
- [ ] **3.2.6 Consistent Help (A)**: Help mechanisms appear in consistent locations

### 3.3 Input Assistance
- [ ] **3.3.1 Error Identification (A)**: Input errors are identified and described
- [ ] **3.3.2 Labels or Instructions (A)**: Labels/instructions provided for user input
- [ ] **3.3.3 Error Suggestion (AA)**: Suggestions provided for correcting input errors
- [ ] **3.3.4 Error Prevention (Legal, Financial, Data) (AA)**: Reversible submissions or confirmation/review/correction
- [ ] **3.3.5 Help (AAA)**: Context-sensitive help available
- [ ] **3.3.6 Error Prevention (All) (AAA)**: All submissions reversible or confirmed

---

## ROBUST (Principle 4)

### 4.1 Compatible
- [ ] **4.1.1 Parsing (A)**: Valid HTML with unique IDs, proper nesting, complete tags
- [ ] **4.1.2 Name, Role, Value (A)**: Custom components have accessible names, roles, values
- [ ] **4.1.3 Status Messages (AA)**: Status messages announced by assistive technologies
- [ ] **4.1.4 Reflow (AA)**: Content maintains meaning and order when reflowed
- [ ] **4.1.5 Properties (AAA)**: Custom component properties settable via AT

---

## WCAG 2.2 AA QUICK REFERENCE

### Minimum Contrast Ratios
| Element | Ratio |
|---------|-------|
| Normal text (< 18pt regular, < 14pt bold) | 4.5:1 |
| Large text (≥ 18pt regular, ≥ 14pt bold) | 3:1 |
| UI components, graphics, focus indicators | 3:1 |

### Minimum Touch Target Sizes
| WCAG Level | Size |
|------------|------|
| AA (2.5.8) | 24x24px minimum |
| AAA (2.5.5) | 44x44px minimum |

### Focus Requirements
- **2.4.7 Focus Visible (AA)**: Focus must be visible
- **2.4.11 Focus Not Obscured (AA)**: Focus must not be hidden by sticky elements
- **2.4.13 Focus Appearance (AAA)**: Focus must have 3:1 contrast ratio

---

## TESTING PROTOCOL

### Manual Testing
1. **Keyboard Navigation**: Tab through entire interface
2. **Screen Reader Test**: Test with NVDA, VoiceOver, or JAWS
3. **Zoom Test**: Zoom to 400%, verify no content loss
4. **Color Contrast**: Verify all text meets 4.5:1 ratio
5. **Focus Visibility**: Confirm focus indicator visible on all interactive elements

### Automated Testing Tools
- axe DevTools
- WAVE
- Lighthouse Accessibility audit
- Pa11y
- ARC Toolkit

### Assistive Technology Testing
- NVDA + Firefox (Windows)
- JAWS + Chrome (Windows)
- VoiceOver + Safari (macOS/iOS)
- TalkBack + Chrome (Android)

---

## WCAG 2.2 AA COMPLIANCE STATEMENT TEMPLATE

```
This website conforms to WCAG 2.2 Level AA standards. 
Conformance testing was performed using [testing tools/methods] on [date].
Contact [email] to report accessibility issues.
```

---

**Last Updated**: 2026-04-12  
**Compliance Level**: WCAG 2.2 AA  
**Next Review**: Quarterly or after major updates
