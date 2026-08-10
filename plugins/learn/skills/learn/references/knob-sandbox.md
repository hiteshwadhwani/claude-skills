# Centerpiece spec: parameter sandbox

For causal-quantitative topics: sliders for the real governing parameters, live consequences, the math shown. The intuition comes from breaking the system. If the topic has no honest quantitative structure, do not fake one - fall back to conceptual (no centerpiece).

## Shape data required (in KNOWLEDGE.md)

- 3-8 parameters: name, symbol, unit, realistic range (min/default/max, sourced), physical meaning, and **why real systems sit near the default** (cost, physics limit, diminishing returns - operating points are design decisions).
- Outputs: name, unit, why the reader cares.
- Relationships: each equation (or well-sourced empirical approximation) in plain text and as coded, its source, its **validity domain**, and 2-3 real-world **anchor points** the model must reproduce. Prefer a simple model honest about its domain over a rich model quietly wrong.
- "What the model deliberately ignores" - the honest list of second-order effects left out.
- Review requirement: dimensional consistency and anchor-point reproduction must be checked arithmetically. If an anchor does not reproduce, fix the model, never the anchor.

## Layout and interaction

- Knobs (left on desktop, top on phones): each slider shows name, symbol, live value with unit, range; per-knob reset and global reset. Log-scale where the range spans decades.
- Outputs: one primary chart of the headline output against the most interesting parameter, updating live, current operating point marked; secondary outputs as numeric tiles with units.
- **Show the math**: a collapsible panel per output showing the governing equation with current values substituted, visibly computing to the displayed result. This separates a lab from a toy. Each equation lives in a named JS function with a comment citing its KNOWLEDGE.md relationship, so the page is auditable against the file.
- **Regime warnings**: when knobs exit a validity domain, shade the affected chart region and warn in a plain sentence ("below ~2 nm this model ignores tunneling; real behavior diverges"). Never silently extrapolate.
- **Breaking points**: where the system qualitatively fails, mark the threshold and explain the failure mechanism in one sentence when crossed.
- Presets (3-5 buttons): named real operating points from the anchor data ("TSMC N5-class", "Merlin sea level") setting all knobs at once - these anchor the sandbox to reality.
- Moving a knob whose equation involves a backbone concept highlights that concept in the side rail (the SKILL.md linking rule).
- Verify before done: presets reproduce anchors within stated tolerance; extremes trigger the right warnings; sliders keyboard-operable.
