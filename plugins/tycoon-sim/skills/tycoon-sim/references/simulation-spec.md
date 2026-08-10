# Simulation page specification

The deliverable is one self-contained `index.html`. No external requests of any kind: no CDNs, no web fonts, no remote images. Inline everything. Target under 1 MB without embedded images, under 5 MB with them.

## Visual style

- Isometric projection (2:1 pixel ratio, i.e. x-step 2 units per 1 unit of y), drawn in SVG. No WebGL, no canvas unless the station count makes SVG measurably slow.
- Low-poly / flat-shaded look: each object is a handful of polygons with three tones per hue (top face light, left face mid, right face dark) to fake lighting. This is the RollerCoaster Tycoon sensibility — chunky, readable shapes, not detail.
- Restrained palette: one dominant hue for ground/track, one accent hue for the cart and interactive highlights, desaturated tones for buildings. Text on a solid panel background, never directly over the scene.
- Buildings differ per station so stations are tellable apart at a glance: a furnace looks like a furnace (chimney, glow), a cleanroom looks like a cleanroom (flat white box, blue windows). Invent simple silhouettes from the stage's "Where it happens" line.
- Subtle ambient animation earns a lot: smoke puffs from chimneys, a blinking status light, a slowly rotating radar dish. One or two per scene, CSS-animated, cheap.

## Layout and track

- The track is a single winding path (S-curves or a loose spiral) visiting every station in order. Compute station positions from the count; do not hardcode for one topic.
- Station spacing generous enough that the payload transformation is visible while the cart travels between them.
- The whole scene lives in one `<svg>` with a `viewBox`; pan and zoom by transforming a root `<g>`. Mouse drag and wheel to pan/zoom on desktop; one-finger drag and pinch on touch.
- A "follow cart" toggle (default on) keeps the camera centered on the cart; any manual pan turns it off until re-enabled.

## The cart and payload

- The cart is the protagonist. It moves along the track at constant speed, pauses at each station for a dwell time (during which the station "operates": a simple animation like doors closing, glow, motion), then departs.
- The payload sits visibly on the cart. At each station's dwell, it swaps (with a short transition — scale-down/scale-up is enough) to the next visual state, taken from the knowledge base's "Output: how it visibly differs" lines. Every state must be visually distinct at a glance.
- Above the cart, a small label names the payload's current state ("quartz sand", "polysilicon chunks", "monocrystalline ingot", ...).

## HUD and controls

- Top bar: topic title, current stage name and number ("Stage 4 of 12: Czochralski growth"), progress bar.
- Bottom-left control cluster: play/pause, step back, step forward, restart, speed selector (0.5x / 1x / 2x / 4x).
- Keyboard: space play/pause, left/right arrows step, R restart, +/- zoom.
- Controls are real buttons with visible focus states and `aria-label`s. Hit targets at least 44x44 px on touch.

## Station info panel

- Clicking or tapping a station (or the "details" button while the cart dwells there) opens a panel: stage name, Input / What happens / Output as short labeled sections, key numbers pulled verbatim from KNOWLEDGE.md, and the stage's "common misconception" as a visually distinct call-out.
- Desktop: side panel (right, ~360 px). Small screens: bottom sheet covering at most 60% of the viewport, swipe-down or button to dismiss.
- Panel text is copied from the reviewed KNOWLEDGE.md. Do not paraphrase from memory.
- While the panel is open the simulation keeps running unless the user paused it; opening the panel must not steal play state.

## Quiz mode (when requested)

- Toggle in the top bar, default off. When on, the cart stops after leaving each station and one multiple-choice question about the stage just completed appears (3–4 options, one correct).
- Correct: brief confirmation, cart proceeds. Wrong: show the relevant KNOWLEDGE.md excerpt, let the user retry. No scoring pressure beyond a simple "8/12 first-try" tally at the end.
- Write questions that test the mechanism ("why is the melt rotated counter to the crystal?") rather than trivia recall.

## Responsiveness and quality bar

- Portrait phone (375 px wide): HUD collapses to essentials, controls stay reachable with a thumb, scene remains pannable.
- Respect `prefers-reduced-motion`: when set, replace continuous movement with discrete jumps between stations and disable ambient animations.
- No layout horizontal scroll ever; the SVG scene pans within its own bounds.
- Page must work opened as a local file (`file://`) — this is a consequence of the no-external-requests rule, and a good smoke test.
- Before declaring Phase 3 done, verify: every stage from KNOWLEDGE.md appears; every payload state is distinct; controls all work via mouse, touch simulation, and keyboard; no console errors.
