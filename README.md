# claude-skills

A Claude Code plugin marketplace with skills for learning-by-simulation workflows.

## Install

In any Claude Code session:

```
/plugin marketplace add hiteshwadhwani/claude-skills
/plugin install tycoon-sim@hitesh-skills
```

Replace `tycoon-sim` with any plugin name below; repeat the install line per plugin you want.

## Plugins

All skills share two principles: an adversarial accuracy-review phase (a separate agent tries to refute the researched knowledge base before anything is built from it), and the origin question (every design choice must answer "why this way and not the obvious alternative" — not just "VQ-VAE uses discrete latents" but why discrete).

### tycoon-sim

Learn how a complex process works by building an accurate, RollerCoaster Tycoon-style interactive simulation of it. A cart travels through the real stages of the process (chip fabrication, rocket engines, EUV machines, LLM inference, ...) and the payload visibly transforms at each station.

Usage:

```
/tycoon-sim how chips are made
```

The skill runs four phases:

1. **Knowledge base** — researches the topic from primary sources and writes a stage-by-stage `KNOWLEDGE.md`.
2. **Adversarial review** — a separate agent tries to refute every claim; defects are fixed and re-reviewed until none remain. This is what keeps the result hallucination-free.
3. **Simulation** — a single self-contained `index.html`: isometric low-poly SVG, pan/zoom camera, playback controls, per-station info panels, optional quiz mode. Works on phones and as a local file.
4. **Publish** — to GitHub Pages (repo created and Pages enabled for you) or as a Claude Artifact.

### depth-dial

The same topic at five zoom levels on one interactive page. Opens with everything at L1 (a two-minute read); any concept expands in place down to L5 (practitioner grade — equations, edge cases, open problems). L4 is mandatory design rationale: the constraints that forced the design and the alternatives that lost. For when explanations pick one depth and force it on the whole topic.

```
/depth-dial how EUV lithography works
```

### anatomy

The structural sibling of tycoon-sim: systems instead of processes. An interactive exploded-view diagram — an assembled/exploded slider spreads the components apart, interface lines show what flows between them (matter/energy/data/force), clicking a component reveals its role, design rationale, and a "without it" mode that visualizes failure propagation.

```
/anatomy EUV machine
```

### teach-back

The examiner: you explain the topic in your own words, it grades you against a verified knowledge base — correct / wrong / missing, plus illusions of depth (right term, missing mechanism) — re-teaches only the gaps, and re-tests with transformed questions until they close. Ends with a gap report and questions to re-ask in a week.

```
/teach-back chip fabrication
```

### knob-lab

A parameter sandbox for topics that are really causal structures: sliders for the real governing variables, live charts, the equations shown with current values substituted in, regime warnings when you leave a model's validity domain, and preset buttons anchored to real-world operating points. Intuition comes from breaking the system.

```
/knob-lab chip fab yield
```

### paper-walkthrough

Turns a dense paper into a verified interactive companion (not a summary): three-layer overview, notation glossary in a drawer, every section at two depths with paper anchors, figures re-explained, a design-decisions section answering "why this approach and not the alternative", and a claims register rating each claim's support. Reviewed line-by-line against the paper itself.

```
/paper-walkthrough https://arxiv.org/abs/1711.00937
```

### prereq-map

For when the topic won't stick because the missing depth is a layer below it. Builds a prerequisite dependency graph, locates your actual knowledge frontier with a 5-10 question diagnostic (binary-search over the graph, not an exam), and emits the shortest learning path — each step routed to the learning skill that fits its shape.

```
/prereq-map diffusion models
```
