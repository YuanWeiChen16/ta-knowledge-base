# Lab 06 — Substrate Material Layering System

**Difficulty**: ⭐⭐⭐ Senior  
**Estimated Time**: 1–2 days  
**Engine Version**: UE5.3+ (Substrate must be enabled)

---

## Original Publication

- **Source**: SIGGRAPH 2023 Advances in Real-Time Rendering
- **Paper Title**: *"Substrate: A New Material Shading Framework for Unreal Engine"*
- **PDF**: https://advances.realtimerendering.com/s2023/2023%20Siggraph%20-%20Substrate.pdf
- **Authors**: Charles de Rousiers et al. (Epic Games)

**Reading focus**: The Slab concept (matter building block), the semantic difference between Horizontal Mixing vs Vertical Layering, energy-conserving layering, GBuffer packing architecture, and Roughness Tracking (upper layer roughness influences perceived roughness of the layer below).

---

## Your Task

**Use Substrate to create a "snow-covered rock" Master Material that demonstrates physically correct layering with Vertical Layering.**

### Required Features
1. **Base Slab**: Rock (high Roughness, no metalness, stone Normal)
2. **Top Slab**: Snow (high Roughness, white Albedo, distinct Normal)
3. **Vertical Layering**: Use the world normal Y component as a mask — snow on horizontal surfaces, rock exposed on vertical surfaces
4. **Roughness Tracking**: Confirm it is enabled and that snow layer thickness influences the perceived roughness of the rock layer beneath
5. **Thickness Parameter**: Expose a Snow Coverage (0–1) parameter to control how much snow covers the surface

### Bonus Features
- Wetness parameter: simulate wet snow (lower Roughness, F0 adjustment)
- Third layer: thin ice layer (translucent Slab) over the snow
- Edge wear mask (driven by a Curvature map)

---

## Substrate Core Nodes

```
Substrate Slab BSDF
  ├── DiffuseAlbedo    ← diffuse color
  ├── F0               ← base reflectance (non-metals ~0.04)
  ├── Roughness        ← roughness
  ├── Normal           ← normal
  └── Thickness        ← used for SSS / transmission

Substrate Vertical Layering
  ├── Top (Slab)       ← top layer material (snow)
  ├── Base (Slab)      ← bottom layer material (rock)
  └── Thickness        ← top layer thickness (affects Roughness Tracking)

Substrate Horizontal Mixing
  ├── A (Slab)
  ├── B (Slab)
  └── Mix (0-1)        ← lerp between two materials
```

---

## Vertical Layering vs Horizontal Mixing

| Concept | Vertical Layering | Horizontal Mixing |
|---------|-------------------|-------------------|
| Physical analogy | Coating (top layer covers the bottom) | Blending (A and B side by side) |
| Energy conservation | ✅ Top layer absorbs light, bottom receives the remainder | ✅ Blended proportionally |
| Roughness Tracking | ✅ (top roughness affects bottom layer) | ❌ |
| Applicable scenarios | Snow/ice/paint/dirt coverage | Material blending (grass and mud) |

---

## World Normal Mask (Snow Distribution)

```
// Snow only appears on near-horizontal surfaces
float3 worldNormal = normalize(TransformTangentVectorToWorld(
    Parameters.TangentToWorld, Normal
));
float snowMask = saturate(worldNormal.z);  // Z+ = facing up
snowMask = pow(snowMask, SnowSharpness);   // controls edge hardness
snowMask *= SnowCoverage;                  // global coverage amount
```

---

## Acceptance Criteria

| Category | Criteria |
|----------|----------|
| Visual | Snow only appears on horizontal surfaces; rock is exposed on vertical faces |
| Visual | Natural transition at the snow/rock boundary |
| Visual | Transition is smooth when SnowCoverage goes from 0 to 1 |
| Technical | Uses Substrate Vertical Layering, not manual lerp |
| Technical | Roughness Tracking enabled (confirmed in Settings) |
| Technical | Material produces correct GI in a Lumen scene (snow reflects more light) |

---

## Key Questions (Answer After Completing)

1. What exactly does Roughness Tracking do in Substrate Vertical Layering? Why does top layer thickness influence the perceived roughness of the layer below?
2. Could the same "snow-covered rock" effect be achieved with a standard material + manual lerp between two parameter sets? What's the difference?
3. How is the Substrate material's GBuffer packed? Why doesn't a complex material infinitely increase the GBuffer size?
4. Substrate is still Experimental as of UE5.4. How would you evaluate the risk of using it in a production environment?

---

## Related Resources

- 📄 [SIGGRAPH 2023 Substrate PDF](https://advances.realtimerendering.com/s2023/2023%20Siggraph%20-%20Substrate.pdf)
- 📖 [Unreal Substrate Documentation](https://docs.unrealengine.com/5.4/en-US/substrate-materials-in-unreal-engine/)
- 🎥 [GDC 2023 Electric Dreams Demo](https://www.youtube.com/watch?v=iX4SbL0XB00) — first public showcase of Substrate
