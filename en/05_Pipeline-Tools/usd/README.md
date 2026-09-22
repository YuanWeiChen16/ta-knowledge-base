# USD (Universal Scene Description)

**Stability Tag**: `[STABLE]` concepts, `[VOLATILE]` toolchain integration

---

## Why USD Matters

USD is an open-source scene description format from Pixar, becoming the **universal exchange standard** for the games/film/XR industry.

**Core value**: Non-destructive layered (Layering) scene description — multiple departments can simultaneously modify different aspects of a scene.

---

## USD Core Concepts

### Prim (Primitive)
Every object in the scene is a Prim:

```
/World                   ← Stage root node
  /World/Environment     ← Scope (group)
  /World/Character       ← Xform (node with transform)
    /World/Character/Body  ← Mesh
  /World/Lighting        ← Scope
    /World/Lighting/Sun  ← DomeLight
```

### Layering (Non-Destructive Layers)
```
Base Layer:    scene geometry (geometry.usd)
Layout Layer:  placement (layout.usd)       ← stacked on top of Base
Lighting Layer: light setup (lighting.usd)  ← stacked on top of that
FX Layer:      effects (fx.usd)             ← topmost layer

Each layer can be edited independently; later layers override earlier layer properties
```

### Variant (Variations)
Different versions of the same object (LOD, day/night versions, damage level):

```python
# Set variant in code
stage = Usd.Stage.Open("character.usd")
prim = stage.GetPrimAtPath("/Character")
variantSet = prim.GetVariantSets().GetVariantSet("costume")
variantSet.SetVariantSelection("armor")  # switch to armor version
```

---

## USD in Game Pipelines

### Asset Exchange
```
Maya/Blender/Houdini → export USD → engine imports
High-fidelity scene exchange between different DCC applications (more complete than FBX)
```

### Collaborative Workflows
```
Artist A responsible for: character_geo.usd (geometry)
Artist B responsible for: character_materials.usd (materials)
Lighting artist responsible for: character_lighting.usd (lighting)
→ merged into character_final.usd (all layers combined)
```

### Unreal Stage Actor
UE5 natively supports USD:
```
File → Import → USD Stage
Or place a USD Stage Actor in the Level to directly load a .usd scene
```

---

## USDZ (Apple / Web Format)

Compressed-package USD for AR preview (iOS AR Quick Look) and Web 3D display.

```python
# Python conversion to USDZ
from pxr import UsdUtils
UsdUtils.CreateNewUsdzPackage("scene.usd", "scene.usdz")
```

---

## Learning Resources

- 📖 [USD Official Documentation](https://openusd.org/release/index.html)
- 📖 [Pixar USD Tutorials](https://openusd.org/release/tut_usd_tutorials.html)
- 🎥 [NVIDIA USD Workshop](https://developer.nvidia.com/usd)
- 📖 [Unreal USD Pipeline](https://docs.unrealengine.com/5.4/en-US/universal-scene-description-in-unreal-engine/)
