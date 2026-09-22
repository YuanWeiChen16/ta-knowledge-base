# GPU Architecture Concepts

**Stability Tag**: `[STABLE]`

> The foundational knowledge that makes profiler numbers meaningful. TAs don't need to write GPU drivers, but they need to understand the GPU's decision-making logic.

---

## The Essential Difference Between GPU and CPU

| | CPU | GPU |
|--|-----|-----|
| Core count | Few (8-32) | Many (thousands) |
| Per-core speed | Fast | Slow |
| Good at | Complex logic, branching, serial tasks | Massive parallel identical operations |
| Memory | Large capacity, high-latency cache | High bandwidth, specialized architecture |

**TA conclusion**: `if` branches in shaders are not as cheap as on a CPU. GPUs tend to execute both branches and then select one result.

---

## Memory Hierarchy (Why Bandwidth Matters)

```
VRAM (GPU display memory)
  ↓ high bandwidth but limited capacity
L2 Cache
  ↓
L1 Cache / Shared Memory (one per SM)
  ↓ extremely fast but extremely small
Registers (per-thread)
```

**TA concerns**:
- Texture sampling → reads from VRAM; cache misses are expensive
- If fragments in the same draw call have UVs that jump around a lot → constant cache misses → slow
- Reducing texture resolution or using Mips → improves cache locality

---

## Mobile vs Desktop GPU Architecture Differences (Important!)

### Desktop GPU (Immediate Mode Rendering, IMR)
- Each primitive is immediately rasterized and output to the framebuffer
- VRAM and display memory are separate
- Bandwidth is plentiful

### Mobile GPU (Tile-Based Deferred Rendering, TBDR)
- Screen is divided into small tiles (Tiles); all geometry for each tile is collected first, then rasterized together
- The entire tile's computation is done in small on-chip cache, **without constantly reading/writing system memory**
- Saves significant bandwidth = saves battery power

**TA impact**:
- Blending and depth tests are nearly free on TBDR (within the tile cache)
- But operations that "read current framebuffer content" (GrabPass/SceneColor) are **extremely expensive** on TBDR — require flushing the tile to memory
- Overdraw penalty is lower on TBDR than desktop, but bandwidth penalty is stricter

---

## Draw Calls and Batching

**Draw Call**: The instruction from CPU to GPU saying "draw this object."

**Why draw calls have cost**:
- Each draw call requires the CPU to set state (material, shader, transform)
- GPU executes very fast, but CPU data preparation has overhead
- Large numbers of small draw calls → CPU bound

**Ways to reduce draw calls**:
- **GPU Instancing**: Same mesh + material, N objects use only 1 draw call
- **Static Batching**: Merge geometry of static objects
- **SRP Batcher** (Unity): Reduces CPU setup cost per draw call

---

## Vulkan/DX12 Conceptual Layer (Track A — All Senior TAs)

No need to write code, but need to understand these concepts:

| Concept | Why TAs Need to Know |
|---------|---------------------|
| **Render Pass** | Explains why certain effects must be in their own pass |
| **Resource Barrier** | Explains why "reading a just-written texture" requires special handling |
| **Descriptor Sets** | Explains the source of material "slot" costs |
| **Command Buffer** | Explains how multi-threaded rendering works |
| **Memory Heaps** | Explains the difference between VRAM budget and upload heap |

---

## Learning Resources

- 📖 [Jasper St. Pierre — GPU Architecture](https://fgiesen.wordpress.com/2011/07/09/a-trip-through-the-graphics-pipeline-2011-index/) — Fabian Giesen's "A Trip Through the Graphics Pipeline," the most in-depth free resource
- 📖 [Arm Mali GPU Best Practices](https://developer.arm.com/documentation/102444/latest/) — required reading for mobile GPU
- 🎥 [GDC: Tile-Based GPU Architectures](https://developer.apple.com/videos/play/wwdc2020/10602/) — Apple GPU architecture (representative of all TBDR GPUs)
- 📖 [GPU Gems 1-3](https://developer.nvidia.com/gpugems/gpugems/foreword) — free online, classic GPU technology

---

## Hands-On Exercises (project-ideas)

1. **Bandwidth test**: On a mobile target platform (or emulator), profile the bandwidth consumption of the same effect made with GrabPass vs without. Compare the two in a profiler.
2. **Instancing benchmark**: In the same scene, measure draw call count and frame time for 500 objects with: No batching / Static batching / GPU Instancing.
