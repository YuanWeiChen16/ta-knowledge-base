# Deferred vs Forward Rendering Architecture

**Stability Tag**: `[STABLE]`

---

## Core Differences

### Forward Rendering
```
For each object:
  For each light affecting this object:
    calculate lighting → output color
```
- Lighting is calculated in each object's pass
- Multiple lights = multiple passes or a loop inside the shader

### Deferred Rendering
```
Pass 1 (G-Buffer):  store geometry info (position/normal/material properties) to multiple Render Targets
Pass 2 (Lighting):  use G-Buffer info to calculate all lights
```
- Geometry pass and lighting pass are separated
- Many lights are cheap (only affect the Lighting pass)

---

## Comparison Table

| Feature | Forward | Deferred |
|---------|---------|---------|
| Many dynamic lights | Per-object lighting can cost more; Forward+ / clustered paths reduce the set of lights per object | Separates lighting from geometry; lighting cost still depends on resolution, light count, and materials |
| MSAA anti-aliasing | Often a more direct path; support still depends on engine/platform | Extra G-Buffer storage and bandwidth can be costly; support depends on engine/platform |
| Transparent objects | Can use forward shading; cost depends on overdraw and lighting | Usually uses a separate forward/translucency pass; exact behavior depends on engine |
| Mobile bandwidth | May avoid extra G-Buffer bandwidth | G-Buffer increases bandwidth and memory use; device and resolution matter |
| Custom lighting models | Depends on shader path and engine features | May be constrained by G-Buffer encoding and available fields |
| Extension: Tiled/Clustered Forward | ✅ Combines advantages | — |

---

## G-Buffer Structure (Core of Deferred)

G-Buffers typically store surface properties and depth needed by later lighting passes. Unreal's render targets, formats, and channel packing vary by UE version, platform, rendering settings, and Substrate format. Inspect the target version with Buffer Visualization or RenderDoc instead of relying on a fixed channel table.

| Common data | Purpose |
|-------------|---------|
| Base Color, Normal, Roughness, Metallic, and other material properties | Reconstruct surface response during deferred lighting |
| Shading Model / material flags | Select the material shading path |
| Depth (and velocity where provided by the pipeline) | Reconstruct position, depth testing, or post-processing |

**TA use**: The `r.VisualizeBuffer` command lets you view each G-Buffer channel in real time, which is very useful for material debugging.

---

## Unity's Architecture Choices

| Pipeline | Architecture |
|----------|-------------|
| URP | Forward, Forward+, or Deferred; depends on Unity/URP version and Renderer settings |
| HDRP | Forward or Deferred can be selected in the HDRP Asset; support and cost depend on settings |

## Unreal's Architecture

- **Default**: Deferred Rendering
- **Mobile**: Mobile Forward or Mobile Deferred is available; defaults and support depend on target platform/version settings
- **Translucency**: Commonly uses a separate forward-shading pass; depends on material Lighting Mode and engine settings

---

## Practical TA Impact

**Transparent objects in a Deferred pipeline**:
- Cannot receive Deferred lighting (because they don't write to G-Buffer)
- Usually supplemented with Capsule Shadow or Forward lighting
- High cost, use carefully

**Custom Shading Model (Deferred)**:
- Requires reserving a G-Buffer field
- UE5: The Substrate material system redesigns this mechanism

---

## Hands-On Exercises (project-ideas)

1. **G-Buffer analysis**: Use Buffer Visualization or RenderDoc in the target Unreal version to inspect the actual G-Buffer; record the version, platform, formats, and purpose of the main data.
