---
name: craft-pass
description: Elevate an existing app or website to a mature, premium level of craft through deep, atomic refinement of every moment — motion, color and light, continuity, loading and anticipation, typography and copy, feedback, input, edge states, accessibility, and performance — in a way that grows out of that specific product instead of imposing a house style. Use when the user asks to polish, refine, elevate, or "level up" an interface; to make it feel premium, beautiful, mature, smooth, or next-level; or asks what else could make it feel better.
---

# Craft pass

Turn a working interface into one that feels considered in every detail. The goal is not a new look. The goal is that every moment of the product feels like it already knew what the person was about to do, and that nothing anywhere feels accidental.

This skill is a method, not a style. Two apps that go through it should come out feeling more like themselves, not more like each other.

## The standard

A mature interface passes these tests at every moment:

1. **Nothing teleports.** Things that move between places travel there. What you clicked becomes what you see. Closing returns things to where they came from.
2. **Nothing waits where it could have been ready.** The next thing is loaded, decoded, and measured before it's needed. No blank frames, no layout jumps, no spinners where a placeholder from data you already had would do.
3. **Nothing is ignored.** Every input gets a response within about 100 ms: visual, and haptic or audible where the platform supports it.
4. **Nothing is generic.** Copy, color, and motion come from the content and the product's concept, not from defaults.
5. **Nothing is careless.** Edge cases (empty, slow, offline, failed, rate-limited, huge, tiny, long text, no text) are designed, not tolerated.
6. **Nothing is rude.** It respects accessibility settings, battery, data, attention, privacy, and context (presenting, full screen, late at night).
7. **Nothing costs when idle.** Zero CPU and wakeups at rest, memory released when a surface closes, no main-thread work during animation.

## Process

Work through these phases in order. Do not skip to implementation; the depth comes from phases 1–3.

### 1. Find the product's soul

Before proposing anything, understand what the product is for and how it should feel.

- Read the code and run the app. Use it as a person would, end to end.
- Name its **concept** in one sentence (for example, "a notch you hover for a quick look at baby animals before careful work").
- Name its **emotional register**: calm, playful, precise, luxurious, urgent, warm, technical.
- Find what **already works** and what the user has said they love. These are the threads to pull. Amplify existing signature ideas before inventing new ones.
- Note the platform's native idioms (macOS, iOS, web, Android, terminal). Mature means *native-feeling at its best*, not cross-platform generic.

### 2. Map every moment

List the product's journey as discrete moments, then the atomic elements inside each. Typical moments:

idle → discover/trigger → hover/focus → open/enter → first paint → content shown → navigate next/back → wait/load → interact (select, favorite, share, edit, submit) → success → error → empty → offline → pause/resume → finish → close/exit → return later.

For each moment, list its elements (containers, media, text, controls, indicators, backgrounds) and its states (resting, hover, pressed, focused, selected, disabled, loading, failed).

### 3. Interrogate each moment through the lenses

For every moment, ask the questions in `references/lenses.md`. The lenses are:

- **Continuity**: does anything teleport, pop, or swap identity?
- **Anticipation**: what could already be loaded, decoded, computed, or warmed?
- **Motion**: is it spring-based, interruptible, directional, proportional, and does it settle gently?
- **Color and light**: can color come from the content? Does it blend smoothly from one state to the next? Does chrome pick up a hint of it?
- **Hierarchy and chrome**: is content first? Does chrome step back when it's not needed and return instantly when it is?
- **Typography and copy**: is user-generated text cleaned up? Is the voice warm and specific? Are numbers tabular? Does truncation look intentional?
- **Feedback**: is every action acknowledged, in proportion to its importance?
- **Input**: does it work, and feel good, with mouse, trackpad gestures, keyboard, scroll wheel, touch, and screen reader? Does the first click always land?
- **Resilience**: are loading, empty, error, offline, and slow states designed and on-brand?
- **Respect**: reduced motion, reduced transparency, contrast, low power, low data, presenting, time of day, memory.
- **Performance as feel**: frame pacing, idle cost, memory, main-thread work.
- **Signature**: is there one memorable moment that comes from the product's concept (not decoration)?

Generate many ideas, then filter hard (see Restraint).

### 4. Rank and present

Present the ideas grouped by moment, each as one concrete line describing the change and why it matters. Then recommend a first batch, ordered by:

1. **Friction fixes**: moments that currently feel slightly wrong (a blank frame, a dropped click, a jump, a lag). These matter most.
2. **Continuity and anticipation**: the changes that make the whole product feel connected and ready.
3. **Systemic polish**: tokens, motion, and type changes that lift everything at once.
4. **Delight**: signature moments and small rewards.

If the user says "do all of it", do all of it, but still build in this order.

### 5. Build a system before details

Before touching individual details, create or consolidate **design tokens**, so every refinement draws from the same few decisions:

- One accent color (plus derived shades), one neutral ramp, content-derived colors where they fit.
- Two or three named springs (for example, `open` with a touch of overshoot, `settle` with none) and one crossfade duration.
- One corner-radius family, one spacing scale, one type scale.
- Shared helpers for haptics, reduced-motion checks, and power and data state.

### 6. Implement in passes, verifying as you go

- Make one coherent pass per lens or per moment, not scattered edits.
- After each pass, build and verify (see Verification). Fix what you find before moving on.
- Keep the code idiomatic for its codebase: match naming, structure, and comment density. Document *why* a detail exists in a short comment where it isn't obvious ("held until the next picture is decoded so the player never cuts to empty").

### 7. Report honestly

Tell the user what changed, grouped by moment, in plain language. State exactly what was verified and how, and what could not be (motion and feel often can't be fully verified headlessly). Never claim an animation "looks great" if you only compiled it.

## Restraint

Polish is as much subtraction as addition. Every idea must pass all of these:

- **It serves the concept.** If removing it wouldn't make the product feel less like itself, cut it.
- **It doesn't add things to look at.** No counters, badges, stats, streaks, or decorative effects unless the product is about them.
- **It's quieter than you think.** Shift by 12 pt, not 40. Tint at 10%, not 40%. A zoom of 6% over 9 seconds, not 20% over 3.
- **One signature, not five.** A single memorable moment beats many tricks.
- **Off by default if it interrupts.** Anything that speaks up on its own (reminders, sounds) is opt-in and gentle.
- **Never trade function for flourish.** If motion delays an action, make it interruptible or remove it.

## Verification

You often can't watch the app. Verify anyway:

- **Build with zero warnings.**
- **Render states offscreen** (headless snapshots, Storybook, preview canvases, Playwright screenshots, simulator screenshots) for each key state: resting, hover/selected, loading, empty, error, mid-transition.
- **Reproduce before fixing.** When the user reports a bug, simulate the exact input first (synthetic events, test harness) and confirm it fails; then fix and confirm it passes. Keep that check for regressions.
- **Measure, don't guess**: idle CPU and wakeups, memory after closing, frame pacing during transitions, time to first content.
- **Know your capture's limits.** Offscreen capture may omit blur, video, materials, or layer ordering. When a render looks wrong, check whether it's the app or the capture before "fixing" it.
- **Watch for layout side effects of visual tricks.** A fill-scaled background, a scale effect, or an overflow can quietly resize or shift a layout. Constrain decorative layers so they can't affect layout.
- **Don't trigger system prompts** (screen recording, accessibility) on the user's machine to verify; find another way or ask.

## References

- `references/lenses.md`: the full question bank for each lens, with concrete patterns and examples.
- `references/platforms.md`: platform techniques (SwiftUI/AppKit, UIKit, web, React, Android) for springs, shared-element transitions, preloading, decoding, haptics, content-derived color, and accessibility settings.

Read `lenses.md` before phase 3. Read the relevant part of `platforms.md` before phase 6.
