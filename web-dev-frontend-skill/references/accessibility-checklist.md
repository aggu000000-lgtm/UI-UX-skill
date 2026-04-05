# Accessibility Checklist — WCAG 2.1 AA (2026)

> Standard: WCAG 2.1 Level AA. Not AAA (aspirational). Not 2.0 (outdated). 2.1 AA is the legal and practical baseline in 2026.

---

## COLOR AND CONTRAST

| # | Check | Pass Criteria |
|---|-------|--------------|
| 1 | Normal text contrast | Minimum 4.5:1 against background (text under 18px or under 14px bold) |
| 2 | Large text contrast | Minimum 3:1 against background (text 18px+ or 14px+ bold) |
| 3 | UI components contrast | Minimum 3:1 against adjacent colors (buttons, inputs, icons that convey meaning) |
| 4 | Focus indicator contrast | Minimum 3:1 against the background they appear on |
| 5 | Color not sole indicator | Never use color alone to convey meaning (red=error, green=success). Always pair with text, icons, or patterns |
| 6 | Dark mode contrast | All dark theme color combinations pass the same contrast ratios as light theme |
| 7 | Hover state contrast | Hover/focus states maintain sufficient contrast with their backgrounds |

## TYPOGRAPHY AND READABILITY

| # | Check | Pass Criteria |
|---|-------|--------------|
| 8 | Body text size | Minimum 16px. Not 14px. Not 12px. |
| 9 | Line height | Minimum 1.5x font size for body text |
| 10 | Line width | Maximum 80 characters per line (~600-700px for body text) |
| 11 | Paragraph spacing | At least 1.5x line height between paragraphs |
| 12 | No all-caps paragraphs | All-caps acceptable only for short labels (buttons, tabs, badges) |
| 13 | Font clarity | Typefaces with clear letterform distinction (I/l/1, O/0 easily distinguishable) |

## INTERACTIVE ELEMENTS

| # | Check | Pass Criteria |
|---|-------|--------------|
| 14 | Touch targets | Minimum 44x44px for all tappable elements (buttons, links, icons, toggles) |
| 15 | Focus states | Every interactive element has a visible focus indicator |
| 16 | Form labels | Always visible. Never use placeholder text as the only label |
| 17 | Error messages | Specific and actionable ("Email must include @" not "Something went wrong") |
| 18 | Error field connection | Error messages visually and programmatically connected to the specific field |
| 19 | Link text | Descriptive ("Read accessibility guide" not "Click here") |
| 20 | Button labels | Describe the action ("Submit application" not "Go") |

## MOTION AND ANIMATION

| # | Check | Pass Criteria |
|---|-------|--------------|
| 21 | prefers-reduced-motion | Respected. All animations disabled or reduced when user has this setting enabled |
| 22 | No auto-play with sound | Auto-playing video must be muted. Visible pause control required |
| 23 | Flash limit | Nothing flashes more than 3 times per second (seizure risk) |
| 24 | Auto-moving content | Pause, stop, or hide controls for carousels, ticker tapes, animated backgrounds |
| 25 | Animation duration | Less than 5 seconds or has user control. No infinite loops without stop |

## NAVIGATION AND STRUCTURE

| # | Check | Pass Criteria |
|---|-------|--------------|
| 26 | Heading hierarchy | H1, then H2s, then H3s. Never skip levels |
| 27 | Keyboard navigation | Every interactive element reachable via Tab, Enter, Escape, Arrow keys |
| 28 | Focus order | Logical order matching visual reading order |
| 29 | Skip navigation | "Skip to main content" link at top of page (can be visually hidden until focused) |
| 30 | Consistent navigation | Main navigation in same position on every page |
| 31 | Page titles | Unique, descriptive title for every page |
| 32 | Breadcrumbs | Present for sites with more than two levels of hierarchy |

## ARIA AND SEMANTIC HTML

| # | Check | Pass Criteria |
|---|-------|--------------|
| 33 | Semantic elements | Use correct HTML elements (nav, main, article, section, aside, header, footer) |
| 34 | ARIA labels | Custom components have appropriate ARIA roles and labels |
| 35 | aria-describedby | Used for form hints and supplementary descriptions |
| 36 | aria-invalid | Set on form fields with validation errors |
| 37 | aria-live | Used for dynamic content updates (notifications, loading states) |
| 38 | Image alt text | All images have alt text. Decorative images have alt="" (empty string) |
| 39 | Icon buttons | Every icon functioning as a button has aria-label |
| 40 | Screen reader only text | Use .sr-only class (visually hidden, screen reader accessible) when needed |

## MODALS AND OVERLAYS

| # | Check | Pass Criteria |
|---|-------|--------------|
| 41 | Focus trap | Focus is trapped within the modal when open |
| 42 | Focus return | Focus returns to the triggering element when modal closes |
| 43 | Escape key | Modal closes on Escape key press |
| 44 | Background scroll | Background content scroll is disabled when modal is open |
| 45 | aria-modal | Set to "true" on modal dialogs |

## COMMON PITFALLS (2026)

| # | Pitfall | Fix |
|---|---------|-----|
| 46 | Infinite scroll without keyboard access | Provide "Load more" button as keyboard-accessible alternative |
| 47 | Custom components without ARIA | Add ARIA roles, states, and properties to custom dropdowns, sliders, toggles |
| 48 | Icons without text labels | Add aria-label to every functional icon |
| 49 | Hover-only interactions | Every hover interaction must have a tap-based equivalent |
| 50 | Display:none for screen reader hiding | Use .sr-only class instead of display:none for screen-reader-only content |

---

## QUICK VERIFICATION TESTS

Run these 5 tests before every handoff:

1. **Contrast check** — Use Stark, Contrast, or axe DevTools. Fix everything that fails AA. (~5 min)
2. **Keyboard navigation** — Tab through entire interface. Reach everything? See focus? Can go back? (~10 min)
3. **Screen reader scan** — VoiceOver (Cmd+F5) or TalkBack. Does it make sense? Images described? Buttons labeled? (~10 min)
4. **Zoom to 200%** — Browser zoom. Layout break? Text readable? Navigation usable? (~3 min)
5. **Squint test** — Squint until blurry. Can you see visual hierarchy? Distinguish primary from secondary actions? (~1 min)

Total: under 30 minutes.
