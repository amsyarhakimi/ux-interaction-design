# Motion and Microinteractions

Read this for animations, transitions, press feedback, haptics, and reduced motion.

## Contents
1. When motion earns its place
2. Durations
3. Easing
4. Interaction states
5. What to animate (performance)
6. Reduced motion and accessibility
7. Haptics (and sound)
8. Gestures
9. Recipes
10. Sources

## 1. When motion earns its place

Animate for one of these reasons, or don't animate:
- **Feedback**: the action registered (press, toggle, success check).
- **Orientation**: where something came from or went (a card expanding into a page, a sheet sliding up from its button).
- **Continuity**: what changed between two states (list item inserted, tab content swapping).
- **Attention**: one important change the user must notice. Use rarely.

Apple's HIG: avoid adding motion to interactions that happen frequently, let people cancel or skip motion, never make them wait for an animation to finish, and never use motion as the only way to convey information.

Dan Saffer's microinteraction model is a good checklist for any small interaction: **trigger** (what starts it), **rules** (what happens), **feedback** (how the user sees it), **loops and modes** (what happens over time or in special states).

## 2. Durations

NN/g:
- Most UI animations: **100–500 ms**.
- Simple feedback (checkbox, toggle, press): about **100 ms**.
- Medium changes (a modal or sheet appearing): about **200–300 ms**.
- Large movements across big screens: up to about **400 ms**. At 500 ms motion starts to feel like a drag.
- The more often an animation is seen, the shorter and subtler it should be.
- **Exits are faster than entrances** (for example 300 ms in, 200–250 ms out). Users want things to get out of the way.

Material 3 duration tokens (Flutter: `Durations.short1` … `Durations.extralong4`):

| Token | ms |
|---|---|
| short1–4 | 50 / 100 / 150 / 200 |
| medium1–4 | 250 / 300 / 350 / 400 |
| long1–4 | 450 / 500 / 550 / 600 |
| extralong1–4 | 700 / 800 / 900 / 1000 |

Scale duration with the distance or area the element travels. Small things move fast; full-screen transitions take longer.

## 3. Easing

Use ease-out (decelerate) for elements entering, ease-in (accelerate) for elements leaving, and a standard curve for things that move within the screen. Never use linear for movement; it looks mechanical. Linear is fine for opacity-only fades and continuous spinners.

Material 3 easing tokens:

| Token | CSS | Use |
|---|---|---|
| standard | `cubic-bezier(0.2, 0, 0, 1)` | Moves within the screen |
| standardDecelerate | `cubic-bezier(0, 0, 0, 1)` | Enters |
| standardAccelerate | `cubic-bezier(0.3, 0, 1, 1)` | Exits |
| emphasizedDecelerate | `cubic-bezier(0.05, 0.7, 0.1, 1)` | Important element enters |
| emphasizedAccelerate | `cubic-bezier(0.3, 0, 0.8, 0.15)` | Important element exits |
| emphasized | two-part path (no single cubic-bezier) | Large transitions that start and end on screen; Flutter `Curves.easeInOutCubicEmphasized` |

Flutter exposes most of these as `Easing.standard`, `Easing.standardDecelerate`, `Easing.emphasizedDecelerate`, and so on.

Springs: physics-based motion (Material 3 Expressive, SwiftUI springs, Framer Motion springs) feels natural for drag, swipe and interruptible gestures because it keeps the finger's velocity. Use springs for gesture-driven motion and keep them well damped (little bounce) for routine UI.

## 4. Interaction states

Every interactive element needs these (NN/g):

| State | Look | Timing |
|---|---|---|
| Hover (pointer only) | Slightly darker or lighter background, pointer cursor | Can delay 150–200 ms on large hover targets to avoid accidental triggers |
| Focus (keyboard) | Clear outline or ring, not color alone; ≥ 3:1 contrast | Immediate |
| Pressed | Darker state, ripple, or slight scale down (about 0.97) | Within 100 ms |
| Disabled | Muted but readable; explain why if not obvious | — |
| Loading | Keep the enabled look, show a spinner with the label ("Saving…") | After press; can end in a brief check mark |

Touch users never see hover, so nothing important may live only in a hover state.

Material 3 state-layer opacities (over the content color): hover 8%, focus 10%, pressed 10%. Disabled container uses onSurface at 12%, disabled content at 38%.

## 5. What to animate (performance)

- Animate **`transform`** (translate, scale, rotate) and **`opacity`**. They can run on the compositor and stay smooth at 60 fps.
- Avoid animating `width`, `height`, `top`, `left`, `margin`, `padding` or `box-shadow`; they trigger layout or paint every frame. To animate size, use `transform: scale` or animate a pseudo-element's opacity for shadows.
- Never use `transition: all`. List the properties.
- Use `will-change` only for a measured problem, and remove it after.
- Don't animate on every keystroke, arrow key or tab press. Keyboard users repeat these constantly.
- Changing opacity on many elements at once is expensive; animate a container instead.

## 6. Reduced motion and accessibility

- Respect the OS setting: iOS Reduce Motion, Android "Remove animations", macOS Reduce motion, Windows "Animation effects".
- When reduced motion is on: remove non-essential animation, replace sliding, zooming and parallax with a short fade or an instant change, stop auto-playing and looping motion, and avoid animating blur and depth.
- Motion that can trigger vestibular problems: parallax, scroll-jacking, large zooms, auto-advancing carousels.
- WCAG 2.2.2 Pause, Stop, Hide (Level A): anything that moves on its own for more than 5 seconds alongside other content needs a pause, stop or hide control.
- WCAG 2.3.3 Animation from Interactions (AAA): motion triggered by interaction can be turned off unless essential.
- WCAG 2.3.1 (Level A): nothing flashes more than 3 times per second.

Web:
```css
.card { transition: transform 200ms cubic-bezier(0.2, 0, 0, 1), opacity 200ms; }

@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```
Place the reduced-motion rule after the normal rules so it wins. In JS: `window.matchMedia('(prefers-reduced-motion: reduce)').matches`. Framer Motion: `useReducedMotion()`.

Flutter:
```dart
final reduceMotion = MediaQuery.disableAnimationsOf(context) ||
    View.of(context).platformDispatcher.accessibilityFeatures.reduceMotion;
final duration = reduceMotion ? Duration.zero : Durations.medium2;
```
`MediaQuery.disableAnimationsOf` follows Android's "Remove animations"; iOS Reduce Motion is the separate `accessibilityFeatures.reduceMotion` flag. Check both in custom animations.

## 7. Haptics

Apple's HIG: use system haptic patterns only for their documented meanings, pair haptics with visual feedback, keep them short and rare, and make them optional.

| Moment | Haptic |
|---|---|
| Selection changes (picker, segmented control) | selection |
| Toggle, small confirm | light impact |
| Success / warning / error result | notification type |
| Destructive or heavy action | medium or heavy impact, sparingly |

Flutter `HapticFeedback`: `selectionClick`, `lightImpact`, `mediumImpact`, `heavyImpact`, `successNotification`, `warningNotification`, `errorNotification`. Standard Material and Cupertino widgets already play platform haptics; don't double them. Some calls do nothing on older Android versions, so never rely on haptics alone.

### Sound

Most apps don't need UI sounds. If you add them:
- Make them optional, and on the web off by default. Respect silent mode and system volume.
- Always pair a sound with a visual cue; many users have sound off or can't hear it.
- Keep them short and quiet, and use few distinct sounds.
- Sounds that resemble a real-world event (auditory icons, like a paper crumple for delete) are learned faster than abstract tones (earcons), which users must memorize.
- Avoid very low pitches for sounds that must be told apart, and avoid harsh high-frequency tones.
- WCAG 1.4.2 Audio Control (Level A): audio that plays automatically for more than 3 seconds needs a pause/stop or a volume control.

## 8. Gestures

- Hidden gestures are rarely discovered (NN/g on contextual swipe). Every gesture needs a visible alternative: a button, a menu item, or an "Edit" mode. Apple: custom gestures must not be the only way to do something important.
- Swipe-to-delete: keep it consistent across the app, keep the item's label visible during the swipe, and offer a visible Undo.
- Don't fight system gestures. Horizontal edge swipes mean Back on iOS and Android; the bottom edge belongs to the home gesture. Support predictive back on Android 13+.
- Drag interactions need a single-pointer alternative (WCAG 2.5.7 Dragging Movements, AA), such as move-up/move-down buttons for reordering.

## 9. Recipes

| Pattern | Spec |
|---|---|
| Button press | Scale 0.97 or state layer, 100 ms, standard easing; release springs back |
| Toggle / checkbox | 100–150 ms, standard easing |
| Dropdown / popover open | Fade + scale from 0.95, 150–200 ms, decelerate; close 100–150 ms, accelerate; transform-origin at the trigger |
| Bottom sheet / modal | Slide or fade up 250–300 ms, decelerate; dismiss 200 ms, accelerate; dim the background |
| Page / screen change | Platform default first (Flutter route transitions, View Transitions on web), 300–400 ms |
| List item added | Fade + slide in 200–250 ms; briefly highlight the new item |
| List item removed | Collapse 150–200 ms, then show Undo |
| Success | Check mark draw or scale-in 200–300 ms, plus text; optional success haptic |
| Skeleton shimmer | Slow (about 1.5 s loop), low contrast; static under reduced motion |
| Tab content swap | Crossfade 150–200 ms |

## 10. Sources

- NN/g, Executing UX animations: duration — https://www.nngroup.com/articles/animation-duration/
- NN/g, Button states — https://www.nngroup.com/articles/button-states-communicate-interaction/
- NN/g, Contextual swipe — https://www.nngroup.com/articles/contextual-swipe/
- Material Components, Motion (tokens) — https://github.com/material-components/material-components-android/blob/master/docs/theming/Motion.md
- Flutter, Durations — https://api.flutter.dev/flutter/material/Durations-class.html
- Flutter, Easing — https://api.flutter.dev/flutter/material/Easing-class.html
- web.dev, High-performance animations — https://web.dev/articles/animations-guide
- MDN, CSS and JavaScript animation performance — https://developer.mozilla.org/en-US/docs/Web/Performance/Guides/CSS_JavaScript_animation_performance
- MDN, prefers-reduced-motion — https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion
- W3C, Understanding 2.3.3 Animation from Interactions — https://www.w3.org/WAI/WCAG22/Understanding/animation-from-interactions.html
- W3C, Understanding 2.2.2 Pause, Stop, Hide — https://www.w3.org/WAI/WCAG22/Understanding/pause-stop-hide.html
- Apple HIG, Motion; Playing haptics; Gestures — https://developer.apple.com/design/human-interface-guidelines/
- Flutter, HapticFeedback — https://api.flutter.dev/flutter/services/HapticFeedback-class.html
- Flutter, MediaQuery.disableAnimationsOf — https://api.flutter.dev/flutter/widgets/MediaQuery/disableAnimationsOf.html
- Dan Saffer, Microinteractions (O'Reilly, 2013)
