# Rendering Architecture Deep Dive (System Design-Style)

**The area where programmers have the biggest advantage — understanding rendering as a distributed system.**

---

## Core Analogy: Rendering = Distributed System

System design concepts familiar to programmers map directly onto rendering:

| System Design Concept | Rendering Equivalent |
|----------------------|---------------------|
| Producer/consumer queue | Command Buffer (CPU produces, GPU consumes) |
| Cache / Cache Invalidation | Texture Cache, Mip Maps, Shader compilation cache |
| Concurrency and synchronization | Resource Barriers, Pipeline Stages, Semaphores |
| Memory hierarchy | VRAM / L2 / L1 / Register File |
| Batching | Draw Call Batching, GPU Instancing |
| Flow control | Texture Streaming, Virtual Geometry (Nanite) |
| Backpressure | CPU Bound vs GPU Bound |
| Pipeline design | Render Passes, G-Buffer, Deferred vs Forward |

---

## Essential Reading (Ordered by Depth)

### Level 1 — Building the Right Mental Model

**"A Trip Through the Graphics Pipeline 2011"**
- Author: Fabian Giesen (AMD / RAD Game Tools engineer)
- URL: https://fgiesen.wordpress.com/2011/07/09/a-trip-through-the-graphics-pipeline-2011-index/
- 13 posts covering the full depth of DX11 drivers through pixel shaders
- **This is the first thing programmers should read.** Think of it as a Linux kernel deep dive, but for the GPU.

**"Getting Started in Computer Graphics"**
- Author: Jeremy Ong (Senior Graphics Programmer)
- URL: https://www.jeremyong.com/graphics/2024/05/19/getting-started-in-computer-graphics/
- An entry-point guide from a programmer's perspective, including a learning path and the right questions to ask yourself

---

### Level 2 — System Design Depth

**Real-Time Rendering 4th ed.**
- URL: https://www.realtimerendering.com/ (partially free)
- How to read it as a programmer: treat each chapter as a design doc for a subsystem
  - Ch. 7 Shadow — design tradeoffs across shadow map approaches
  - Ch. 11 Global Illumination — the design space of GI caching
  - Ch. 20 Pipelines — modern GPU pipeline architecture

**Physically Based Rendering (PBRT 4th ed.)**
- URL: https://pbr-book.org (completely free)
- Rendering treated as a CS problem: algorithms, data structures, and math derivations — all rigorous
- Key chapters for programmers: Ch. 1 (system architecture), Ch. 4 (sampling theory), Ch. 9 (material models)

---

### Level 3 — Low-Level APIs and Hardware

**Vulkan Tutorial**
- URL: https://vulkan-tutorial.com
- The best Vulkan introduction — builds a triangle from scratch
- How deep a TA needs to go: understanding Command Buffers, Render Passes, and Barrier concepts is enough (you don't need to implement everything)

**DirectX 12 Samples (Microsoft)**
- URL: https://github.com/microsoft/DirectX-Graphics-Samples
- Complete examples from HelloWorld to Advanced
- For programmers: these samples make Vulkan concepts concrete

**Arm Mali GPU Best Practices**
- URL: https://developer.arm.com/documentation/102444/latest/
- The best official documentation on mobile TBDR architecture
- How to read it as a programmer: understand "why tile-based rendering changes every bandwidth assumption you have"

---

## System Design-Style Rendering Problems

Practice thinking through these problems as you would a system design interview:

### Shadow System Design
```
Problem: Design a system that supports 100 dynamic lights, each with independent shadows

Dimensions to consider:
- Memory: 100 Shadow Maps @ 1024×1024 = 400MB — how do you handle that?
- Performance: what's the cost of updating 100 shadow maps per frame?
- Quality: how do you handle resolution degradation on distant shadows?
- Dynamism: which lights need per-frame updates? Which can be cached?

Real-world solution:
→ MegaLights (SIGGRAPH 2025) solves this with Stochastic Sampling
→ Read the original paper and understand their design decisions
```

### Texture Streaming Design
```
Problem: An open world game has 50GB of textures, but only 8GB of VRAM — how do you manage this?

Dimensions to consider:
- Priority: how do you decide which textures live in VRAM? (camera distance, screen coverage)
- Pre-loading: how do you predict which textures will be needed as the player moves?
- Mip Streaming: only load the mip levels you actually need
- Eviction policy: what happens when VRAM is full? LRU? Priority queue?

Real-world implementations:
→ UE5 Virtual Texture
→ Read "Texture Streaming in Unreal Engine" documentation
```

### GI Cache Design
```
Problem: Design a real-time GI system that supports dynamic lights and dynamic geometry

Dimensions to consider:
- Storage format: Irradiance Probes? Spherical Harmonics? Radiance Cache?
- Update strategy: full update every frame? Incremental? Async?
- Light leaking: how do you prevent probes from sampling incorrect GI through walls?
- Scalability: how do you degrade gracefully on low-end hardware?

Real-world implementations:
→ Lumen (UE5) has detailed design documentation at GDC/SIGGRAPH
```

---

## Rendering Debug Methodology for Programmers

```
Traditional programming debug:
  1. Read the stack trace
  2. Add logs
  3. Set breakpoints

Rendering debug:
  1. Capture a frame in RenderDoc (equivalent to a core dump)
  2. Inspect pass by pass (equivalent to reading a call stack)
  3. Visualize intermediate values (equivalent to print debugging)
  4. Step through shaders line by line (equivalent to breakpoints)

Your programmer advantages:
  - You know how to systematically narrow down the problem space
  - You won't "randomly change things and hope for the best"
  - You know to find the root cause, not just treat the symptom
```

---

## Learning Milestones

```
Milestone 1: Explain the complete data flow for a single frame
  - From CPU submitting a draw call to pixel output on screen
  - What are the inputs and outputs of each pass?

Milestone 2: Diagnose CPU Bound vs GPU Bound
  - Read profiler numbers fluently
  - Know which number corresponds to which bottleneck

Milestone 3: Estimate the implementation cost of a rendering feature
  - Say "this effect costs roughly X ms"
  - Propose 3 different implementations with different cost/quality tradeoffs

Milestone 4: Design a rendering subsystem
  - Given requirements (X lights, Y ms budget, Z platform)
  - Propose an architecture and explain the tradeoffs
```
