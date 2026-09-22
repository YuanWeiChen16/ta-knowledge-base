# TA Skill Self-Assessment Matrix

Use this table to evaluate your current state in each domain. **TA growth is non-linear** — you can be Senior in one area and Junior in another. Honest assessment is more valuable than pretending to be well-rounded.

---

## Rating Level Definitions

| Level | Definition |
|-------|-----------|
| **0 — None** | No exposure whatsoever |
| **1 — Junior** | Know how to use the tools, but get stuck on edge cases |
| **2 — Mid** | Understand the "why," can independently diagnose most problems |
| **3 — Senior** | Reason from first principles, can design systems and set strategy |

---

## Skill Matrix

| Domain | Sub-Skill | Your Level (0-3) | Target Level | Reference Section |
|--------|-----------|-----------------|--------------|-------------------|
| **Shader Authoring** | HLSL/GLSL syntax | | | `01_Shaders-Materials/hlsl-core` |
| | UV manipulation and texture sampling | | | `01_Shaders-Materials/hlsl-core` |
| | Vertex shader applications | | | `01_Shaders-Materials/hlsl-core` |
| | Compute shaders | | | `01_Shaders-Materials/hlsl-core` |
| **Material Systems** | PBR microfacet theory | | | `01_Shaders-Materials/pbr-theory` |
| | Unity ShaderGraph / URP / HDRP | | | `01_Shaders-Materials/unity` |
| | Unreal Material Editor / Instances | | | `01_Shaders-Materials/unreal` |
| | Shader debugging (RenderDoc) | | | `01_Shaders-Materials/shader-debugging` |
| **Lighting & GI** | Real-time lighting principles | | | `02_Rendering-Pipeline/lighting-gi` |
| | Lightmap baking workflow | | | `02_Rendering-Pipeline/lighting-gi` |
| | Unity APV / HDRP Probe Volume | | | `02_Rendering-Pipeline/lighting-gi` |
| | Unreal Lumen | | | `02_Rendering-Pipeline/lighting-gi` |
| | Shadow techniques (CSM, VSM, PCSS) | | | `02_Rendering-Pipeline/shadows` |
| **VFX & Particles** | Particle system design principles | | | `03_VFX-Systems/fundamentals` |
| | Unity VFX Graph | | | `03_VFX-Systems/unity-vfx-graph` |
| | Unreal Niagara | | | `03_VFX-Systems/unreal-niagara` |
| | VFX performance budgeting | | | `03_VFX-Systems/fundamentals` |
| **Performance Profiling** | GPU performance bottleneck diagnosis methodology | | | `04_Performance-Profiling/gpu-methodology` |
| | Unity Profiler / Frame Debugger | | | `04_Performance-Profiling/unity-profiler` |
| | Unreal Insights / GPU Visualizer | | | `04_Performance-Profiling/unreal-insights` |
| | Mobile performance (tile-based GPU) | | | `04_Performance-Profiling/mobile` |
| | RenderDoc full workflow | | | `01_Shaders-Materials/shader-debugging` |
| **Pipeline & Tools** | Python DCC scripting (Maya/Blender/Houdini) | | | `05_Pipeline-Tools/python-scripting` |
| | Houdini for Games (VAT) | | | `05_Pipeline-Tools/houdini` |
| | Substance Designer/Painter advanced | | | `05_Pipeline-Tools/substance` |
| | USD format and exchange workflow | | | `05_Pipeline-Tools/usd` |
| | Unreal PCG Framework | | | `05_Pipeline-Tools/unreal-pcg` |
| **Low-Level Graphics Concepts** | Render pass / G-Buffer architecture | | | `02_Rendering-Pipeline/low-level-graphics` |
| | Draw call batching / GPU instancing | | | `02_Rendering-Pipeline/low-level-graphics` |
| | Memory hierarchy (VRAM / system RAM) | | | `02_Rendering-Pipeline/low-level-graphics` |
| | Synchronization barrier concepts | | | `02_Rendering-Pipeline/low-level-graphics` |
| | Vulkan/DX12 conceptual layer (Track A) | | | `02_Rendering-Pipeline/low-level-graphics` |
| **Math Foundations** | Linear algebra (vectors, matrices) | | | `00_Foundations/linear-algebra` |
| | Quaternions and rotations | | | `00_Foundations/linear-algebra` |
| | Color science (gamma, ACES, sRGB) | | | `00_Foundations/color-science` |
| | Rendering pipeline data flow | | | `00_Foundations/rendering-pipeline` |

---

## Usage Recommendations

1. **Re-evaluate every 3 months**
2. **Identify the area where you're weakest and that most impacts your daily work** — prioritize that one
3. **Don't chase a 3 in everything** — most TAs have deep specialization in 2-3 areas; others just need to be functional
4. **Your portfolio reflects your 3-level areas** — turn your strongest skills into demonstrable work

---

## The Non-Linear Path of a Senior TA

The real skill distribution of a Senior TA typically looks like this (not maxed out in everything):

```
Shader Writing:      ███████████ 3
Material Systems:    ███████████ 3  
Lighting & GI:       ████████░░░ 2.5
VFX & Particles:     ███████░░░░ 2
Performance:         ███████████ 3
Pipeline & Tools:    ████████░░░ 2.5
Low-Level Concepts:  ███████░░░░ 2
Math Foundations:    ████████░░░ 2.5
```

The goal is **deep specialization + broad enough coverage**, not an even distribution across all areas.
