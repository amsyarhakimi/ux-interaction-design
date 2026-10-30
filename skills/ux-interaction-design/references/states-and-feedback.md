# States and Feedback

Read this for anything that loads data, submits, saves, deletes, or can fail.

## Contents
1. The state matrix
2. Response times and which indicator to use
3. Feedback for actions
4. Toasts, inline messages and dialogs
5. Undo vs confirmation
6. Optimistic UI
7. Empty states
8. Error states
9. Offline
10. Sources

## 1. The state matrix

Before building a screen or async component, write down what it shows in each state. A screen that only has the "ideal" state is unfinished.

| State | What the user sees | Common mistake |
|---|---|---|
| Loading (first load) | Skeleton in the shape of the content, or a labeled spinner for a small module | Blank screen, or a full-page spinner that hides the frame |
| Empty (first use) | What goes here, why it's empty, one action to start | Nothing at all, or "No data" |
| Empty (no results) | "No results for 'xyz'", plus ways to widen the search or clear filters | Same message as first-use empty |
| Partial | Few items; layout still looks intentional and invites more | Layout built only for 20+ items looks broken with 1 |
| Ideal | The content | — |
| Error | What failed, what to do, a Retry; the rest of the page still works | "Something went wrong" with no action |
| Offline | What still works, what's queued, when it will sync | Same as a generic error |
| Success | Confirmation that names the result ("Team 'Blue' created") and a next step | Silent success, so users repeat the action |
| Refreshing / background update | Existing content stays; a small indicator shows the update | Content disappears and is replaced by a spinner |

Scott Hurff's "UI stack" (ideal, empty, error, partial, loading) is the usual name for this idea; offline and success are added here because apps that sync data need them.

Loading inline, not as a full-page takeover, keeps filters, search and navigation usable while content loads.

## 2. Response times and which indicator to use

Jakob Nielsen's three limits:

- **0.1 s**: feels instant. Just show the result.
- **1 s**: the user's flow of thought stays intact. Delays between 0.1 and 1 s need no loading indicator, but the control must still show it was pressed.
- **10 s**: the limit of attention. Beyond this, show percent-done, an estimate, and a way to cancel.

The Doherty threshold (IBM, 1982) says keeping system response under about **400 ms** keeps people productive and engaged. Treat 400 ms as the target for interactive responses.

Which indicator (NN/g):

| Expected wait | Use |
|---|---|
| < 1 s | No spinner or skeleton; it would flash. Show the press state only. |
| ~2–10 s, whole page or list | Skeleton screen that mirrors the real layout |
| ~2–10 s, one module (card, chart, video) | Spinner inside that module, with a label ("Loading comments…") |
| ≥ 10 s | Determinate progress bar, padded time estimate, Cancel |
| Upload, download, conversion | Progress bar, never a skeleton. For batches: "Updating 3 of 50" |

Rules:
- Label waits with what is happening ("Uploading photo…"), not a static "Loading…".
- Don't make skeletons that only draw the header and footer; they should mirror the content.
- Shimmer and pulse animations can distract and cause accessibility problems; keep them subtle and stop them under reduced motion.
- Skeletons don't replace performance work. Make it faster first.
- Start progress bars slower and finish faster; it feels quicker.
- Never show "Don't click twice". Show that the first click registered and guard against the second.
- Avoid flicker on fast responses: delay showing an indicator by about 300–500 ms, and once it shows, keep it visible for a short minimum so it doesn't flash. (Implementation practice, not a published standard.)

## 3. Feedback for actions

- Every interactive element needs a pressed state. Apple's HIG: a custom button without a press state feels unresponsive. On web, also hover and keyboard focus.
- For actions that don't finish instantly, show progress inside the button and change its label: "Save" becomes "Saving…". Disable it, or ignore repeat presses, until the request finishes. This prevents duplicate submits.
- After success, confirm in the place the user is looking: the button shows "Saved ✓" briefly, the new item appears highlighted in the list, or a small nonmodal message appears.
- Keep one or two prominent buttons per view. Never give the most prominent style to a destructive action.
- Feedback should arrive right after the action (Saffer's microinteraction model: trigger, rules, feedback, loops and modes).

## 4. Toasts, inline messages and dialogs

| Message type | Use | Don't use for |
|---|---|---|
| Inline message (next to field or section) | Validation errors, section load errors, warnings about specific content | — |
| Toast / snackbar (nonmodal, disappears after a few seconds) | Passive confirmation of the user's own action ("Archived"), with optional Undo | Errors. NN/g calls toasts a bad choice for error messages: they vanish and sit away from the cause |
| Banner (persistent, top of page or section) | System status that lasts: offline, trial ending, sync paused | One-off confirmations |
| Modal dialog | Decisions the user must make before continuing; serious, irreversible consequences | Confirming success, routine notices, anything a nonmodal message can do |

If a toast carries an action (Undo), keep it on screen long enough to act, and make sure keyboard and screen-reader users can reach it.

## 5. Undo vs confirmation

Prefer undo wherever it's technically possible. It lets people work quickly and catches mistakes that a habitually-dismissed dialog would not.

Use a confirmation dialog only when the consequence is serious (destroying work, spending money) and especially when it can't be undone. Overused confirmations train people to click through without reading.

When you do confirm:
- Restate the action and its consequence: "Delete 'Q3 report'? This removes it for everyone on the team."
- Label buttons by outcome ("Delete report" / "Keep report"), not Yes/No or OK/Cancel.
- Never make the destructive option the default.
- For the rare most dangerous actions (delete a whole account or project), require a deliberate step, such as typing the name.

Soft-delete pattern for undo: mark the item deleted, hide it, show "Deleted. Undo" for several seconds, and only purge it afterwards.

## 6. Optimistic UI

For actions that almost always succeed (like, star, rename, reorder, mark done), update the UI immediately and send the request in the background. If it fails, roll back and tell the user inline what didn't save, with Retry.

Don't use optimistic updates for payments, irreversible actions, or anything where showing a false success would cause harm.

React's `useOptimistic` reverts automatically when the action fails, but you still need to catch and show the error. In Flutter, keep the previous value, update state, and restore it in the `catch`.

## 7. Empty states

NN/g's three jobs of an empty state:
1. **Communicate status.** Say why it's empty: "No tasks due this week." Never show "No records" while data is still loading; users stop trusting the screen when items appear later.
2. **Teach.** Explain what will appear here and how: "Star a project to pin it here."
3. **Give a direct path.** One primary action ("Create your first project"), optionally a "Learn more" link or sample data.

Never leave a panel completely blank. Don't hide or disable a tab just because its content is empty; show it and explain why (Apple HIG).

## 8. Error states

- Say what happened in plain words, then what the user can do: "Couldn't load your teams. Check your connection and try again." Add a Retry button.
- Blame the system, not the user. No jargon, no raw error codes in the main message (put a short code in small text if support needs it).
- Keep the rest of the screen working. A failed widget shouldn't take down the page.
- Preserve whatever the user typed or selected.
- Distinguish "you're offline" from "our service is down"; the user's next step differs.

Form and field errors: see `forms-and-errors.md`.

## 9. Offline

From web.dev's offline UX guidelines:
- Tell users what state the app is in and what still works: "You're offline. Messages will send when you reconnect."
- Keep content browsable; disable only actions that truly need a connection (Buy, Send).
- Queue actions instead of blocking with full-screen modals, and show sync status.
- Don't signal offline with color or graying out alone, and avoid technical words.

## 10. Sources

- Nielsen, Response Times: The 3 Important Limits — https://www.nngroup.com/articles/response-times-3-important-limits/
- NN/g, Progress Indicators — https://www.nngroup.com/articles/progress-indicators/
- NN/g, Skeleton Screens — https://www.nngroup.com/articles/skeleton-screens/
- Laws of UX, Doherty Threshold — https://lawsofux.com/doherty-threshold/
- Apple HIG, Buttons — https://developer.apple.com/design/human-interface-guidelines/buttons
- Apple HIG, Tab bars — https://developer.apple.com/design/human-interface-guidelines/tab-bars
- NN/g, Indicators, Validations and Notifications — https://www.nngroup.com/articles/indicators-validations-notifications/
- NN/g, Confirmation Dialogs — https://www.nngroup.com/articles/confirmation-dialog/
- NN/g, Empty States — https://www.nngroup.com/articles/empty-state-interface-design/
- web.dev, Offline UX design guidelines — https://web.dev/articles/offline-ux-design-guidelines
- React, useOptimistic — https://react.dev/reference/react/useOptimistic
- Dan Saffer, Microinteractions (O'Reilly, 2013)
