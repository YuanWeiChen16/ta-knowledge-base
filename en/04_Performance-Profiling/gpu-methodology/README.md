# GPU Performance Diagnosis Methodology

**Stability Tag**: `[STABLE]`

---

## CPU Bound vs GPU Bound

**The most important first step**: Confirm where your bottleneck is.

```
CPU Bound: CPU finishes processing a frame; GPU is waiting for CPU
GPU Bound: GPU finishes rendering a frame; CPU is waiting for GPU

How to determine:
  Unity  → Profiler → CPU Usage → check Gfx.WaitForPresent time
  Unreal → stat unit → check which of Frame/Game/Draw/GPU is highest
```

**Common CPU Bound causes**:
- Too many Draw Calls (CPU overhead for setting state)
- Large amounts of C#/Blueprint Tick
- Physics simulation
- AI calculations

**Common GPU Bound causes**:
- High overdraw (translucency)
- Complex shaders (too many texture samples, complex math)
- High-resolution render targets
- Too many lights

---

## Diagnosis Flow

```
Step 1: Open profiler, capture a problematic frame
  ↓
Step 2: CPU or GPU bound?
  ↓ GPU Bound
Step 3: Which pass is most expensive?
  (Shadow? Lighting? Translucency? Post Process?)
  ↓
Step 4: In that pass, which Draw Call is most expensive?
  ↓
Step 5: Is it shader complexity? Texture bandwidth? Overdraw? Geometry density?
  ↓
Step 6: Change only one thing, measure again
```

---

## GPU Bottleneck Types

### Fillrate Limited
**Symptom**: Reducing resolution → noticeable performance improvement  
**Cause**: Too many pixels to process per frame (high resolution + high overdraw)  
**Fix**: Lower render target resolution, reduce translucent overdraw, use upscaling (DLSS/FSR)

### Bandwidth Limited
**Symptom**: Reducing texture size → performance improvement  
**Cause**: GPU reads/writes memory too frequently  
**Fix**: Texture compression (BC7/ETC2), correct Mip Map settings, reduce number of render targets

### Compute Limited
**Symptom**: Simplifying shader → performance improvement  
**Cause**: Shader instructions are too complex  
**Fix**: Simplify shader math, pre-compute (bake) values that can be pre-computed, use half instead of float (mobile)

### Vertex Limited
**Symptom**: Reducing polygon count → performance improvement  
**Cause**: Too many geometry vertices (non-Nanite scenes)  
**Fix**: LOD system, reduce mesh complexity, Nanite (UE5)

---

## Common Performance Metrics

| Metric | PC Target (60fps) | Mobile Target (30fps) |
|--------|------------------|----------------------|
| Frame Budget | 16.6ms | 33.3ms |
| Draw Calls | < 2000 | < 200 |
| Triangle Count | < 3M/frame | < 300K/frame |
| Shadow Map Passes | < 4ms | < 3ms |
| Translucency | < 2ms | < 1ms |
| Post Processing | < 3ms | < 2ms |

> These are reference values; actual targets vary by game type and hardware spec.

---

## Learning Resources

- 📖 [NVIDIA GPU Performance Guide](https://developer.nvidia.com/blog/the-peak-performance-analysis-method-for-optimizing-any-gpu-workload/)
- 📖 [ARM Mali GPU Best Practices](https://developer.arm.com/documentation/102444/latest/)
- 🎥 [GDC: Performance Optimization talks](https://gdcvault.com/) — search "GPU optimization"
