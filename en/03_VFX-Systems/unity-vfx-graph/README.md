# Unity VFX Graph

**Stability Tag**: `[ENGINE-VERSIONED: Unity 6 / VFX Graph 17]`

---

## VFX Graph vs Particle System (Shuriken)

| | VFX Graph | Particle System (Shuriken) |
|--|-----------|--------------------------|
| Execution location | GPU (Compute Shader) | CPU |
| Max particle count | Millions | Tens of thousands |
| Dependencies | Requires URP or HDRP | All pipelines |
| Visual nodes | ✅ | ❌ |
| Physics collision | Limited | Full |

**Selection principle**: Large particle counts + visual effects → VFX Graph. Needs physics collision interaction → Particle System.

---

## VFX Graph Core Architecture

```
Context (defines lifecycle stages):
  Initialize    → runs once when particle is born
  Update        → updates every frame
  Output        → render output (Quad/Mesh/Strip)

Block (building blocks):
  Placed inside each Context to define behavior
  Example: Set Velocity Random, Set Color over Life, Flipbook Player

Operator (operation nodes):
  Math operations, texture sampling, Noise, etc., connected to Block properties
```

---

## Common Block Quick Reference

```
Initialize:
  Set Position (Shape: Sphere/Box/Mesh)  ← spawn position
  Set Velocity Random                     ← initial velocity
  Set Lifetime Random                     ← lifetime
  Set Size Random                         ← initial size

Update:
  Gravity                                 ← gravity
  Drag                                    ← drag (slows particles)
  Turbulence                              ← turbulence (noise-driven)
  Conform to Sphere/Signed Distance Field ← make particles conform to a surface
  Update Position                         ← update position by velocity (required)

Output Particle Quad:
  Set Color over Life                     ← color over lifetime
  Set Alpha over Life                     ← opacity over lifetime
  Set Size over Life                      ← size over lifetime
  Flipbook Player                         ← animated texture playback
  Orient: Face Camera                     ← always face camera
```

---

## Blackboard (Parameter Exposure)

Expose internal VFX values for external C# control:

```csharp
// Control VFX parameters in C#
VisualEffect vfx = GetComponent<VisualEffect>();
vfx.SetFloat("Intensity", 2.5f);
vfx.SetVector3("SpawnPosition", transform.position);
vfx.SendEvent("OnHit"); // trigger an Event in the VFX
```

---

## Performance Optimization

1. **Capacity**: Set max particle count; VFX Graph pre-allocates memory
2. **Output Mesh vs Quad**: Mesh particles are more complex, use carefully
3. **Texture format**: VFX textures usually don't need highest quality; ETC2/BC7 compression
4. **GPU Event**: Let particles on GPU spawn child particles (avoids CPU→GPU roundtrip)

---

## Learning Resources

- 📖 [Unity VFX Graph Documentation](https://docs.unity3d.com/Packages/com.unity.visualeffectgraph@latest)
- 🎥 [Gabriel Aguiar — VFX Graph Tutorials](https://www.youtube.com/@GabrielAguiarProd)
- 🎥 [Unity Official VFX Graph Playlist](https://www.youtube.com/playlist?list=PLX2vGYjWbI0RlZMAbKWC2-kVUcEJ7Pq7L)
