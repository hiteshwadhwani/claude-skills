---
name: prereq-map
description: Find where the user's knowledge actually ends before they study a topic - builds a prerequisite dependency graph for the target, calibrates against what they already know with a short diagnostic, and produces the shortest learning path from their real frontier. Use when the user says a topic is over their head, does not know where to start, asks "what do I need to know first", or invokes /prereq-map <topic>. Pairs with tycoon-sim, depth-dial, anatomy, and knob-lab as the router that decides what to learn in which order.
---

# Prereq Map: the problem is usually the stack under the topic

When a topic will not stick, the missing depth is usually one or two layers below it. This skill maps the prerequisite structure of a target topic, finds the user's actual knowledge frontier with a short diagnostic, and emits the shortest learning path from frontier to target - routing each step to whichever learning skill fits its shape.

Target topic from the argument (`/prereq-map diffusion models`) or conversation. If missing, ask. Also ask one framing question up front if not evident: what do they want to be able to DO - follow papers, build something, or hold a technical conversation? The path depends on the destination depth.

## Style

- No emojis. Real terms, defined in place.
- The diagnostic is calibration, not examination: keep it short, never scold, and treat "no idea" as the single most useful answer the user can give.

## Phase 1 — Build the prerequisite graph

Research what the topic actually depends on (textbook tables of contents, course syllabi, survey papers are the best sources for dependency structure), then write `PREREQ.md`:

```markdown
# <Target topic> — prerequisite map

## Target
The topic, and the stated goal depth (follow papers / build / converse).

## Concept graph
For each of 10-25 concepts:
- Name and one-sentence definition
- Depends on: which other concepts in this list (2-4 max; only true hard dependencies)
- Why the target needs it: the specific place this concept bites when learning the target
- Depth needed: gist / working / deep - relative to the stated goal
- Shape: process / system / causal-quantitative / conceptual (this routes it to a learning skill later)
- Probe: one diagnostic question that reveals whether someone has the needed depth - a question that cannot be answered by having heard the term (ask for the why, the consequence of removal, or a discriminating example)

## Sources
Syllabi, textbooks, surveys consulted.
```

Keep dependencies honest: "helps" is not "depends on". The graph must be a DAG; if two concepts seem mutually dependent, one is really a subtopic of the other or the dependency is soft - resolve it. Cap at 25 concepts; a 60-node graph is a syllabus, not a map.

## Phase 2 — Adversarial graph review

Spawn a subagent instructed to refute:

> Review PREREQ.md for <target>. Check: (1) missing prerequisites - concepts any standard treatment assumes that are absent; (2) false dependencies - edges where one can honestly learn the dependent without the dependency at the stated depth; (3) inflated depth ratings relative to the stated goal; (4) probe questions answerable by term-recognition alone (these are the worst defect - they make the diagnostic lie); (5) cycles or subtopic-masquerading-as-peer issues. Return a numbered defect list with corrections.

Fix everything; re-review if structural defects were found; log rounds under `## Review log`.

## Phase 3 — Calibrate the frontier

Diagnose interactively, exploiting the graph structure - this is a binary-search, not an exam:

1. Start with probes from the middle of the dependency depth, not the bottom. One question at a time, wait for each answer.
2. A confident, correct answer marks that concept AND its entire dependency subtree as known - do not probe below a solid answer without reason. An answer showing term-familiarity without mechanism marks the concept "gist only"; probe one level below it.
3. "No idea" marks the concept unknown; probe its dependencies to find where solid ground resumes.
4. Grade answers against the reviewed PREREQ.md probes, not recall. Typical topics need only 5-10 questions to locate the frontier; say so at the start so the user knows this is short.
5. Trust honest self-reports ("I know undergrad linear algebra cold") for whole subtrees - verify with at most one spot-check probe, not a quiz per node.

## Phase 4 — The path

Compute the shortest path: unknown and gist-only concepts that are ancestors of the target, in topological order, at the depth the goal requires - nothing else. Known subtrees appear as a single line ("assumed known: ..."), so the user sees what they were right to skip.

Write `PATH.md` and present it:

- Ordered steps, each with: concept, depth to reach, why the target needs it (from the graph), estimated effort (hours-scale honesty), and the **recommended vehicle** - route by shape: process → tycoon-sim, system → anatomy, causal-quantitative → knob-lab, conceptual/layered → depth-dial, dense primary source → paper-walkthrough; plain reading with a named specific resource when a skill is overkill. After milestone concepts, suggest a teach-back checkpoint.
- Mark the 2-3 steps that unlock the most downstream nodes ("keystone" steps) so the user knows where the leverage is.
- End with the first concrete action, stated imperatively ("Start: /tycoon-sim how a transformer processes a token - 1 evening"), and offer to kick it off immediately.

Optionally (if the user wants it), render the graph as a self-contained `index.html`: the DAG drawn top-down, nodes colored known / gist / to-learn, the computed path highlighted, click a node for its definition and why-needed, publishable like the other skills' pages. Build this on request rather than by default - the path document is the deliverable; the picture is a bonus.
