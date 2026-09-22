# Linear Algebra for TA

**Stability Tag**: `[STABLE]`

---

## Quick Reference for Core Concepts

### Vectors
- **Dot Product**: `a · b = |a||b|cos(θ)` — used to calculate angles, lighting direction
- **Cross Product**: `a × b` — used to calculate normals, build tangent space
- **Normalization**: `normalize(v) = v / |v|` — direction vectors must be normalized

### Matrices
- **TRS Matrix**: Translation × Rotation × Scale — Unity/Unreal object transform
- **Model → World → View → Clip**: the transform chain across four coordinate spaces
- **Transpose vs Inverse**: Normal vectors use `inverse(transpose(M))` for transformation, not direct multiplication by M

### Quaternions
- Rotation representation that avoids Gimbal Lock
- `q = (w, x, y, z)` where w is the real part
- Interpolation: SLERP (spherical linear interpolation) is smoother than Euler interpolation

### Coordinate Spaces
| Space | Definition | Common TA Usage |
|-------|-----------|----------------|
| Object Space | Relative to object origin | Vertex shader input |
| World Space | Global scene coordinates | Lighting calculations |
| View Space | Relative to camera | Certain post-processing effects |
| Clip Space | After projection (-1 to 1) | Source of depth values |
| Tangent Space | Relative to vertex normal | Normal map storage space |
| UV Space | 0-1 texture coordinates | Texture sampling |

---

## Tangent Space Deep Dive (The Most Common Pitfall)

Normal maps are stored in tangent space and require the TBN matrix to transform:
- **T**angent (along U direction)
- **B**itangent (along V direction)
- **N**ormal

**Common problem**: Normal map seams → usually an inconsistency in how tangents are calculated (Maya vs engine differences)

---

## Learning Resources

- 🎥 [3Blue1Brown — Essence of Linear Algebra](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab) — the best visual explanation
- 📖 [Math for Game Developers (YouTube)](https://www.youtube.com/c/JorgeRodriguez) — game-focused applications
- 📖 [Graphics Codex](https://graphicscodex.courses.nvidia.com/) — free from NVIDIA, includes extensive graphics math

---

## Hands-On Exercises (project-ideas)

1. **Tangent space visualization tool**: Write a Debug shader in Unity/Unreal that displays the TBN three vectors as colors. Goal: be able to inspect whether tangents are correct on any mesh.
2. **Hand-written matrix transform**: Without using engine APIs, manually write a World to View transform in pure HLSL, and verify the result matches the engine.
