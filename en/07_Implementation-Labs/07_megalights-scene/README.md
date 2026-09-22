# Lab 07 — MegaLights Large-Scale Dynamic Lighting Scene

**Difficulty**: ⭐⭐⭐⭐ Senior+  
**Estimated Time**: 3–5 days  
**Engine Version**: UE5.5+ (MegaLights full release)

---

## Original Publication

- **Source**: SIGGRAPH 2025 Advances in Real-Time Rendering
- **Paper Title**: *"MegaLights: Stochastic Direct Lighting in Unreal Engine 5"*
- **PDF**: https://advances.realtimerendering.com/s2025/content/MegaLights_Stochastic_Direct_Lighting_2025.pdf
- **YouTube**: https://www.youtube.com/watch?v=dmmN8_c8Tb0
- **Authors**: Krzysztof Narkowicz, Tiago Costa (Epic Games)

**Reading focus**: The Weighted Reservoir Sampling principle for light selection, why MegaLights is better suited for large numbers of lights than traditional Shadow Map approaches, how Area Light Guiding (2×2 bitmask) reduces wasted rays, and how Tile Classification reduces register pressure.

---

## Your Task

**Design and build a scene that showcases the power of MegaLights, and quantify the visual and performance differences between the traditional approach and MegaLights.**

### Scene Design Requirements
An interior scene (e.g., factory, dungeon, neon street), containing:
- **At least 50 dynamic point lights / area lights** (this would immediately break traditional approaches)
- **Mixed light types**: at least 5 each of Point Light, Spot Light, and Rect Light
- **Heavy shadow requirements**: all lights cast dynamic shadows (this is exactly MegaLights' design target)
- **Dynamic elements**: moving objects in the scene (NPCs, rotating machinery, etc.) to verify dynamic shadow correctness

---

## Three-Phase Experiment Workflow

### Phase 1: Baseline (Traditional Approach)
```
Disable MegaLights:
r.MegaLights.Enable 0

Record:
- Light count vs GPU time (start at 5 lights, add 5 at a time, up to 50)
- GPU Visualizer screenshot (Shadow Depths Pass time)
- Visual quality screenshots
```

### Phase 2: Enable MegaLights
```
Enable MegaLights:
r.MegaLights.Enable 1
r.MegaLights.SamplesPerPixel 1  ← default value

Record the same metrics and compare against Phase 1.
```

### Phase 3: MegaLights Quality Tuning
```
Test quality/performance tradeoffs at different settings:
r.MegaLights.SamplesPerPixel 1   ← console / low-spec
r.MegaLights.SamplesPerPixel 2   ← PC low settings
r.MegaLights.SamplesPerPixel 4   ← PC high settings

Denoiser settings:
r.MegaLights.Denoiser 1

Record: GPU time + noise screenshots for each setting
```

---

## TA-Perspective Technical Analysis

After completing the experiments, answer the following questions that a TA would face in real production:

### Material Setting Impact
- How is shadow quality on materials with very low Roughness (< 0.05)?
- How is shadow quality on Masked materials (foliage, railings)? Do they need a fallback to Shadow Maps?
- How does Substrate multi-layer material perform under MegaLights?

### Nanite Integration
- Nanite meshes use a proxy mesh for MegaLights ray tracing — how significant is the visual error?
- Which types of geometry are most prone to proxy mismatch?

### Performance Budget Planning
```
Fill in the following table based on your experimental data:

| Light Count | Traditional Shadow GPU Time | MegaLights GPU Time | Quality Comparison |
|-------------|----------------------------|---------------------|--------------------|
| 10          | ___ms                       | ___ms               |                    |
| 25          | ___ms                       | ___ms               |                    |
| 50          | ___ms                       | ___ms               |                    |
| 100         | ___ms                       | ___ms               |                    |
```

---

## Acceptance Criteria

| Category | Criteria |
|----------|----------|
| Scene | At least 50 dynamic shadow-casting lights |
| Scene | 3 or more light types |
| Experiment | Complete traditional vs MegaLights performance comparison data table |
| Analysis | At least 2 limitations of MegaLights identified in your scene |
| Technical | Know how to use `r.VisualizeBuffer` to inspect MegaLights intermediate buffers |

---

## Key Questions (Answer After Completing)

1. MegaLights uses Weighted Reservoir Sampling to choose which lights to sample. What is "Logarithmic perceptual weighting"? Why use a log scale instead of a linear weight?
2. In your scene with 50 lights, how many times faster is MegaLights compared to the traditional approach? Under what conditions does the traditional approach actually win?
3. The paper mentions that Directional Lights require special handling (limiting sample budget). Did you have a Directional Light in your test scene? How was its shadow quality?
4. Is MegaLights suitable for mobile devices? Infer your answer from the design assumptions stated in the paper.
5. If your Art Director says "the shadows in the 50-light scene are too noisy," what knobs can you turn? What is the performance cost of each?

---

## Key Console Commands

```
// Toggle MegaLights
r.MegaLights.Enable 0/1

// Quality settings
r.MegaLights.SamplesPerPixel 1/2/4
r.MegaLights.Denoiser 0/1

// Visualization (UE5.5+)
r.MegaLights.Visualize.LightSamples 1   ← show sample points
r.MegaLights.Visualize.Denoised 1       ← show denoised result

// Related profiling
stat GPU
ProfileGPU
r.VisualizeOverdraw 1
```

---

## Related Resources

- 📄 [SIGGRAPH 2025 MegaLights PDF](https://advances.realtimerendering.com/s2025/content/MegaLights_Stochastic_Direct_Lighting_2025.pdf)
- 🎥 [SIGGRAPH 2025 MegaLights Talk](https://www.youtube.com/watch?v=dmmN8_c8Tb0)
- 📖 [Unreal MegaLights Documentation](https://docs.unrealengine.com/5.5/en-US/megalights-in-unreal-engine/)
- 📄 [ReSTIR Paper (MegaLights comparison approach)](https://research.nvidia.com/publication/2020-07_spatiotemporal-reservoir-resampling-real-time-ray-tracing-dynamic-direct)
