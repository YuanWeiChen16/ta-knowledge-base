# Lab 05 — Niagara Ribbon Trail & Flipbook Splash

**Difficulty**: ⭐⭐ Mid  
**Estimated Time**: 4–8 hours  
**Engine Version**: UE5.2+

---

## Original Publication

- **Source**: 80.lv — *"Wukong's Spinning Staff Water VFX Powered By UE5's Niagara & LiquiGen"* (2025)
- **URL**: https://80.lv/articles/wukong-s-spinning-staff-water-vfx-powered-by-ue5-s-niagara-liquigen
- **Author**: Vitaliy Lupul (Senior VFX Artist)

**Reading focus**: Ribbon UV Tiling settings for a blurred trail feel, secondary ribbon using a masked shader + dithering to prevent overdraw, and an 8×8 flipbook + Motion Vector texture to smooth low-framerate animation.

---

## Your Task

**Create a weapon swing trail VFX with a primary trail, a secondary trail, and particle splashes, attached to a moving object in a UE5 scene.**

### Required Features
1. **Primary Ribbon Trail**: Follows a weapon bone, uses Ribbon Renderer, UV Tiling set for a visual blur effect
2. **Secondary Ribbon (Outline)**: A second Ribbon using a masked shader with dithering to prevent hard-edge overdraw
3. **Flipbook Splash**: Water or energy splashes using a flipbook sprite sheet (at least 4×4), with Motion Vector Texture for sub-frame interpolation
4. **Ribbon Material**: Custom ribbon material containing UV scroll + alpha erosion

### Bonus Features
- Ribbon width varies with speed (faster swing = wider trail)
- Splash particle count dynamically scales with swing speed
- Trail color shifts along the bone position (gradient)

---

## Ribbon Renderer Key Settings

```
Niagara → Ribbon Renderer:
  UV Distribution: Stretch (extends UV along the ribbon for panning effect)
  UV Tiling Distance: controls tile spacing (smaller value = denser tiles = more blur feel)
  Facing Mode: Screen (always faces camera) or Custom (along bone normal)
  
  Tessellation: increases ribbon curve smoothness (performance cost++)
  Segments Per Ribbon: controls subdivision count
```

---

## Flipbook Motion Vector Workflow

```
1. Create flipbook animation (JangaFX EmberGen / Houdini / hand-drawn)
2. Export as 8x8 sprite sheet (64 frames)
3. Also export a Motion Vector texture (records pixel motion direction per frame)
4. In the Niagara material:
   - Flipbook Player node (auto-calculates SubUV coordinates)
   - Sample Motion Vector, lerp between current frame and next frame
   Result: 64-frame flipbook visually approaches 128-frame smoothness
```

---

## Dithering to Prevent Overdraw

```hlsl
// Material Graph:
// 1. Calculate Alpha
float alpha = tex2D(SpriteTex, uv).a;

// 2. Dither matrix (4x4 Bayer Matrix)
// Use a Custom node or the Dither Temporal AA node
// Turns semi-transparent edges into a dot pattern instead of true alpha blend

// 3. Clip (Masked blend mode)
clip(alpha - ditherThreshold);

// Result: visually appears semi-transparent, but technically Masked (no overdraw)
// Trade-off: visible dithering grain up close (nearly invisible on fast-moving VFX)
```

---

## Acceptance Criteria

| Category | Criteria |
|----------|----------|
| Visual | Primary trail is smooth, no visible segment seams |
| Visual | Flipbook has Motion Vector interpolation, no visible frame popping |
| Visual | Two Ribbons have clear layering (primary trail + secondary outline) |
| Technical | Secondary Ribbon material uses Masked + Dithering, not Translucent |
| Technical | Niagara Stat shows reasonable overdraw layer count |
| Performance | Entire VFX < 0.5ms (per single trigger) |

---

## Key Questions (Answer After Completing)

1. What do `UV Tiling Distance` and `UV Distribution` each control on the Ribbon? How do you combine them to achieve a blurred, flowing feel?
2. Where does the main performance difference between Dithering + Masked and straight Translucent come from? Would the gap be larger or smaller on mobile?
3. How does Motion Vector Flipbook "interpolate" between two frames? What are its limitations (in what situations does interpolation break down)?
4. What performance issues arise from setting Ribbon Tessellation too high? How do you decide on the right value?
