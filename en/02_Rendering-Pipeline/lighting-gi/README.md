# Lighting & Global Illumination (GI)

**Stability Tag**: Concepts `[STABLE]`, engine implementation `[ENGINE-VERSIONED: UE5.4 / Unity 6]`

---

## Light Types

| Type | Description | Performance |
|------|-------------|------------|
| Directional Light | Parallel light from infinite distance (sun) | Low |
| Point Light | Spherical area light | Medium |
| Spot Light | Cone-shaped area light | Medium |
| Rect Light (Area) | Rectangular area light source | High (real-time) |
| Sky Light / IBL | Ambient lighting from environment | Low (pre-computed) |

---

## Global Illumination (GI) Solution Comparison

### Static Baked GI (Cheapest, cannot move)
- Scene and lights are all static
- Pre-compute Lightmaps, baked to textures
- **Unity**: Progressive Lightmapper
- **Unreal**: CPU/GPU Lightmass
- **Use case**: Architecture, scene backgrounds, static environments

### Dynamic GI Solutions

| Solution | Engine | Principle | Use Case |
|---------|--------|-----------|---------|
| **Lumen** | Unreal 5 | Software ray tracing + SDF | PC/console dynamic scenes |
| **RTXGI** | Unreal (RTX) | Hardware ray-traced probes | RTX GPU cards |
| **APV** (Adaptive Probe Volume) | Unity 6 | Spatial probe interpolation | Dynamic object GI |
| **SSGI** | Both | Screen space GI | Cheap but screen-space limited |
| **LPV** (Light Propagation Volume) | Legacy | Voxel propagation | Nearly deprecated |

---

## Unreal Lumen Deep Dive

**How Lumen works**:
1. Traces rays using SDF (Signed Distance Fields)
2. Supplements high-frequency detail in Screen Space
3. Uses Radiance Cache to accelerate long-range GI

**Key Lumen settings**:
```
Post Process Volume:
  Lumen Global Illumination → Scene Detail  (detail vs performance)
  Lumen Reflections → Quality              (reflection quality)
  
r.Lumen.DiffuseIndirect.Allow 1            // enable Lumen GI
r.Lumen.Reflections.Allow 1               // enable Lumen reflections
```

**Lumen limitations (TA must know)**:
- Transparent objects cannot act as GI receivers/emitters
- Nanite mesh WPO has a delay on Lumen SDF
- Very low Roughness materials (< 0.1) have poor reflection quality; supplement with Screen Space Reflections

---

## Unity APV (Adaptive Probe Volume)

Replaces the old Light Probe Group, automatically fills the scene.

```
Hierarchy → right-click → Light → Probe Volume (Auto)
Window → Rendering → Lighting → Probe Volumes Tab
```

**Key settings**:
- Subdivision Level controls probe density
- Max Subdivision Distance prevents probes from penetrating walls
- Dilation fills invalid probes (those blocked by geometry)

---

## Reflection Systems

| Solution | Accuracy | Cost | Dynamic Support |
|---------|----------|------|----------------|
| Cubemap / IBL | Low (static) | Very low | ❌ |
| Reflection Probe | Medium | Low | Partial |
| Screen Space Reflections | High (within screen) | Medium | ✅ |
| Lumen Reflections | High | High | ✅ |
| Ray Traced Reflections | Very high | Very high | ✅ |

**TA strategy**: Combine — IBL as distant backdrop, SSR for near high-quality, Lumen for dynamic objects.

---

## Learning Resources

- 🎥 [GDC: Lumen in UE5](https://gdcvault.com/play/1027022) — in-depth Epic official presentation
- 📖 [Unity APV Documentation](https://docs.unity3d.com/6000.0/Documentation/Manual/probevolumes.html)
- 📖 [SIGGRAPH: Advances in Real-Time Rendering](https://advances.realtimerendering.com/) — annual GI technology advances

---

## Hands-On Exercises (project-ideas)

1. **GI solution performance comparison**: In the same indoor scene, test Baked Lightmap / Lumen / APV frame time and visual quality respectively, and create a comparison screenshot and data table.
2. **Dynamic lighting scene**: Design a day-night cycle scene using only dynamic lights (no baking), and tune Lumen settings to achieve acceptable quality under a 60fps target. Document the final setting values and reasoning.
