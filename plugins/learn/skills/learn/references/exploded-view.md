# Centerpiece spec: exploded system view

For system-shaped topics: components spread apart in space, each clickable for role, interfaces, design rationale, and failure propagation.

## Shape data required (in KNOWLEDGE.md)

6-12 top-level components (at most one level of subcomponents), each with: role (one or two sentences), how it works (mechanism, key numbers), **interfaces** - which components it connects to and what crosses each one (matter / energy / data / force; if two components interact and no interface line says how, the decomposition is not done), **without it** - what specifically fails or degrades (if the honest answer is "nothing much", demote or merge the component), why this design (the origin question - why tin droplets and not a solid target), notable fact, common misconception.

## Visuals and interaction

- Isometric or straight-on SVG, low-poly flat-shaded, each component a simple **distinct silhouette** invented from its real form - distinctness matters more than detail. Three tones per hue.
- **Assembled/exploded slider** in the header: at 0 the components form the whole system; toward 1 they spread along sensible axes with thin leader lines back to assembled positions. Smooth and reversible - this control is the soul of the page.
- Interface lines visible when exploded, color-coded by what crosses, with a small legend. Hovering a line shows the flow.
- Click a component: it highlights, direct neighbors stay normal, the rest dims; info panel opens (side panel on desktop, bottom sheet on phones) and the linked backbone concept expands per the SKILL.md linking rule. Subcomponents get a mini exploded view inside the panel.
- **"Without it" mode** (toggle in the panel): the selected component turns red/ghosted and everything that fails as a consequence dims in cascade - failure propagation made visible.
- Parts-list sidebar mirroring the decomposition; click to select in scene. Collapses to a bottom sheet on phones.
- Pan/zoom (drag/wheel, drag/pinch). Keyboard: tab cycles components, enter opens panel, escape closes, arrows nudge the explode slider.
- `prefers-reduced-motion`: explode becomes a discrete two-state toggle.
