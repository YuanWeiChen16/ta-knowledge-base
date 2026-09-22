# Unreal Insights & Performance Tools

**Stability Tag**: `[ENGINE-VERSIONED: UE5.4]`

---

## stat Commands (The Fastest First Step)

In PIE (Play In Editor) or in-game, press `` ` `` to open the Console and type:

```
stat unit       ← most important! shows Frame/Game/Draw/GPU time
stat fps        ← FPS display
stat gpu        ← time per GPU pass (requires r.GPUStatsEnabled 1)
stat scenerendering ← Draw Calls, triangle count
stat memory     ← memory usage
stat streaming  ← texture streaming state

// Visualize Buffers
vis SceneColor          ← final color
r.VisualizeBuffer BaseColor    ← GBuffer BaseColor
r.VisualizeBuffer WorldNormal  ← GBuffer normals
r.VisualizeBuffer Roughness    ← GBuffer roughness
r.VisualizeBuffer SceneDepth   ← depth
r.VisualizeOverdraw 1          ← overdraw visualization (deeper red = more expensive)
```

---

## GPU Visualizer

`Ctrl + Shift + ,` or type `ProfileGPU` in Console

### How to Read It
1. Click **Start** → capture a frame
2. Left side: pass tree structure (sorted by GPU time)
3. The most expensive passes are usually: Shadow, Translucency, PostProcess

### Key Pass Time Reference (60fps = 16.6ms total budget)

| Pass | Healthy | Warning |
|------|---------|---------|
| Shadow Depths | < 2ms | > 4ms |
| Base Pass | < 4ms | > 8ms |
| Lighting | < 3ms | > 6ms |
| Translucency | < 1ms | > 3ms |
| Post Processing | < 2ms | > 4ms |

---

## Unreal Insights

Standalone application — provides deeper tracing than in-editor tools:

```
Launch UnrealInsights.exe (in engine installation directory)
or from Editor → Tools → Run Unreal Insights
```

### Connecting to Game
```
-trace=cpu,gpu,frame,log,memory,counters
// Add to game launch parameters, or configure in Editor Preferences
```

### Primary Use Cases
- **CPU Thread analysis**: Find bottlenecks on main thread and render thread
- **Hitch analysis**: Find causes of frame rate stutters
- **Memory tracking**: Track memory allocation timing

---

## Nanite Performance Visualization

```
r.Nanite.Visualize overview    ← Nanite overview
r.Nanite.Visualize triangles   ← actual rendered triangle density
r.Nanite.Visualize clusters    ← cluster distribution
r.Nanite.Visualize overdraw    ← overdraw (Nanite itself has very little overdraw)
```

---

## Lumen Performance Visualization

```
r.Lumen.Visualize.Overview 1          ← Lumen overview
r.Lumen.DiffuseIndirect.Visualize 1   ← GI visualization
show LumenScene                        ← show Lumen scene proxy
```

---

## Learning Resources

- 📖 [Unreal Engine Performance Guide](https://docs.unrealengine.com/5.4/en-US/performance-and-profiling-in-unreal-engine/)
- 📖 [Unreal Insights Documentation](https://docs.unrealengine.com/5.4/en-US/unreal-insights-in-unreal-engine/)
- 🎥 [GDC: Optimizing for Unreal Engine](https://gdcvault.com/) — search "Unreal optimization"
