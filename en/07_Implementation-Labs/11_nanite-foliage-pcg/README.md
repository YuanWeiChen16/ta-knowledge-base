# Lab 11 — Nanite Foliage + PCG Procedural Forest

**Difficulty**: ⭐⭐⭐ Senior  
**Estimated Time**: 1–2 days  
**Engine Version**: UE5.2+ (historical workflow; PCG node names and operations vary by version)

---

## Original Source

- **Source**: GDC 2024 — "Nanite for Artists"
- **Speaker**: Epic Games Technical Art Team
- **YouTube**: https://www.youtube.com/watch?v=eoxYceDfKEM
- **Related Talk**: GDC 2023 "New Tools for Building Photoreal Worlds in UE5.2"
  - YouTube: https://www.youtube.com/watch?v=dYk7byKHSRw

> This lab records the GDC 2024 PCG component-assembly and traditional LOD/billboard approach; it is not the UE5.8 Nanite Foliage feature. UE5.8 Nanite Foliage is Experimental and uses systems such as Nanite Assemblies, Voxels, and Skinning. Check the target version's documentation and profile before shipping.

**Key Takeaways**:
- Why Nanite trees have a memory cost problem (geometry duplication when the same tree is instanced in large numbers)
- PCG decomposition of trees into components (Trunk + Branch + Leaf Sprig)
- PCG to Point Data conversion (storing an entire tree's layout as point data)
- Assembling and scattering trees from PCG components
- Tradeoffs of traditional LOD/billboard fallbacks (not the current Nanite Foliage distance strategy)

---

## Your Task

**Use PCG + Nanite to build a procedural forest, then compare component assembly against conventional tree assets for disk size, streaming memory, CPU/GPU time, and image quality.**

### Core Insight (measure with the target version and assets)

Do not use asset sizes from a talk or online example as universal baselines. Compare both approaches with the same content, engine version, quality settings, and platform. Record disk size, load/streaming memory, CPU/GPU time, and visual differences separately. Whether PCG reuses geometry depends on the generated data and asset references.

---

## System Architecture

### Phase 1: Build Tree Components
```
Trunk mesh      ← main trunk (low poly, Nanite)
Branch mesh     ← single branch (reusable)
Leaf Sprig mesh ← leaf cluster (Nanite Masked, small mesh)

Import each component into UE5 separately — keep them small and reusable
```

### Phase 2: PCG Tree Assembly (in Level)
```
PCG Graph — Tree Assembly:
  Sample Trunk Mesh Surface
    → Get Branch Attachment Points (by vertex color / bone positions)
    → Copy Branch Sprig meshes at attachment points
    → Scatter Leaf Sprigs along branches
    → Filter by Distance from Trunk (dense near trunk, sparse farther out)
    → Add Gradient LOD (branches near trunk use full mesh, distant ones use billboard)
```

### Phase 3: Convert PCG to Point Data and Scatter at Scale
```
1. In the Level, convert the full PCG tree to Point Data:
   Right-click → Scripted Actions → Convert PCG Level to PCG Settings

2. Build a Forest Scatter PCG:
   Landscape Sampler (get terrain points)
   → Filter by Slope (only grow trees where slope < 30°)
   → Filter by Height  
   → Copy Points (using the tree PCG point data from above)
   → Static Mesh Spawner

3. Large-scale test: 1km × 1km area
```

### Phase 4: Billboard LOD Fallback

This phase keeps traditional billboard LOD as a comparison. UE5.8 Nanite Foliage uses a different distant-geometry path; do not treat this step as its setup.
```
Tree LOD chain:
  LOD0 (< 20m):   Full PCG component tree
  LOD1 (20–50m):  Trunk + simplified Branch
  LOD2 (50m+):    Billboard (2D impostor)

Billboard settings:
  Nanite → Disallow Nanite for billboard layer
  WPartition Layer Type → Instancing
```

---

## Acceptance Criteria

| Item | Standard |
|------|----------|
| Memory | Record asset disk size and runtime streaming memory; use no fixed threshold |
| Visual | Show natural variation in a documented target area and view distance |
| Functionality | Adjusting PCG parameters (density, species) updates the entire forest in real time |
| LOD | Record the selected LOD/billboard strategy and transition artifacts; if using Nanite Foliage, verify its geometry transitions against that version's docs |
| Performance | State the hardware, resolution, and frame-rate target, then report reproducible CPU/GPU measurements |
| Nanite | Use Nanite Visualization available in the target UE version to inspect density and cost |

---

## Key Nanite Foliage Settings

```
Mesh Import Settings:
  Build Nanite: ✅
  Two-Sided: ✅ (required for leaves)

Use Nanite Visualization in the target UE version to inspect geometry density and rendering state; setting names, view modes, and displacement support vary by version.

WPO Settings (wind):
  Material → World Position Offset ← wind animation
  Mesh → Nanite Settings → Evaluate WPO: ✅ (UE5.2+, has cost)
  Mesh → Nanite Settings → Max WPO Displacement: 50  ← set a reasonable upper bound
```

---

## Key Questions (Answer After Completing the Lab)

1. How do your two tree asset approaches compare for disk size and streaming memory? What are the effects of geometry reuse, Nanite compression, and PCG output?
2. After the PCG is converted to Point Data, if the original Branch mesh is updated, will the forest auto-update? What iteration pitfalls does this workflow have?
3. Use Nanite Visualization to inspect geometry and overdraw at different distances. Which asset settings or leaf materials affect image quality and performance most?
4. What techniques can make the visual transition between Billboard LOD and Nanite mesh smoother (hint: Dithering LOD transition, Cross-Fade LOD)?
5. Can this system run on **mobile devices**? What is Nanite's current support status for mobile platforms?

---

## Related Resources

- 🎥 [GDC 2024 Nanite for Artists](https://www.youtube.com/watch?v=eoxYceDfKEM)
- 🎥 [GDC 2023 New Tools for Photoreal Worlds](https://www.youtube.com/watch?v=dYk7byKHSRw)
- 📖 [Unreal Nanite Documentation](https://docs.unrealengine.com/5.4/en-US/nanite-virtualized-geometry-in-unreal-engine/)
- 📖 [Unreal PCG Documentation](https://docs.unrealengine.com/5.4/en-US/procedural-content-generation-overview/)
- 📖 [Nanite Foliage (UE5.8, Experimental)](https://dev.epicgames.com/documentation/unreal-engine/nanite-foliage)
