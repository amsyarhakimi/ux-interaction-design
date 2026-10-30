# Forms and Error Messages

Read this for any form, input, validation, sign-up, login, checkout, search box, or error message.

## Contents
1. Before building: cut fields
2. Layout
3. Labels and help text
4. Required and optional
5. Input types, keyboards and autofill
6. Choosing controls
7. Validation timing
8. Error messages
9. Passwords and sign-in
10. Submit and after submit
11. Sources

## 1. Before building: cut fields

Every field costs effort and some users. Baymard found the average checkout had 11.3 fields while most sites need only about 8. For each field ask: is it needed now, can it be inferred, can it be asked later?

- One "Full name" field beats first + last: 42% of Baymard's testers typed their full name into "First name".
- Hide rarely needed fields behind a link: address line 2, coupon code, a separate billing address (default to "same as shipping").
- Ask for account creation after the main task (for example on the confirmation step), not before it.
- Prefill what you already know (country from locale, email from the account).

## 2. Layout

- **One column.** Multi-column forms cause skipped and misread fields. Exception: short, related fields can share a line (city / postcode, expiry / CVC, date parts).
- Group related fields under a short heading.
- Size each field to its expected input. A postcode field shouldn't be full width.
- Long forms: split into steps with a progress indicator ("Step 2 of 3"), and allow back without losing input. Showing progress makes people push to finish (goal-gradient effect).

## 3. Labels and help text

- Always use a visible label outside the field, above it on mobile. A placeholder is not a label: it disappears on typing, makes review and error fixing hard, and is mistaken for prefilled data.
- Placeholders may show an example format ("e.g. 010-1234-5678"), never essential instructions.
- Put format rules and constraints before the user types, under the label, not only after an error.
- Floating labels are untested for usability; if you use them, keep room for full error text and keep the label readable when it floats.
- Connect labels and help text programmatically (`<label for>`, `aria-describedby`; Flutter `InputDecoration.labelText` / `helperText`).

## 4. Required and optional

- Baymard: mark both. Asterisk for required, "(optional)" for optional. Only 14% of sites do both, and users guess wrong otherwise.
- NN/g: put the required asterisk in high contrast, explain it once at the top, and make it available to screen readers (`aria-required` or the `required` attribute).
- Keep optional fields to one or two. If most fields are optional, question why they're there.

## 5. Input types, keyboards and autofill

Web:
- Use the right `type`: `email`, `tel`, `url`, `number` (only for real quantities; use `inputmode="numeric"` for codes, card numbers and postcodes so leading zeros survive).
- Use `inputmode` and `pattern` to bring up the right mobile keyboard.
- Turn off autocorrect and autocapitalize for names, emails, usernames and codes (`autocapitalize="off"`, `autocorrect="off"`, `spellcheck="false"`).
- Use `autocomplete` tokens so browsers and password managers fill fields: `name`, `email`, `tel`, `street-address`, `postal-code`, `username`, `new-password`, `current-password`, `one-time-code`, `cc-number`, `cc-exp`.

Flutter:
- `keyboardType: TextInputType.emailAddress / phone / number / url`.
- `textInputAction: TextInputAction.next` to move between fields, `.done` on the last.
- `autofillHints: [AutofillHints.email]` and wrap related fields in `AutofillGroup`.
- `autocorrect: false` and `enableSuggestions: false` for emails, usernames, codes.

## 6. Choosing controls

| Situation | Control |
|---|---|
| 2–5 options, all worth seeing | Radio buttons or segmented control (not a dropdown) |
| Many options (countries) | Searchable select / autocomplete |
| On/off that applies immediately | Switch / toggle |
| On/off that applies on submit | Checkbox |
| Number in a small range | Stepper or slider with visible value |
| Date | Native date picker; for well-known dates (birthday) allow typing |

Don't add Reset or Clear-all buttons to forms; they get pressed by mistake.

## 7. Validation timing

- Validate a field **when the user leaves it** (on blur), not while they type. Showing "invalid email" after one letter is hostile. NN/g: don't show errors before the user has deliberately entered input.
- Exception: fixed-length inputs (postcode, card number, CVC, OTP) can validate once the expected length is reached.
- Once an error is showing, re-check **on every keystroke** and remove the error the moment it's fixed.
- Show positive confirmation for fields that are easy to get wrong (a check mark for a valid email or username available).
- Always validate again on submit and on the server. On submit, move focus to the first invalid field and show a summary for long forms.
- About a third of sites have no inline validation at all (Baymard); adding it is a cheap, visible win.

## 8. Error messages

NN/g's error message rubric, condensed:

**Visibility**
- Put the message next to its cause, visible without scrolling or clicking.
- Use at least two or three signals reserved for errors: text, icon, and border or color. Never color alone.
- Match severity. Warnings shouldn't look like errors; modals only for serious decisions.

**Communication**
- Plain language, low reading level, no jargon or raw codes.
- Short and specific, key information first.
- Tell the user how to fix it.
- Neutral, respectful tone. Blame the system, not the user. No jokes.

**Efficiency**
- Keep what the user typed, even if wrong, and refer to it.
- Offer the likely fix ("Did you mean gmail.com?").
- Link help for that specific error when it's complex.
- Prevent the error upstream next time (better defaults, constraints, formatting).

Rewrite examples:

| Bad | Better |
|---|---|
| Invalid input | Enter an email like name@example.com |
| Error 422 | That username is taken. Try "amsyar.dev" or add a number. |
| Password does not meet requirements | Use at least 8 characters. |
| Something went wrong | Couldn't save your changes. Check your connection and try again. |
| Field required | Enter your team name |

## 9. Passwords and sign-in

- Baymard: 82% of sites had overly complex password rules, and users who went through a password reset abandoned checkout 18.75% of the time.
- Require a minimum length (NIST: at least 8; longer is better), allow all characters including spaces and paste, and don't set a low maximum. Show the rule before typing.
- Offer a show/hide password toggle instead of a "confirm password" field.
- Let password managers work: correct `autocomplete` / `autofillHints`, no paste blocking.
- WCAG 2.2 (3.3.8 Accessible Authentication, AA): don't require a memory or puzzle test to log in unless there's an alternative; allow paste and password managers. See `accessibility.md`.
- Don't ask users to re-enter information they already gave in the same flow (WCAG 3.3.7 Redundant Entry).

## 10. Submit and after submit

- The submit button names the result: "Create account", "Send message", "Pay ₩12,000".
- On press: show progress in the button, prevent double submit, keep the form visible.
- On failure: keep every value, show the error inline (field-level or form-level), focus it.
- On success: confirm with what happened and what's next ("Team created. Invite members?").
- Don't clear a form after a server error.

## 11. Sources

- Baymard, Avoid multi-column forms — https://baymard.com/blog/avoid-multi-column-forms
- Baymard, Checkout flow average form fields — https://baymard.com/blog/checkout-flow-average-form-fields
- Baymard, Inline form validation — https://baymard.com/blog/inline-form-validation
- Baymard, Required and optional form fields — https://baymard.com/blog/required-optional-form-fields
- Baymard, Mobile forms: avoid inline labels — https://baymard.com/blog/mobile-forms-avoid-inline-labels
- Baymard, Mobile touch keyboards — https://baymard.com/blog/mobile-touch-keyboards
- Baymard, Password requirements — https://baymard.com/blog/password-requirements-and-password-reset
- NN/g, Web form design — https://www.nngroup.com/articles/web-form-design/
- NN/g, Placeholders in form fields — https://www.nngroup.com/articles/form-design-placeholders/
- NN/g, Marking required fields — https://www.nngroup.com/articles/required-fields/
- NN/g, Error message scoring rubric — https://www.nngroup.com/articles/error-messages-scoring-rubric/
- web.dev, Autofill — https://web.dev/learn/forms/autofill
- Laws of UX, Goal-gradient effect — https://lawsofux.com/goal-gradient-effect/
