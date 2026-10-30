# Review Checklist

Use in review mode, or as the final self-check on a large build. Walk the user's main task first, then go section by section. Report findings with the format and severity levels in SKILL.md.

## Contents
1. Nielsen's 10 heuristics, as checks
2. States
3. Feedback and speed
4. Forms and errors
5. Navigation and structure
6. Accessibility
7. Motion
8. Content and microcopy
9. Testing it with people
10. Sources

## 1. Nielsen's 10 heuristics, as checks

| # | Heuristic | What to look for |
|---|---|---|
| 1 | Visibility of system status | Every action shows feedback; loads, saves and syncs are visible; the user knows where they are |
| 2 | Match with the real world | User's words, not database or internal terms; natural order |
| 3 | User control and freedom | Cancel, Back, Undo everywhere; no traps in modals or flows |
| 4 | Consistency and standards | Same word and icon for the same action; platform conventions followed |
| 5 | Error prevention | Constraints, good defaults, confirmations only for destructive commits |
| 6 | Recognition over recall | Options, recent values and context visible; nothing to memorize between screens |
| 7 | Flexibility and efficiency | Shortcuts for frequent users (keyboard, recent items, bulk actions) that don't burden beginners |
| 8 | Aesthetic and minimalist design | Nothing on screen that doesn't serve the task; one clear primary action |
| 9 | Help users recover from errors | Plain-language errors that say what happened and how to fix it |
| 10 | Help and documentation | In-context help where users get stuck; searchable, task-focused |

## 2. States

- [ ] Loading state for every async view (skeleton for pages and lists, labeled spinner for modules)
- [ ] First-use empty state that explains and offers an action
- [ ] No-results state distinct from first-use empty
- [ ] Error state with plain message and Retry; rest of page still works
- [ ] Offline behavior defined (if the app syncs)
- [ ] Success confirmation that names the result
- [ ] Layout works with 1 item, 100 items, very long names, and missing images

## 3. Feedback and speed

- [ ] Pressed state on every tappable element; hover and focus on web
- [ ] Network buttons show progress and block double submit
- [ ] No spinner for waits under 1 s (no flicker)
- [ ] Waits over 10 s show progress and allow cancel
- [ ] Optimistic updates roll back with a visible message on failure
- [ ] Toasts used only for non-critical confirmations, never as the only error signal
- [ ] Web: LCP ≤ 2.5 s, INP ≤ 200 ms, CLS ≤ 0.1 (check with Lighthouse or PageSpeed Insights)

## 4. Forms and errors

- [ ] Visible labels (not placeholders); one column
- [ ] Required and optional marked
- [ ] Right keyboard and autofill for each field
- [ ] Validation on blur, error cleared as soon as fixed
- [ ] Errors next to the field, specific, with a fix; input preserved
- [ ] Submit button labeled with the outcome
- [ ] No unnecessary fields; rare ones hidden behind a link
- [ ] Paste and password managers allowed

## 5. Navigation and structure

- [ ] Primary navigation visible (not only in a hamburger) when there are 4 or fewer top-level destinations, and always on desktop
- [ ] Mobile bottom navigation has 3–5 destinations with labels, same on every top-level screen
- [ ] User always knows where they are (active nav item, page title, breadcrumbs for deep hierarchies)
- [ ] Back behaves predictably and never loses work
- [ ] One primary action per screen; secondary actions visually quieter
- [ ] Advanced options behind progressive disclosure, at most 2 levels deep

## 6. Accessibility

- [ ] Text contrast ≥ 4.5:1 (large text ≥ 3:1); UI components and icons ≥ 3:1
- [ ] Touch targets ≥ 44 pt / 48 dp on mobile; ≥ 24 CSS px on web, with spacing
- [ ] Everything works with keyboard only, in a logical order, with visible focus that isn't hidden behind sticky headers
- [ ] Every control has an accessible name; images have alt text or are marked decorative
- [ ] Color is never the only signal
- [ ] Layout survives 200% text size (web) and the largest system font size (mobile)
- [ ] Async updates and errors announced to screen readers
- [ ] Dragging and gestures have a single-tap or button alternative

## 7. Motion

- [ ] Every animation has a purpose (feedback, orientation, continuity)
- [ ] Durations mostly 150–300 ms; exits faster than entrances
- [ ] Only transform and opacity animated on web; no layout thrash
- [ ] Reduced-motion setting respected
- [ ] No animation on frequent keyboard actions or typing
- [ ] Nothing flashes more than 3 times per second

## 8. Content and microcopy

- [ ] Buttons use verbs that describe the outcome
- [ ] Headings say what the screen is for
- [ ] Consistent terms for the same thing across the app
- [ ] Numbers, dates and currency formatted for the user's locale
- [ ] No lorem ipsum or placeholder copy left in

## 9. Testing it with people

Expert review catches many issues, but watching users catches the ones you can't see.

- **5 users find about 85% of usability problems** in a round (NN/g). Three rounds of 5, fixing between rounds, beat one round of 15.
- **Hallway test:** ask the next person nearby to do the main task while you watch silently. Don't help. Note where they hesitate.
- **Task success:** define success before testing; score each task 1 or 0. Across many studies the average completion rate is about 78% (MeasuringU).
- **SUS (System Usability Scale):** 10 questions, 0–100 score. 68 is average; 80.8+ is excellent; below 51.7 is poor. It is not a percentage.
- For a hackathon: one 5-minute test with a teammate who didn't build the screen, before the final demo, catches the worst flow problems.

## 10. Sources

- NN/g, 10 Usability Heuristics — https://www.nngroup.com/articles/ten-usability-heuristics/
- NN/g, Why you only need to test with 5 users — https://www.nngroup.com/articles/why-you-only-need-to-test-with-5-users/
- NN/g, Hamburger menus — https://www.nngroup.com/articles/hamburger-menus/
- Joel Spolsky, The Joel Test (hallway testing) — https://www.joelonsoftware.com/2000/08/09/the-joel-test-12-steps-to-better-code/
- MeasuringU, Completion rates — https://measuringu.com/completion-rates/
- MeasuringU, Interpreting SUS scores — https://measuringu.com/interpret-sus-score/
- web.dev, Core Web Vitals — https://web.dev/articles/vitals
