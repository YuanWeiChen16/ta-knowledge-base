# Mobile Performance Optimization

**Stability Tag**: Concepts `[STABLE]`, platform details `[VOLATILE]`

---

## The Fundamental Difference of Tile-Based GPUs (Required Reading)

Mobile GPUs (Qualcomm Adreno, ARM Mali, Apple GPU) are all **TBDR (Tile-Based Deferred Rendering)** architecture — fundamentally different from desktop GPUs.

```
Screen divided into Tiles (typically 16×16 or 32×32 pixels)
All geometry for each Tile collected first → computed in on-chip cache → then written to memory
```

**Core impact on TAs**:

| Operation | Desktop Cost | Mobile Cost |
|-----------|-------------|------------|
| Alpha Blend | Medium | Low (within tile cache) |
| Depth Test | Medium | Low (within tile cache) |
| **Read framebuffer (GrabPass)** | Medium | **Very high** (forces tile flush) |
| Many Draw Calls | High | High (even more severe) |
| High Bandwidth textures | Medium | **Very high** (battery killer) |

---

## Mobile Performance Golden Rules

### 1. Never Use GrabPass / SceneColor (Unless Necessary)
Forces tile data to flush to system memory and read it back.

**Alternatives**:
- Depth Fade instead of refraction edge blending
- Distortion Pass instead of GrabPass refraction
- Render Feature using copy color pass (acceptable in some cases)

### 2. Texture Bandwidth Is the #1 Performance Killer
- Compress every texture (Android: ETC2 / ASTC, iOS: ASTC)
- Always enable Mip Maps (no Mips = forces full resolution reads)
- Texture Atlases reduce Draw Calls and texture switches

### 3. Control Overdraw
Mobile pixel fill rate is far lower than desktop:
- Translucent objects < 3 layers stacked
- Limit maximum particle size (usually 1/4 of screen)
- Early-Z Rejection: ensure opaque objects can early-reject

### 4. Draw Call Budget Is Stricter
2000 Draw Calls is normal on desktop; mobile target < 200:
- Static Batching
- GPU Instancing
- Atlas Texture + merge materials

---

## Common Mobile Performance Pitfalls

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Missing Depth Prepass | High overdraw | Ensure opaque objects have depth prepass |
| Uncompressed textures | Memory explosion, bandwidth overflow | Force all textures to compressed format |
| Too many shader variants | Long load times, high memory | Shader stripping, reduce keywords |
| Full-screen post-processing stacked | Frame time spikes | Simplify post-processing for mobile version |
| Real-time Light > 1 | Draw Call multiplication | Mobile usually uses only 1 directional light |

---

## Mobile Profiling Tools

| Tool | Platform | Use |
|------|---------|-----|
| **Android GPU Inspector** | Android | Deep GPU analysis (Adreno/Mali) |
| **Snapdragon Profiler** | Android (Qualcomm) | Adreno detailed analysis |
| **Xcode GPU Frame Capture** | iOS | Complete Apple GPU analysis |
| **Mali Graphics Debugger** | Android (ARM) | Mali GPU analysis |
| Unity Remote + Profiler | iOS/Android | Basic Unity Profiler remote |

---

## Learning Resources

- 📖 [ARM Mali GPU Best Practices](https://developer.arm.com/documentation/102444/latest/)
- 📖 [Qualcomm Adreno Optimization Guide](https://developer.qualcomm.com/software/adreno-gpu-sdk/gpu)
- 📖 [Apple Metal Best Practices](https://developer.apple.com/documentation/metal/resource_fundamentals/reducing_the_memory_footprint_of_metal_apps)
- 📖 [Unity Mobile Optimization](https://docs.unity3d.com/Manual/MobileOptimizationGraphicsMethods.html)

---

## Hands-On Exercises (project-ideas)

1. **GrabPass cost test**: For the same water surface effect, use GrabPass and Depth Fade approximation, and measure the bandwidth difference on an actual mobile device (or Android GPU Inspector).
2. **Draw Call optimization challenge**: Optimize a scene from > 500 Draw Calls down to < 150 without reducing visual quality by more than 10%. Document each optimization step and Draw Calls saved.
