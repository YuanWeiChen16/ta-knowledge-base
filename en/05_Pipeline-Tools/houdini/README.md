# Houdini for Games

**Stability Tag**: `[STABLE]` concepts, `[ENGINE-VERSIONED]` engine plugins

---

## Why TAs Need Houdini

Houdini is the industry standard for procedural generation. For TAs, the three most important use cases are:

1. **VAT (Vertex Animation Texture)**: Bake complex simulations into textures, play back at zero cost in-engine
2. **Procedural assets**: Generate infinitely varied scene elements from parameters
3. **Pipeline tools**: Integrate procedural systems directly into Unity/Unreal via Houdini Engine

---

## VAT (Vertex Animation Texture) Deep Dive

**Principle**: Store each vertex's position/normal/color for each frame in a texture; vertex shader reads the texture to reconstruct the animation.

**Advantages**:
- Zero CPU simulation cost
- Perfect complex fluid/cloth/destruction animation
- Supports GPU Instancing (play different time points simultaneously)

### VAT Types

| Type | Best For | Houdini SOP |
|------|----------|------------|
| **Soft Body** | Cloth, flags, foliage | Labs VAT Soft Body |
| **Rigid Body** | Destruction, rigid body simulation | Labs VAT Rigid Body |
| **Fluid / Spray** | Water, smoke, fire | Labs VAT Fluid |
| **Dynamic Topology** | Particle swarms, liquid | Labs VAT Dynamic Remesh |

### VAT Production Workflow

```
1. Houdini simulation (RBD / Cloth / Fluid SOP)
2. Labs VAT SOP → bake output:
   - Position texture (RGB = XYZ offset)
   - Normal texture (normal direction)
   - Output mesh (UV in TEXCOORD1, used for VAT lookup)
3. Import to engine
4. Write Vertex Shader to read texture
```

### VAT Vertex Shader Logic (pseudocode)
```hlsl
// Read VAT Position texture
float frame = _Time * _FPS;  // current frame number
float2 vatUV = float2(uv1.x, (frame + 0.5) / _TotalFrames);
float3 posOffset = SampleVATTexture(vatUV) * _BoundsSize + _BoundsMin;
float3 finalPos = basePos + posOffset;
```

---

## Houdini Engine (Engine Integration)

Use Houdini's procedural systems directly inside Unity/Unreal:

**Unity**: Houdini Engine for Unity Plugin  
**Unreal**: Houdini Engine for Unreal Plugin

**Workflow**:
1. Build a parametric procedural system in Houdini (HDA — Houdini Digital Asset)
2. Import to engine; parameters are exposed in the Inspector/Details panel
3. Artists adjust parameters directly in-engine; Houdini computes in background and updates the mesh

**Use cases**: Procedural terrain, procedural building facades, procedural foliage distribution

---

## Houdini for Games Learning Path

```
Level 1 (Basics):
  → Understand SOP (Surface Operator) node flow
  → Use Geometry VOP for basic procedural meshes
  → Understand Attributes (@P, @N, @Cd, @uv)

Level 2 (TA Core):
  → Full Labs VAT workflow
  → Houdini Engine engine integration
  → Python SOP for scripting procedural systems

Level 3 (Advanced):
  → VEX procedural logic (Houdini's shader-like language)
  → FLIP Fluid simulation
  → Custom HDA design for artists to use
```

---

## Learning Resources

- 📖 [SideFX Labs (free toolkit)](https://www.sidefx.com/products/houdini/houdini-labs/) — VAT tools are here
- 🎥 [Steven Knipping — Applied Houdini](https://www.appliedhoudini.com/) — game-oriented Houdini tutorials
- 🎥 [SideFX Official YouTube](https://www.youtube.com/c/houdini3d)
- 🎥 [Rebelway Houdini for Games](https://www.rebelway.net/) — paid but high quality
- 📖 [Houdini VAT Documentation](https://www.sidefx.com/tutorials/vertex-animation-textures/)

---

## Hands-On Exercises (project-ideas)

1. **Cloth VAT**: Use Houdini Cloth SOP to simulate a flag fluttering (60 frames), export VAT, and write a vertex shader in Unity URP to play it back. Constraint: supports GPU Instancing, 5 flags playing at different time points simultaneously, < 0.1ms.
2. **Procedural rock HDA**: Create a procedural rock HDA with adjustable detail/scale/variation parameters. Import with Houdini Engine into Unreal so artists can adjust parameters directly in-scene to generate different shapes.
