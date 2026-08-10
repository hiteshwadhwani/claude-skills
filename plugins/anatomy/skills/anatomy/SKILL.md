---
name: anatomy
description: Learn how a complex system is built by exploring an interactive exploded-view diagram of its components - an EUV machine, a jet engine, a GPU, a Kubernetes cluster, a transformer model. Use when the user wants to understand a system's structure and parts (rather than a process over time - that is tycoon-sim's job), asks "what's inside X" or "how is X built", or invokes /anatomy <system>.
---

# Anatomy: learn a system by taking it apart

Processes are learned by watching them run; systems are learned by taking them apart. This skill builds an interactive exploded-view page for a system: components spread apart in space, each clickable to reveal its role, its interfaces to neighbors, and what breaks without it.

If the topic is actually a process (a sequence of transformations over time), say so and suggest tycoon-sim instead. Physical machines, architectures, and organizational structures belong here.

System comes from the argument (`/anatomy EUV machine`) or conversation. If missing, ask.

## Writing style (all phases)

- No emojis anywhere: documents, page UI, replies.
- Real technical terms, defined in place. The reader is a technical adult.
- Concrete numbers with units over adjectives; every non-obvious number carries a source.

## Phase 1 — Build the decomposition knowledge base

Research from primary sources (vendor documentation, teardowns, engineering references, papers), then write `KNOWLEDGE.md`:

```markdown
# <System> — anatomy knowledge base

## Overview
What the system does as a whole, its rough scale (size, cost, part count), who builds it.

## Decomposition
Nested outline: 6-12 top-level components, each optionally with 2-6 subcomponents. One level of nesting maximum - deeper than that and the diagram becomes noise.

## <Component name>
- Role: what it does for the system, in one or two sentences
- How it works: the mechanism, with key numbers (dimensions, tolerances, power, throughput, cost)
- Interfaces: which components it connects to and what crosses each interface (matter, energy, data, force)
- Without it: what specifically fails or degrades if this component is removed or broken
- Why this design: why the component works this way and not the obvious alternative — the constraint that decided it (why tin droplets and not a solid target, why a reflective and not refractive optic)
- Notable: vendor, a surprising fact, or a historical note - whichever is most memorable
- Common misconception: one thing people usually get wrong about this part

## Cross-cutting facts
Totals that span components: mass/cost/power budgets, assembly time, part counts.

## Sources
Numbered URLs actually consulted.
```

The **Interfaces** lines matter most: they become the visible connection lines in the diagram, and they are what separates understanding a system from memorizing a parts list. If two components interact and no interface line says how, the decomposition is not done.

The **Without it** line is the comprehension test: if the honest answer is "nothing much", the component does not deserve top-level billing — demote or merge it.

## Phase 2 — Adversarial accuracy review

Spawn a subagent instructed to refute, not approve:

> Review KNOWLEDGE.md for the anatomy of <system>. Find: (1) factual errors and wrong numbers, checked against authoritative sources; (2) wrong or missing interfaces - components that interact in reality but not in the file, or claimed interactions that do not exist; (3) missing components a practitioner would consider essential, and listed components that are really subcomponents of another; (4) claims that sound invented or lack a source. Return a numbered defect list with severity (wrong / misleading / incomplete) and a corrected claim with source URL for each.

Fix every defect; source or delete unverifiable claims. Re-review after fixing any *wrong*-severity defect; stop when a round returns no wrong or misleading findings. Log rounds under `## Review log`.

## Phase 3 — Build the exploded view

One self-contained `index.html`, inline CSS/JS, zero external requests, works from `file://`. All component text copied verbatim from the reviewed `KNOWLEDGE.md`.

Visual model:

- Isometric or straight-on SVG, low-poly flat-shaded style: each component a simple distinct silhouette (invent shapes from the component's real form), three tones per hue to fake lighting. Distinct silhouettes matter more than detail - every component must be tellable apart at a glance.
- **Assembled/exploded slider** in the header: at 0 the components sit together as the whole system; dragging toward 1 spreads them apart along sensible axes with thin leader lines back to their assembled positions. This one control is the soul of the page - it must be smooth and reversible.
- Interface lines between components, visible in exploded state: color-coded by what crosses (matter / energy / data / force), with a small legend. Hovering an interface line shows what flows across it.
- Clicking a component: it highlights, its direct neighbors stay normal, everything else dims, and a panel opens with Role / How it works / Interfaces / Without it / Misconception (the misconception visually distinct as a call-out). Components with subcomponents get their own mini exploded view inside the panel or a drill-in.
- "Without it" mode (toggle in the panel): the selected component turns red/ghosted and every component that fails or degrades as a consequence dims in cascade - failure propagation made visible.
- A parts-list sidebar mirrors the decomposition outline; clicking a name selects that component in the scene. On phones it collapses into a bottom sheet, and the info panel becomes a bottom sheet too.
- Pan/zoom on the scene (drag/wheel, touch drag/pinch). Keyboard: tab cycles components, enter opens the panel, escape closes, arrow keys nudge the explode slider.
- Respect `prefers-reduced-motion`: explode becomes a discrete two-state toggle. No horizontal page scroll; touch targets at least 44 px.

## Phase 4 — Publish

Offer GitHub Pages (create repo, push `index.html` + `KNOWLEDGE.md`, enable Pages via `gh api repos/<owner>/<repo>/pages -X POST -f 'source[branch]=main' -f 'source[path]=/'`) or an Artifact if that tool is available. Report the live URL, where `KNOWLEDGE.md` lives, and how many review rounds the content survived.
