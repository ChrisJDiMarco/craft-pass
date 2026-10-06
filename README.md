# craft-pass

An open-source skill that teaches AI agents to take any app or website to a mature, premium level of craft.

Pair it with any interface and the agent works through it moment by moment: how things open and close, what's ready before you need it, how color and motion behave, what the words say, how every input feels, what happens when things go wrong, and what it costs when nothing is happening. It doesn't apply a house style. The changes come out of what the product already is, so two apps that go through it end up more like themselves, not more like each other.

## The standard

A mature interface passes these at every moment:

1. **Nothing teleports.** What you clicked becomes what you see; closing returns things to where they came from.
2. **Nothing waits where it could have been ready.** The next thing is loaded, decoded and measured before it's needed.
3. **Nothing is ignored.** Every input gets a response within about 100 ms.
4. **Nothing is generic.** Copy, color and motion come from the content and the product's concept.
5. **Nothing is careless.** Empty, slow, offline, failed and extreme states are designed.
6. **Nothing is rude.** It respects accessibility settings, battery, data, attention and context.
7. **Nothing costs when idle.** Zero CPU at rest, memory released, no main-thread work during animation.

## How it works

The skill gives the agent a method, not a checklist of effects:

1. **Find the product's soul.** What it's for, how it should feel, and which ideas already work and should be amplified.
2. **Map every moment**, from idle through open, navigate, wait, succeed, fail and close, and the atomic elements in each.
3. **Interrogate each moment through twelve lenses:** continuity, anticipation, motion, color and light, hierarchy and chrome, typography and copy, feedback, input, resilience, respect, performance as feel, and signature.
4. **Rank** ideas: friction fixes first, then continuity and anticipation, then system-wide polish, then delight.
5. **Build a token system** (one accent, a few named springs, one radius family) before touching details.
6. **Implement in passes**, verifying each with builds, offscreen renders, simulated input and measurements.
7. **Report honestly**: what was verified, and what couldn't be.

A restraint section keeps it tasteful: every addition must serve the product's concept, effects stay quieter than you'd first think, there's one signature moment rather than five, and anything that interrupts is off by default.

## What's inside

```
skills/craft-pass/
├── SKILL.md                 # the method, the standard, restraint and verification
└── references/
    ├── lenses.md            # the question bank for each lens, with patterns and anti-patterns
    └── platforms.md         # techniques for SwiftUI/AppKit, UIKit, web, React, Android and CLIs
```

## Install

### Claude Code (plugin)

In Claude Code, run:

```
/plugin marketplace add ChrisJDiMarco/craft-pass
/plugin install craft-pass@craft-pass
```

### Claude Code (personal skill)

Clone the repo and copy the skill into your skills folder:

```bash
git clone https://github.com/ChrisJDiMarco/craft-pass.git
```

```bash
cp -R craft-pass/skills/craft-pass ~/.claude/skills/
```

### Other agents

The skill is plain Markdown. Point any agent at `skills/craft-pass/SKILL.md` (and let it read the two reference files when the skill tells it to), or paste it into the agent's instructions.

## Use

Open a project and ask in your own words, for example:

- "Give this app a craft pass."
- "Make this site feel premium. Go deep on every detail."
- "What else could make this feel better?"

The agent will study the product, propose changes grouped by moment with a recommended first batch, then build them in passes and tell you exactly what it verified.

## Where it came from

It was distilled from taking [NotchCute](https://github.com/ChrisJDiMarco/NotchCute), a small macOS app that shows cute animals when you hover the MacBook notch, through round after round of refinement: preloaded color that changes the instant you move on, cards that become the post you open, a notch with eyes that follow your pointer, swipes that follow your fingers, an idle cost of zero. The skill keeps the method and leaves the style behind.

## Contributing

Issues and pull requests are welcome, especially new platform techniques, sharper lens questions, and before-and-after examples from real projects.

## License

[MIT](LICENSE)
