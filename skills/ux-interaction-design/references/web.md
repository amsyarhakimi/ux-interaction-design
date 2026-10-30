# Web (HTML, CSS, React and friends)

Read this when the project is a website or web app. Apply the general rules from the other reference files; this file maps them to web APIs and frameworks.

## Contents
1. Semantic structure
2. Interaction states in CSS
3. Loading, async and optimistic UI
4. Forms
5. Layout shift and performance
6. Motion and transitions
7. Responsive layout
8. Browser support notes (2026)
9. Sources

## 1. Semantic structure

- Native elements first: `<button>` for actions, `<a href>` for navigation (never a button that navigates or a link that submits), `<form>` with a real submit so Enter works.
- Landmarks (`header`, `nav`, `main`, `footer`), one `h1`, headings in order.
- `<dialog>` with `showModal()` for modals; restore focus to the opener on close.
- Use the URL for state users expect to share or go back to: tabs, filters, search queries, pagination. Back should undo navigation, not close the app.

## 2. Interaction states in CSS

```css
.btn {
  min-height: 44px;                 /* comfortable touch target; WCAG floor is 24px */
  padding-inline: 16px;
  transition: background-color 150ms, transform 100ms;
}
.btn:hover { background: var(--btn-hover); }
.btn:active { transform: scale(0.97); }
.btn:focus-visible { outline: 2px solid var(--focus); outline-offset: 2px; }
.btn[aria-disabled="true"] { opacity: 0.6; cursor: not-allowed; }
.btn[aria-busy="true"] { cursor: progress; }

/* Hover only where a real pointer exists, so touch devices don't get stuck hover states */
@media (hover: hover) and (pointer: fine) {
  .card:hover { box-shadow: var(--shadow-raised); }
}

/* Keep focused elements visible under a sticky header (WCAG 2.4.11) */
html { scroll-padding-top: 80px; }
```

- Use `:focus-visible`, not `:focus`, for custom focus rings. Never remove an outline without a replacement.
- Prefer `aria-disabled="true"` plus an explanation over the `disabled` attribute when users need to discover why something is unavailable; `disabled` removes it from the tab order.

## 3. Loading, async and optimistic UI

- Model async state explicitly (`idle | loading | success | error`), not with a single boolean. With TanStack Query, SWR or RTK Query, render `isPending`, `isError`, empty data and data separately.
- Keep previous data on screen while refetching (`placeholderData: keepPreviousData` in TanStack Query) and show a small refresh indicator.
- React Suspense: put boundaries around sections, not the whole page, so the frame stays. Give each a skeleton fallback.
- Next.js App Router: `loading.tsx` for route-level skeletons, `error.tsx` with a Retry (`reset()`), `not-found.tsx` for 404s.
- Optimistic UI: React 19 `useOptimistic` (inside an Action or `startTransition`) reverts when the action ends; catch the error and show it inline. TanStack Query: update the cache in `onMutate`, roll back in `onError`.
- Double-submit guard: disable or ignore while pending (`useFormStatus().pending` in React 19 forms), and make server endpoints idempotent where possible.
- Announce results to screen readers with a persistent `role="status"` region.

## 4. Forms

See `forms-and-errors.md` for rules. Web specifics:

```html
<label for="email">Email</label>
<input id="email" name="email" type="email" autocomplete="email"
       autocapitalize="off" spellcheck="false" required
       aria-describedby="email-hint email-error">
<p id="email-hint">We'll send the invite here.</p>
<p id="email-error" role="alert" hidden>Enter an email like name@example.com</p>
```

- `type`, `inputmode`, and `autocomplete` tokens on every field.
- Validate on `blur`; after an error shows, re-validate on `input` to clear it.
- On submit with errors: focus the first invalid field; for long forms add an error summary at the top with links to fields.
- React Hook Form: `mode: 'onTouched'` gives validate-on-blur then re-validate-on-change.
- Never block paste on password or code fields. One-time code: `autocomplete="one-time-code"`, `inputmode="numeric"`.

## 5. Layout shift and performance

Core Web Vitals (good at the 75th percentile): **LCP ≤ 2.5 s, INP ≤ 200 ms, CLS ≤ 0.1**. Poor: LCP > 4 s, INP > 500 ms, CLS > 0.25.

- Reserve space for images and embeds: `width`/`height` attributes or `aspect-ratio`.
- Reserve `min-height` for content that loads later (ads, banners, async sections).
- Skeletons the same size as final content.
- Fonts: preload the critical font, `font-display: optional` or `swap` with a metric-matched fallback (`size-adjust`, `ascent-override`) to avoid text jumping.
- INP: keep event handlers light; show the visual response first, then do heavy work (`await` a frame or use `startTransition`); break long tasks; avoid huge re-renders on each keystroke (debounce search input, about 200–300 ms).
- Prefetch likely next pages: `<link rel="prefetch">` everywhere; Speculation Rules are Chromium-only, so feature-detect. Never prefetch URLs with side effects (logout, add to cart).
- Measure with Lighthouse, PageSpeed Insights, and the Web Vitals extension, on a throttled mobile profile.

## 6. Motion and transitions

- CSS transitions for two-state changes, keyframes for sequences; JS (Web Animations API, Framer Motion) for gesture-driven or physics motion.
- Animate `transform` and `opacity` only. Never `transition: all`.
- `@media (prefers-reduced-motion: reduce)` after normal rules; Framer Motion `useReducedMotion()` or `<MotionConfig reducedMotion="user">`.
- View Transitions: same-document `document.startViewTransition()` is supported in all major engines (Baseline since late 2025). Cross-document `@view-transition` is not in Firefox; treat it as progressive enhancement.
- Avoid scroll-jacking and heavy parallax.

## 7. Responsive layout

- Mobile first: design the narrow layout, then add columns.
- Reflow at 320 CSS px without horizontal scrolling (WCAG 1.4.10).
- Container queries (size) are Baseline; use them for components that live in different widths.
- Body text 16 px minimum, readable width `max-inline-size: 66ch` (45–75 characters per line).
- Use `dvh`/`svh` instead of `100vh` for full-height mobile layouts so browser toolbars don't cut content.
- Respect safe areas on notched phones: `env(safe-area-inset-bottom)` for fixed bottom bars.
- Fixed bottom actions on mobile: keep them clear of the home indicator and above the keyboard.

## 8. Browser support notes (2026)

| Feature | Status |
|---|---|
| `:focus-visible` | Baseline widely available |
| `prefers-reduced-motion` | Baseline widely available |
| Same-document View Transitions | Baseline (Chrome 111, Safari 18, Firefox 144) |
| Cross-document View Transitions | Chrome and Safari only |
| Container size queries | Baseline widely available |
| Scroll-driven animations (`animation-timeline`) | Only just reached all engines; feature-detect with `@supports` |
| Speculation Rules | Chromium only |

## 9. Sources

- web.dev, Core Web Vitals — https://web.dev/articles/vitals
- web.dev, Optimize CLS — https://web.dev/articles/optimize-cls
- web.dev, INP — https://web.dev/articles/inp
- web.dev, High-performance animations — https://web.dev/articles/animations-guide
- web.dev, Typography — https://web.dev/learn/design/typography
- web.dev, Same-document view transitions Baseline — https://web.dev/blog/same-document-view-transitions-are-now-baseline-newly-available
- MDN, :focus-visible — https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-visible
- MDN, View Transition API — https://developer.mozilla.org/en-US/docs/Web/API/View_Transition_API
- MDN, Scroll-driven animations — https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_scroll-driven_animations
- MDN, Speculation Rules API — https://developer.mozilla.org/en-US/docs/Web/API/Speculation_Rules_API
- React, useOptimistic — https://react.dev/reference/react/useOptimistic
- W3C, Understanding 1.4.10 Reflow — https://www.w3.org/WAI/WCAG22/Understanding/reflow.html
