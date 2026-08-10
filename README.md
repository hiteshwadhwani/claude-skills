# claude-skills

Personal Claude Code plugin marketplace.

## Install

In any Claude Code session:

```
/plugin marketplace add hiteshwadhwani/claude-skills
/plugin install tycoon-sim@hitesh-skills
```

## Plugins

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
