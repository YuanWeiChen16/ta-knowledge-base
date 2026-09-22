# Lab 08 — World-Driven Materials (World Information in Shaders)

**Difficulty**: ⭐⭐ Mid  
**Estimated Time**: 4–8 hours  
**Engine Version**: UE5.2+

---

## Original Source

- **Source**: GDC Technical Artist Summit
- **Talk Title**: *"Technical Artist Summit: Bringing the World to Your Shaders"*
- **Speaker**: Matt Oztalay (Senior Developer Relations Technical Artist, Epic Games)
- **GDC Vault**: https://gdcvault.com/play/1027568/Technical-Artist-Summit-Bringing-the
- **Alternate Link**: https://gdcvault.com/play/1028009/Technical-Artist-Summit-Bringing-the

**Key Takeaways**: How to feed external scene information (terrain height, landscape layers, world coordinates) into materials to drive dynamic effects; design patterns for World Position texture projection; and tooling philosophy for giving artists control over material variation.

---

## Your Task

**Build a "world-aware material system" in UE5 where materials read scene information and adapt to their environment automatically.**

### Three Required Features

#### Feature A: World Space Texture Projection
- Use world coordinates (World Position) as UV, instead of the mesh's own UV
- Result: no matter how the mesh is placed or rotated, the texture always aligns to world space
- Use case: large-scale terrain detail, seamless textures spanning multiple meshes

#### Feature B: Landscape Height-Driven Material Blending
- Read Landscape Layer Weights (grass, dirt, rock)
- Use height values to automatically blend between grass / dirt / rock
- Make meshes placed on terrain automatically match surrounding materials (RVT — Runtime Virtual Texture)

#### Feature C: Dynamic Macro Variation
- Sample a large-scale Noise/Variation texture using world coordinates
- Give the same material subtle color/Roughness variation at different world positions
- Eliminate the "clone stamp" repetition feeling on large surfaces sharing the same material

---

## RVT (Runtime Virtual Texture) Quick Start

```
Steps:
1. Add a RuntimeVirtualTextureVolume to the scene
2. Set VT type (Base Color + Normal + Roughness)
3. Landscape Material: Write to RVT
4. Mesh Materials placed on terrain: Read from RVT

Result: mesh bases automatically blend with terrain material, seamlessly merging into the environment
```

---

## Acceptance Criteria

| Item | Standard |
|------|----------|
| Feature A | Rotating/moving the mesh, texture stays world-space aligned |
| Feature B | Grass/dirt/rock ratios transition naturally at different heights |
| Feature C | 100 objects with the same material in scene — no obvious tiling repetition |
| Technical | World Position calculation lives in the Vertex Shader (Vertex Interpolator) |
| Technical | RVT read/write correct (mesh base shows terrain material blending) |

---

## Key Questions (Answer After Completing the Lab)

1. What is the relationship between World Space UV projection and Tri-Planar Mapping? When is Tri-Planar better than single-axis projection?
2. RVT "stamps" terrain information onto meshes — where is the performance cost of this technique, and what are its limitations?
3. What is the ideal resolution and tiling scale for a Macro Variation texture? What visual problems arise when it's too dense or too sparse?
4. How do you ensure World Space materials don't cause excessive Texture Bandwidth on mobile platforms (TBDR)?

---

## Relevant Nodes

```
World Position              ← vertex world coordinate
Vertex Interpolator         ← moves WP calculation to VS
Runtime Virtual Texture     ← RVT sample
Landscape Layer Blend       ← terrain layer blending
Absolute World Position     ← world coordinate unaffected by pivot
```
