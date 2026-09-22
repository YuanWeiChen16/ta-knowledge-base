# Unity Profiler Tools

**Stability Tag**: `[ENGINE-VERSIONED: Unity 6]`

---

## Unity Profiler

`Window → Analysis → Profiler`

### Main Modules
| Module | What to Look At |
|--------|----------------|
| **CPU Usage** | CPU time per system per frame |
| **GPU Usage** | GPU time per pass (requires Graphics Jobs settings) |
| **Memory** | Memory allocations, GC Alloc |
| **Rendering** | Draw Call count, Batch count, triangle count |

### Key Metric Locations
```
CPU Usage → expand Rendering:
  Camera.Render          ← total CPU cost of rendering
  Gfx.WaitForPresent     ← CPU waiting for GPU (indicator of GPU Bound)
  
Rendering Module:
  Batches                ← Draw Call count (lower is better)
  SetPass Calls          ← material switch count
  Triangles              ← total triangles for this frame
  Vertices               ← total vertices for this frame
```

---

## Frame Debugger

`Window → Analysis → Frame Debugger`

The fastest tool for "what is this pass doing."

### How to Use
1. Open Frame Debugger
2. Click **Enable** to pause and capture the current frame
3. Expand passes in the left tree structure
4. Click any Draw Call → view Render Target and state on the right

### Key Uses
- **Confirm SRP Batcher effectiveness**: Draw Calls batched in `SRP Batch` groups
- **Find translucent passes**: Draw Calls under `Transparent` or `Translucency`
- **Confirm Shadow passes**: Cost of the `ShadowCaster` pass

---

## Memory Profiler (Unity 6 Package)

`Window → Analysis → Memory Profiler`

Install: `Package Manager → Memory Profiler`

### Uses
- View VRAM usage
- Find the largest textures (sort by Size)
- Track memory leaks

---

## Common Debug Commands (Edit Mode)

```csharp
// Display Draw Call stats in real time (requires Stats panel)
// Game View → Stats button

// Programmatically enable/disable a rendering feature
// URP RendererFeature's SetActive()

// View shader variants for a specific object
// Project Settings → Graphics → Shader Stripping → Log
```

---

## Unity Performance Tool Reference

| Problem | Tool |
|---------|------|
| Find most expensive Draw Call | Frame Debugger |
| CPU vs GPU bound | Profiler → Gfx.WaitForPresent |
| Memory usage | Memory Profiler |
| Draw Call count | Profiler Rendering Module |
| Shader complexity | RenderDoc + Shader Inspector |
| Mobile performance | Android GPU Inspector / Xcode GPU Debugger |

---

## Hands-On Exercises (project-ideas)

1. **Performance baseline**: Take a Profiler snapshot of your scene and record Draw Calls, Triangle Count, and GPU time distribution. Set optimization targets and track improvements.
