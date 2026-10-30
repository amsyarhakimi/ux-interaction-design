# ux-interaction-design

**A Claude Code skill that makes AI-built UI work for real users, not just look good.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
![Claude Code plugin](https://img.shields.io/badge/Claude_Code-plugin-D97757)
![Agent Skills](https://img.shields.io/badge/Agent_Skills-SKILL.md-555)
![Web and Flutter](https://img.shields.io/badge/works_with-Web_%7C_Flutter-02569B)

AI coding tools are good at making screens look polished. They are worse at the behavior: the button that gives no feedback, the list with no empty state, the error that says "Something went wrong", the form that complains while you're still typing.

This skill teaches Claude to build and review UI the way a careful product designer would: around the user's task, with every screen state handled and clear feedback for every action. It works for **web** (HTML, CSS, React, Next.js, Vue, Svelte) and **Flutter**.

---

## What changes

| Without the skill, Claude often… | With the skill, Claude… |
|---|---|
| Shows a blank screen or a spinner while loading | Builds a skeleton shaped like the content, plus empty, error and success states |
| Writes "Something went wrong" | Says what failed and how to fix it, with a Retry button |
| Uses placeholder text as the field label | Uses a visible label and validates when the user leaves the field |
| Asks "Are you sure?" with Yes / No | Offers Undo, or confirms by name: "Delete team" / "Keep team" |
| Adds icon-only buttons with tiny tap areas | Uses 44–48 px targets with labels screen readers can read |
| Animates everything, slowly | Uses short, purposeful motion and respects reduced-motion settings |
| Lets the AI feature interrupt and act on its own | Lets the AI suggest when unsure, act only when confident, and always undo |

## Features

- **Build mode**: user and task first, then the flow, then a state matrix for every screen
- **Review mode**: severity-ranked findings (Blocker, Major, Minor, Polish) with concrete fixes
- **Quick fixes**: applies the relevant rule directly for small changes
- **"Time is short" priority list** for demos and hackathons
- **UX slop table**: 15 common AI-generated UX mistakes and their fixes
- **Accessibility built in**: WCAG 2.2 AA contrast, target size, focus, screen readers, text scaling
- **AI feature rules**: when the AI should act, ask or stay quiet; explanations users can act on; chat and generation UX
- **Platform guides** for Flutter (Material 3, Semantics, SnackBar undo, a11y tests) and web (focus, forms, Core Web Vitals, View Transitions)

It does **not** pick colors or fonts. Pair it with a visual skill such as [impeccable](https://github.com/pbakaus/impeccable), Anthropic's frontend-design, or [ui-ux-pro-max](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill). This skill decides behavior and structure; the visual skill decides style.

## Install

Replace `YOUR-GITHUB-USERNAME` with the account that hosts this repo.

**Option 1: Claude Code plugin (recommended)**

```
/plugin marketplace add YOUR-GITHUB-USERNAME/ux-interaction-design
/plugin install ux-interaction-design@ux-interaction-design
```

**Option 2: skills CLI (Claude Code, Cursor, Codex and other agents)**

```
npx skills add YOUR-GITHUB-USERNAME/ux-interaction-design
```

**Option 3: manual copy**

```
git clone https://github.com/YOUR-GITHUB-USERNAME/ux-interaction-design.git
cp -r ux-interaction-design/skills/ux-interaction-design ~/.claude/skills/
```

Restart Claude Code after installing. Run `/plugin` to confirm it's enabled.

## Usage

The skill turns on by itself for UI work. Try:

```
Build the team sign-up screen in Flutter with name, email and role.
Add loading and error handling to the dashboard.
Review lib/screens/home.dart for UX problems.
The app feels clunky. What's wrong with it?
Add a smooth transition when a card opens.
Add an AI "suggest a reply" button to the chat screen.
```

To call it explicitly:

```
Use the ux-interaction-design skill to audit src/pages/checkout.tsx.
```

### Example review output

```
### [Blocker] Deleted team stays in the list
- Where: lib/screens/team_screen.dart:38 > deleteTeam
- Problem: The user taps Delete, confirms, and the team is still there, so they try again.
- Why it matters: Visibility of system status; every action needs visible feedback.
- Fix: Remove the item from state right away, show "Deleted 'Blue'. Undo", and restore it on failure.
```

## How it works

```
skills/ux-interaction-design/
├── SKILL.md                          # workflow, core rules, cheat sheet, UX slop table
└── references/                       # each loaded only when the task needs it
    ├── states-and-feedback.md        # loading, empty, error, offline, toasts, undo, optimistic UI
    ├── forms-and-errors.md           # labels, validation timing, error messages, passwords
    ├── accessibility.md              # WCAG 2.2 AA, targets, keyboard, screen readers
    ├── motion.md                     # durations, easing, states, reduced motion, haptics, sound
    ├── navigation-and-layout.md      # nav patterns, hierarchy, laws of UX, readability, AI-look fixes
    ├── ai-features.md                # mixed-initiative AI, explanations, chat and generation UX
    ├── flutter.md                    # Flutter APIs for all of the above
    ├── web.md                        # web APIs for all of the above
    └── review-checklist.md           # heuristics checklist and testing with users
```

Only the skill's name and description load at the start of a session. The main file loads when a UI task matches, and each reference file loads only when that topic comes up. This keeps token use low.

## Tips

- **Using ui-ux-pro-max too?** Make it manual-only so it doesn't load on every UI task: add `disable-model-invocation: true` to its SKILL.md frontmatter, then call it with `/ui-ux-pro-max` when you pick a palette.
- **Hackathon or demo?** Ask for the "time is short" order. It puts working states and feedback before visual polish.
- **Neumorphism or glass style?** The skill flags controls that rely only on soft shadows, which usually fail contrast.

## Research basis

Every reference file lists its sources. The main ones:

- **Usability**: Nielsen Norman Group (heuristics, response times, empty states, error messages, navigation), Laws of UX, Baymard Institute (forms, checkout)
- **Accessibility**: W3C WCAG 2.2, WAI-ARIA Authoring Practices, Apple Human Interface Guidelines, Android accessibility
- **Motion and platforms**: Material Design 3 motion tokens, MDN, web.dev Core Web Vitals, Flutter documentation
- **AI and adaptive interfaces**: Horvitz, *Principles of Mixed-Initiative User Interfaces* (CHI 1999); Gajos et al., *Predictability and Accuracy in Adaptive User Interfaces* (CHI 2008); Kulesza et al., *Principles of Explanatory Debugging* (IUI 2015); Amershi et al., *Guidelines for Human-AI Interaction* (CHI 2019)
- **Practice**: Upslide Design Studio, *UI UX Best Practices: 12 Principles for Better UX*

Numbers in the skill were checked against these sources in October 2026. Folk rules (like 60-30-10) are labeled as such or left out.

## Contributing

This is a personal project and I'm not accepting pull requests. Feel free to fork it.

## Changelog

- **1.1.0** — Added `ai-features.md` (mixed-initiative AI, Microsoft's 18 human-AI guidelines, explanatory debugging, chat UX), UI sound guidance, design tokens, F-pattern note.
- **1.0.0** — First release: build and review workflows, states, forms, accessibility, motion, navigation, Flutter and web guides.

## License

MIT. See [LICENSE](LICENSE).

This is a community skill. It is not affiliated with or endorsed by Anthropic.
