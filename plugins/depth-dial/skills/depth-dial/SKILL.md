---
name: depth-dial
description: Explain a topic at five zoom levels on one interactive page, where every claim can be expanded one level deeper in place. Use when the user wants to learn a topic without committing to one depth, says an explanation is "too shallow" or "too dense", asks for a layered or drill-down explainer, or invokes /depth-dial <topic>. Research-heavy topics where the user needs practitioner depth in only some subtopics are the ideal case.
---

# Depth Dial: one topic, five zoom levels, reader-controlled

The failure mode of every explanation is picking one depth and forcing it on the whole topic. This skill builds a single interactive page where the topic is readable at level 1 in two minutes, and any individual concept can be dialed down to level 5 (practitioner grade) without leaving the page.

Topic comes from the argument (`/depth-dial how EUV lithography works`) or conversation. If missing, ask.

## Writing style (all phases)

- No emojis anywhere: documents, page UI, replies.
- Use real technical terms at every level; what changes between levels is how much mechanism is exposed, never whether terms are dumbed down. Define terms in place at the level where they first appear.
- Concrete numbers with units over adjectives. Every non-obvious number carries a source.

## The five levels

- **L1 — Claim**: one or two sentences. What it is and why it matters. No mechanism.
- **L2 — Shape**: a short paragraph. The major moving parts and how they relate. A reader stopping here can follow a conversation about the topic.
- **L3 — Mechanism**: how it actually works, step by step, with the key numbers. The default reading depth.
- **L4 — Why this way**: the constraints and trade-offs that forced this design; the alternatives that lost and why. Answer the origin question explicitly: not "VQ-VAE uses discrete latents" but "discrete, because continuous latents let the decoder ignore the prior — here is what broke". This is the level that creates real understanding, and it is mandatory for every concept that reaches L4: an L4 that merely adds detail without a why-not-the-alternative is a level violation.
- **L5 — Practitioner**: the details experts argue about — edge cases, failure modes, open problems, the equations, primary-source references.

Levels are cumulative refinements of the same concept, not different essays: L3 must expand on exactly what L2 asserted, never contradict or restart it.

## Phase 1 — Build the layered knowledge base

Research from primary sources (web search, manufacturer docs, papers), then write `KNOWLEDGE.md`:

```markdown
# <Topic> — layered knowledge base

## Concept tree
Nested outline of 6-12 top-level concepts, each with subconcepts. This becomes the page structure.

## <Concept name>
### L1
### L2
### L3
### L4
### L5
(each level's text, with source-numbered claims)

## Sources
Numbered URLs actually consulted.
```

Not every concept needs all five levels: a minor concept may stop at L3. Every top-level concept must reach at least L3, and at least a third of them should reach L5 — if nothing reaches L5, the research is too shallow to justify this skill.

## Phase 2 — Adversarial accuracy review

Spawn a subagent instructed to refute, not approve:

> Review KNOWLEDGE.md for <topic>. Find: (1) factual errors and wrong numbers, checked against authoritative web sources; (2) level violations — L1/L2 text that is vague rather than short, deeper levels that contradict shallower ones, L4 sections that just add detail instead of explaining trade-offs; (3) claims that sound invented or lack a source; (4) missing concepts a practitioner would consider essential. Return a numbered defect list with severity (wrong / misleading / incomplete / level-violation) and a corrected version with source URL for each.

Fix every defect. Unverifiable claims get sourced or deleted, never shipped. Re-review after fixing if any defect was *wrong*-severity; stop when a round returns no wrong or misleading findings. Log rounds under `## Review log` in `KNOWLEDGE.md`.

## Phase 3 — Build the page

One self-contained `index.html`, inline CSS/JS, zero external requests, works from `file://`. All text copied verbatim from the reviewed `KNOWLEDGE.md` — never re-generated from memory.

Interaction model:

- The page opens with every concept at L1: the whole topic fits on one or two screens.
- Each concept block has a depth control (either a +/- stepper or clicking the block body to go deeper, with an explicit control as fallback) and a visible level indicator (e.g. "L3 of 5" with five dots).
- Expanding is **in place**: deeper text appears inside the same block with a subtle left border per level, so the document reads top-to-bottom at whatever mixed depths the reader has dialed. No modals, no navigation away.
- Global dial in the header: "Set all to L1 / L2 / L3" for readers who want a uniform pass, plus "expand all to max".
- A thin progress rail on the side shows the concept tree with each concept's current level, and jumps on click.
- Terms defined elsewhere on the page are dotted-underlined; clicking scrolls to the defining concept at the appropriate level.
- Reading-time estimate in the header, recomputed live from currently visible text.
- Responsive: on phones the progress rail collapses into a menu; blocks stay full-width; touch targets at least 44 px. Respect `prefers-reduced-motion` (no expand animations). No horizontal page scroll.
- State (per-concept levels) persists in `localStorage` so a returning reader keeps their dialed depths.

## Phase 4 — Publish

Offer GitHub Pages (create repo, push `index.html` + `KNOWLEDGE.md`, enable Pages via `gh api repos/<owner>/<repo>/pages -X POST -f 'source[branch]=main' -f 'source[path]=/'`) or an Artifact if that tool is available. Report the live URL, where `KNOWLEDGE.md` lives, and how many review rounds the content survived.
