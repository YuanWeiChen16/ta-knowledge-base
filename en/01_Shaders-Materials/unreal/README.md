# Unreal Engine Material System

**Stability Tag**: `[ENGINE-VERSIONED: UE5.4]`

---

## Material Editor Core Concepts

### Material Domain
| Domain | Usage |
|--------|-------|
| Surface | Standard object surface (most common) |
| Deferred Decal | Decal effects |
| Light Function | Light source mask |
| Post Process | Post-processing effects |
| UI | HUD/interface |

### Blend Mode
| Mode | Characteristics | Performance |
|------|----------------|------------|
| Opaque | No transparency, fastest | ✅ Best |
| Masked | Masked transparency (clip), Nanite compatible | ✅ Good |
| Translucent | Semi-transparent, doesn't support most GI | ⚠️ Expensive |
| Additive | Additive blending (good for VFX) | ⚠️ Expensive |

---

## Material Instance (The Most Important Workflow)

Don't modify the Parent Material directly. Create a Material Instance:

```
Right-click Material → Create Material Instance
```

**Why:**
- Modifying the Parent Material → all Instances update automatically
- Switching instances is nearly zero cost (same shader variant)
- Artists can safely adjust Instance parameters without touching the shader

**Material Instance Dynamic (MID)**:
```cpp
// Blueprint
UMaterialInstanceDynamic* MID = UMaterialInstanceDynamic::Create(Material, this);
MID->SetScalarParameterValue("Roughness", 0.5f);
MID->SetTextureParameterValue("BaseMap", MyTexture);
```

---

## Custom HLSL Nodes

Add a Custom node in the Material Editor to write native HLSL:

```
Material Graph → right-click → Custom
```

```hlsl
// Custom node example: Voronoi function
float2 uv = Inputs[0].xy;  // Input 0
float cellSize = Inputs[1]; // Input 1

// Calculate Voronoi...
float2 cell = floor(uv / cellSize);
float minDist = 1.0;
// [logic...]
return minDist;
```

**Limitations:**
- Custom nodes cannot define functions, only inline code
- For complex logic, use `.ush` include files

---

## Nanite Compatibility Notes

Nanite (UE5's virtual geometry technology) has material restrictions:

| Feature | Nanite Support |
|---------|---------------|
| Opaque | ✅ Full support |
| Masked (hard clip) | ✅ Supported (UE 5.1+) |
| Translucent | ❌ Not supported |
| World Position Offset (WPO) | ✅ Supported (UE 5.2+, with performance cost) |
| Pixel Depth Offset | ❌ Not supported |
| Two-Sided | ✅ Supported |

**TA golden rule**: When using WPO wind sway + Masked for foliage, evaluate whether the Nanite WPO cost is worth it.

---

## Lumen Material Considerations

Lumen (UE5 software ray-traced GI) affects materials:

- **Emissive materials**: Can serve as Lumen light sources (requires "Use Emissive for Static Lighting" checked)
- **Very low Roughness materials (< 0.1)**: Lumen reflection quality drops (supplement with Screen Space Reflections)
- **High Roughness (>0.4) metals**: Lumen performs best

---

## Material Functions (Reusable Material Library)

Reusable material logic fragments:

```
Content Browser → right-click → Materials → Material Function
```

TA workflow: Encapsulate common logic (Tri-planar mapping, rain wetness, edge wear) into Material Functions for project-wide reuse.

---

## Commonly Used Built-in Material Functions

```
MF_Triplanar               // tri-planar texture projection
MF_VertexInterpolator      // move calculation to Vertex Shader
MF_Desaturate              // desaturation
MF_HeightBlend             // height-based blending of two materials
MF_CheapContrast           // cheap contrast enhancement
```

---

## Learning Resources

- 📖 [Unreal Engine Material Documentation](https://docs.unrealengine.com/5.4/en-US/unreal-engine-materials/)
- 🎥 [Ben Cloward — Unreal Shader Tutorials](https://www.youtube.com/c/BenCloward)
- 🎥 [William Faucher YouTube](https://www.youtube.com/@WilliamFaucher) — in-depth UE5 material tutorials
- 📖 [Unreal Source: Engine/Shaders/](https://github.com/EpicGames/UnrealEngine) — engine shader source code (requires application)

---

## Hands-On Exercises (project-ideas)

1. **Substrate multi-layer material** (UE5.3+): Use the Substrate material system to create a "snow-covered rock" material — base rock layer + top snow layer, blended based on world normal direction. Constraint: supports Nanite, no WPO.
2. **Material Function library**: Build 3 reusable Material Functions: (a) procedural edge wear (b) rain-wetted surface (c) procedural fake AO. Each function should have clear parameter comment documentation.
