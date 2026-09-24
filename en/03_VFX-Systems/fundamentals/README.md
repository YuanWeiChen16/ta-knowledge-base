# VFX Fundamentals

**Stability Tag**: `[STABLE]`

---

## VFX Design Principles

### 1. Readability First
VFX must communicate "what's happening" to the player within 1/30th of a second.  
- Shape > Detail (silhouette must be clear)
- Color contrast > Realism (red = danger, blue = friendly in combat)
- Motion curves > Static fidelity (the easing on an explosion matters more than texture resolution)

### 2. Define Performance Budget Before Creating
Set CPU/GPU time, particle-count, and overdraw budgets for the target device, frame rate, and expected simultaneous effects. Treat these as starting examples and measure on the target platform:
- **Foreground / Hero VFX** (skills, explosions): for example, start by measuring a single effect against 2ms and 500 particles
- **Background / Ambient VFX** (environmental smoke, fire): for example, start with 0.5ms and 100 particles per instance
- **UI VFX**: also consumes GPU time and fill rate; measure for the resolution, blend mode, and screen coverage

### 3. Visual Impact = Shape + Motion + Timing
- **Shape**: clear silhouette, has directionality
- **Motion**: non-uniform speed, use easing curves (ease-in-fast-out or slow-in-fast-out)
- **Timing**: an explosion should have a "compression" frame before expanding (hit-stop principle)

---

## VFX Key Performance Metrics

| Metric | Impact | How to View |
|--------|--------|------------|
| **Overdraw** | Semi-transparent particles stacked = heavy pixel overdraw | RenderDoc / GPU Visualizer |
| **Particle Count** | Too many particles = CPU/GPU simulation cost | Profiler particle stats |
| **Texture Bandwidth** | Large particles + high-res textures | Bandwidth monitor |
| **Draw Calls** | Batch efficiency of each VFX system | Frame Debugger |

### Overdraw Is the Mobile VFX Killer
Every transparent particle pixel must be blended:
- 1 large particle covering 1920×1080 = 2 million blend operations
- 10 such particles stacked = 20 million blend operations/frame

**Solutions**:
1. Reduce particles' screen-space coverage and overlap; adding particles does not necessarily reduce overdraw
2. Use Depth Fade to improve visual intersections with scene geometry; it does not automatically reduce overdraw
3. Limit maximum particle size (usually < 1/4 screen area on mobile)

---

## Flipbook Animation Textures

**Principle**: Store animation frames in a grid on one texture; switch UVs in the shader to play the animation.

```
8×8 flipbook = 64 animation frames
Texture size: 512×512 → 64×64 pixels per frame
```

**Production tools**:
- Houdini → the most powerful flipbook simulation output
- Substance Designer → procedural flipbook textures
- Unity VFX Graph has a built-in Flipbook Player node

**Motion Vector Flipbook**: Paired with a motion vector texture for sub-frame interpolation, making low frame-rate flipbooks look smoother.

---

## VAT (Vertex Animation Texture)

Bakes vertex animation (cloth, fluid, destruction) into textures, read back in the vertex shader:

**Advantages**: Zero CPU simulation cost, GPU reads the texture directly to play animation  
**Use cases**: Grass swaying, flag fluttering, destruction animation, fluid

**Production workflow**: Houdini → VAT SOP → output position texture + normal texture → engine shader reads it

---

## Learning Resources

- 🎥 [Klemen Lozar — VFX Tutorials](https://www.youtube.com/@KlemenLozar) — Niagara/VFX Graph in action
- 🎥 [Tech Art Aid YouTube](https://www.youtube.com/@TechArtAid) — TA-oriented VFX techniques
- 📖 [Realtimecolors VFX](https://realtimecolors.com/) — color in VFX applications
- 🎥 [GDC: Technical Artist VFX talks](https://gdcvault.com/) — search "VFX Technical Artist"

---

## Hands-On Exercises (project-ideas)

1. **Explosion effect performance analysis**: Create an explosion VFX, measure overdraw and GPU time with a profiler. Iteratively optimize until < 1ms on the mobile target. Document before/after data at each optimization step.
2. **VAT cloth animation**: Simulate a flag fluttering in Houdini, export VAT, and play it back in Unity/Unreal using a vertex shader. Constraint: no Skinned Mesh, driven purely by texture.
