---
name: paper-walkthrough
description: Turn a dense research paper into a verified interactive companion page - notation glossary, figures re-explained in plain terms, every section readable at two depths, prerequisite concepts surfaced. Use when the user shares a paper (PDF, arXiv link, URL) they need to deeply understand, says a paper is too dense, or invokes /paper-walkthrough <paper>. The companion is checked against the paper itself, so it cannot drift from what the authors actually claimed.
---

# Paper Walkthrough: a companion, not a summary

A summary replaces the paper; a companion gets you through it. This skill produces an interactive page that sits next to the paper: the paper's own structure, section by section, with a plain-language layer, the notation decoded, figures explained, and the concepts the authors assumed you already knew.

Input: a PDF path, arXiv link, or URL from the argument or conversation. If missing, ask. Fetch and read the **entire paper** before writing anything - every section, every figure caption, the appendices if they carry substance. A companion built from the abstract is a summary with extra steps.

## Writing style (all phases)

- No emojis anywhere.
- Plain language is not simplified language: keep the authors' terms, decode them in place. Never replace a precise claim with a vaguer one.
- The companion explains; it does not editorialize. Keep "is this a good paper" judgments out unless the user asks separately.

## Phase 1 — Build the companion knowledge base

Write `COMPANION.md` with these parts:

```markdown
# Companion: <paper title> (<authors, year, venue/arXiv id>)

## The paper in three layers
- One sentence: the claim.
- One paragraph: problem, approach, headline result with its actual number.
- One page: the full argument - why the problem is hard, what is new, how it is validated, honest limitations (the authors' stated ones plus any obvious unstated ones, labeled as yours).

## Design decisions
The paper's 3-6 load-bearing choices, each answering the origin question: what was chosen, what the obvious alternative was, and why the authors rejected it (as they argue it, or inferable and labeled as inference). For VQ-VAE this section would carry "why discrete latents and not continuous" - the question a summary never answers and the one the reader actually has.

## Prerequisites
Concepts the paper assumes without explaining, each with: name, two-to-four-sentence explanation sufficient for THIS paper's usage, and where in the paper it first bites. Rank by how much of the paper is unreadable without it.

## Notation glossary
Every recurring symbol and abbreviation: symbol, meaning, where introduced, and a gloss in words ("KL(p||q) - how much probability mass p places where q does not expect it").

## Sections
For each section of the paper, at two depths:
- Gist (2-3 sentences): what this section establishes and why the argument needs it.
- Detail (a paragraph or two): the actual mechanism/derivation/experiment, restated plainly, key equations translated into words, with anchor references to the paper ("Eq. 4", "Sec 3.2", page numbers).

## Figures and tables
For each: what is on each axis / in each column, what the reader is supposed to see, and the one number worth remembering. Flag any figure whose visual impression overstates the tabulated result.

## Claims register
The paper's 5-10 load-bearing claims, each with: the claim, where it is supported (experiment/theorem/citation), and how strong the support is (proven / measured / asserted / cited).
```

## Phase 2 — Adversarial review against the paper

The reference here is the paper itself, not the web. Spawn a subagent with access to the paper:

> You have the paper <ref> and COMPANION.md. Verify the companion against the paper line by line: (1) every number in the companion appears in the paper with the same value and context; (2) section gists/details claim nothing the section does not establish - watch for strengthened claims ("proves" for "suggests", dropped conditions, benchmark results generalized beyond the tested setting); (3) notation glossary matches the paper's actual definitions; (4) figure explanations match what the figures show; (5) nothing load-bearing in the paper is missing from the companion. Return a numbered defect list (wrong / overstated / missing) with the paper location that decides each one.

Additionally, have the reviewer (or a second pass) sanity-check the **Prerequisites** explanations against general sources, since those are the one part not derivable from the paper.

Fix every defect; re-review after any *wrong* finding; stop when clean. Log rounds under `## Review log`.

## Phase 3 — Build the companion page

One self-contained `index.html`, inline CSS/JS, zero external requests, works from `file://`. All content copied verbatim from the reviewed `COMPANION.md`.

- Layout: the three-layer overview on top (layers expandable), then sections in paper order. Each section shows its Gist with a control to expand to Detail in place - same interaction as reading deeper, not navigating away.
- Persistent notation drawer: a button (and `?` key) opens the glossary as a side drawer; symbols appearing in companion text are dotted-underlined and open the drawer scrolled to that symbol.
- Prerequisites up front as collapsed cards between overview and sections, ranked; each card names where in the paper it first bites, and that mention links back to the card.
- Figures/tables explanations placed with their section, labeled with the paper's own numbering ("Figure 3").
- Claims register as a compact table at the end: claim, support type, strength - strength shown as a plain badge (proven / measured / asserted / cited).
- Every section header carries its paper anchor ("Sec 4.1, p.7") so the user can sit the companion beside the PDF and cross-read - this pairing is the intended use, say so at the top of the page.
- Reading progress persisted in `localStorage` (sections marked read on expand; a "resume" jump on return).
- Responsive; drawer becomes a bottom sheet on phones; no horizontal page scroll; `prefers-reduced-motion` respected.

## Phase 4 — Publish

Offer GitHub Pages (repo per paper, push `index.html` + `COMPANION.md`, enable Pages via `gh api repos/<owner>/<repo>/pages -X POST -f 'source[branch]=main' -f 'source[path]=/'`) or an Artifact if available. Do not embed or republish the paper's own PDF or figure images - link to the canonical source; the companion carries only your explanations. Report the live URL and how many review rounds the companion survived.
