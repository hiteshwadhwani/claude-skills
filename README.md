# claude-skills

A Claude Code plugin marketplace with one skill for learning anything deeply.

## Install

In any Claude Code session:

```
/plugin marketplace add hiteshwadhwani/claude-skills
/plugin install learn@hitesh-skills
```

## learn

One command from "I don't get X" to verified understanding:

```
/learn how chips are made
/learn https://arxiv.org/abs/1711.00937
```

The flow:

1. **Frame** — classifies the topic's shape: process, system, causal-quantitative, paper, or conceptual. The shape decides which interactive centerpiece the page gets.
2. **Calibrate** (skippable) — 5-7 probe questions binary-search your prerequisite knowledge, so the page starts at your actual frontier instead of at zero or over your head.
3. **Research** — builds a `KNOWLEDGE.md` from primary sources: every concept at up to five cumulative depth levels, prerequisite primers, common misconceptions. Every design choice must answer the origin question — not "VQ-VAE uses discrete latents" but *why discrete and not continuous*.
4. **Adversarial review** — a separate agent tries to refute the knowledge base against authoritative sources (for papers: against the paper itself, line by line). Defects are fixed and re-reviewed until a round comes back clean. Unverifiable claims are sourced or deleted, never shipped.
5. **Build** — one self-contained `index.html`. The backbone is always a depth-dial: everything readable at level 1 in two minutes, any concept expandable in place to practitioner grade. On top sits the shape-matched centerpiece:
   - *process* → a RollerCoaster Tycoon-style simulation: a cart rides through the real stages, the payload visibly transforming at each station
   - *system* → an exploded-view diagram with an assembled/exploded slider, interface lines, and failure-propagation mode
   - *causal-quantitative* → a parameter sandbox: sliders for the real governing variables, live charts, sourced equations shown with values substituted in, regime warnings
   - *paper* → a companion for side-by-side reading: notation drawer, two-depth sections with paper anchors, design decisions, claims register
   Plus an optional quiz mode testing mechanism and origin, never term recall.
6. **Publish** — to GitHub Pages (repo created, Pages enabled) or a Claude Artifact.
7. **Teach-back** (when you're ready) — you explain the topic in your own words; it grades you against the knowledge base, finds wrong/missing/illusion-of-depth items, re-teaches only those, and re-tests with transformed questions until they close. Ends with a gap report and questions to re-ask in a week.

Style contract: no emojis, no dumbed-down explanations, concrete numbers with sources over adjectives.

## History

Earlier versions shipped this as seven separate plugins (tycoon-sim, depth-dial, anatomy, teach-back, knob-lab, paper-walkthrough, prereq-map); they were merged into `learn` since picking a tool per topic defeated the point. The originals live in git history before commit "Merge the seven learning skills into one /learn skill".
