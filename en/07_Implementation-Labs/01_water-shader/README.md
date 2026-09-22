# Lab 01 — Procedural Water Shader

**Difficulty**: ⭐⭐ Mid  
**Estimated Time**: 4–8 hours  
**Engine Version**: UE5.4+

---

## Original Publication

- **Source**: 80.lv — *"Crafting a Stylized Water Shader in UE5"* (2025)
- **URL**: https://80.lv/articles/how-to-build-stylized-water-shader-design-implementation-for-nimue
- **Authors**: Kolja Bopp, Leanna Geideck, Stephan zu Münster (Hamburg University of Applied Sciences)

**Reading focus**: How they used Single Layer Water, triple tiling to break repetition, and Vertex Interpolator for optimization.

---

## Your Task

**Recreate a procedural stylized water surface in UE5 without any third-party plugins.**

### Required Features
1. **Wave Normals**: At least 2 layers of panning normal maps, moving in opposite directions at different speeds
2. **Tiling Breakup**: At least 1 method to reduce texture tiling repetition (noise-distorted UVs, distance mask, or macro variation)
3. **Water Depth Color**: Scatter + Absorption that changes color based on water depth (SceneDepth - PixelDepth)
4. **Edge Foam**: Use depth difference to generate a foam mask at object intersection edges
5. **WPO Wave Motion**: World Position Offset sinusoidal displacement along the Z axis

### Bonus Features
- Interactive ripples (Render Target records character walking)
- Camera distance LOD (disable WPO and some Normals at distance)
- Caustic projection (ColorScaleBehindWater input)

---

## Acceptance Criteria

| Category | Criteria |
|----------|----------|
| Visual | No obvious texture tiling visible in a still frame |
| Visual | Clear color difference between shallow and deep water |
| Visual | Foam line visible at object intersection edges |
| Performance | Water pass < 1.5ms in GPU Visualizer (1080p) |
| Technical | Normal map set to Linear (not sRGB) |
| Technical | WPO sine calculation moved to Vertex Shader (Vertex Interpolator node) |

---

## Key Questions (Answer After Completing)

1. What is the fundamental performance difference between Single Layer Water and a regular Translucent material? Why choose the former?
2. What technique did you use to break up texture tiling? What is the cost of that technique?
3. In UE5.4, there is a known bug with Single Layer Water + Lumen Reflections — did you encounter it? How did you handle it?
4. What are the benefits of putting the WPO sine wave in the Vertex Shader instead of the Pixel Shader? What are the trade-offs?

---

## Required UE5 Nodes / Features

```
Single Layer Water material Domain
SceneDepth, PixelDepth        ← calculate water depth
ColorScaleBehindWater         ← caustics, water body color
Vertex Interpolator           ← move calculations to VS
DepthFade                     ← edge fade
World Position Offset         ← wave displacement
Noise (Voronoi)               ← caustic texture generation
```

---

## Reference Screenshot Comparison

After completing, take screenshots and compare against the original article:
- Front-facing angle
- 30-degree oblique view
- Close-up of object waterline edge
- GPU Visualizer screenshot (including water pass time)
