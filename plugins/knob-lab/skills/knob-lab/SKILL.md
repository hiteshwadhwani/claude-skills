---
name: knob-lab
description: Build causal intuition for a topic through an interactive parameter sandbox - sliders for the real governing variables (defect density vs chip yield, batch size vs LLM inference cost, chamber pressure vs rocket thrust) with live-updating charts and the sourced equations shown. Use when the user wants to understand why a system behaves as it does, asks "what happens if X changes", wants to explore trade-offs quantitatively, or invokes /knob-lab <topic>.
---

# Knob Lab: learn the why by breaking the system

Descriptions teach what a system does; only manipulation teaches why. This skill builds a sandbox page where the user drags the real governing parameters of a system and watches the consequences propagate - including into the regimes where the system breaks. The intuition comes from the breaking.

Topic comes from the argument (`/knob-lab chip fab yield`) or conversation. If missing, ask. If the topic has no meaningful quantitative structure (it is a narrative or a taxonomy), say so and suggest depth-dial or tycoon-sim instead - a knob-lab with made-up equations is worse than nothing.

## Writing style (all phases)

- No emojis anywhere: documents, page UI, replies.
- Real technical terms, defined in place. Concrete numbers with units; every equation and constant carries a source.

## Phase 1 — Build the model knowledge base

This skill lives or dies on the honesty of its model. Research the actual governing relationships from primary sources (textbooks, papers, engineering references), then write `KNOWLEDGE.md`:

```markdown
# <Topic> — causal model knowledge base

## What the model captures
Two or three sentences: the system, the question the sandbox answers.

## Parameters (the knobs)
For each of 3-8 input parameters:
- Name, symbol, unit
- Realistic range (min / default / max) with a source for why that range
- What it means physically, in one sentence
- Why real systems sit near the default: the constraint that puts them there (cost, physics limit, diminishing returns) — operating points are design decisions, not facts

## Outputs
For each displayed output: name, unit, why the reader should care.

## Relationships
For each governing relationship:
- The equation (or well-sourced empirical approximation), in plain text and as it will be coded
- Source (textbook/paper/reference URL)
- Validity domain: the parameter regime where this equation holds
- Anchor points: 2-3 known real-world (input, output) pairs the model must reproduce

## What the model deliberately ignores
Honest list of second-order effects left out, so the user knows the sandbox's edges.

## Sources
Numbered URLs actually consulted.
```

Prefer a simple model that is right about its domain over a rich model that is quietly wrong. First-order equations with honest "ignores" beats fitted curves with no provenance.

## Phase 2 — Adversarial model review

Spawn a subagent instructed to refute, not approve:

> Review KNOWLEDGE.md, the causal model for a <topic> sandbox. Check: (1) each equation against its cited source - form, constants, units; (2) dimensional consistency of every relationship; (3) whether the anchor points actually reproduce under the stated equations - do the arithmetic; (4) parameter ranges against reality; (5) missing first-order effects that would change the qualitative behavior inside the stated validity domain. Return a numbered defect list with severity (wrong / misleading / incomplete) and corrections with sources.

Fix every defect. If an anchor point does not reproduce, the model is wrong - fix the model, never the anchor. Re-review after any *wrong*-severity fix; stop when a round is clean. Log rounds under `## Review log`.

## Phase 3 — Build the sandbox

One self-contained `index.html`, inline CSS/JS, zero external requests, works from `file://`. The JS model must implement exactly the equations in the reviewed `KNOWLEDGE.md` - put each equation in a named function with a comment citing its KNOWLEDGE.md relationship, so the page is auditable against the file.

Layout and interaction:

- Left (or top, on phones): the knobs. Each slider shows name, symbol, current value with unit, and its realistic range; a small reset-to-default per knob and a global "reset all". Log-scale sliders where the range spans decades.
- Right (or below): the outputs - one primary chart showing the headline output against the most interesting parameter, updating live as any knob moves, with the current operating point marked. Secondary outputs as large numeric tiles with units and sparkline-style mini charts where useful.
- **Show the math**: a collapsible panel per output showing the governing equation with the current values substituted in, computing visibly to the displayed result. This is what separates a lab from a toy.
- **Regime warnings**: when knobs leave an equation's validity domain, shade the affected chart region and show a plain-sentence warning ("below ~2 nm this model ignores quantum tunneling; real behavior diverges"). Never silently extrapolate.
- **Breaking points**: where the system qualitatively fails (yield collapses, engine flames out, cost explodes), mark the threshold on the chart and explain the mechanism of failure in one sentence when crossed.
- Preset scenarios (3-5 buttons): named real-world operating points from the anchor data ("TSMC N5-class", "Merlin sea level"), each setting all knobs at once - these anchor the sandbox to reality.
- Optional challenge mode if the user asked for it: "get output above X using only these two knobs" style goals that force discovering the trade-off structure.
- Responsive (phone portrait to desktop), keyboard-operable sliders with visible focus, `prefers-reduced-motion` respected, no horizontal page scroll, touch targets at least 44 px.

Verify before declaring done: each preset reproduces its anchor point to within the tolerance stated in KNOWLEDGE.md; sliders at extremes trigger the right regime warnings; no console errors.

## Phase 4 — Publish

Offer GitHub Pages (create repo, push `index.html` + `KNOWLEDGE.md`, enable Pages via `gh api repos/<owner>/<repo>/pages -X POST -f 'source[branch]=main' -f 'source[path]=/'`) or an Artifact if that tool is available. Report the live URL, where `KNOWLEDGE.md` lives, and how many review rounds the model survived.
