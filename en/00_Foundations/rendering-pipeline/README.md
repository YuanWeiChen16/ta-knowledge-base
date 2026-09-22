# Rendering Pipeline Fundamentals

**Stability Tag**: `[STABLE]`

> Understand how data goes from a 3D scene to pixels on screen. This is the framework for all shader work.

---

## Rendering Pipeline Data Flow

```
CPU                          GPU
─────                        ─────────────────────────────────────────
Scene Data          →   [Vertex Shader]
(Mesh, Transform,       ↓ executes once per vertex
 Materials)         →   [Primitive Assembly / Rasterization]
                         ↓ fills triangles into pixel fragments
                     →   [Fragment / Pixel Shader]
                         ↓ executes once per pixel
                     →   [Output Merger / ROP]
                         ↓ depth test, blending
                     →   Framebuffer → Screen
```

---

## TA Focus Points at Each Stage

### Vertex Shader
- **Input**: Position, Normal, Tangent, UV, VertexColor (per vertex)
- **Output**: Clip space position (required), others interpolated to fragment
- **TA uses**: Vertex animation (grass, simple cloth simulation), Outline, Displacement

### Rasterization
- Fills pixels by interpolating triangle edges
- Generates **Fragments** (candidate pixels)
- **TA concern**: This is where aliasing (jaggies) comes from

### Fragment / Pixel Shader
- **Input**: Interpolated vertex data + Texture samples
- **Output**: Color (RGBA)
- **TA's main workspace**: All PBR calculations, texture blending, and effects happen here

### Depth Test & Blending
- **Depth Test**: Compares depth buffer, decides whether to discard
- **Alpha Blending**: Translucent blending (order-dependent, performance killer)
- **TA pitfall**: Translucent objects must be sorted back-to-front and cannot use depth write

---

## Forward vs Deferred (Full explanation in `02_Rendering-Pipeline/`)

| | Forward | Deferred |
|--|---------|---------|
| Lighting calculation | Per-object × per-light | Store G-Buffer first, calculate all at once |
| Transparency | Easy | Difficult (requires special handling) |
| MSAA | Easy | Difficult/expensive |
| Mobile | Usually more suitable | High bandwidth |

---

## The Concept of Passes

Modern rendering isn't drawn in one pass — it's multiple passes stacked together:

```
Shadow Pass     → generate Shadow Map
G-Buffer Pass   → store geometry info (Deferred)
Lighting Pass   → calculate lighting
Translucency    → translucent objects
Post Process    → TAA, Bloom, Tone mapping
UI              → overlay last
```

**Key TA question**: "Does the data this effect needs exist at the time this pass runs?"

---

## Learning Resources

- 📖 [LearnOpenGL](https://learnopengl.com/) — the best introduction, clear concepts, interactive examples
- 🎥 [Acerola YouTube](https://www.youtube.com/@Acerola_t) — visual rendering pipeline explanations
- 📖 [Real-Time Rendering 4th ed.](https://www.realtimerendering.com/) — industry standard reference (some chapters freely browsable online)

---

## Hands-On Exercises (project-ideas)

1. **Pass visualization**: Capture a frame with RenderDoc, inspect it pass by pass, write down the inputs and outputs of each pass. Goal: be able to describe the complete lifecycle of a frame.
2. **DIY Wireframe overlay**: Using only shaders (no mesh modification), overlay a wireframe in a forward pass. Constraints: does not affect normal rendering, supports skinned meshes.
