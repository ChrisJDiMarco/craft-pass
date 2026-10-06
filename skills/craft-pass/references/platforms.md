# Platform techniques

Concrete tools for each lens. Prefer the platform's native mechanism; check API availability against the project's deployment target before using anything here.

---

## SwiftUI and AppKit (macOS)

**Motion**
- Springs: `.spring(response:dampingFraction:)`, `.interactiveSpring`, or `.snappy`, `.smooth`, `.bouncy`. Springs retarget mid-flight by default, which makes them interruptible for free.
- Stagger: `.animation(open ? base.delay(Double(i) * 0.035) : .easeIn(duration: 0.12).delay(Double(n - 1 - i) * 0.022), value: open)`.
- Directional transitions: `.transition(.asymmetric(insertion: .opacity.combined(with: .offset(x: 16 * dir)), removal: .opacity.combined(with: .offset(x: -16 * dir))))`, with `.id(item.id)` so each item is its own view.
- Symbol state changes: `.contentTransition(.symbolEffect(.replace))`, `.symbolEffect(.bounce, value: count)`.
- Ambient motion: `TimelineView(.animation(minimumInterval: 1/60, paused: notVisibleOrResting))`. Drive it from an eased clock (velocity easing toward a target) so pause and resume glide.
- Shared elements within one view tree: `matchedGeometryEffect(id:in:)`. Across windows (panel → overlay): animate the destination from the source's screen rect with `scaleEffect` plus `offset`, then shrink back to the origin on close.
- Hover: `onHover`, `onContinuousHover` (pointer location for tilt), `rotation3DEffect(_:axis:perspective:)`.

**Color and light**
- Average color: draw the `CGImage` into an 8×8 `CGContext` and average rows (row 0 is the top), or use `CIAreaAverage`.
- Animate colors smoothly with a `ViewModifier, Animatable` whose `animatableData` holds the RGB components (`AnimatablePair` nesting). Don't swap gradient views.
- Materials: `.ultraThinMaterial`; fall back to solid when `accessibilityReduceTransparency` is on.

**Anticipation**
- Decode off the main thread: `CGImageSourceCreateThumbnailAtIndex` with `kCGImageSourceThumbnailMaxPixelSize` (display size) and `kCGImageSourceShouldCacheImmediately: true`, inside `Task.detached`.
- `NSCache` for decoded images; deduplicate in-flight requests with a `[URL: Task]` map.
- Focus points: Vision `VNRecognizeAnimalsRequest`, `VNDetectFaceRectanglesRequest`, then `VNGenerateAttentionBasedSaliencyImageRequest` as a fallback. The first request in a process loads models (~1 s), so run it in the background.
- Video: hold a prepared `AVPlayer` for the next item (`preferredForwardBufferDuration`). `AVPlayerItemVideoOutput` can grab the last frame for a seamless loop crossfade.

**Feedback and input**
- Haptics: `NSHapticFeedbackManager.defaultPerformer.perform(.alignment | .generic | .levelChange, performanceTime: .now)`.
- Click and double-click on content: `SpatialTapGesture(count: 2).exclusively(before: TapGesture())`.
- Trackpad swipes: a local `.scrollWheel` monitor. Use `phase` (`.began`, `.changed`, `.ended`), ignore `momentumPhase`, use `isDirectionInvertedFromDevice` for finger direction, and rubber-band with `x / (1 + |x| / 320)`.
- Global shortcut without permissions: Carbon `RegisterEventHotKey`.
- First click on non-key windows: subclass `NSHostingView` and return `true` from `acceptsFirstMouse`. A panel whose `canBecomeKey` is `true` can still swallow the first click on scroll views, so allow key status only when keyboard control is actually needed.

**Respect**
- `@Environment(\.accessibilityReduceMotion)`, `\.accessibilityReduceTransparency`, `\.colorSchemeContrast`; outside views, `NSWorkspace.shared.accessibilityDisplayShouldReduceMotion`.
- `ProcessInfo.processInfo.isLowPowerModeEnabled`; `NWPathMonitor` (`path.isConstrained || path.isExpensive`, `status`).
- Full-screen detection without Screen Recording permission: `CGWindowListCopyWindowInfo` bounds (not names) of the frontmost app's layer-0 windows.
- Idle cost: replace polling timers with `NSEvent.addGlobalMonitorForEvents(matching: .mouseMoved)` and run timers only while something is in progress.

**Pitfalls**
- A `scaledToFill()` image in a `ZStack` reports an oversized frame and stretches siblings. Put decorative fills in `Color.clear.overlay { … }.clipped()`.
- Offscreen `cacheDisplay` renders skip blur, materials, video, and sometimes layer order. Don't "fix" what's only a capture artifact.
- `NSViewRepresentable` views composite separately; prefer SwiftUI `Image` for stills (GIFs still need `NSImageView` to animate).

---

## UIKit (iOS)

- Interruptible animation: `UIViewPropertyAnimator` (scrub, reverse, continue); springs via `UISpringTimingParameters`.
- Zoom transitions (iOS 18+): `preferredTransition = .zoom(sourceViewProvider:)`; otherwise `UIViewControllerAnimatedTransitioning` with snapshot views.
- Decoding: `UIImage.preparingThumbnail(of:)` and `byPreparingForDisplay()` off the main thread.
- Haptics: `UIImpactFeedbackGenerator(style: .light)`, `UISelectionFeedbackGenerator` (call `prepare()` ahead of time); in SwiftUI, `.sensoryFeedback(_:trigger:)`.
- Settings: `UIAccessibility.isReduceMotionEnabled`, `.isReduceTransparencyEnabled`, `.isDarkerSystemColorsEnabled`; Dynamic Type via `UIFontMetrics`.
- Prefetch: `UICollectionViewDataSourcePrefetching`.

---

## Web (HTML, CSS, JavaScript)

**Motion**
- Shared elements and page continuity: the View Transitions API (`document.startViewTransition`, `view-transition-name`; cross-document `@view-transition { navigation: auto; }`). Use FLIP where it isn't supported.
- Springs: CSS `linear()` easing generated from a spring, the Web Animations API, or a library (Motion/Framer Motion `type: "spring"`).
- Stagger: `transition-delay: calc(var(--i) * 30ms)`.
- Animate only `transform` and `opacity` (and `filter` sparingly); use `will-change` only around active animations.
- Reduced motion: `@media (prefers-reduced-motion: reduce)` → crossfades.

**Color and light**
- Average color: draw into a small `<canvas>` and read `getImageData` (needs CORS), or precompute on the server and ship it with the data.
- Animate gradients by registering custom properties with `@property --glow { syntax: '<color>'; inherits: false; initial-value: transparent; }` and transitioning them. `color-mix()` for subtle chrome tints.
- `prefers-color-scheme`, `prefers-contrast`, `prefers-reduced-transparency`.

**Anticipation**
- `<link rel="preload">`/`prefetch`, the Speculation Rules API for likely navigations, `fetchpriority="high"` for the hero.
- `await img.decode()` before swapping an image in.
- Prevent layout shift: `width`/`height` or `aspect-ratio` on media, blurred low-quality placeholders, skeletons in the content's real shape.
- `IntersectionObserver` with a generous `rootMargin` to load what's about to scroll in; `content-visibility: auto` for long pages.
- Respect `navigator.connection.saveData` and the `Save-Data` header.

**Typography**
- `font-variant-numeric: tabular-nums`; `text-wrap: balance` for headings and `pretty` for body; `hanging-punctuation`.
- Fade truncation: `-webkit-line-clamp` plus `mask-image: linear-gradient(to bottom, #000 60%, transparent)`.

**Input and feedback**
- Pointer events; `touch-action` and `overscroll-behavior` for custom gestures; `:focus-visible` rings; full keyboard support with roving `tabindex`.
- `navigator.vibrate` only on supporting devices, very sparingly.
- Keep hidden chrome visible with `:focus-within` so keyboard users never lose it.

**Performance**
- Measure INP, LCP, and CLS (Lighthouse, Performance panel). Use `requestAnimationFrame` for visual updates and `requestIdleCallback` for warm-up work; pause work when `document.hidden`.

---

## React

- Shared elements: Motion's `layoutId`; exits with `AnimatePresence`; `useReducedMotion()`.
- Avoid layout shift from suspense fallbacks: reserve space and use skeletons shaped like the content.
- Prefetch on hover or intent (router `prefetch`, `queryClient.prefetchQuery`).
- Keep animation state out of render-heavy trees; prefer CSS transforms or motion values over state updates per frame.

---

## Android (Jetpack Compose)

- `spring()` specs, `AnimatedContent`, `animateContentSize`, `SharedTransitionLayout` with `Modifier.sharedElement` for shared elements; `updateTransition` for coordinated states.
- Content color: the Palette API (`Palette.from(bitmap)`).
- Preload images with Coil's `ImageLoader.enqueue` for upcoming items.
- Haptics: `LocalHapticFeedback` or `View.performHapticFeedback(HapticFeedbackConstants.CLOCK_TICK)`.
- Respect `Settings.Global.ANIMATOR_DURATION_SCALE` (0 means reduce motion), `PowerManager.isPowerSaveMode`, and metered connections via `ConnectivityManager`.

---

## Command-line tools

The same lenses apply in a terminal.

- **Anticipation and feedback**: show progress with counts and an ETA instead of a bare spinner; print the first useful line immediately.
- **Continuity**: update a single status line in place (carriage return) instead of scrolling noise; leave a clean summary at the end.
- **Copy**: errors say what happened and the exact command to fix it.
- **Respect**: honor `NO_COLOR`, detect non-TTY output and drop decoration, keep exit codes meaningful, and never block on prompts in CI.
