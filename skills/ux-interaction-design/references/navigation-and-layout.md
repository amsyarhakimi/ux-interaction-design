# Navigation, Structure and Layout

Read this for app structure, menus, tabs, screen hierarchy, readability, and the laws of UX. The last section covers visual fixes for when no visual-style skill is installed.

## Contents
1. Laws of UX for builders
2. Navigation patterns
3. Screen hierarchy
4. Progressive disclosure and defaults
5. Readability
6. Spacing and grouping
7. If no visual skill is installed: avoiding the generic AI look
8. Sources

## 1. Laws of UX for builders

| Law | What it says | Apply it |
|---|---|---|
| Fitts's law | Time to hit a target depends on its size and distance | Big primary buttons near where the thumb or cursor already is; spacing between targets |
| Hick's law | Decision time grows with the number of choices | Fewer visible options; highlight a recommended one; split long choices into steps |
| Jakob's law | Users expect your app to work like the others they use | Conventional placement for nav, search, back, cart, profile |
| Miller's law | Working memory holds a few chunks | Chunk long numbers and lists; group settings. Don't use "7" as an arbitrary limit |
| Tesler's law | Some complexity can't be removed, only moved | Move it into the system: smart defaults, address lookup, card-type detection |
| Von Restorff effect | The item that differs is remembered | One distinct primary action; restraint elsewhere; not by color alone |
| Serial position effect | First and last items are remembered best | Key nav items at the ends of a list or bar |
| Peak-end rule | Experiences are judged by their peak and end | Polish the success moment and the final screen; avoid painful peaks |
| Goal-gradient effect | Effort rises as the goal gets closer | Show progress ("Step 2 of 3"); a head start motivates |
| Doherty threshold | Productivity rises when response < ~400 ms | Fast feedback; see `states-and-feedback.md` |

## 2. Navigation patterns

- **Visible beats hidden.** In NN/g's study, hidden (hamburger) navigation was used far less than visible navigation (57% vs 86% on mobile), was slower, and felt harder. On desktop, never hide primary navigation. On mobile, show up to 4 top-level items as visible links or tabs; put the rest in a menu.
- **Bottom navigation bar (mobile):** 3–5 destinations of equal importance, icon plus short text label, same bar on every top-level screen. Tabs are for navigation, not actions; put actions in the screen or a toolbar.
- **Don't hide or disable a tab** because its content is empty; show it and explain.
- **Wide screens:** switch to a side navigation rail or sidebar.
- **Where am I?** Highlight the active nav item, give every screen a clear title, use breadcrumbs for deep hierarchies.
- **Back** always goes to the previous screen and never loses work. Warn only when leaving would discard unsaved input.
- **Thumb reach:** people hold phones in many ways (one-handed, cradled, two-handed) and use either thumb. Put the main action where it's easy to reach in common grips, usually lower on the screen, but don't trust the classic "thumb zone" heatmaps too much; their author later walked them back.
- **Search:** if the app has a lot of content, search should be visible (a field or a clear icon), and results should handle typos and empty results.

## 3. Screen hierarchy

- One screen, one main job. Decide what the user does most here and make it the most prominent thing.
- One primary action, visually dominant. Secondary actions quieter (outlined or text buttons). Destructive actions never get the primary style.
- Order content by importance and task sequence, top to bottom, left to right (in LTR languages).
- Squint test: blur your eyes. The most important element should still stand out; if everything stands out, nothing does.
- NN/g: use contrast, scale and grouping. Limit to about 3 text sizes per screen and 2 large elements.
- Make hierarchy with weight and color as well as size; size alone makes layouts jumpy.

## 4. Progressive disclosure and defaults

- Show the core, frequent options first; put advanced or rare options behind a clearly labeled control ("Advanced settings", "More filters").
- No more than **2 levels** of disclosure; deeper nesting gets users lost.
- Staged disclosure (a wizard) is different: everyone passes every step. Use it for linear tasks like onboarding or checkout.
- **Defaults are the experience** for most users, who never change them. Prefill the most common or most likely value (country from locale, today's date, last used option). Never default to the most expensive or riskiest option, and don't let alphabetical order pick by accident.
- Remember the user's last choices where it helps (last used filter, sort order).

## 5. Readability

| Property | Guideline |
|---|---|
| Body text size | 16 px web baseline (16–18 px common); iOS default 17 pt; inputs ≥ 16 px on web |
| Line length | 45–75 characters (66 often cited as ideal); `max-inline-size: 66ch` |
| Line height | About 1.5 for body text; tighter (1.1–1.25) for large headings |
| Sizes per screen | About 3–4 text sizes |
| Weights | Usually 2 (regular and semibold or bold) |
| Families | One, or two clearly different ones |
| All caps | Only short labels, with extra letter spacing |
| Alignment | Left-aligned body text; avoid justified text and centered paragraphs |

Material 3 type scale (sp) for reference: Display 57/45/36, Headline 32/28/24, Title 22/16/14, Body 16/14/12, Label 14/12/11.

People scan before they read. NN/g's eye-tracking found an F-shaped scanning pattern on pages with walls of text: people read the first lines, then only the left edge. Treat that as a warning, not a layout to copy. Break text with clear headings, put the key words first in headings and list items, and use short paragraphs and lists.

Write like a person: short sentences, specific verbs, the user's words. Buttons describe outcomes. Headings say what the screen is for.

## 6. Spacing and grouping

- Use a spacing scale, commonly multiples of 4 or 8 (4, 8, 12, 16, 24, 32, 48, 64). Neighbouring values should be clearly different.
- **Proximity groups things** more strongly than color or borders (NN/g). A label sits closer to its field than to the next one; a heading sits closer to its section than to the one above.
- More space between groups than within them. Add borders or backgrounds only when spacing isn't enough.
- Check that groups still read correctly when columns stack on mobile.

**Design tokens keep this consistent.** Don't scatter raw hex values and pixel numbers through components. Use three layers: primitive values (`blue-600`, `space-4`), semantic roles that point to them (`color-primary`, `color-error`, `space-section`), and component tokens where needed (`button-primary-bg`). In Flutter this is `ColorScheme` and `TextTheme`; on the web, CSS custom properties or a Tailwind theme. For a shared JSON format, the Design Tokens Community Group published its first stable spec (2025.10); it's a W3C Community Group report, not a W3C Recommendation.

## 7. If no visual skill is installed: avoiding the generic AI look

If impeccable, frontend-design or ui-ux-pro-max is installed, let it own this. Otherwise, avoid the patterns designers now associate with machine-made UI. They don't just look generic; most also weaken hierarchy and clarity.

| Tell | Fix |
|---|---|
| Purple/indigo gradient on white, gradient text in headings | Palette from the product or brand, 4–6 colors named by role (primary, surface, text, error…); gradient at most in one place |
| Inter or system font everywhere, all one weight | Choose 1–2 typefaces on purpose; use a type scale; weight for hierarchy |
| Centered hero + three identical icon feature cards | Lead with the real product or the user's main task; vary layout to match content importance |
| Card soup: identical rounded cards, same shadow, nested cards | Cards only for items that act independently; rows or tables for lists of data; vary size to show importance |
| Emoji as icons or bullets | One consistent icon set (same stroke, size, style) with text labels |
| Glassmorphism, glows, neon on dark | Solid surfaces; if you use glass, meet contrast and use it sparingly |
| Same fade-up animation on every section | One meaningful transition; otherwise animate only in response to actions |
| Generic copy ("Build faster. Ship smarter.") and invented stats | Specific copy about this product; remove numbers you can't source |
| Everything centered | Left-align text and forms; center only short, standalone elements |

**Soft UI / neumorphism and glass styles** are popular because they look calm, but they often break usability:
- Soft shadows rarely reach 3:1 against the surface, so a button or input whose only boundary is a shadow fails WCAG 1.4.11. Give inputs a visible border and the primary button a solid accent fill.
- Raised vs pressed (inset) states look alike. Add a second cue for state: fill color, icon, or label change ("Following").
- Light grey text on light grey surfaces fails 4.5:1. Keep text dark.
- Users shouldn't have to guess what is clickable. Use the soft style for containers and decoration; make interactive elements clearly distinct.
- Keep icon labels and a strong focus ring.

Dark mode: use dark gray surfaces (Material's baseline is #121212), not pure black; lighter, less saturated brand colors; higher surfaces slightly lighter; and re-check contrast.

Icons: only a few are universally understood (home, search, print). Pair icons with visible text labels, especially in navigation; hover-only labels don't work on touch.

## 8. Sources

- Laws of UX — https://lawsofux.com/
- NN/g, Hamburger menus and hidden navigation — https://www.nngroup.com/articles/hamburger-menus/
- Android, Navigation bar — https://developer.android.com/develop/ui/compose/components/navigation-bar
- Apple HIG, Tab bars — https://developer.apple.com/design/human-interface-guidelines/tab-bars
- Steven Hoober, How do users really hold mobile devices? — https://www.uxmatters.com/mt/archives/2013/02/how-do-users-really-hold-mobile-devices.php
- NN/g, Progressive disclosure — https://www.nngroup.com/articles/progressive-disclosure/
- NN/g, The power of defaults — https://www.nngroup.com/articles/the-power-of-defaults/
- NN/g, Minimize cognitive load — https://www.nngroup.com/articles/minimize-cognitive-load/
- NN/g, Visual hierarchy — https://www.nngroup.com/articles/visual-hierarchy-ux-definition/
- NN/g, Gestalt proximity — https://www.nngroup.com/articles/gestalt-proximity/
- NN/g, Icon usability — https://www.nngroup.com/articles/icon-usability/
- Baymard, Line length readability — https://baymard.com/blog/line-length-readability
- web.dev, Typography — https://web.dev/learn/design/typography
- Material Components, Typography — https://github.com/material-components/material-components-android/blob/master/docs/theming/Typography.md
- Google codelab, Material dark theme — https://codelabs.developers.google.com/codelabs/design-material-darktheme/
- NN/g, F-shaped pattern of reading on the web — https://www.nngroup.com/articles/f-shaped-pattern-reading-web-content/
- Design Tokens Community Group, first stable version 2025.10 — https://www.w3.org/community/design-tokens/2025/10/28/design-tokens-specification-reaches-first-stable-version/
- Axess Lab, Neumorphism the accessible and inclusive way — https://axesslab.com/?p=2528
- Anthropic, Improving frontend design through skills — https://claude.com/blog/improving-frontend-design-through-skills
- Upslide Design Studio, UI/UX best practices — https://upslidedesignstudio.com/blogs/best-ui-ux-design-practices
