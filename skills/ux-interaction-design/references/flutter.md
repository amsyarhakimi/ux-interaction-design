# Flutter

Read this when the project is Flutter or Dart. Apply the general rules from the other reference files; this file maps them to Flutter APIs.

## Contents
1. Check the project's Flutter version first
2. Theme and color
3. Tap targets and layout
4. Screen readers (Semantics)
5. Text scaling
6. States: loading, empty, error, refresh
7. Feedback: buttons, SnackBars, undo
8. Motion
9. Platform adaptation
10. Forms
11. Accessibility tests
12. Sources

## 1. Check the project's Flutter version first

APIs moved recently. Read `pubspec.yaml` and the imports before writing code, and follow what the project already uses.

- Material 3 has been the default since Flutter 3.16 (`useMaterial3: true`). Don't build new screens in Material 2 style.
- Newer Flutter releases split Material and Cupertino into separate packages, `material_ui` and `cupertino_ui` (imports like `package:material_ui/material_ui.dart`). Older projects import `package:flutter/material.dart`. Match the project; a migration is `dart fix --apply --code=migrate_design_widgets`.
- `textScaleFactor` is deprecated since 3.16; use `TextScaler`.
- Flutter 3.38 changed two defaults: SnackBars with an action no longer auto-dismiss, and Android uses a predictive-back page transition.

## 2. Theme and color

- Define the color scheme once: `ThemeData(colorScheme: ColorScheme.fromSeed(seedColor: brand, brightness: Brightness.light))`, plus a dark theme with `Brightness.dark`.
- In widgets, read `Theme.of(context).colorScheme` and `Theme.of(context).textTheme`. Don't hard-code `Colors.blue` or font sizes; hard-coded values break dark mode and contrast.
- Use semantic roles: `primary` for the main action, `error` for errors, `surface` / `onSurface` for content, `surfaceContainer*` for cards and sheets.
- `ColorScheme.fromSeed` has a `contrastLevel` (0 default, 0.5 medium, 1.0 high) if you need stronger contrast.
- Override a subtree with `Theme(data: Theme.of(context).copyWith(...), child: ...)`.

## 3. Tap targets and layout

- Minimum tap target: `kMinInteractiveDimension` = 48 (Material), `kMinInteractiveDimensionCupertino` = 44. `materialTapTargetSize` is `padded` on mobile; don't set `shrinkWrap` on mobile to save space.
- Small icons still need a 48x48 hit area: use `IconButton` (already sized) or wrap in `SizedBox`/`Padding` inside an `InkWell`.
- Wrap screens in `SafeArea` (or use `Scaffold` bodies that already handle it) so content clears notches and the home indicator. Use `SliverSafeArea` inside slivers.
- Respect the keyboard: `Scaffold(resizeToAvoidBottomInset: true)` (default) and scrollable forms so the focused field stays visible.
- Test on a small phone (360 dp wide) and a tablet width. Use `LayoutBuilder` or `MediaQuery.sizeOf` for breakpoints; for wider screens switch `NavigationBar` to `NavigationRail`.

## 4. Screen readers (Semantics)

- Standard widgets already expose semantics. Custom controls (a `GestureDetector` on a `Container`) don't: use a real button widget or wrap with `Semantics(button: true, label: 'Add member', onTap: ...)`.
- `IconButton(tooltip: 'Delete')` gives both a tooltip and an accessible name. Every icon-only button needs one.
- Headings: `Semantics(header: true, child: Text(...))`.
- Group related pieces read as one item with `MergeSemantics`; hide decorative widgets with `ExcludeSemantics`.
- Announce async results: `Semantics(liveRegion: true, child: Text(status))`, or `SemanticsService.announce(...)`.
- Images: `Image(semanticLabel: '...')`, or `excludeFromSemantics: true` for decoration.
- Flutter web: semantics are off by default; enable with `SemanticsBinding.instance.ensureSemantics()`.
- Debug with `MaterialApp(showSemanticsDebugger: true)` and test with TalkBack and VoiceOver.

## 5. Text scaling

- Read the scaler with `MediaQuery.textScalerOf(context)`. Never force text scale to 1.0 for the whole app; low-vision users rely on it.
- Test at the largest system font on a small phone. Layouts break where text sits in fixed-height containers or `Row`s without `Flexible`/`Expanded`.
- Fix overflow with `Flexible`, `Wrap`, scrollable areas, and `maxLines` + `overflow: TextOverflow.ellipsis` only for non-essential text.
- If one area truly can't grow (for example a compact chip row), cap it locally: `MediaQuery.withClampedTextScaling(maxScaleFactor: 1.5, child: ...)`.

## 6. States: loading, empty, error, refresh

A common pattern with `FutureBuilder` / `StreamBuilder` or a state-management `AsyncValue`:

```dart
switch (state) {
  AsyncLoading() => const TeamListSkeleton(),            // shape of the real list
  AsyncError(:final error) => ErrorView(
      message: 'Couldn\'t load teams. Check your connection.',
      onRetry: refresh,
    ),
  AsyncData(:final value) when value.isEmpty => EmptyView(
      title: 'No teams yet',
      body: 'Create a team to start inviting members.',
      action: FilledButton(onPressed: createTeam, child: const Text('Create team')),
    ),
  AsyncData(:final value) => TeamList(teams: value),
}
```

- Skeletons: build a placeholder with the real layout, or use the `skeletonizer` package to turn existing widgets into skeletons. The Flutter cookbook's shimmer uses one shared `ShaderMask` gradient across the screen. Make shimmer static under reduced motion.
- Keep old data visible while refreshing; show a small indicator, not a full-screen spinner.
- Pull to refresh: `RefreshIndicator(onRefresh: () async => ...)`, with `physics: const AlwaysScrollableScrollPhysics()` so it works when the list is short. `RefreshIndicator.adaptive` gives the iOS style on iOS.
- Small module loads: `CircularProgressIndicator.adaptive()` inside the module, with a text label for waits over a couple of seconds.

## 7. Feedback: buttons, SnackBars, undo

- Use `FilledButton` for the one primary action, `OutlinedButton` / `TextButton` for secondary ones. Label with an outcome verb.
- Loading button: keep it enabled-looking but ignore presses while busy, and swap the child:

```dart
FilledButton(
  onPressed: saving ? null : save,          // null disables; or keep enabled and guard in save()
  child: saving
      ? const Row(mainAxisSize: MainAxisSize.min, children: [
          SizedBox.square(dimension: 16, child: CircularProgressIndicator(strokeWidth: 2)),
          SizedBox(width: 8),
          Text('Saving…'),
        ])
      : const Text('Save changes'),
)
```

- SnackBar via `ScaffoldMessenger.of(context).showSnackBar(...)`. Default duration is 4 s. Use it for confirmations, not errors that need fixing.
- Undo SnackBar: since Flutter 3.38, a SnackBar with an `action` stays until dismissed by default. For a timed undo, set `persist: false`. With TalkBack or VoiceOver on, action SnackBars don't time out, which is intended.

```dart
ScaffoldMessenger.of(context).showSnackBar(SnackBar(
  content: Text('Deleted "${item.name}"'),
  persist: false,                          // Flutter 3.38+; omit on older versions
  action: SnackBarAction(label: 'Undo', onPressed: () => restore(item)),
));
```

- Swipe to delete: `Dismissible` needs a unique `key`, `onDismissed` that updates the data, and a `background`. Add an Undo SnackBar, and provide a visible delete option too (menu or edit mode).
- Haptics: standard widgets already play them. For custom success or error moments, `HapticFeedback.lightImpact()` or the notification variants, sparingly.

## 8. Motion

- Use M3 tokens: `Durations.short4` (200 ms), `Durations.medium2` (300 ms), and curves `Easing.standard`, `Easing.emphasizedDecelerate`, `Curves.easeInOutCubicEmphasized`. Implicit animations default to `Curves.linear`, so always pass a curve.
- Implicit animations for simple changes: `AnimatedContainer`, `AnimatedOpacity`, `AnimatedPadding`, `AnimatedAlign`, `AnimatedSwitcher`, `TweenAnimationBuilder`.
- `AnimatedSwitcher` children need distinct keys (`ValueKey(state)`), or nothing animates.
- `Hero` for continuity between a list item and its detail screen: same `tag` on both routes, similar widget trees.
- Keep platform default page transitions unless there's a reason. Android now uses predictive back.
- Respect reduced motion: check `MediaQuery.disableAnimationsOf(context)` and the iOS `accessibilityFeatures.reduceMotion` flag (see `motion.md`).
- Profile animations in profile mode (`flutter run --profile`) on a real device; debug mode is slow.

## 9. Platform adaptation

- Many widgets have `.adaptive()` constructors: `Switch`, `Slider`, `Checkbox`, `Radio`, `CircularProgressIndicator`, `RefreshIndicator`, `AlertDialog`. `Icons.adaptive` picks platform icons (share, back, more).
- Navigation, scroll physics, back gestures and typography already adapt per platform.
- Bottom navigation: `NavigationBar` with 3–5 destinations, labels on, the same on every top-level screen. On iOS, `CupertinoTabBar` matches platform expectations if the app aims for native feel.
- Don't block the system back gesture. Use `PopScope` to confirm leaving a form with unsaved changes, not to trap users.

## 10. Forms

- `TextFormField` with `decoration: InputDecoration(labelText: ..., helperText: ..., hintText: 'e.g. ...')`. The label stays visible; hint is only an example.
- Validation timing: `autovalidateMode: AutovalidateMode.onUnfocus` (newer Flutter) validates when the field loses focus. If unavailable, use `onUserInteraction` only after first submit, or validate on submit.
- `keyboardType`, `textInputAction: TextInputAction.next`, `autofillHints`, `AutofillGroup`, `autocorrect: false` for emails and usernames.
- On submit failure, focus the first invalid field (`FocusNode.requestFocus()`), keep all values.
- Show/hide toggle for passwords via `suffixIcon` `IconButton` with a tooltip.

## 11. Accessibility tests

```dart
testWidgets('home screen meets a11y guidelines', (tester) async {
  final handle = tester.ensureSemantics();
  await tester.pumpWidget(const MyApp());
  await expectLater(tester, meetsGuideline(androidTapTargetGuideline));  // 48x48
  await expectLater(tester, meetsGuideline(iOSTapTargetGuideline));      // 44x44
  await expectLater(tester, meetsGuideline(labeledTapTargetGuideline));
  await expectLater(tester, meetsGuideline(textContrastGuideline));
  handle.dispose();
});
```

Also run Android Accessibility Scanner and Xcode Accessibility Inspector on a device.

## 12. Sources

- Flutter, Material 3 by default — https://docs.flutter.dev/release/breaking-changes/material-3-default
- Flutter blog, Decoupling Material and Cupertino — https://flutter.dev/blog/decoupling-material-cupertino
- pub.dev, material_ui — https://pub.dev/packages/material_ui
- Flutter, ColorScheme.fromSeed — https://api.flutter.dev/flutter/material/ColorScheme/ColorScheme.fromSeed.html
- Flutter, Themes cookbook — https://docs.flutter.dev/cookbook/design/themes
- Flutter, kMinInteractiveDimension — https://api.flutter.dev/flutter/material/kMinInteractiveDimension-constant.html
- Flutter, MaterialTapTargetSize — https://api.flutter.dev/flutter/material/MaterialTapTargetSize.html
- Flutter, Accessibility — https://docs.flutter.dev/ui/accessibility-and-internationalization/accessibility
- Flutter, Assistive technologies — https://docs.flutter.dev/ui/accessibility/assistive-technologies
- Flutter, Semantics — https://api.flutter.dev/flutter/widgets/Semantics-class.html
- Flutter, Deprecate textScaleFactor — https://docs.flutter.dev/release/breaking-changes/deprecate-textscalefactor
- Flutter, withClampedTextScaling — https://api.flutter.dev/flutter/widgets/MediaQuery/withClampedTextScaling.html
- Flutter, SafeArea — https://api.flutter.dev/flutter/widgets/SafeArea-class.html
- Flutter, Platform adaptations — https://docs.flutter.dev/ui/adaptive-responsive/platform-adaptations
- Flutter, Default Android page transition — https://docs.flutter.dev/release/breaking-changes/default-android-page-transition
- Flutter, Implicit animations — https://docs.flutter.dev/ui/animations/implicit-animations
- Flutter, Hero animations — https://docs.flutter.dev/ui/animations/hero-animations
- Flutter, Shimmer loading cookbook — https://docs.flutter.dev/cookbook/effects/shimmer-loading
- pub.dev, skeletonizer — https://pub.dev/packages/skeletonizer
- Flutter, RefreshIndicator — https://api.flutter.dev/flutter/material/RefreshIndicator-class.html
- Flutter, SnackBar with action behavior update — https://docs.flutter.dev/release/breaking-changes/snackbar-with-action-behavior-update
- Flutter, Dismissible cookbook — https://docs.flutter.dev/cookbook/gestures/dismissible
- Flutter, Accessibility testing — https://docs.flutter.dev/ui/accessibility/accessibility-testing
