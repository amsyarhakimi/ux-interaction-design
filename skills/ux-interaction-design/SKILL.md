---
name: ux-interaction-design
description: Build and review UI so it works for real users. Covers screen states (empty, loading, error, success, offline), feedback and response times, forms and validation, error messages, navigation, accessibility (WCAG 2.2 AA), touch targets, motion and microinteractions, perceived performance, and AI features (chat, suggestions, recommendations, generated content), for web (HTML/CSS/React) and Flutter apps. Use this skill whenever the user builds or changes a screen, page, form, flow, component or interaction, asks to improve UX or usability, wants an app to feel smoother or more responsive, adds animations or transitions, fixes a confusing flow, asks to review or audit a UI, prepares a demo, or says the UI feels off, clunky or AI-made, even if they never say "UX". It handles behavior and usability; pair it with a visual-style skill (impeccable, frontend-design) for colors and typography.
---

# UX and Interaction Design

Most AI-built interfaces fail on behavior, not looks. The screen is pretty, but a button gives no feedback, a list has no empty state, an error says "Something went wrong", and a form rejects input while the user is still typing. Real users notice these first. This skill makes Claude build and review UI the way a careful product designer would: around the user's task, every state of every screen, and clear feedback for every action.

Best practices only help when they serve the user's goal on this screen. Treat the rules here as defaults with reasons. When a rule and the user's real task disagree, follow the task and say why.

## Scope

This skill owns **how the interface behaves and how usable it is**: flows, states, feedback, forms, errors, navigation, accessibility, motion, perceived speed, and how AI features behave when they are unsure or wrong.

It does not pick a visual style. If a visual skill is installed (impeccable, frontend-design, ui-ux-pro-max), let it own colors, fonts and aesthetic direction. When both apply, decide behavior and structure first, then style. A beautiful screen with a broken flow is still broken.

## Choose the mode

- **Build mode**: the user wants a new or changed screen, flow, component or interaction. Follow the build workflow.
- **Review mode**: the user wants feedback, an audit, or "why does this feel off". Follow the review workflow and use `references/review-checklist.md`.
- **Quick fix**: a small, clear change ("add a loading state to this button"). Apply the relevant rules directly. Don't run the full workflow.

## Build workflow

1. **Name the user and the main task.** One sentence: who uses this screen and what they come to do. Everything else on the screen supports that task or gets demoted. If the user's request doesn't say, infer it from the code and product, and state your assumption.
2. **Sketch the flow before the screen.** List the steps from entry to done, including the way back (cancel, back, undo). Count the steps and cut any that don't need the user's decision.
3. **Write the state matrix.** For each screen or async component, decide what it shows when it is: first-time empty, loading, partly loaded, loaded, error, offline, and after success. See `references/states-and-feedback.md`. Missing states are the most common UX bug in generated code.
4. **Build with the core rules below**, reading the reference file for whatever the screen contains (forms, motion, Flutter, and so on).
5. **Self-check before saying done.** Run the "Before you finish" list at the end of this file. If you can run the app, click through the main task once, including one failure path.

## Core rules

These are the rules that most often separate a usable product from a demo. Each has a reason; apply the reason when a case isn't covered.

1. **Every action gets visible feedback within 100 ms.** Press states, ripple or a state change. Users click again when nothing happens, which causes double submits. If the result takes longer than about 1 second, show progress (see the timing table).
2. **One primary action per screen.** Make it visually dominant and label it with a verb that says what happens ("Save changes", "Create team"), not "Submit" or "OK". Secondary actions look secondary. Too many equal choices slow the decision (Hick's law).
3. **Every screen handles empty, loading, error and success.** An empty state explains what will appear and offers the first action. An error state says what happened and what to do next. Never leave a blank screen or an endless spinner.
4. **Prevent errors before reporting them.** Use the right input type, sensible defaults, constraints, and disabled-with-reason rather than letting users fail. When an error does happen, show it next to its cause, in plain language, with the fix.
5. **Prefer undo over "Are you sure?".** Confirmation dialogs get clicked through by habit. For reversible actions, act immediately and offer undo. Keep confirmations for actions that are truly destructive and irreversible, make them name the thing ("Delete 'Q3 report'?"), and label the buttons by outcome ("Delete report" / "Keep report"), not Yes/No.
6. **Use familiar patterns.** Users spend most of their time in other apps (Jakob's law). Put things where they expect: navigation, back, search, settings, profile. Novelty belongs in content and brand, not in basic controls.
7. **Make it reachable and readable for everyone.** Touch targets at least 44x44 pt (iOS) / 48x48 dp (Android); 24x24 CSS px is the WCAG 2.2 AA floor on web. Text contrast at least 4.5:1, UI components at least 3:1. Full keyboard use and visible focus on web. Labels for screen readers. Never use color as the only signal. See `references/accessibility.md`.
8. **Motion explains, it doesn't decorate.** Animate to show where something came from, what changed, or that an action worked. Keep most UI transitions around 150–300 ms, exits faster than entrances, and respect reduced-motion settings. See `references/motion.md`.
9. **Show progress and keep context in long flows.** Multi-step flows show the current step and total. Never lose user input on back, error, or refresh.
10. **Reveal complexity gradually.** Show what most users need now; put advanced options behind a clear "More options" or a second step (progressive disclosure). Fewer visible choices make the main task faster.

## Timing and size cheat sheet

| What | Rule |
|---|---|
| Feels instant | ≤ 0.1 s: just update the UI |
| Keeps users engaged | aim for ≤ 400 ms responses (Doherty threshold) |
| Wait < 1 s | no spinner or skeleton (it would flash); show the press state only |
| Wait ~2–10 s | skeleton for a whole page or list; labeled spinner for one module |
| Wait ≥ 10 s, uploads, downloads | determinate progress bar, time or step estimate, Cancel |
| UI transition length | most 150–300 ms; small feedback ~100 ms; large movements up to ~400 ms; exits faster than entrances |
| Touch target | ≥ 44x44 pt iOS, ≥ 48x48 dp Android/Material, ≥ 24x24 CSS px web (AA floor), with spacing between targets |
| Text contrast | ≥ 4.5:1 normal text, ≥ 3:1 large text and UI components/icons |
| Body text | ≥ 16 px on web; 45–75 characters per line; line height about 1.5 |
| Bottom navigation | 3–5 destinations, icon plus text label |
| Web performance (good) | LCP ≤ 2.5 s, INP ≤ 200 ms, CLS ≤ 0.1 |

Details and sources for each number live in the reference files.

## UX slop: patterns to replace

Generated UIs repeat the same behavioral mistakes. When you see one in code you write or review, replace it.

| Slop | Why it hurts | Do this instead |
|---|---|---|
| Spinner for every load, centered on a blank page | Feels slow; layout jumps when content arrives | Skeleton in the shape of the content; keep the page frame visible |
| "Something went wrong" | User can't act on it | Say what failed and what to do: "Couldn't save. Check your connection and try again." plus a Retry button |
| Errors only in a toast | Toasts vanish and sit far from the cause | Inline error at the field or section; toast only for global, non-blocking news |
| Placeholder used as the label | Disappears while typing; low contrast | Visible label above the field; placeholder only for an example format |
| Validating while the user types | Shows errors for half-typed input | Validate when the field loses focus or on submit; remove the error as soon as it's fixed |
| Submit button disabled with no reason | User can't tell what's missing | Keep it enabled and show what's missing on press, or explain next to the disabled button |
| Confirm dialog on every action | Trained click-through; slows everyone | Undo for reversible actions; confirm only destructive, irreversible ones |
| Icon-only buttons with unclear meaning | Users guess; screen readers say nothing | Icon plus text label, or at least a tooltip and an accessible name |
| Empty list shows nothing | Looks broken | Empty state: what goes here, why it's empty, one action to start |
| No pressed, hover or focus state | Feels dead; double taps | Clear pressed and focus styles on every interactive element |
| Gesture-only actions (swipe to delete, long-press menus) | Hidden; many users never find them | Always provide a visible alternative control |
| Animations everywhere, slow and bouncy | Feels sluggish; can cause motion sickness | Animate only state changes that need explaining; short durations; honor reduced motion |
| Modal on top of modal | Lost context; hard to escape | One modal at a time, or move the task to its own page |
| Controls marked only by soft shadows or glass (neumorphism, glassmorphism) | Low contrast; users can't tell what's clickable or pressed | Solid fill for the primary button, visible input borders, a second cue for states |
| Lost input after an error or back | Users give up | Keep form state; restore drafts |

## Review workflow

1. Identify the user's main task on the screen and walk through it step by step, as the user would.
2. Check the screen against `references/review-checklist.md`: heuristics, states, forms, accessibility, motion, performance.
3. Report each finding in this format, most severe first:

```
### [Severity] Short title
- Where: file:line or screen > component
- Problem: what the user experiences (not what the code does)
- Why it matters: the principle or rule it breaks
- Fix: the concrete change, with code when short
```

Severity: **Blocker** (user can't finish the task, or it fails accessibility in a way that excludes people), **Major** (frequent confusion, errors, or lost data), **Minor** (friction or inconsistency), **Polish** (small improvement). End with the three fixes that give the biggest gain for the least work. Skip praise padding; mention what already works only when it should be kept during fixes.

## When time is short (demos, hackathons)

Spend effort in this order. Each step gives more user-visible quality per minute than the next.

1. The main task works end to end, with one clear primary action per screen.
2. Loading, empty and error states on every screen that fetches data.
3. Instant press feedback and disabled-while-submitting on every button that calls the network.
4. Clear, specific error messages with a retry.
5. Touch target size, contrast, and labels.
6. One or two meaningful transitions (screen change, success confirmation). Skip decorative motion.

Seed the demo with realistic data so empty states don't appear by accident, but still build them.

## Reference files

Read only the file the current task needs.

| File | Read when |
|---|---|
| `references/states-and-feedback.md` | Anything that loads data, submits, saves, deletes, or can fail; toasts, dialogs, undo, optimistic UI |
| `references/forms-and-errors.md` | Any form, input, validation, sign-up, checkout, search box, or error message |
| `references/accessibility.md` | Building or reviewing any UI; contrast, focus, screen readers, touch targets, text size |
| `references/motion.md` | Animations, transitions, microinteractions, haptics, reduced motion |
| `references/navigation-and-layout.md` | App structure, menus, tabs, screen hierarchy, defaults, laws of UX, readability; fixing a generic "AI-made" look when no visual skill is installed |
| `references/ai-features.md` | Any AI or LLM feature: chat, assistant, suggestions, autocomplete, recommendations, generated content, adaptive UI, explanations |
| `references/flutter.md` | The project is Flutter or Dart |
| `references/web.md` | The project is web (HTML, CSS, React, Next.js, Vue, Svelte) |
| `references/review-checklist.md` | Review mode, or the final self-check on a large build |

## Before you finish

Check the work you just did against this list. Fix what fails, or say plainly what you left out.

- [ ] The main task can be completed, and its primary action is obvious.
- [ ] Every data-driven view has loading, empty, error and success states.
- [ ] Every button and link shows pressed (and on web, hover and focus) feedback.
- [ ] Network actions disable or guard against double submit and show progress after ~1 s.
- [ ] Error messages say what happened and how to fix it, next to the cause.
- [ ] Destructive actions are either undoable or confirmed by name.
- [ ] Touch targets, contrast and labels meet the cheat-sheet numbers.
- [ ] Motion is short, purposeful, and respects reduced-motion settings.
- [ ] User input survives errors, back navigation and validation.
