# PBR — Physically Based Rendering Theory

**Stability Tag**: `[STABLE]`

---

## Why We Need PBR

Traditional Phong/Blinn-Phong lighting: artists tweak parameters to make things "look good," but behavior is inconsistent across different lighting environments.

PBR (Physically Based Rendering): based on physical principles, **produces consistent and correct behavior in any lighting environment**.

---

## Core Model: Microfacet Theory

At the microscopic scale, a surface is composed of countless tiny perfect mirrors. The macroscopic Roughness describes how randomly distributed these microfacets are.

```
Low Roughness (smooth) → microfacets aligned → sharp reflections
High Roughness (rough) → microfacets scattered → blurry diffuse reflection
```

### Key Functions

**PBR BRDF** = D × F × G / (4 × NdotL × NdotV)

| Function | Name | Description |
|----------|------|-------------|
| **D** | Distribution Function | Normal distribution, determines specular shape (GGX most common) |
| **F** | Fresnel Term | Grazing angle reflection increase (Schlick approximation) |
| **G** | Geometry Function | Microfacet self-shadowing (Smith GGX) |

---

## Metallic-Roughness Workflow (Industry Standard)

### Metallic (0-1)
- **0 = Non-metal (Dielectric)**: Albedo has color, specular is nearly white
- **1 = Metal (Conductor)**: Albedo becomes the specular color, almost no diffuse reflection
- **In-between values**: Used for transition areas (rust, paint peeling)

### Roughness (0-1)
- **0 = Perfectly smooth**: Mirror reflection
- **1 = Completely rough**: Full diffuse reflection
- **Perceptually linear**: Artists generally feel that 0.5 is visually "medium roughness"

### Albedo (Base Color)
- Non-metals: reflectance 50-240 sRGB (avoid pure black or pure white)
- Metals: specular color (iron = gray, copper = orange, gold = yellow)

---

## Fresnel Effect

At grazing angles, reflectance increases for all surfaces. This is a physical phenomenon, not a stylistic choice.

```hlsl
// Schlick Fresnel approximation
float3 F_Schlick(float3 F0, float VdotH) {
    return F0 + (1.0 - F0) * pow(1.0 - VdotH, 5.0);
}
// F0 = base reflectance: ~0.04 for non-metals, use Albedo for metals
```

---

## IBL (Image Based Lighting)

Ambient lighting doesn't come from a single directional light, but from the entire environment.

- **Diffuse IBL**: Irradiance Map (pre-computed ambient diffuse from environment, using SH or low-mip cubemap)
- **Specular IBL**: Radiance Map (different mips = different Roughness reflections) + BRDF LUT
- **Engine implementation**: Unity Reflection Probe / Unreal Sky Light

---

## Common PBR Mistakes

| Mistake | Symptom | Cause |
|---------|---------|-------|
| Albedo too dark or too bright | Material looks unrealistic under any lighting | Albedo values that violate energy conservation |
| Using gradient grayscale for Metallic | Edge areas look dirty | Metallic should be 0 or 1; use mask for transitions |
| Normal map in Gamma space | Weird lighting, incorrect normal calculations | Normal maps must be set to Linear |
| Roughness contrast too strong | Specular distribution is unnatural | Roughness perceptual non-linearity requires perceptual roughness = roughness² |

---

## Learning Resources

- 📖 [Substance PBR Guide](https://substance3d.adobe.com/tutorials/courses/the-pbr-guide-part-1) — produced by Adobe/Allegorithmic, industry standard introduction
- 📖 [Filament PBR Documentation](https://google.github.io/filament/Filament.html) — complete PBR math derivation from Google's Filament engine, free
- 🎥 [GDC: Physically Based Shading in Theory and Practice](https://gdcvault.com/play/1024478) — free version of SIGGRAPH course
- 📖 [Real-Time Rendering Ch.9](https://www.realtimerendering.com/) — complete PBR mathematics

---

## Hands-On Exercises (project-ideas)

1. **Material sphere comparison display**: Build a scene showing the same sphere in a 5×5 Roughness/Metallic matrix (0.0, 0.25, 0.5, 0.75, 1.0). Do it in both Unity and Unreal, and compare the visual PBR differences between the two engines.
2. **Fresnel visualization**: Write a debug shader in pure HLSL that only displays the Fresnel term, and confirm that the reflection increase at grazing angles behaves correctly. Constraint: must be able to adjust the F0 value.
