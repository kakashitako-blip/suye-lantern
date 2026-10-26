# Suye ရဲ့ မီးပုံးခရီး

A small third-person 3D Thadingyut game. The village is dark; Suye walks it with one
candle, lighting five places — the house, the banyan, the rock by the creek, the pagoda,
and the last lantern — and a hidden gift waits at each. The world brightens as she plays.
The prize draw is the finale.

Live: https://kakashitako-blip.github.io/suye-lantern/

## Files

- `index.html` — the game. Single file, three.js r128 from cdnjs, no build step.
- `lantern.html` — the original tap-a-lantern draw. Also the fallback for devices without WebGL.
- `preview.jpg` — link preview image.

## How it works

- **Controls**: drag anywhere on the left half for the thumbstick, drag the right half to
  turn the camera, tap the glowing button to act. WASD and space also work on a laptop.
- **Prize bands**: 8% at 1–1.9 lakh, 72% at 2–4 lakh, 14% at 4.1–7 lakh, 3% at 7.1–9.9 lakh,
  3% at exactly 1,000,000 Ks. Average ~361,000 Ks.
- **Progress and prize** are saved in `localStorage`, so closing the tab keeps her place and
  a refresh cannot re-roll the amount.
- Open with `#reset` on the URL to wipe the save and start the night over.

## Rendering

Voxel world built from instanced cubes — ground, props, plants and emissive blocks are four
`InstancedMesh` draw calls with per-block colour, so the whole village is a handful of
batches. On top of that: ACES filmic tone mapping, a shadow-casting moon (2048 map on
desktop, 1024 on touch), `UnrealBloomPass` for the lantern glow, a PMREM sky environment
feeding the reflective water, a scrolling normal map for ripples, and falling blossom.

The camera raycasts against tree trunks and buildings and pulls in when one would block the
view of Suye.

This is the art direction of a shader pack, not path tracing — a phone browser cannot do
the latter.
