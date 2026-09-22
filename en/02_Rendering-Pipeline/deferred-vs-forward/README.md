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
| Many dynamic lights | ❌ Expensive (per-light × per-object) | ✅ Cheap |
| MSAA anti-aliasing | ✅ Cheap | ❌ Expensive/incompatible |
| Transparent objects | ✅ Direct support | ❌ Needs a Forward pass supplement |
| Mobile bandwidth | ✅ Lower | ❌ G-Buffer bandwidth is high |
| Custom lighting models | ✅ Easy | ⚠️ Needs G-Buffer field support |
| Extension: Tiled/Clustered Forward | ✅ Combines advantages | — |

---

## G-Buffer Structure (Core of Deferred)

Typical G-Buffer layout (using Unreal as example):

| Render Target | Stored Content |
|---------------|---------------|
| RT0 (RGBA8) | BaseColor (RGB) + Shading Model ID (A) |
| RT1 (RGBA8) | Metallic + Specular + Roughness + AO |
| RT2 (RGB10A2) | World Normal (RGB) + Per-object data |
| RT3 (R11G11B10) | Emissive / VelocityBuffer |
| Depth | Scene Depth |

**TA use**: The `r.VisualizeBuffer` command lets you view each G-Buffer channel in real time, which is very useful for material debugging.

---

## Unity's Architecture Choices

| Pipeline | Architecture |
|----------|-------------|
| URP | Forward+ (Tiled Forward, URP 14+) |
| HDRP | Deferred (default) + Forward for translucent supplementation |

## Unreal's Architecture

- **Default**: Deferred Rendering
- **Mobile**: Forward Rendering (optional)
- **Translucency**: Always a Forward pass (added on top of Deferred)

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

1. **G-Buffer analysis**: In Unreal, use `r.VisualizeBuffer` to screenshot all G-Buffer channels one by one, and write a reference document noting what each channel stores and its purpose.
