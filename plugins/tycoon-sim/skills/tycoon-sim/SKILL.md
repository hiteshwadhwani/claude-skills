---
name: tycoon-sim
description: Learn how a complex process works by building an accurate, RollerCoaster Tycoon-style interactive simulation of it (chip fabrication, rocket engines, EUV machines, LLM inference, etc.). Use when the user wants to learn a topic visually, asks to "explain X as a simulation or game", "build a tycoon-style sim of X", or invokes /tycoon-sim <topic>. Runs a four-phase flow - research a knowledge base, adversarially review it for accuracy, build a low-poly animated simulation page, publish it.
---

# Tycoon Sim: learn a process by simulating it

Turn a topic the user wants to learn into an interactive, low-poly, RollerCoaster Tycoon-style simulation: a cart travels along a track through the real stages of the process, and the payload visibly transforms at each station. The user learns by watching the flow and clicking into stations, not by reading a bulleted list.

The topic is given as an argument (`/tycoon-sim how chips are made`) or in the surrounding conversation. If no topic was given, ask for one before doing anything else.

Never skip a phase. The accuracy review (Phase 2) is the entire reason this method beats "just ask the LLM to explain it" — it is not optional.

## Writing style (all phases)

- No emojis anywhere: not in documents, not in the simulation UI, not in your replies.
- Do not dumb the content down. Use the real technical terms and define them in place. The reader is a technical adult, not a child.
- Prefer concrete numbers with units over adjectives ("a 13.5 nm wavelength" beats "an extremely small wavelength").
- Always answer the origin question. For every design choice, state why it is this way and not the obvious alternative — why this stage exists at all, why this method beat the one it replaced. "VQ-VAE uses discrete latents" is trivia; "discrete latents, because X breaks under continuous ones" is understanding. A claim without its why is not done.

## Phase 1 — Build the knowledge base

Research the topic and write `KNOWLEDGE.md` in the project directory. Use web search and primary sources (manufacturer documentation, engineering references, papers) — not just recall. Structure the file as a **process pipeline**, because that is what the simulation will render:

```markdown
# <Topic> — process knowledge base

## Overview
Three or four sentences: what goes in, what comes out, why it is hard.

## Stages
### Stage 1: <name>
- Input: what arrives at this station (physical state, form, quantity)
- What happens: the actual mechanism, with key numbers (temperatures, durations, tolerances, costs)
- Output: what leaves, and how it visibly differs from the input
- Where it happens: machine/facility name, notable vendors if relevant
- Why this way: the constraint that forces this method — what breaks with the obvious alternative, or what this replaced and why
- Common misconception: one thing people usually get wrong about this stage

### Stage 2: ...

## Cross-cutting facts
Facts that span stages: total duration, yield rates, cost breakdown, scale.

## Sources
Numbered list of URLs actually consulted. Every non-obvious number above cites one.
```

Aim for 8–15 stages. Fewer than 8 means the pipeline is too coarse to teach anything; more than 15 makes the simulation a slog. Collapse or split real-world steps to land in that range, but note in the stage text when a station compresses several real steps.

The "Output: how it visibly differs" line matters most: it is what drives the payload's visual transformation in Phase 3. If you cannot say what changed visibly, you do not understand the stage yet — research more.

## Phase 2 — Adversarial accuracy review

Spawn a subagent (general-purpose, or run a deliberately hostile fresh pass if subagents are unavailable) with instructions to **refute** the knowledge base, not to admire it:

> Review KNOWLEDGE.md for a simulation about <topic>. Your job is to find errors, not to approve. Check: (1) factual claims and numbers against authoritative sources on the web; (2) stage ordering; (3) missing stages a practitioner would consider essential; (4) oversimplifications that would teach the reader something false; (5) numbers that look invented or lack a source. Return a numbered list of defects with severity (wrong / misleading / incomplete) and a corrected claim with a source URL for each. If a claim cannot be verified either way, flag it as unverifiable.

Then fix every defect in `KNOWLEDGE.md`: correct *wrong*, rephrase *misleading*, and either source or delete *unverifiable* claims — never ship an unverifiable number. If the review found any *wrong*-severity defects, run one more review round after fixing. Stop when a round returns no wrong or misleading findings.

Record the review outcome at the bottom of `KNOWLEDGE.md` under `## Review log` (date, rounds, defects found and fixed).

## Phase 3 — Build the simulation

Read `references/simulation-spec.md` (in this skill's directory) for the full visual and UX specification, then build the simulation as a **single self-contained `index.html`** — inline CSS and JS, no CDN or external requests, so it works identically on GitHub Pages, as an Artifact, and from a local file.

Core requirements (the spec file has the details):

- Isometric low-poly look, SVG-based, RollerCoaster Tycoon palette sensibility.
- One station per knowledge-base stage, laid out along a winding track.
- A cart moves along the track; the payload it carries visually changes at every station, matching the "Output" lines from `KNOWLEDGE.md`.
- Controls: play/pause, speed (0.5x/1x/2x/4x), step forward/back one station, restart. Keyboard: space, arrows, R.
- Clicking a station opens an info panel with that stage's content from the knowledge base, including the "common misconception" as a call-out.
- Responsive: usable on a phone in portrait and a large monitor. The scene pans/scales; the info panel becomes a bottom sheet on small screens.
- Optional quiz mode (build it if the user asked for challenges): between stations, a single multiple-choice question about the *previous* stage; wrong answers show the relevant knowledge-base excerpt.

All station text in the HTML must be copied from the reviewed `KNOWLEDGE.md`, never re-generated from memory — the review only certifies the file.

## Phase 4 — Publish

Ask the user (or honor what they already said) between:

1. **GitHub Pages**: create a repo named after the topic (e.g. `chip-tycoon`), push `index.html` and `KNOWLEDGE.md`, enable Pages on the main branch via `gh api repos/<owner>/<repo>/pages -X POST -f 'source[branch]=main' -f 'source[path]=/'`, and report the `https://<owner>.github.io/<repo>/` URL. It can take a minute or two to go live.
2. **Artifact** (if the Artifact tool is available): publish `index.html` directly and report the URL.

Either way, finish by telling the user: the live URL, where `KNOWLEDGE.md` lives, and how many review rounds the content survived.

## Improving an existing sim

When the user returns wanting upgrades, common requests map to:

- "More realistic visuals": replace low-poly SVG shapes for specific payload states with more detailed drawings, or embed rendered images as data URIs. Keep total page size under 5 MB.
- "Add challenges": implement quiz mode from Phase 3 if it was skipped.
- "Add a topic branch": some processes fork (e.g. logic vs. memory chips). Add a track switch the user can toggle before pressing play.
- "It's wrong about X": treat as a Phase 2 defect — verify, fix `KNOWLEDGE.md` first, then propagate to the HTML.
