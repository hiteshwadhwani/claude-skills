# Centerpiece spec: process simulation (RollerCoaster Tycoon style)

For process-shaped topics: a cart travels a track through the real stages; the payload visibly transforms at each station.

## Shape data required (in KNOWLEDGE.md)

8-15 stages, each with: input (physical state/form), what happens (mechanism, key numbers), output **and how it visibly differs from the input** (this drives the payload's transformation - if you cannot say what changed visibly, research more), where it happens (machine/facility, for the building silhouette), why this way (the origin question), common misconception. Note when a station compresses several real steps.

## Visuals

- Isometric SVG (2:1 ratio), low-poly flat-shaded: a handful of polygons per object, three tones per hue (top light, left mid, right dark). Chunky readable shapes, not detail.
- Restrained palette: one hue for ground/track, one accent for the cart and highlights, desaturated buildings. Text on solid panels, never over the scene.
- Buildings differ per station so each is tellable at a glance - invent silhouettes from "where it happens" (furnace gets a chimney and glow; cleanroom is a flat white box with blue windows).
- One or two cheap CSS ambient animations (smoke puffs, a blinking status light).

## Track, cart, payload

- Single winding path (S-curves or loose spiral) visiting stations in order; positions computed from stage count, not hardcoded. Spacing generous enough that transformations are visible in transit.
- The scene is one `<svg>` with a `viewBox`; pan/zoom via a root `<g>` transform. Drag/wheel on desktop, drag/pinch on touch. "Follow cart" toggle, default on; manual pan disables it until re-enabled.
- The cart moves at constant speed, dwells at each station while it "operates" (doors close, glow), then departs. The payload rides visibly on the cart and swaps to its next visual state during dwell (short scale transition). Every state visually distinct. A small label above the cart names the current state ("quartz sand", "monocrystalline ingot").

## HUD

- Top bar: title, "Stage 4 of 12: Czochralski growth", progress bar.
- Controls: play/pause, step back/forward, restart, speed 0.5x/1x/2x/4x. Keyboard: space, arrows, R, +/- zoom. Real buttons, `aria-label`s.
- Clicking a station (or "details" during dwell) opens the info panel → expands the linked backbone concept per the SKILL.md linking rule. Desktop: side panel ~360 px; phones: bottom sheet ≤60% of viewport. Opening it never steals play state.
- `prefers-reduced-motion`: discrete jumps between stations, ambient animations off.
