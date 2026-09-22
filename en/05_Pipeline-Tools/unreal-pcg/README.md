# Unreal PCG Framework

**Stability Tag**: `[ENGINE-VERSIONED: UE5.4]`

---

## What Is PCG

PCG (Procedural Content Generation) Framework is a procedural generation system built into UE5.2+, letting TAs dynamically generate scene content in the Editor and at runtime.

---

## PCG Core Architecture

```
PCG Graph: node graph defining generation logic
  Input Nodes:
    Surface Sampler      ← sample points on a surface
    Landscape Sampler    ← sample points on terrain
    Spline Sampler       ← sample points along a spline
    Volume Sampler       ← sample points within a volume

  Filter Nodes:
    Filter by Slope      ← filter by slope (rocks only on steep slopes)
    Filter by Density    ← density filter
    Difference           ← exclude overlapping areas (no grass on roads)
    Intersection         ← keep only overlapping areas

  Transform Nodes:
    Transform Points     ← random rotation/scale
    Project Points       ← project to surface (make objects conform to terrain)
    Copy Points          ← copy point data

  Output Nodes:
    Static Mesh Spawner  ← spawn Static Mesh
    Actor Spawner        ← spawn Actor
    Spline Mesh Spawner  ← spawn Mesh along spline
```

---

## Basic Workflows

### Scene Foliage Generation
```
Landscape Sampler
  → Filter by Slope (0-30 degrees = flat ground = grass)
  → Filter by Height (altitude limit)
  → Add Random Offset (random position jitter)
  → Transform Points (random Y-axis rotation, random scale 0.8-1.2)
  → Project to Terrain (conform to terrain)
  → Static Mesh Spawner (grass/small flowers/mushrooms)
```

### Generate Railing Along a Path
```
Spline Input
  → Sample Spline (spacing = railing width)
  → Align to Spline Direction (align direction)
  → Static Mesh Spawner (railing mesh)
```

---

## PCG Attribute System

Each Point has Attributes that can be passed and modified between nodes:

```
Built-in Attributes:
  Position (float3)    ← world coordinates
  Rotation (rotator)   ← rotation
  Scale (float3)       ← scale
  Density (float)      ← density (0-1, used for filtering)
  Color (FLinearColor) ← color (can pass to Mesh)

Custom Attributes:
  Add Attribute → define your own attributes
  Example: Slope, BiomeType, WetnessFactor
  These can be passed to Material Parameters (dynamic material variation)
```

---

## PCG vs Foliage Tool

| | PCG | Foliage Tool |
|--|-----|-------------|
| Updates | Dynamic (change parameter → instantly recalculates) | Static (manually painted) |
| Rules | Full procedural logic | Simple brush density |
| Memory | Can skip storing (generated at runtime) | Permanently stored |
| Best for | Procedurally generated natural scenes | Manually fine-tuned scenes |

---

## Learning Resources

- 📖 [Unreal PCG Documentation](https://docs.unrealengine.com/5.4/en-US/procedural-content-generation-overview/)
- 🎥 [PCG Framework Tutorial (Official)](https://www.youtube.com/watch?v=Ol5Z9Tpz5gQ)
- 🎥 [Unreal Sensei — PCG Tutorials](https://www.youtube.com/@UnrealSensei)

---

## Hands-On Exercises (project-ideas)

1. **Procedural forest scene**: Use PCG to generate trees/rocks/grass on terrain with rules: trees only grow at slope < 25 degrees, rocks only at slope > 30 degrees, no vegetation within 3m of roads. Constraint: a 1km² area must be able to generate in real time without impacting game performance.
