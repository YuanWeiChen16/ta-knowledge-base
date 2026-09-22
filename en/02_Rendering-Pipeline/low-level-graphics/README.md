# Low-Level Graphics Concepts (Vulkan / DX12 Conceptual Layer)

**Stability Tag**: `[STABLE]`

> TAs don't write Vulkan code, but understanding these concepts lets you read profiler output and understand the engine's design decisions.

---

## Track A — Conceptual Layer (Required for All Senior TAs)

### Render Pass and Attachments

**Concept**: A Render Pass defines a set of rendering operations and the Render Targets (Attachments) they use.

**Why TAs need to know this**:
- Understand why certain effects must be done in their own pass
- Understand why the G-Buffer is "multiple Render Targets"
- Understand why GrabPass is expensive on tile-based GPUs (requires ending the current pass)

```
Render Pass 1: Shadow Map pass
  Attachment: Depth buffer
  
Render Pass 2: G-Buffer pass  
  Attachments: Color RT0 (BaseColor), RT1 (Normal), RT2 (Roughness), Depth
  
Render Pass 3: Lighting pass
  Attachment: HDR Color RT
  Input: reads G-Buffer (if in the same Render Pass = free tile operation)
```

---

### Resource Barriers (Synchronization Barriers)

**Concept**: Tells the GPU "this resource is transitioning from state X to state Y."

**Why they're needed**: GPUs are highly parallel. When you've just finished writing a Shadow Map and the next pass needs to read it, the GPU must ensure the write is fully complete.

**Practical TA impact**:
- Complex post-processing chains (output of effect A is input of effect B) require correct barriers
- Incorrect barriers → visual errors (reading incomplete data) or performance loss (unnecessary waiting)
- In Unreal's RDG (Render Dependency Graph), these barriers are managed automatically

---

### Descriptor Sets / Binding (Resource Binding)

**Concept**: How the GPU "finds" the textures and buffers it needs when executing a shader.

**Descriptor Set**: A binding table for a set of resources. Switching materials = switching Descriptor Sets.

**Practical TA impact**:
- Each material / shader variant switch has a binding cost
- This is why Material Instances are cheaper than switching Parent Materials
- Bindless Rendering (modern technique): puts all textures in one large array, shaders access by index — reduces switching cost

---

### Memory Hierarchy

```
System RAM (CPU)
  ↑ PCIe / Unified Memory
VRAM / GPU Memory
  ↑ L2 Cache
  ↑ L1 Cache (per SM)
  ↑ Registers (per thread)
```

**Practical TA impact**:
- Texture streaming: loads from System RAM into VRAM on demand
- VRAM budget: exceeding budget → streaming kicks in → stutter
- Unified Memory (Apple Silicon, PS5): CPU/GPU share memory, affects streaming strategy

---

### Command Buffer / Multi-threaded Rendering

**Concept**: CPU pre-records GPU instructions into a Command Buffer, then submits it to the GPU for execution.

**Why TAs need to know this**:
- Explains why "CPU Bound" and "GPU Bound" are different problems
- Multi-threaded rendering: multiple CPU cores simultaneously record Command Buffers → reduces CPU bound
- Unreal's RHI Thread does exactly this

---

## Track B — Implementation Layer (Engine Development / Specific Studios)

> Only needed for studios with an in-house engine or very low-level rendering work. Most TAs don't need this layer.

If you need to go deeper:
- 📖 [Vulkan Tutorial](https://vulkan-tutorial.com/) — the best Vulkan introduction
- 📖 [Vulkan Guide](https://vkguide.dev/) — more modern Vulkan tutorial (Descriptor Sets, Render Pass 2)
- 📖 [D3D12 HelloWorld Samples](https://github.com/microsoft/DirectX-Graphics-Samples) — Microsoft official DX12 samples

---

## GPU-Driven Rendering (Modern Technology Concepts)

The core technology behind Nanite and modern high-end engines:

**Traditional approach**: CPU decides which objects to draw → one draw call per object  
**GPU-Driven**: CPU uploads all object data → GPU decides which to draw (Culling on GPU) → executes in batches with Indirect Draw Calls

**TA impact**:
- Nanite's meshlet system is part of GPU-Driven rendering
- Understand why Draw Call count is no longer the main performance metric for Nanite geometry
- How GPU Occlusion Culling works (HZB — Hierarchical Z Buffer)

---

## Learning Resources

- 📖 [Fabian Giesen — A Trip Through the Graphics Pipeline](https://fgiesen.wordpress.com/2011/07/09/a-trip-through-the-graphics-pipeline-2011-index/) — the deepest free resource
- 🎥 [GDC: GPU-Driven Rendering Pipelines](https://gdcvault.com/) — search "GPU Driven Rendering"
- 📖 [Sascha Willems Vulkan Examples](https://github.com/SaschaWillems/Vulkan) — practical Vulkan examples (even without writing code, reading these helps understand concepts)
