# Lab 09 — Pure Shader Particle System (No Niagara)

**Difficulty**: ⭐⭐⭐ Mid-Senior  
**Estimated Time**: 1–2 days  
**Engine Version**: UE5.0+ (Material Editor only)

---

## Original Source

- **Source**: GDC Technical Artist Summit 2024
- **Talk Title**: *"Technical Artist Summit: Build Your Own Particle System"*
- **Speaker**: Ben Cloward (Technical Artist, Unity)
- **GDC Vault**: https://www.gdcvault.com/play/1034588/Technical-Artist-Summit-Build-Your
- **Ben Cloward YouTube**: https://www.youtube.com/@BenCloward (tutorial series on the same topic)

**Key Takeaways**: The core techniques for achieving particle effects using pure shader nodes (no CPU simulation) — Camera Facing, pseudo-random numbers, UV animation, Depth Fade. This approach is extremely performant because all computation runs on the GPU as Vertex/Pixel Shader work.

---

## Core Concept: How Shaders "Fake" Particles

```
Traditional Niagara: CPU tracks position/velocity/lifetime for every particle
Shader particles:    GPU uses math to produce visuals that LOOK like particles

Key tools:
  - Instance ID / Vertex ID → each instance gets a unique pseudo-random seed
  - Frac(Time * Speed + Seed) → 0-1 cyclic value, simulates particle lifetime
  - Pseudo-random numbers (Hash function) → gives each particle different speed, size, color
  - Camera-Facing Billboard → mesh always faces the camera
  - World Position Offset → math-driven vertex displacement (no CPU required)
```

---

## Your Task

**In UE5's Material Editor, without using Niagara, build a complete spark/stardust particle effect using pure shader nodes.**

### Required Features

1. **Camera-Facing Billboard**
   - A Plane mesh that always faces the camera
   - Implemented with World Position Offset + Camera Vector
   - No Niagara or Sprite Renderer

2. **Pseudo-Random Per-Particle Variation**
   - Each mesh instance has different: size, speed, start phase
   - Use PerInstanceCustomData or Vertex Color to pass in seed values
   - Or use Object Position Hash to auto-generate seeds

3. **Lifecycle Animation**
   - Each particle has a full Born → Live → Die cycle
   - Size: grows from 0 to maximum, then shrinks back to 0
   - Alpha: fades in and out matching the lifecycle

4. **World-Space Motion**
   - Particles move in world space (floating upward, expanding outward, etc.)
   - Driven entirely by WPO, no CPU

5. **Depth Fade**
   - Particle edges fade out when near other geometry
   - Prevents hard-edge z-fighting

---

## Core HLSL / Node Logic

```hlsl
// Pseudo-random number (inside a Custom node)
float Hash(float seed) {
    return frac(sin(seed * 127.1 + 311.7) * 43758.5453);
}

// Unique seed per particle
float seed = dot(GetObjectWorldPosition(), float3(1, 1, 1));

// Lifetime (triangle wave 0→1→0)
float lifetime = frac(Time * speed + Hash(seed));
float size = sin(lifetime * 3.14159); // largest at mid-life

// Camera-Facing WPO
float3 toCamera = normalize(CameraPosition - WorldPosition);
float3 up = float3(0, 0, 1);
float3 right = normalize(cross(up, toCamera));
// Use right and up to reconstruct camera-facing vertex offset
```

---

## Acceptance Criteria

| Item | Standard |
|------|----------|
| Visual | Each Plane instance reads as an independent particle |
| Visual | Billboard always faces the camera as it orbits |
| Visual | Particles have a full lifecycle (born, move, disappear) |
| Technical | 500 instances in scene, CPU usage barely changes |
| Technical | Depth Fade present — edges blend naturally near the ground |
| Technical | Material Draw Call = 1 (all instances share one draw) |

---

## Why This Technique Matters

| Comparison | Niagara Particles | Shader Particles |
|------------|------------------|-----------------|
| CPU cost | CPU tick per particle | Near zero |
| GPU cost | Normal overdraw | Normal overdraw |
| Flexibility | Very high | Medium (limited by shader math) |
| Best use | Complex effects needing physics/collision | Large ambient background particles (dust, fireflies, stardust) |

**The CPU cost difference between 500 Niagara particle systems vs 500 Shader particle instances can be 10–50×.**

---

## Key Questions (Answer After Completing the Lab)

1. Is your Camera-Facing implementation mathematically equivalent to Niagara's `Orient: Face Camera`? What edge cases (e.g. camera looking straight down) will cause problems?
2. What is the difference between pseudo-random numbers (Hash functions) and true randomness? Why must "random" in shaders always be pseudo-random?
3. What is the biggest limitation of this approach? In what situations would you be forced to use Niagara instead of Shader particles?
4. What is the Overdraw of 500 Shader particle instances? How would you optimize it (hint: Depth Prepass, particle size, Additive blending)?

---

## Related Resources

- 🎥 [Ben Cloward YouTube — Shader-Based Particles](https://www.youtube.com/@BenCloward)
- 🎥 [GDC Vault Talk](https://www.gdcvault.com/play/1034588/Technical-Artist-Summit-Build-Your)
