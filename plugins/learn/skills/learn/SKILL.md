---
name: learn
description: Learn any topic or paper deeply with one command - calibrate what the user already knows, research a verified knowledge base, build a single interactive page in the representation that fits the topic's shape (process simulation, exploded system view, parameter sandbox, layered explainer, paper companion), publish it, then examine the user on it. Use when the user wants to learn or deeply understand something, says a topic or paper is over their head, or invokes /learn <topic or paper link>.
---

# Learn: one command from "I don't get X" to verified understanding

One flow, six phases. The user gives a topic (or paper link); everything else is decided here, not by making them choose between tools. Phases 1 and 6 involve the user; 2-5 run autonomously with brief status lines.

## Style (all phases, non-negotiable)

- No emojis anywhere: documents, page UI, replies.
- Real technical terms at every depth, defined in place. What varies between depth levels is how much mechanism is exposed, never vocabulary dumbing.
- Concrete numbers with units over adjectives. Every non-obvious number carries a source.
- **The origin question is mandatory.** Every design choice in the material must answer why it is this way and not the obvious alternative. "VQ-VAE uses discrete latents" is trivia; "discrete, because continuous latents let the decoder bypass the codebook - here is what broke" is understanding. A claim without its why is not done.
- **Draw cheap ASCII diagrams whenever a concept is a shape.** Mathematical notation and its geometric interpretation, a model architecture, a data flow, a matrix factorization, a worked example - if the reader would benefit from a picture, draw a small monospace ASCII diagram instead of describing the shape in prose. They cost almost no tokens, render everywhere (chat, KNOWLEDGE.md fenced blocks, the page in a `<pre>` block inside an `overflow-x: auto` container), and anchor the algebra: an equation like h = W0x + BAx lands harder next to a two-branch box diagram with the dimensions written on the boxes. Rules: label the dimensions/quantities on the diagram, not just in surrounding text; keep each diagram under ~15 lines; one idea per diagram; when the diagram is your interpretation rather than a redraw of the source's figure, label it "(diagram ours)". Reach for SVG only when the concept genuinely needs color, curves, or interaction - the centerpiece owns that; inline explanations own ASCII.
- No praise inflation when examining: "correct" is a grade. Never mark a wrong answer right.

## Phase 0 — Frame the topic

Classify the topic's **shape** (it drives which interactive centerpiece the page gets):

- **process** - a sequence of transformations over time (how chips are made, how a request flows through a system) → centerpiece per `references/process-sim.md`
- **system** - a structure of interacting parts (an EUV machine, a GPU, a K8s cluster) → `references/exploded-view.md`
- **causal-quantitative** - governed by parameter relationships (fab yield, inference cost, rocket thrust) → `references/knob-sandbox.md`
- **paper** - a specific dense document the user linked → `references/paper-companion.md`
- **conceptual** - none of the above dominates → no centerpiece; the layered text (Phase 4 backbone) carries the page alone

Big topics mix shapes; pick the primary for the centerpiece and fold secondary shapes into deep levels of the relevant concepts (a "how chips are made" page is a process sim whose lithography station's L4 may explain the machine's anatomy in prose). Announce the chosen shape in one line; if genuinely torn between two, ask the user - that is the only tool-choice question this skill is ever allowed to ask.

If the goal depth is not evident, ask once: follow papers / build something / hold a technical conversation. Default: follow papers.

## Phase 1 — Calibrate (fast, skippable)

Find where the user's knowledge actually ends, so the page starts there instead of at zero or over their head. Offer to skip ("say 'skip' to jump straight to research").

1. Sketch the topic's 8-15 prerequisite concepts and their dependencies (a mental DAG; write it into KNOWLEDGE.md later as the Prerequisites section).
2. Binary-search with at most 5-7 probe questions, one at a time, starting mid-depth. Probes must be unanswerable by term-recognition alone - ask for the why, the consequence of removal, or a discriminating example. A solid answer clears that concept and everything below it; "no idea" is the most useful answer and is never scolded.
3. Trust self-reports for whole subtrees, with at most one spot-check.

Output: a frontier - which prerequisites need a primer on the page, which can be one-line assumptions.

## Phase 2 — Research the knowledge base

Research from primary sources (web search; manufacturer docs, textbooks, papers - not recall) and write `KNOWLEDGE.md`:

```markdown
# <Topic> - knowledge base

## Overview
Three or four sentences: what it is, why it matters, why it is hard.

## Prerequisites
From Phase 1: each below-frontier concept with a 2-4 sentence primer sufficient for THIS topic; each cleared concept as a one-line assumption.

## Concepts
6-15 concepts in teaching order. Each at up to five cumulative depth levels:
- L1 Claim: one or two sentences, no mechanism.
- L2 Shape: the moving parts and how they relate.
- L3 Mechanism: how it actually works, with the key numbers.
- L4 Why this way: the constraint that forced this design; the alternative that lost and what breaks with it. Mandatory for every concept that reaches L4 - detail without a why-not is a defect.
- L5 Practitioner: edge cases, failure modes, open problems, primary references.
Minor concepts may stop at L3; at least a third must reach L5 or the research is too shallow.

## Shape data
The section the centerpiece consumes - per the shape's reference file (stages with visible input/output transformations for process; components with interfaces and without-it consequences for system; parameters, sourced equations, validity domains, and anchor points for causal-quantitative; section/notation/claims data for paper).

## Misconceptions
The 5 most common wrong beliefs about this topic, each with the correction.

## Sources
Numbered URLs actually consulted.
```

## Phase 3 — Adversarial review

Spawn a subagent instructed to refute, not approve:

> Review KNOWLEDGE.md for <topic>. Find: (1) factual errors and wrong numbers, checked against authoritative sources (for a paper topic: against the paper itself, line by line); (2) level violations - deeper levels contradicting shallower ones, or L4 sections adding detail without answering why-not-the-alternative; (3) claims that sound invented or lack a source; (4) missing concepts a practitioner would consider essential; (5) shape-data defects per its kind: stage transformations that are not visible, missing component interfaces, equations failing dimensional analysis or not reproducing their anchor points (do the arithmetic), probe/claim strength overstatements. Return a numbered defect list with severity (wrong / misleading / incomplete) and a corrected claim with source URL each.

Fix every defect. Unverifiable claims get sourced or deleted, never shipped. Re-review after any *wrong*-severity fix; stop when a round returns no wrong or misleading findings. Log rounds under `## Review log` in `KNOWLEDGE.md`.

## Phase 4 — Build the page

One self-contained `index.html`: inline CSS/JS, zero external requests, works from `file://`, responsive from phone portrait to large monitor, `prefers-reduced-motion` respected, no horizontal page scroll, touch targets at least 44 px, keyboard operable with visible focus. All text copied verbatim from the reviewed `KNOWLEDGE.md`, never re-generated from memory.

**Design pass (before writing any CSS):** load the `frontend-design` skill if it is available (install once: `claude plugin install frontend-design@claude-plugins-official`) and follow its process — ground the visual identity in the topic's own world (a chip-fab page and a rocket page should not share a look), plan a compact token system (4-6 named colors, 2-3 type roles from system font stacks since no webfonts are allowed, a layout concept, and one signature element), check that plan against the skill's list of AI-default looks and revise anything generic, then build from the plan. If the skill is not installed, still do that planning step yourself. The quality floor in the previous paragraph is non-negotiable either way.

Structure - backbone plus centerpiece:

**Backbone (always): the depth dial.** Concepts render in teaching order, all at L1 initially, so the whole topic reads in two minutes. Each concept expands in place one level at a time (visible "L2 of 5" indicator, subtle per-level left border), so the document stays readable top-to-bottom at whatever mixed depths the reader dials. Header: global "set all to L1/L2/L3" plus live reading-time estimate. Side rail: concept list with current levels, click to jump (collapses to a menu on phones). Cross-referenced terms are dotted-underlined and jump to their defining concept. Prerequisite primers sit as collapsed cards before the first concept. Per-concept levels persist in `localStorage`.

**Centerpiece (by shape):** read the shape's reference file from this skill's `references/` directory and build its spec as an interactive section pinned near the top of the page, fed from `## Shape data`. Selecting an element in the centerpiece (a station, a component, a knob's equation) scrolls to and expands the corresponding backbone concept - the two halves must be linked, not parallel documents.

**ASCII diagrams in the backbone:** per the style rule above, concepts with a shape get a small labeled ASCII diagram in a `<pre>` block (own `overflow-x: auto` wrapper so it scrolls on phones instead of breaking the layout, monospace, theme-token colors). Typical spots: the mechanism level of a concept (L3), design decisions comparing two architectures, and prerequisite primers. Diagrams also belong in KNOWLEDGE.md itself (fenced code blocks) so the page and the knowledge base stay in sync.

**Quiz mode (toggle, default off):** multiple-choice questions between centerpiece milestones testing mechanism and origin ("why is the melt rotated counter to the crystal?"), never term recall. Wrong answers show the relevant KNOWLEDGE.md excerpt.

Verify before declaring done: every concept present, centerpiece↔backbone links work, controls work by mouse/touch/keyboard, no console errors.

## Phase 5 — Publish

Default: a single **library repo** named `learn`, one folder per topic - never one repo per topic.

1. If `<owner>/learn` does not exist yet: create it (`gh repo create learn --public`), add a root `index.html` library page (simple card list: topic title, one-line description, kind of page, date - same typography as the topic pages, theme-aware), and enable Pages once via `gh api repos/<owner>/learn/pages -X POST -f 'source[branch]=main' -f 'source[path]=/'`. Clone lives at `~/projects/learn` or wherever the user keeps projects.
2. Every run: add `<topic-slug>/index.html` + `<topic-slug>/KNOWLEDGE.md`, add a card for it to the root library index, commit, push. The page lands at `https://<owner>.github.io/learn/<topic-slug>/`.
3. Only deviate (separate repo, or an Artifact if that tool is available) if the user asks for it - e.g. a page they want to share or delete independently of the library.

Report the live URL, where `KNOWLEDGE.md` lives, and how many review rounds the content survived. For paper topics, never embed the paper's PDF or figures - link to the canonical source.

## Phase 6 — Teach-back (offer, don't force)

After publishing: "When you've gone through it, say 'examine me' and I'll test what stuck." When invoked (now or in a later session - re-read `KNOWLEDGE.md` first; it is the answer key, never grade against recall), run the examiner loop in `references/teach-back.md`. It ends with a gap report file and three transformed questions to re-ask in a week.

## Returning users

- "It's wrong about X" → treat as a Phase 3 defect: verify, fix `KNOWLEDGE.md` first, propagate to the page.
- "Go deeper on X" → research that concept to L5, re-review the addition, update the page.
- "Examine me" → Phase 6 against the existing `KNOWLEDGE.md`.
