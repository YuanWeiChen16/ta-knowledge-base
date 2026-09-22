# External Similar Resources Index

**Similar knowledge bases, courses, and code repositories built by others on GitHub and the web.**

> Use these alongside this folder as complements: this folder covers "concepts + methodology," while the resources below provide "directly runnable code examples."

---

## GitHub Knowledge Bases (closest in structure to this folder)

### frepiso/TechArt-Guide ⭐ Top Recommendation
**URL**: https://github.com/frepiso/TechArt-Guide  
**Fork**: https://github.com/joelRVC/TechArt-Guide

```
Content coverage:
  Houdini (VEX, USD, LOP)
  OpenUSD (full concepts and scripting)
  Python, C++, Linux/Docker/Git
  Rendering math (vectors, matrices, coordinate systems)
  PBR (Normal Map, MaterialX)
  Vulkan API (full conceptual layer)
  Cameras (shutter, aperture, depth of field)

Highlights:
  - Includes a Docker DevContainer — Python + USD works out of the box
  - Has runnable Houdini VEX example code
  - Broad coverage, well-suited for developers with a programming background (C++ and Linux chapters included)
  - Actively maintained
```

**Differences from this folder**: TechArt-Guide leans toward Houdini/USD/toolchain; this folder leans toward Unreal/Shader/GDC implementation.

---

## GitHub Code Repositories (directly runnable Shader examples)

### csdjk/LearnUnrealShader ⭐ Essential for Unreal
**URL**: https://github.com/csdjk/LearnUnrealShader  
**Engine**: Unreal Engine 5.3.2

```
Included Shader examples:
  Surface Shaders:
    - TriPlanar Projection
    - Ice (refraction + SSS translucency)
    - InteriorCube (fake interior space Cubemap Raymarching)

  Post-Process Effects:
    - Outline (depth + normal Edge Detection)
    - PixelationFilter (UV quantization pixelation)
    - ActionLines (procedural ray animation)
    - VHS (old TV effect)

Architecture patterns demonstrated:
  - Material Instance Pattern
  - Material Function Pattern
  - Parameter Collection Pattern
```

### PacktPublishing/UE5-Shaders-Cookbook
**URL**: https://github.com/PacktPublishing/Unreal-Engine-5-Shaders-and-Effects-Cookbook-2nd-Edition  
**Book**: Unreal Engine 5 Shaders and Effects Cookbook (2nd ed.) by Brais Brenlla

```
50+ complete UE5 material and effects examples:
  - Lumen / Nanite integration materials
  - Full PBR pipeline workflow
  - Multi-platform optimization techniques
  - Advanced effects (Subsurface, Translucency)
The book's code serves as one reference answer set for the Labs in this folder
```

### DeGGeD/ShaderStory ⭐ Unity Version
**URL**: https://github.com/degged/shaderstory  
**Engine**: Unity 6.0+ URP

```
Freely available Unity HLSL learning resource library:
  - Common HLSL function examples
  - Edge transition techniques
  - Pattern and shape generation
  - Lighting model implementations
Creative Commons licensed — free to study and adapt
```

### Toffee-Meow/Shader-Grimoire ⭐ Chinese TA Learning Journal
**URL**: https://github.com/Toffee-Meow/Shader-Grimoire  
**Engine**: Unreal Engine 5.x, Unity 2022

```
A Shader learning notebook repository from a Chinese developer studying TA:
  Focus area: Toon/NPR rendering
  Includes: character toon shading, general materials, VFX, post-processing, stylized rendering
  Languages: HLSL, GLSL, ShaderLab
  Highlights: detailed Chinese explanations and render effect screenshots
  Why it's relevant: similar learning path to yours — see how others record and organize knowledge
```

### SaiiPrashanth/Shader_Dump
**URL**: https://github.com/SaiiPrashanth/Shader_Dump  
**Engine**: Unity Built-in / URP / HDRP

```
Personal Unity HLSL snippet collection:
  Surface/: Toon, PBR variants, Dissolve
  PostProcess/: Outline, Vignette, Scanline
  Particles/: Soft Particle, Distortion VFX
  Utility/: Noise functions, Blend utilities
Each Shader includes pipeline tags and comments
```

---

## Advanced: Unreal Low-Level Graphics Programming (developer-focused)

### heyx3/ExtendedGraphicsProgramming ⭐ Essential for Programmers
**URL**: https://github.com/heyx3/ExtendedGraphicsProgramming  
**Article series**: https://medium.com/@manning.w27/advanced-graphics-programming-in-unreal-part-1-10488f2e17dd

```
7-part article series + companion Plugin:

Article topics:
  Part 1: Introduction
  Part 2: Rendering Abstractions (RHI, RDG) and threading
  Part 3: Scenes, Views, SceneViewExtension
  Part 4: Global Shaders
  Part 5: Simple Material Shaders
  Part 6: Advanced Material Shaders
  Part 7: Mesh-Material Shaders and Mesh Passes

Plugin features:
  - Custom Post-Process Material Shaders
  - Screen-Space Passes (Compute or Vertex+Pixel)
  - Custom Render Passes + Mesh Passes
  - Material Shader Compilation API

Why it's relevant:
  - The author explicitly states "C++ multithreading knowledge required"
  - Treats the Unreal rendering system as a C++ engineering problem
  - Currently the clearest resource on the web for custom UE5 render passes
  
This is the bridge resource for transitioning from TA to Graphics Programmer.
```

---

## Comparison Table

| Resource | Engine | Type | Relevance | Highlights |
|----------|--------|------|-----------|------------|
| frepiso/TechArt-Guide | Engine-agnostic | Knowledge base | ⭐⭐⭐⭐⭐ | Most comprehensive TA knowledge base, includes Docker |
| csdjk/LearnUnrealShader | UE5 | Code examples | ⭐⭐⭐⭐⭐ | Shader examples that run directly in UE5 |
| UE5 Shaders Cookbook | UE5 | Book + code | ⭐⭐⭐⭐ | 50+ recipes, cross-referenceable with the book |
| DeGGeD/ShaderStory | Unity | Code examples | ⭐⭐⭐ | Free Unity URP HLSL resources |
| Toffee-Meow/Shader-Grimoire | UE5+Unity | Learning journal | ⭐⭐⭐⭐ | Chinese, focused on toon rendering |
| heyx3/ExtendedGraphicsProgramming | UE5 | Plugin + articles | ⭐⭐⭐⭐⭐ | Essential for programmers, low-level rendering |

---

## How to Use Alongside This Folder

```
This folder:              External resources:
Concept understanding  →  csdjk/LearnUnrealShader (see UE5 examples directly)
Lab implementation ref →  UE5 Shaders Cookbook (find solutions to similar problems)
Houdini/USD            →  frepiso/TechArt-Guide (more complete than this folder)
Deep low-level render  →  heyx3/ExtendedGraphicsProgramming (programmer bridge)
Unity reference        →  ShaderStory / Shader_Dump
```
