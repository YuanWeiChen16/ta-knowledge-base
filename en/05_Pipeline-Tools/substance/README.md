# Substance Designer & Painter

**Stability Tag**: `[ENGINE-VERSIONED: Substance 3D; verify the export preset for your project version]`

---

## Designer vs Painter Responsibilities

| Tool | Purpose | TA Use Case |
|------|---------|------------|
| **Substance Designer** | Procedural texture generation | Creating reusable material libraries, Trim Sheets, special effect textures |
| **Substance Painter** | 3D texture painting | Character/prop hand-painted textures, ID Mask baking |

---

## Substance Designer Core TA Skills

### Procedural Tileable Materials
```
Graph structure:
  Noise/Shape nodes → Height Map
  Height → Normal (Normal node)
  Height → AO (Ambient Occlusion node)
  Height + Levels → Roughness
  Color Input + Variation → BaseColor
  Final outputs: BaseColor, Normal, Roughness, Metallic, AO
```

### Trim Sheet Production Workflow
Trim Sheet = one texture containing multiple different cross-section material strips, UV-mapped to building/prop edges.

```
1. Create multiple material strips in Designer (brick wall top/bottom/corner/window frame)
2. Use Tile Sampler or manually arrange on a 2048×512 texture
3. Map to model UV Layout in-engine
Advantage: small number of textures covers large amounts of architectural detail
```

### Essential Designer Nodes for TAs
```
Tile Sampler         ← procedural pattern distribution
Histogram Scan       ← convert grayscale to mask (adjust brightness/darkness threshold)
Histogram Select     ← select specific grayscale range
Warp                 ← distort one texture using another
Blend                ← multi-texture blending (using Mask)
Normal Combine       ← stack multiple Normal maps
Bevel                ← generate edge highlights from Height (chamfer effect)
```

---

## Substance Painter Core TA Skills

### Baking Settings
Baking is the starting point of the Painter workflow — wrong settings cause problems in all subsequent work:

```
Bake Mesh Maps:
  High Poly: high-resolution mesh (with detail)
  Low Poly: low-resolution mesh (for games)
  
Important maps:
  Normal (from mesh)  ← transfer detail from high to low poly
  World Space Normal  ← used by certain special shaders
  Ambient Occlusion   ← ambient occlusion
  Curvature           ← edge highlight / crevice shadow mask
  Position            ← position mask (for locating procedural materials)
  Thickness           ← for subsurface scattering
  ID Map              ← color IDs for quick zone selection
```

### ID Mask Workflow
```
1. Mark different parts of high or low poly with vertex colors or multi-material IDs
2. Bake ID Map (each part gets a solid color)
3. Use Color Selection in Painter to quickly mask specific parts
4. Greatly improves painting efficiency on multi-material complex objects
```

---

## Engine Export Settings

### Unity URP Export
```
Output Template: Unity Universal Render Pipeline (Metallic)
Output textures:
  BaseColor (+ Alpha for Opacity)
  Metallic + Roughness + AO (packed in R G B channels)
  Normal (choose the convention expected by the Unity import setup; OpenGL/Y+ is a common default, so verify the texture and export preset)
```

### Unreal Engine Export
```
Output Template: Unreal Engine 4 (Metallic/Roughness)
Output textures:
  BaseColor
  ORM (Occlusion-Roughness-Metallic packed)
  Normal (DirectX format)
Note: Unreal commonly uses the DirectX convention; Unity's import convention may differ. Keep the Substance export preset, green channel, and engine import settings consistent.
```

### Normal Map Direction Convention
```
DirectX and OpenGL refer to tangent-space normal-map green-channel conventions; engines, export presets, and importers may handle them differently.
Unity commonly defaults to the OpenGL convention, while Unreal assets commonly use DirectX. Do not infer the convention from the tool name alone.
Check the material in the target engine, invert the green channel if needed, and record the export settings.
```

---

## Learning Resources

- 📖 [Adobe Substance 3D Documentation](https://helpx.adobe.com/substance-3d-designer/home.html)
- 📖 [Unity Normal Map Import](https://docs.unity3d.com/6000.1/Documentation/Manual/StandardShaderMaterialParameterNormalMap.html) — Unity normal map import and green-channel handling
- 🎥 [Stylized Station YouTube](https://www.youtube.com/@StylizedStation) — Substance Designer procedural materials
- 🎥 [Substance Academy](https://substance3d.adobe.com/tutorials) — Adobe official tutorials
- 📖 [The PBR Guide (Substance)](https://substance3d.adobe.com/tutorials/courses/the-pbr-guide-part-1) — PBR and Substance integration
