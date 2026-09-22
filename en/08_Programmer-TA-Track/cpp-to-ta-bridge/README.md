# Mapping C++ / Systems Programming Knowledge to TA Work

**Your programming background maps more directly to TA work than you think. Here are the explicit connections.**

---

## Direct Mappings

### C++ → HLSL/GLSL

```cpp
// C++ — what you already know
struct Vertex {
    glm::vec3 position;
    glm::vec3 normal;
    glm::vec2 uv;
};

float dot(glm::vec3 a, glm::vec3 b) { return a.x*b.x + a.y*b.y + a.z*b.z; }
```

```hlsl
// HLSL — almost identical
struct Vertex {
    float3 position : POSITION;
    float3 normal   : NORMAL;
    float2 uv       : TEXCOORD0;
};

// dot(), cross(), normalize() are all built-in with identical syntax
float result = dot(a, b);  // ← same as glm
```

**Migration cost: minimal.** HLSL syntax is simpler than C++ — no pointers, no memory management.

---

### Data Structure Thinking → GPU Data Layout

```cpp
// The C++ programmer's instinct
std::vector<Particle> particles;  // Array of Structures (AoS)

struct Particle {
    float3 position;
    float3 velocity;
    float  lifetime;
};
```

```hlsl
// GPUs prefer Structure of Arrays (SoA)
// because the GPU processes 32 threads simultaneously — SoA keeps memory access contiguous

StructuredBuffer<float3> positions;   // position for all particles
StructuredBuffer<float3> velocities;  // velocity for all particles
StructuredBuffer<float>  lifetimes;   // lifetime for all particles

// Contiguous reads, cache-friendly
float3 pos = positions[threadId];
```

**Knowledge you already have**: cache locality, memory alignment — these matter even more on the GPU.

---

### Multithreading → Compute Shaders

```cpp
// C++ multithreading
std::for_each(std::execution::par, particles.begin(), particles.end(),
    [](Particle& p) { p.position += p.velocity * deltaTime; });
```

```hlsl
// Compute Shader — same parallel concept, but on the GPU
[numthreads(64, 1, 1)]
void UpdateParticles(uint3 id : SV_DispatchThreadID) {
    uint i = id.x;
    if (i >= particleCount) return;

    positions[i] += velocities[i] * deltaTime;
    lifetimes[i] -= deltaTime;
}

// A single Dispatch(N/64, 1, 1) launches N threads
// 64 threads per group (one Warp/Wave) execute simultaneously
```

**Key difference**:
- CPU threads = dozens, each very powerful
- GPU threads = thousands, each very weak — they must all do the same thing together

---

### Systems Programming → Render Pipeline Synchronization

```cpp
// C++ synchronization primitives
std::mutex mutex;
std::condition_variable cv;
std::atomic<int> counter;
```

```
Vulkan/DX12 equivalents:
  VkSemaphore    = cross-queue synchronization (like an OS semaphore)
  VkFence        = CPU waiting for GPU completion (like cv.wait)
  VkBarrier      = resource state transitions within a queue
  VkEvent        = fine-grained synchronization points

What a TA needs to understand (without writing the code):
  "Why can't I sample the current frame's Shadow Map inside the Shadow Pass?"
  → Because the Shadow Map write for this frame isn't finished yet (requires a barrier)
  → The engine handles this automatically, but you need to understand "ordering dependencies"
```

---

### Compiler Knowledge → Shader Compilation

```
C++ compilation pipeline:
  Source → Preprocessor → Compiler → Linker → Binary

Shader compilation pipeline:
  HLSL/GLSL → Shader Compiler (DXC/glslang)
            → Intermediate (SPIR-V / DXIL)
            → Driver Compiler → GPU machine code

Concepts you already understand:
  - Shader Variant    = conditional compilation (#define / multi_compile)
  - Shader hot reload = similar to dynamic loading
  - Shader cache      = avoid recompilation (PSO Cache)

UE5's shader system:
  - Each material + feature permutation = one shader variant
  - Pre-compiling all variants = long packaging time but fast at runtime
  - Dynamic compilation = hitching during play (shader compilation hitching)
```

---

## Direct Applications of Python in a TA Pipeline

Your Python skills can generate immediate value:

```python
# 1. Batch asset validation (the most common TA tool)
import unreal

def validate_texture_naming():
    """
    Scan all textures and flag anything that doesn't follow naming conventions.
    TA daily routine: run this weekly and report results to artists.
    """
    assets = unreal.AssetRegistryHelpers.get_asset_registry()
    textures = assets.get_assets_by_class("Texture2D")
    
    issues = []
    for tex in textures:
        name = tex.asset_name
        if not (name.startswith("T_") or 
                name.endswith("_D") or name.endswith("_N")):
            issues.append(f"Naming violation: {tex.package_name}")
    
    for issue in issues:
        unreal.log_warning(issue)
    
    unreal.log(f"Scan complete — {len(issues)} issues found")

# Run it
validate_texture_naming()
```

```python
# 2. Texture settings automation (the biggest time-saver)
import unreal

def fix_normal_map_settings():
    """
    Find all textures with _N in the name, ensure they're set to Normal Map and sRGB = False.
    Doing this manually: 3 clicks per texture. 100 textures = 300 clicks.
    This script: 3 seconds.
    """
    factory = unreal.AssetRegistryHelpers.get_asset_registry()
    textures = factory.get_assets_by_class("Texture2D")
    
    fixed = 0
    for tex_data in textures:
        if "_N." in str(tex_data.package_name) or "_Normal." in str(tex_data.package_name):
            tex = unreal.load_asset(tex_data.package_name)
            if tex:
                tex.set_editor_property("compression_settings", 
                    unreal.TextureCompressionSettings.TC_NORMALMAP)
                tex.set_editor_property("srgb", False)
                unreal.EditorAssetLibrary.save_asset(str(tex_data.package_name))
                fixed += 1
    
    unreal.log(f"Fixed {fixed} Normal Map settings")
```

```python
# 3. Automatic LOD settings
import unreal

def set_lod_settings_by_vertex_count():
    """
    Automatically set LOD distances based on vertex count.
    > 10K vertices  → set 4 LODs
    1K-10K          → set 3 LODs
    < 1K            → set 2 LODs
    """
    meshes = unreal.AssetRegistryHelpers.get_asset_registry()\
                   .get_assets_by_class("StaticMesh")
    
    for mesh_data in meshes:
        mesh = unreal.load_asset(mesh_data.package_name)
        if not mesh:
            continue
        
        vertex_count = mesh.get_num_vertices(0)  # LOD 0
        
        lod_group = "LargeAsset" if vertex_count > 10000 else \
                    "MediumAsset" if vertex_count > 1000 else \
                    "SmallAsset"
        
        mesh.set_editor_property("lod_group", lod_group)
```

---

## System Design Interviews → TA Technical Design Thinking

When an Art Director says "I want a system that supports 50 weather states," your response should mirror a system design interview:

```
1. Clarify requirements
   "Are 50 weather states coexisting, or transitioning between each other?"
   "How long does a transition need to take?"
   "What platform is this running on?"

2. Estimate scale
   "50 Post Process Volumes × 5 parameters each = 250 parameters to manage"
   "Interpolating once per second vs every frame = 60x difference in CPU cost"

3. Present architecture options
   Option A: One Blueprint per weather state (50 BPs, high maintenance cost)
   Option B: Data Table + one Weather Manager BP (single source of truth)
   Option C: Data Assets + Curve Assets (most flexible, supports hot-reloading)

4. Analyze tradeoffs
   "Option B is fast to implement, but Data Table changes require recompilation"
   "Option C is the most flexible, but initial setup is complex"

5. Recommend a solution and explain your reasoning
```

**This is the gap between a Senior TA and a Junior TA:** the ability to propose architectural solutions, not just "do what you're told."

---

## Common Mistakes When Programmers Transition to TA

```
❌ Mistake 1: "I have a programming background, I should start with Vulkan"
✓ Correct: Build productivity at the engine level first — fill in low-level knowledge on demand

❌ Mistake 2: "Shaders are just functions, they should be easy"
✓ Correct: Shader math sits at the intersection of CS, physics, and visual art — it requires deliberate practice

❌ Mistake 3: "I know Python, pipeline tools aren't a problem"
✓ Correct: The tools themselves aren't hard — the hard part is knowing what's worth automating and what isn't

❌ Mistake 4: "Performance optimization is one of my strengths"
✓ Correct: CPU optimization ≠ GPU optimization. The GPU bottleneck model is completely different — you need to rebuild your intuition

❌ Mistake 5: "I'll master the technical side first, worry about visual sensibility later"
✓ Correct: Visual intuition requires deliberate training — the later you start, the harder it is to catch up. Practice it in parallel from day one
```
