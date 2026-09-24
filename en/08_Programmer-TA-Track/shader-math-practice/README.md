# Shader Math Practice (LeetCode-Style)

**Shader practice resources for programmers — interactive exercises with immediate visual feedback.**

---

## Core Resources

### 1. Shadertoy — The Platform Most Like LeetCode
**URL**: https://www.shadertoy.com

```
How it works:
  - Online GLSL Fragment Shader editor
  - Immediate visual feedback (change one line, see the result instantly)
  - Massive community library (you can read other people's code)
  - Built-in uniforms: iTime, iResolution, iMouse, etc.

Programmer strategy:
  1. Browse other people's work first
  2. Open the code and read through it line by line
  3. Tweak one variable and observe the visual change
  4. Try to recreate it yourself (without looking at the answer)
```

### 2. The Book of Shaders — Structured Introduction
**URL**: https://thebookofshaders.com

If you have a programming background you can move through this quickly. Key chapters:
- **Ch. 5 — Shaping Functions**: Visual understanding of `smoothstep`, `step`, and `fract`
- **Ch. 9 — Patterns**: Generating repeating patterns with math
- **Ch. 10 — Random**: Why "random" in a shader must be pseudo-random
- **Ch. 11 — Noise**: Value Noise, Perlin Noise, and FBM understood from a programmer's angle
- **Ch. 13 — Fractal Brownian Motion**: The standard technique for layering noise

### 3. Graphics Workshop (Harvard/MIT-Style Assignments)
**URL**: https://github.com/GongXiangShun/graphics-workshop

```
Contains 5 exercises with clear specs:
  1. Quilt Patterns      ← Fragment Shader intro, procedural 2D
  2. Landscape Gen       ← Simplex Noise terrain generation
  3. Rasterization       ← Implement Phong shading from scratch
  4. Stylized Rendering  ← Toon rendering, non-photorealistic style
  5. Ray Tracing         ← Pure shader ray tracer

Everything runs in the browser via WebGL — no installation needed.
For programmers: these exercises are like graphics LeetCode, with clear input/output specs.
```

### 4. CIS 5660 Penn (University of Pennsylvania)
**URL**: https://github.com/CIS-5660-Fall-2024

```
University of Pennsylvania Technical Art course — all assignments are open:
  HW04 Stylization:
    - Implement a Toon Shader (multi-light support)
    - Depth + Normal Buffer Outline (Sobel / Robert's Cross)
    - Full-screen Post-Process Effect
    - Unity URP Render Feature with custom Pass

This is real university-level TA coursework, well-scoped and fully documented.
```

---

## Exercise List (Ordered by Difficulty)

### ⭐ Level 1 — Shader Fundamentals

```glsl
// Complete these on Shadertoy — each should take < 1 hour:
1. Draw a color wheel that rotates over time
2. Draw a soft-edged circle using smoothstep
3. Make an infinitely tiling grid pattern using fract
4. Animate a wave using sin/cos
5. Implement 2D SDFs (Signed Distance Fields) for a circle, rectangle, and star
```

### ⭐⭐ Level 2 — Core Techniques

```glsl
// Each exercise: 1-3 hours:
6.  Implement Value Noise (starting from a hash function)
7.  Implement FBM (Fractional Brownian Motion — layering multiple octaves of noise)
8.  Phong lighting model (diffuse + specular + ambient)
9.  Dissolve effect (noise threshold + discard)
10. Rim Light / Fresnel effect
11. Triplanar Mapping (texture projection without UV)
```

### ⭐⭐⭐ Level 3 — Industry Techniques

```glsl
// Each exercise: 3-8 hours:
12. Toon Shader (quantized lighting + Sobel outline)
13. Parallax Occlusion Mapping
14. Approximate Screen Space Reflection (SSR)
15. Voronoi Noise + Worley Noise
16. Ray Marching basics (SDF scene + basic lighting)
17. GPU particle simulation using Compute Shaders
```

---

## The "Deconstruction" Learning Method for Shadertoy

The best learning approach for programmers:

```
Steps:
1. Search Shadertoy for the effect you want to learn
   (search "pbr" / "dissolve" / "voronoi" / "raymarching")

2. Open a highly-starred shader — don't read the code yet

3. Try implementing it yourself first (even just a rough draft)

4. Compare with the reference code:
   - What are the inputs and outputs of each function?
   - Where does each magic number come from?
   - Is there a more concise way to write this?

5. Fork it and modify it, observing the visual changes

6. Add comments in your own words throughout the code
```

---

## GLSL → HLSL → UE5 Reference Table

Essential syntax mapping for programmers moving into TA:

```hlsl
// Types
GLSL          HLSL (Unreal)
vec2          float2
vec3          float3
vec4          float4
mat4          float4x4
sampler2D     Texture2D + SamplerState

// Functions
mix(a,b,t)    lerp(a, b, t)
fract(x)      frac(x)
mod(a,b)      fmod(a, b)
texture(s,uv) s.Sample(ss, uv)  // HLSL 5.0

// Math (identical)
dot, cross, normalize, length,
sqrt, pow, abs, sin, cos, clamp,
step, smoothstep, floor, ceil

// Key difference
GLSL and HLSL are shader languages and do not determine a normal map's Y-axis convention.
OpenGL / DirectX conventions describe the tangent-space normal map's green-channel direction; check the export preset and engine importer, and invert the green channel if needed.

// UE5 Material Custom Node
// No type declarations needed — just return directly:
return dot(normalize(Normal), normalize(LightDir));
```

---

## Learning Resources

| Resource | URL | Purpose |
|----------|-----|---------|
| Shadertoy | shadertoy.com | Daily practice |
| The Book of Shaders | thebookofshaders.com | Structured introduction |
| Graphics Workshop | github.com/GongXiangShun/graphics-workshop | Spec-driven assignments |
| CIS 5660 Penn | github.com/CIS-5660-Fall-2024 | University TA course assignments |
| Shader Story | github.com/degged/shaderstory | Unity URP open-source example library |
| Khronos GLSL Docs | registry.khronos.org/OpenGL-Refpages | Function reference |

---

## The Shader Debug Mindset Shift for Programmers

```
Programmer debugging:
  print("value =", x)  ← output as text

Shader debugging:
  return float4(x, 0, 0, 1);  ← output as color (brighter red = larger x)

Common techniques:
  // Visualize normals
  return float4(Normal * 0.5 + 0.5, 1);

  // Visualize UVs
  return float4(UV.x, UV.y, 0, 1);

  // Visualize depth
  float d = SceneDepth / 1000.0;
  return float4(d, d, d, 1);

  // Verify a boolean condition
  return condition ? float4(0,1,0,1) : float4(1,0,0,1);  // green=true, red=false
```
