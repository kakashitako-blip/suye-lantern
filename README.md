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

ACES filmic tone mapping with a shadow-casting moon (2048 map on desktop, 1024 on touch
devices), a hemisphere fill that brightens one step per station lit, and a gradient sky dome
so the horizon never ends in a hard edge. Candle flames are emissive meshes plus additive
sprites rather than lights; the only moving light is the one Suye carries, and each lit
station adds one static warm point light. Device pixel ratio is capped at 2.5 on desktop
and 2 on phones.
