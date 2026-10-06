# The lenses

Ask every question of every moment. Most answers will be "fine". The ones that aren't are the work.

Examples in *italics* come from a real pass on a small macOS app (a notch you hover to see cute animals). They show depth, not a style to copy.

---

## 1. Continuity

Objects keep their identity as they move between places and states.

**Ask**
- When something opens, where does it come from? Does it visibly grow from the thing that was clicked?
- When it closes, does it go back where it came from, or just vanish?
- When content changes (next item, new page, new tab), does the old content hand off to the new, or does it cut?
- Does the first thing you see after an action match the thing you acted on?
- Do containers change size smoothly, or snap?
- When a list updates, do items move to their new positions, or jump?

**Patterns**
- Shared-element transitions: the clicked thumbnail *becomes* the detail view.
- Close returns to origin (or to the product's "home", if the origin is gone).
- Directional transitions that match navigation: forward slides or drifts one way, back the other.
- The resting thumbnail's exact content becomes the first content in the destination. *The card's photo flew out and was the first post shown, instead of the player opening on something random.*
- A container's shape morphs between states instead of crossfading two separate containers. *The black notch grew into the panel; it was one shape the whole time.*

**Anti-patterns**: fade-out-then-fade-in between related views; modals that appear from nowhere; content that changes under a static container; a card that changes its image while someone is looking at it.

---

## 2. Anticipation

The product is ready before the person is.

**Ask**
- What will the person almost certainly do next? Is it already loaded?
- Is anything decoded, parsed, measured, or laid out at the moment of display that could have been done earlier, off the main thread?
- What data derived from content (dominant color, focal point, aspect ratio, duration) is computed late? Could it be computed during preload?
- Can the previous item stay warm so "back" is instant?
- Are failures discovered on screen, or before the person gets there?
- When the next item isn't ready on schedule, does the product hold the current one, or cut to blank?

**Patterns**
- Preload the next N items and keep the previous one; size N to the cost.
- Fully decode images off the main thread, downscaled to the display size, before they're shown.
- Precompute derived data at preload (colors, focus points, text measurements) so a change can begin the instant input arrives. *The backdrop tint for the next post was read during preload, so the color started moving on the keypress, not when the image arrived.*
- Pre-buffer the next video.
- Validate upcoming items during preload and silently drop broken ones.
- Hold instead of cutting: an auto-advance waits (with a timeout) for the next item to be ready.
- Show a sharp low-resolution version immediately (a thumbnail you already have) and crossfade to full quality.
- Never refresh visible content while the person is looking at it; refresh after it closes, ready for next time.

**Anti-patterns**: spinners for content you could have prefetched; layout shift when images load; "loading…" on the second visit; a list that reshuffles while you read it.

---

## 3. Motion

Motion explains what happened. It should feel physical, never slow anything down, and always be interruptible.

**Ask**
- Is it a spring, or a linear or default curve? Do springs have the right character (a touch of overshoot to arrive, none to settle)?
- Can every animation be interrupted mid-flight and smoothly reversed or retargeted?
- Are groups staggered on the way in (and reverse-staggered on the way out)?
- Is duration proportional to distance or magnitude (a bigger change takes a little longer)?
- Does continuous motion ease into stops and starts, or freeze and jump? *Pausing glided the slow zoom to a stop over half a second instead of freezing it.*
- Does direction carry meaning (forward and back, in and out)?
- Is anything moving that doesn't need to? Is any motion longer than ~400 ms for a common action?
- Does direct manipulation follow the finger, with resistance at the edges and a commit on distance or flick? *A two-finger swipe dragged the post, rubber-banded, and committed past 90 pt or on a fast flick; otherwise it sprang back.*
- With Reduce Motion on, does every movement become a crossfade or nothing?

**Patterns**
- A small vocabulary: two or three named springs and one crossfade, used everywhere.
- Enter with a slight overshoot; exit faster and flatter than enter.
- Stagger in 25–40 ms steps; keep the total under ~350 ms.
- Subtle ambient motion (a slow zoom, a gentle breathe) only on content the person is watching, eased, and stopped when hidden or in Low Power Mode.
- Hover responses: a small scale (1.03–1.07), a tilt toward the pointer (5–8°), a lifted shadow, all sprung.
- A selection indicator that glides between items instead of jumping.

**Anti-patterns**: ease-in-out on everything; animations that block input; bounces on frequent actions; spinners that spin for 100 ms; motion as decoration.

---

## 4. Color and light

Color that comes from the content feels alive. Color that changes should flow.

**Ask**
- Could the surface around the content take its color from the content (dominant color, top and bottom regions, album art)?
- When that color changes, does it blend channel by channel, or crossfade through grey or black?
- Are derived colors kept within a gentle range (saturation and brightness clamped) so consecutive items never jolt?
- Do larger color changes get slightly longer transitions?
- Does chrome (controls, progress, glass, borders) pick up a faint hint, around 10%, of the content's color?
- Is text over imagery always legible? Is the scrim strength adapted to how bright the image is underneath?
- Is there light at the edges (a hairline glow, a tinted shadow) that makes the content feel lit rather than placed?
- Does the palette respond to context: dark mode, high contrast, late night (dimmer and warmer)?
- Is there exactly one accent, used consistently?

**Patterns**
- Average a tiny downscale (8×8) of the image; lift it to a "glow" (raise saturation a little, clamp brightness to a band).
- Sample regions separately (top above, bottom below) for two-tone light.
- Animate RGB (or a perceptual space) through an animatable modifier, not by swapping gradient views.
- Adaptive scrim: darker over bright image edges, lighter over dark ones.

**Anti-patterns**: pure grey backdrops behind colorful content; colors that pop instead of flow; tints strong enough to compete with the content; more than one accent.

---

## 5. Hierarchy and chrome

Content first. Chrome appears when wanted and disappears when not.

**Ask**
- Is the content as large and edge-to-edge as it can comfortably be? Are there letterbox bars that a blurred copy of the content could fill?
- Do title, credit, and controls step back after a few seconds of no input, and return instantly on any movement?
- Is anything that must stay visible (a countdown, a recording indicator) exempt from hiding?
- Are controls grouped into one coherent object (a single pill or toolbar), not scattered?
- When chrome hides, is any important state still signaled quietly? *A tiny heart marked an already-favorited post while the controls were hidden.*
- Is there one obvious primary action per view?

**Anti-patterns**: permanent chrome over immersive content; controls that hide while the pointer is over them; chrome hiding during loading or error states, when it's most needed.

---

## 6. Typography and copy

Words are interface. Make them warm, specific, and clean.

**Ask**
- Is user-generated or third-party text cleaned up (tags like "[OC]", trailing hashtags, repeated punctuation, emoji pile-ups, straight quotes, double spaces)?
- Are quotes and apostrophes typographic (“ ” ‘ ’)? Ellipses a single character?
- Are changing numbers in tabular (monospaced) figures so they don't jitter?
- Does long text truncate gracefully (a fade, or a line limit with intent) rather than an abrupt "…" mid-word?
- Do empty, error, waiting, and success states each say what happened and what's next, in the product's voice?
- Do moments that repeat often (a completion message) rotate through a few variations so they never feel canned?
- Is there a clear type scale (3–5 sizes), consistent weights, and one family (or a deliberate pair)?
- Is sentence case used consistently? Are labels verbs where they're actions?

**Patterns**
- Error: what happened, then what to do. "Reddit asked for a short break. Back in 42 s."
- Empty: an invitation. "No favorites yet. Press F or ♥ on anything you love."
- Completion with context: "Nicely done. Back to Figma" (with the app's icon).

**Anti-patterns**: raw error strings; "Error:" prefixes; "successfully"; exclamation marks on system copy; truncation that cuts the meaningful part.

---

## 7. Feedback

Every action is acknowledged, in proportion.

**Ask**
- Does every click, key, and gesture produce a visible response within ~100 ms?
- Are acknowledgments proportional (a tick for selection, a pop for a favorite, a glow for completing something)?
- Is haptic feedback used where the platform has it (trackpad, phone), lightly? A tick when crossing between items, a tap on commit.
- Do toggles animate between states (symbol replace, bounce) instead of swapping?
- Are confirmations brief, unobtrusive, and positioned where the eye already is?
- When something is copied, saved, or shared, does a small toast say so, then leave?
- Do celebrations happen where the action happened? *Double-clicking a photo popped a heart at the click point; a new favorite's heart flew up into the notch, "where favorites live".*

**Anti-patterns**: silent actions; heavy modal confirmations for light actions; haptics on every hover; celebrations bigger than the achievement.

---

## 8. Input

Every way a person might reach for it works, and feels good.

**Ask**
- Does the first click always register, including on windows or panels that aren't focused? (Test it; focus handling commonly eats first clicks.)
- Trackpad: swipes for next and back, with direct manipulation? Pinch where zoom makes sense?
- Mouse: does the scroll wheel do the sensible thing? Are hover states present and not sticky?
- Keyboard: can every action be done without a mouse? Are there shortcuts for the frequent ones, shown in tooltips? Can the main surface be summoned by a global shortcut?
- Click targets: are they at least 28–32 pt? Is the whole card clickable, not just its label?
- Clicking content itself: does it do the obvious thing (pause, open, select)? Does double-click do a natural secondary action?
- Screen readers: does every control have a label (not "button")? Are decorative elements hidden from assistive tech? Do images have descriptions?
- Are system shortcuts (⌘C, ⌘W, Esc, Space) honored where people expect them?

---

## 9. Resilience

Every edge state is designed, on-brand, and never blank.

**Ask**, for each surface:
- **Loading**: is there a placeholder with personality (a pulse, a skeleton in the content's shape) rather than a generic spinner? Can a real low-resolution version show instead?
- **Waiting** (rate limits, queues): does it say how long, and count down?
- **Empty**: is it an invitation with a next step?
- **Error**: is it specific, recoverable, and gentle? Does one failed item get skipped silently instead of breaking the whole view?
- **Offline**: does it show what's cached, with a quiet note, instead of an error?
- **Slow network**: does it degrade (smaller assets) before it fails?
- **Extremes**: very long and very short text, huge and tiny images, zero, one, and thousands of items, portrait and landscape media, odd aspect ratios.
- **Interruptions**: closing mid-animation, navigating mid-load, reopening quickly — any stuck or double states?

---

## 10. Respect

Mature software is considerate.

**Ask**
- **Accessibility**: Reduce Motion (motion becomes crossfades), Reduce Transparency (materials become solid), Increase Contrast, larger text, screen readers, full keyboard access.
- **Power**: does Low Power Mode pause background refreshes, ambient motion, and prefetch depth?
- **Data**: does Low Data Mode or a metered connection get smaller assets?
- **Attention**: does it avoid popping up during presentations, full-screen apps, or screen sharing (or require a more deliberate trigger)? Are reminders opt-in, quiet, and dismissible by ignoring them?
- **Time**: should late-night use be dimmer or warmer?
- **Context**: when closing, does focus return exactly where the person was? When sharing, does the UI step aside so the share sheet is visible?
- **Resources**: is memory released when a surface closes? Is idle cost zero (no polling timers; event-driven wakeups)?
- **Privacy**: does it avoid asking for permissions it doesn't need (screen recording, accessibility) and avoid sending data it doesn't have to?
- **Being a good citizen**: are third-party APIs used politely (honoring rate-limit headers, caching, backing off)?

---

## 11. Performance as feel

Performance problems are felt as roughness long before they're measured as slowness.

**Ask**
- Do transitions hold full frame rate (60/120 Hz)? Is anything decoded, parsed, or laid out on the main thread mid-animation?
- Is idle CPU ~0% with ~0 wakeups/s? Is anything polling that could be event-driven?
- Is memory released after closing heavy surfaces? Are caches bounded?
- Is time-to-first-content fast on the second visit (disk cache, warm state)?
- Are expensive per-frame effects (blur, shadows on large layers, timeline-driven views) limited to what's visible, and paused when static?

**Measure** with platform tools (Activity Monitor or `top` for CPU and wakeups, Instruments, browser Performance panel, Lighthouse) before and after.

---

## 12. Signature

One memorable moment that could only belong to this product.

**Ask**
- What is the product's central metaphor or object? Can it have a small, expressive behavior? *The notch got two eyes that appear while you hover, follow the pointer, blink at random, and smile as the panel opens.*
- Can the signature carry meaning, not just charm? *The eyes' filling line showed the hover was registering and the panel was about to open.*
- Is it brief, optional to notice, and absent from places it would get in the way?
- Does it respect Reduce Motion?

Have exactly one, and make it excellent.

---

## Prioritization heuristic

When ranking ideas, score each 1–3 on:

- **Friction removed**: does it fix something that currently feels off?
- **Reach**: how often does someone encounter this moment?
- **Coherence**: does it make the product feel more like one thing?
- **Cost and risk**: inverse of effort and of the chance of breaking something.

Build the highest totals first. Friction fixes on frequent moments almost always win.
