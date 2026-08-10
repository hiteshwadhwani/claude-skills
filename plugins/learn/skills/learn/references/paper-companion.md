# Centerpiece spec: paper companion

For paper-shaped topics (the user linked a specific dense document). A companion gets you through the paper; it does not replace it - say so at the top of the page, and design for side-by-side reading with the PDF.

Read the **entire paper** before writing anything - every section, figure caption, and substantive appendix. Phase 3's reviewer verifies against the paper itself, line by line, not the web (except prerequisite primers, which are checked against general sources).

## Shape data required (in KNOWLEDGE.md)

- Three-layer overview: one sentence (the claim); one paragraph (problem, approach, headline number); one page (the full argument, honest limitations - authors' stated ones plus obvious unstated ones labeled as yours).
- **Design decisions**: the paper's 3-6 load-bearing choices, each answering the origin question - what was chosen, the obvious alternative, why the authors rejected it (as argued, or inferable and labeled as inference). For VQ-VAE: "why discrete latents and not continuous" - the question a summary never answers and the one the reader actually has.
- Notation glossary: every recurring symbol/abbreviation - meaning, where introduced, a gloss in words.
- Per section, two depths: gist (2-3 sentences: what it establishes, why the argument needs it) and detail (mechanism/derivation restated plainly, equations translated to words, anchors like "Eq. 4, Sec 3.2, p.7").
- Figures/tables: each axis/column, what the reader is supposed to see, the one number worth remembering; flag figures whose visual impression overstates the tabulated result.
- Claims register: 5-10 load-bearing claims, each with where supported and how strongly (proven / measured / asserted / cited).
- Review watch-list: strengthened claims ("proves" for "suggests"), dropped conditions, results generalized beyond the tested setting, numbers differing from the paper.

## Page structure

These backbone-integration rules override the generic layout: sections follow the paper's own order; each section's gist/detail ARE its backbone concept at two levels; every section header carries its paper anchor.

- Three-layer overview on top, layers expandable.
- Design decisions as a distinct section right after the overview.
- Prerequisite primers as collapsed cards between overview and sections, each naming where in the paper it first bites (that mention links back to the card).
- Persistent notation drawer (button and `?` key); symbols in text are dotted-underlined and open the drawer scrolled to the symbol. Drawer becomes a bottom sheet on phones.
- Figure explanations sit with their section, labeled with the paper's numbering.
- Claims register as a compact end table with plain badges for support strength.
- Reading progress in `localStorage`, "resume" jump on return.
- Never embed or republish the paper's PDF or figure images - link to the canonical source; the companion carries only your explanations.
