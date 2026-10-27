# pink lottery2 — project handoff

Paste this whole file into a new chat to carry on exactly where we left off.

---

## What this is

A Thadingyut (သီတင်းကျွတ်) gift for **Suye** from **Gintoki** (the user, GitHub `kakashitako-blip`,
a crypto trader). She opens a link, plays a small 3D voxel game, and the finale draws a random
amount of pocket money in Myanmar kyat that he then pays her.

It started as a tap-a-lantern lottery page and grew into a third-person 3D village.

## Live

| Thing | URL |
|---|---|
| **The game (send this to Suye)** | https://kakashitako-blip.github.io/suye-lantern/ |
| Test / re-roll | same URL + `#reset` |
| Simple 2D fallback draw | https://kakashitako-blip.github.io/suye-lantern/lantern.html |
| GitHub repo (public) | https://github.com/kakashitako-blip/suye-lantern |
| Original Claude artifact (2D draw, superseded) | https://claude.ai/artifact/4hNLUgcK5agaYDJPyoYbYu |

GitHub Pages: **Deploy from a branch → `main` → `/ (root)`**. Already enabled.

## Repo contents

```
index.html      the 3D game, single self-contained file (~60 KB)
lantern.html    the original 2D tap-a-lantern draw; also the no-WebGL fallback
preview.jpg     og:image link preview
README.md
.gitignore      ignores shot-*.png, look-*.jpg, __test.html
pink-lottery2.md  this file
```

---

## The prize model — do not change without being asked

Weighted bands, all multiples of 10,000 Ks. Verified over 400,000 simulated draws.

| Band | Weight |
|---|---|
| 100,000 – 190,000 | 8% |
| **200,000 – 400,000** | **72%** |
| 410,000 – 700,000 | 14% |
| 710,000 – 990,000 | 3% |
| **exactly 1,000,000** | **3%** (~1 in 33) |

**Average payout ≈ 361,000 Ks.** Originally uniform 100k–1M (avg 550k); he asked to weight it
toward 2–4 lakh with a small 1M chance.

The draw is saved in `localStorage` so a refresh cannot re-roll it.
Keys: `suye-lantern-journey-v1` (3D), `thadingyut-draw-2026` (lantern.html).
These are **per-domain and per-browser** — the GitHub Pages copy and the Claude artifact keep
separate saves, and clearing site data or a different device gives a fresh draw.

## Burmese copy conventions

- Entire UI is Burmese. Names stay in Latin (`Suye`, `Gintoki`) wrapped in `<span class="nm">`
  so they render in Padauk inside the Burmese text.
- Informal couple register: **နင်** (you) / **ငါ** (I). Casual endings (`ဖြစ်မယ်`, `မရဘူးနော်`),
  never literary (`ဖြစ်မည်`).
- Amounts show three ways: Myanmar numerals (`၃၅၀,၀၀၀`), unit `ကျပ်`, and spoken form in
  **သိန်း / သောင်း** (`သုံးသိန်းငါးသောင်း`). 1,000,000 = `တစ်သန်း`.
- Fonts: `Noto Serif Myanmar` (display) + `Padauk` (body) from Google Fonts. Burmese needs
  generous `line-height` (1.9–2.05 body, ~1.5 headings) or diacritics clip.

## The route and the five stations

The lane is an explicit waypoint list (`LANE`), a loop that starts at the village edge, goes
out, and comes back to her door. It never runs through a building.

| # | key | Place | Button | The action she performs |
|---|---|---|---|---|
| 1 | `tree` | ညောင်ပင် | ချိတ်မယ် | walks up, reaches into the branches, hangs the lanterns |
| 2 | `creek` | ချောင်းနားက ကျောက်တုံး | မျှောမယ် | kneels at the bank, sets the candle boat down, pushes it off |
| 3 | `pagoda` | ဘုရား | ပူဇော်မယ် | kneels, lights seven oil lamps one by one, bows |
| 4 | `gintoki` | Gintoki | တွေ့မယ် | lantern release, the kiss, the village lights, he gives her the မုန့်ဖိုး, she opens it and picks a lantern |


The house is scenery, not a stop — four stops in total.

**Wording:** the gift is always သီတင်းကျွတ် မုန့်ဖိုး, never "present" or လက်ဆောင်.

**The draw happens last, and it is hers to trigger.** The finale pauses twice on `sTap`
gates — the glowing button shows အာဘွားပေးမယ်, then ဖွင့်မယ် — and nothing advances until she taps.
Opening the box shows `#pick`: five lanterns. Tapping one draws **all five** amounts, saves
the whole pool, and the same panel becomes the result — her number big, then every lantern
listed with hers highlighted, so she can see what she missed. The pool is stored, so a reload
shows the same five rather than re-rolling. There is no number in the sky; that was removed.

**A village dog leads the way** instead of arrow markers (which were removed). It trots ahead
along `LANE_PTS` toward the next stop, and when it arrives before she does it turns round and
waits, tail wagging. During a cutscene it parks perpendicular to the camera axis — the first
version targeted "beside her", which is exactly where the close-up camera sits, so it filled
the frame.

**Camera colliders use canopy width, not trunk width.** A sakura canopy is 3-4 blocks across
while the trunk is one, so trunk-sized colliders let trees hide her completely — it happened
at the pagoda. Trees push `r: 2.1-3.4`, and the pagoda keeps a 22-block tree exclusion.

**No modal cards.** Clicking the button plays a scripted scene — she walks, kneels, reaches,
bows — with one line of Burmese subtitle fading in at each beat. Scenes run 7-13 s.

### The cutscene engine

`cut(steps, done)` runs a list of steps; `updateCut(dt)` is called from the top of the loop.
Step builders: `sSay` `sHush` `sWait` `sDo` `sWalk` `sFace` `sArm` `sKneel` `sBow` `sCam`
`sCamOff` `sStagger`. Pose offsets are globals (`kneelAmt`, `armRaise`, `bowAmt`) that the
animation block *adds* to the idle motion, so nothing fights for the same property.

`sCam(yaw, dist, h, look)` sets `camFocus`. **Camera yaw convention:** the camera sits at
`player + (-sin(yaw)*dist, h, -cos(yaw)*dist)`. So `yaw = π` puts it behind someone facing −Z.
Getting this backwards puts a building between the camera and Suye, and the trunk-collision
pull-in then yanks the shot into a steep overhead.

Each station's `light()` is the complete, idempotent end state, so a reload restores a
half-finished village correctly. `litFlame` guards with `userData.flamed`.

---

## How index.html is built

Single file, no build step. Order inside it:

1. `<title>`, meta (incl. `og:image` → `preview.jpg`), Google Fonts
2. `<style>` — design tokens on `:root`, HUD, joystick, action button, overlay sheets
3. Markup — `#boot`, `#hud`, `#stick`, `#act`, `#intro`, `#card`, `#prize`, `#nogl`
4. Script tags: three.js **r128** + postprocessing, all from jsDelivr
5. One IIFE: boot guards → Burmese helpers → prize draw → renderer/bloom → lights → sky →
   voxel world → characters → stations → beacons → input → camera/loop → UI flow

### Libraries (exact URLs — r128 is pinned on purpose)

```html
<script src="https://cdn.jsdelivr.net/npm/three@0.128.0/build/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/shaders/CopyShader.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/shaders/LuminosityHighPassShader.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/postprocessing/EffectComposer.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/postprocessing/RenderPass.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/postprocessing/ShaderPass.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/postprocessing/UnrealBloomPass.js"></script>
```

r128 has no `CapsuleGeometry` but does have `InstancedMesh.setColorAt` and `PMREMGenerator`.
`objects/Reflector.js` and `shaders/FXAAShader.js` are also available on that CDN if needed.

### Rendering

- Voxel world from **three `InstancedMesh` batches**: `SOLID` (~6.3k, casts shadows),
  `PLANT` (~1.6k), `GLOWB` (emissive). Per-block colour via `setColorAt`.
- **Ground is NOT cubes** — one `PlaneGeometry` with a canvas texture at 8 px per block,
  `NearestFilter` magnification, speckled inside each block. Only the top face is ever
  visible, so cubes were pure waste.
- ACES filmic tone mapping, exposure **0.62**; `UnrealBloomPass` strength 0.44 / radius 0.58
  / **threshold 0.90**.
- Shadow-casting moon, 2048 map desktop / 1024 touch.
- `PMREMGenerator` on the sky dome → `scene.environment`; materials use
  `envMapIntensity: 0.25` (full strength acts like a second sun).
- Water: plane, `metalness 0.92 / roughness 0.06`, scrolling canvas normal map.
- Falling blossom + stars + crescent moon sprite.

### Controls

Left half of screen = thumbstick (pointer events), right half = drag to turn camera,
WASD + space also work. Camera raycasts `TRUNKS` (trees + buildings) and pulls in so it
never ends up inside geometry.

### Key tuning constants

```
toneMappingExposure 0.62   hemi 0.13 (base)   moon 0.30   rim 0.08
bloom 0.44 / 0.58 / 0.90   candleLight 1.25, dist 13
CAM_DIST 5.0   CAM_H 2.6   SPEED 6.0   lookAt height 1.15
dt clamp Math.min(0.1, ...)   world x[-45,45] z[-78,10]   river x[27,35] z[-58,-6]
```

---

## Working on this in a new session

### 1. Re-attach the repo

The repo is **not** attached to a fresh session. Use the `add_repo` tool:

```
add_repo(owner="kakashitako-blip", repo="suye-lantern", access="push")
```

Then clone **to an empty path** and push normally:

```bash
git clone --depth 1 https://github.com/kakashitako-blip/suye-lantern /home/user/suye-lantern-gh
# ...edit...
git push -u origin main
```

GitHub Pages redeploys in ~1–3 min. Verify with an md5 compare:

```bash
L=$(md5sum index.html | cut -c1-32)
curl -sS -o /tmp/live.html https://kakashitako-blip.github.io/suye-lantern/
[ "$L" = "$(md5sum /tmp/live.html | cut -c1-32)" ] && echo DEPLOYED
```

### 2. Environment gotchas learned the hard way

- The container's `GH_TOKEN` was **invalid**; `gh auth status` fails. Git push works anyway
  once `add_repo` mints credentials.
- **The GitHub Pages settings API is blocked through the proxy (403).** Pages config must be
  changed by the user in the browser; it is already enabled, so this only matters if it breaks.
- Playwright + Chromium are pre-installed. `require('playwright')` works from any directory.
  **Do not run `playwright install`.** Launch headless WebGL with:
  `chromium.launch({ args: ['--use-gl=swiftshader','--enable-unsafe-swiftshader'] })`
- Software rendering runs at a few fps, so `dt` clamping makes the character crawl in tests.
  That is a test artifact, not a bug. Drive the player by setting its position instead.
- Playwright `click()` times out on the lanterns/characters because they animate constantly
  ("element is not stable"). Use `page.evaluate(() => el.click())`.

### 3. Test harness (recreate these — they live in scratch, not the repo)

Serve the folder, then inject a **temporary** debug hook into a throwaway copy so a test can
drive the player. Never ship the hook.

```bash
python3 -c "
import io; s=io.open('index.html',encoding='utf-8').read()
hook='window.__dbg = { p: player, S: STATIONS, st: state };\n\n})();'
io.open('__test.html','w',encoding='utf-8').write(s.replace('\n})();\n</script>','\n'+hook+'\n</script>',1))
"
nohup python3 -m http.server 8777 >/dev/null 2>&1 &
```

Playwright run that walks all five stations and checks the finale:

```js
const { chromium } = require('playwright');
(async () => {
  const b = await chromium.launch({ args:['--use-gl=swiftshader','--enable-unsafe-swiftshader'] });
  const p = await b.newPage({ viewport:{width:390,height:844}, deviceScaleFactor:2 });
  const errs=[]; p.on('pageerror',e=>errs.push(e.message));
  await p.goto('http://localhost:8777/__test.html',{waitUntil:'networkidle'});
  await p.waitForTimeout(2200);
  await p.click('#go'); await p.waitForTimeout(1200);
  for (let i=0;i<5;i++){
    await p.evaluate(i=>{ const s=window.__dbg.S[i]; const d=[5,5,5,9.6,5][i];
      window.__dbg.p.position.set(s.pos.x+d*0.6,0,s.pos.z+d*0.8); }, i);
    await p.waitForTimeout(900);
    if(!await p.evaluate(()=>document.getElementById('act').classList.contains('show'))){
      console.log('station',i+1,'NO PROMPT'); continue; }
    await p.click('#act'); await p.waitForTimeout(i===4?3800:700);
    if(i<4){ await p.click('#cardNext'); await p.waitForTimeout(700); }
  }
  console.log(await p.evaluate(()=>({
    prize: !document.getElementById('prize').hidden,
    amount: document.getElementById('num').textContent,
    words: document.getElementById('words').textContent,
    pips: document.querySelectorAll('.pip.on').length })));
  console.log('errors:', errs.length?errs:'none');
  await b.close();
})();
```

Always `rm -f __test.html` before committing.

---

## Bugs already fixed — do not reintroduce

1. **`[hidden]` vs `display:grid`** — `#boot` and `#act` set `display:grid`, which beats the
   UA `[hidden]` rule. The start button was unclickable. There is a global
   `[hidden]{display:none!important}` near the top of the CSS. Keep it.
2. **CSS specificity** — `.sheet p` (0,1,1) beat `.amount` (0,1,0), rendering the prize number
   at body size. Prize styles are written `.sheet .amount`, `.sheet .words`, `.sheet .found`,
   `.sheet .locked`. Keep the two-class form.
3. **Camera inside trees** — fixed by the `TRUNKS` raycast pull-in. Any new tall prop must
   push to `TRUNKS`.
4. **Blown-out bloom** — raising light intensities and adding bloom together turned the frame
   pure white. Change exposure and bloom threshold together, and re-screenshot.
5. **Floating bamboo** — meshes were positioned at a fixed `y` regardless of their height.
   Always place at `height/2`.
6. **Flame halo size** — the flicker loop forced every halo to one size; it now reads
   `userData.base`.
7. **Myanmar `ငါ`** uses U+102B (tall aa), not U+102C.
8. **Initial camera yaw** must be `Math.PI` or she faces away from the village.

## Open items

- **Never tested on real mobile hardware** — only headless software rendering here. If it
  stutters on a phone, disable bloom and shadows when `MOBILE` is true (the flag already
  exists and gates map sizes and tree count).
- No way for Gintoki to see her result remotely; it lives in her browser only. Would need a
  backend (Formspree / Google Sheet / Firebase) and an account.
- Repo is public, so the prize weights are readable in the source.
