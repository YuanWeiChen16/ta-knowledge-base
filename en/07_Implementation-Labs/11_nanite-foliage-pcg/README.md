# Lab 11 — Nanite Foliage + PCG Procedural Forest

**Difficulty**: ⭐⭐⭐ Senior  
**Estimated Time**: 1–2 days  
**Engine Version**: UE5.2+

---

## Original Source

- **Source**: GDC 2024 — "Nanite for Artists"
- **Speaker**: Epic Games Technical Art Team
- **YouTube**: https://www.youtube.com/watch?v=eoxYceDfKEM
- **Related Talk**: GDC 2023 "New Tools for Building Photoreal Worlds in UE5.2"
  - YouTube: https://www.youtube.com/watch?v=dYk7byKHSRw

**Key Takeaways**:
- Why Nanite trees have a memory cost problem (geometry duplication when the same tree is instanced in large numbers)
- PCG decomposition of trees into components (Trunk + Branch + Leaf Sprig)
- PCG to Point Data conversion (storing an entire tree's layout as point data)
- Adjusting Nanite Foliage Dicing Rate
- Billboard LOD fallback strategy

---

## Your Task

**Use PCG + Nanite to build a procedural forest where each tree is assembled from components, achieving 90%+ better memory efficiency than traditionally baked trees.**

### Core Insight (from the talk)

```
Traditional approach:
  1 tree = 4.5M triangles (trunk + all branch instances merged) = 150MB on disk
  
PCG component approach:
  Trunk mesh     = 2MB
  Branch sprig   = 3MB
  Leaf cluster   = 1MB
  PCG point data = <1MB (stores only Transform + Mesh reference)
  
  Full tree = <7MB, ~95% less than traditional
  Reason: PCG only stores transforms — geometry is never duplicated
```

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
| Memory | Total component size of a single PCG tree < 10MB (Size on Disk) |
| Visual | 1km² forest — each tree has natural variation |
| Functionality | Adjusting PCG parameters (density, species) updates the entire forest in real time |
| LOD | Distant trees automatically switch to Billboard with no obvious pop |
| Performance | 1km² forest runs at 60fps at 1080p (PC mid-range) |
| Nanite | `r.Nanite.Visualize triangles` shows high density up close, low density at distance |

---

## Key Nanite Foliage Settings

```
Mesh Import Settings:
  Build Nanite: ✅
  Two-Sided: ✅ (required for leaves)

Project Settings → Rendering:
  Nanite Tessellation: ✅ (if displacement is needed)

Console:
  r.Nanite.DicingRate 1    ← default, highest quality
  r.Nanite.DicingRate 4    ← reduce resolution (performance optimization)
  r.Nanite.Visualize overview   ← overview visualization
  r.Nanite.Visualize triangles  ← triangle density visualization

WPO Settings (wind):
  Material → World Position Offset ← wind animation
  Mesh → Nanite Settings → Evaluate WPO: ✅ (UE5.2+, has cost)
  Mesh → Nanite Settings → Max WPO Displacement: 50  ← set a reasonable upper bound
```

---

## Key Questions (Answer After Completing the Lab)

1. The talk describes the root cause of the memory difference between a traditional merged tree (4.5M tri, 150MB) vs PCG components (<7MB). What role does Nanite's compression play in this?
2. After the PCG is converted to Point Data, if the original Branch mesh is updated, will the forest auto-update? What iteration pitfalls does this workflow have?
3. When `r.Nanite.DicingRate` is increased, which visual qualities of Nanite degrade? In what situations should you raise this value?
4. What techniques can make the visual transition between Billboard LOD and Nanite mesh smoother (hint: Dithering LOD transition, Cross-Fade LOD)?
5. Can this system run on **mobile devices**? What is Nanite's current support status for mobile platforms?

---

## Related Resources

- 🎥 [GDC 2024 Nanite for Artists](https://www.youtube.com/watch?v=eoxYceDfKEM)
- 🎥 [GDC 2023 New Tools for Photoreal Worlds](https://www.youtube.com/watch?v=dYk7byKHSRw)
- 📖 [Unreal Nanite Documentation](https://docs.unrealengine.com/5.4/en-US/nanite-virtualized-geometry-in-unreal-engine/)
- 📖 [Unreal PCG Documentation](https://docs.unrealengine.com/5.4/en-US/procedural-content-generation-overview/)
