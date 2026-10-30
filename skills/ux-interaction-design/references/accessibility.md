# Accessibility

Read this when building or reviewing any UI. Accessibility is part of usability: the same fixes (contrast, target size, clear labels, focus) help everyone, including people outdoors in sunlight or using one hand.

## Contents
1. Which standard
2. The criteria that matter most when building UI
3. Touch and click targets
4. Keyboard and focus (web)
5. Screen readers
6. Text and zoom
7. Color
8. Quick test routine
9. Sources

## 1. Which standard

Build to **WCAG 2.2 Level AA**. It is the current W3C Recommendation and was approved as ISO/IEC 40500:2025. WCAG 3.0 is still a Working Draft and not expected to be final for years; don't target it.

## 2. The criteria that matter most when building UI

| Criterion | Level | Rule |
|---|---|---|
| 1.4.3 Contrast (Minimum) | AA | Text ≥ 4.5:1; large text (≥ 24 px, or ≥ 18.5 px bold) ≥ 3:1. Placeholder text counts. 4.49:1 fails; don't round |
| 1.4.11 Non-text Contrast | AA | ≥ 3:1 for anything needed to identify a control or its state: input borders (when the border is the only cue), meaningful icons, toggle states, focus rings, chart parts |
| 1.4.1 Use of Color | A | Color is never the only way to show information, state or action |
| 1.4.4 Resize Text | AA | Text resizes to 200% without losing content or function |
| 1.4.10 Reflow | AA | No two-direction scrolling at 320 CSS px wide (400% zoom). Maps, diagrams and data tables are exempt as a whole |
| 1.4.12 Text Spacing | AA | Nothing breaks when users set line height 1.5×, paragraph spacing 2×, letter spacing 0.12×, word spacing 0.16×. Don't give text containers fixed heights |
| 2.4.7 Focus Visible | AA | Keyboard focus is always visible |
| 2.4.11 Focus Not Obscured | AA | A focused element isn't fully hidden by sticky headers, footers, cookie banners. Use `scroll-padding` |
| 2.4.13 Focus Appearance | AAA | Good default anyway: a solid 2 px outline with ≥ 3:1 change |
| 2.5.7 Dragging Movements | AA | Every drag has a single-pointer alternative (move buttons, tap on slider track, "Move to" menu) |
| 2.5.8 Target Size (Minimum) | AA | ≥ 24×24 CSS px, or enough spacing; exceptions for inline links and equivalent controls |
| 2.2.2 Pause, Stop, Hide | A | Auto-moving content lasting > 5 s can be paused or hidden |
| 3.2.6 Consistent Help | A | Help links and contact options stay in the same place across pages |
| 3.3.7 Redundant Entry | A | Don't make people re-enter what they already gave in the same flow ("Same as shipping", keep values after errors) |
| 3.3.8 Accessible Authentication | AA | No memory or puzzle test without an alternative; allow paste and password managers; OTP boxes accept a full paste; offer show-password, passkeys or email links |
| 4.1.2 Name, Role, Value | A | Every control exposes its name, role and state. Native elements do this; custom ones need ARIA |
| 4.1.3 Status Messages | AA | Status changes are announced without moving focus (`role="status"`, `role="alert"`) |

## 3. Touch and click targets

| Platform | Minimum | Notes |
|---|---|---|
| iOS / iPadOS | 44×44 pt hit region | 28 pt is Apple's absolute floor; visionOS 60 pt |
| Android / Material | 48×48 dp (about 9 mm) | At least 8 dp between targets; a 24 dp icon sits in a 48 dp target with padding |
| Web (WCAG 2.2 AA) | 24×24 CSS px | Legal floor. Use 44–48 px for primary touch controls |

Apple: treat spacing between controls as being as important as their size. The hit area can be larger than the visible shape; extend it with padding.

## 4. Keyboard and focus (web)

- Use native elements: `<button>` for actions, `<a href>` for navigation, `<input>`, `<select>`, `<dialog>`. A `<div role="button">` still needs tabindex, Enter and Space handling, and states; a real `<button>` has them for free.
- First rule of ARIA: if a native element does the job, use it. Never put `aria-hidden="true"` or `role="presentation"` on something focusable.
- Tab order follows visual order. Don't use positive `tabindex`.
- Custom focus styles with `:focus-visible`. Never `outline: none` without a replacement.
- Skip link: "Skip to main content" as one of the first focusable items, visible on focus (not `display: none`), moving focus to `#main`.
- Landmarks: `<header>`, `<nav>`, `<main>`, `<footer>`; one `<h1>` per page and headings in order.
- Modal dialogs: `role="dialog"`, `aria-modal="true"`, `aria-labelledby` on the title. Move focus inside on open (the least destructive button for irreversible actions), trap Tab inside, close on Esc, return focus to the opener on close. Native `<dialog>` with `showModal()` handles inertness and Esc; you still restore focus yourself.

## 5. Screen readers

- Every control needs an accessible name. Icon-only buttons: `aria-label="Delete"` on web, `tooltip` on Flutter `IconButton`, `accessibilityLabel` in React Native.
- Images: decorative → `alt=""`; inside a link or button → describe the action or destination; informative → brief meaning; charts → put the data or summary in text nearby.
- Form fields: visible `<label for>`; help and error text linked with `aria-describedby`; invalid fields `aria-invalid="true"`.
- Async updates: a live region that already exists in the page before content changes.
  - `role="status"` (add `aria-live="polite"` for compatibility) for "5 results", "Saved", cart counts.
  - `role="alert"` for errors; don't also add `aria-live` (iOS VoiceOver reads it twice).
  - `aria-atomic="true"` so the whole message is read.
- Disabled-but-focusable: `aria-disabled="true"` keeps the control discoverable and lets you explain why it's disabled.
- Flutter: see `flutter.md` (Semantics, MergeSemantics, liveRegion).

## 6. Text and zoom

- Use relative units (`rem`) for font sizes on web so user settings apply.
- Mobile: support the system font size up to at least 200%; test the largest size on a small screen.
- Inputs at least 16 px on web, or iOS Safari zooms in on focus.
- Never disable zoom (`user-scalable=no`, `maximum-scale=1`).

## 7. Color

- Check every text/background pair, in both light and dark mode. Tools: WebAIM Contrast Checker, browser DevTools contrast readout, Flutter's `textContrastGuideline` test.
- Never rely on color alone: pair red/green status with an icon or text. Red-green and blue-orange are hard pairs for color-blind users.
- Disabled controls are exempt from contrast rules, but must still be readable enough to understand.
- Support high-contrast / Increase Contrast settings where the platform offers them.

## 8. Quick test routine

Five minutes that catches most problems:
1. **Keyboard only** (web): Tab through the main task. Can you see focus at every step? Can you finish without a mouse? Can you escape every dialog?
2. **Zoom to 200%** (web) or **largest system font** (mobile): does anything overlap, clip, or need sideways scrolling?
3. **Screen reader** on the main screen (VoiceOver: Cmd+F5 on Mac, triple-click side button on iPhone; TalkBack on Android): does every control say what it is?
4. **Contrast check** on text, placeholder, icons and borders.
5. **Automated scan**: Lighthouse or axe DevTools (web), Accessibility Scanner (Android), Xcode Accessibility Inspector (iOS), Flutter `meetsGuideline` tests.

Automated tools catch only part of the problems; steps 1–3 matter most.

## 9. Sources

- W3C, WCAG 2.2 — https://www.w3.org/TR/WCAG22/
- W3C, WCAG 3 introduction (status) — https://www.w3.org/WAI/standards-guidelines/wcag/wcag3-intro/
- W3C, WCAG 2.2 approved as ISO/IEC 40500:2025 — https://www.w3.org/WAI/news/2025-10-21/wcag22-iso
- W3C, Understanding WCAG 2.2 (each criterion) — https://www.w3.org/WAI/WCAG22/Understanding/
- W3C, Using ARIA — https://www.w3.org/TR/using-aria/
- W3C, ARIA APG modal dialog pattern — https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/
- W3C, Images decision tree — https://www.w3.org/WAI/tutorials/images/decision-tree/
- MDN, ARIA live regions — https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Guides/Live_regions
- MDN, dialog element — https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog
- web.dev, Use semantic HTML — https://web.dev/articles/use-semantic-html
- WebAIM, Skip navigation links — https://webaim.org/techniques/skipnav/
- Apple HIG, Accessibility; Buttons — https://developer.apple.com/design/human-interface-guidelines/accessibility
- Android, Touch target size — https://support.google.com/accessibility/android/answer/7101858
