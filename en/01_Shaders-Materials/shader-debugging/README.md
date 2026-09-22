# Shader Debugging Workflow

**Stability Tag**: `[STABLE]` (tools update but workflow is stable)

> The sooner you build debugging habits, the less time you waste guessing.

---

## Golden Rule: Profile First, Then Guess

**Don't optimize without data.**  
Open the profiler → find the real bottleneck → then act.

---

## RenderDoc — Primary Debugging Tool

### Basic Workflow
1. Launch RenderDoc, Attach or Launch the game
2. `F12` to capture a frame
3. Event Browser: view each Draw Call
4. Texture Viewer: view any texture/render target
5. Mesh Viewer: view vertex data
6. Pipeline State: view the shader and state for each stage

### Common Scenarios

**Problem: Material color is wrong**
```
Event Browser → find the Draw Call for the problematic object
→ Pipeline State → Pixel Shader → click the "Debug" button
→ Shader Debugger: step through line by line, see the value of each variable
```

**Problem: Normal map seam**
```
Texture Viewer → find the Normal map
→ confirm Format is R8G8B8A8_UNORM (not sRGB)
→ Mesh Viewer → check if tangent and bitangent directions are correct
```

**Problem: Transparent object sorting issue**
```
Event Browser → view the object order in the Translucency pass
→ Texture Viewer → view depth values in each frame buffer
```

### RenderDoc Capture Tips
- `F12`: capture frame
- In Texture Viewer press `S` to save texture as image
- The Pipeline State shows shader source for each stage

---

## Unity Frame Debugger

`Window → Analysis → Frame Debugger`

More integrated and easier to use than RenderDoc, but provides less information.

**Uses:**
- View pass order (Shadow → Depth → Opaque → Transparent → Post)
- View SRP Batcher batch effectiveness
- View the Render Target of each pass
- **Quick check:** "Is this pass executing?"

---

## Unreal GPU Visualizer

`Shift + L` or `ProfileGPU` command

```
ProfileGPU → click open Frame → view GPU time for each pass
```

**Unreal Console Commands (for debugging):**
```
r.VisualizeBuffer BaseColor      // view GBuffer BaseColor
r.VisualizeBuffer WorldNormal    // view GBuffer normals
r.VisualizeBuffer Roughness      // view GBuffer Roughness
r.VisualizeBuffer Metallic
r.VisualizeBuffer SceneDepth
stat GPU                         // view GPU time per pass
vis SceneColor                   // view final color buffer
```

---

## Debug Shader Techniques

**Visualize any intermediate value (the most-used technique):**

```hlsl
// Display normal direction as color
return float4(normal * 0.5 + 0.5, 1.0); // map -1~1 to 0~1

// Display UV as color
return float4(uv.x, uv.y, 0, 1);

// Display depth as color
return float4(depth, depth, depth, 1);

// Display vertex color
return vertexColor;

// Display Mip level (requires DDX/DDY)
float mip = log2(max(length(ddx(uv)), length(ddy(uv))));
return float4(mip/8.0, 0, 0, 1); // deeper red = higher mip
```

---

## Common Issues Checklist

| Symptom | Check Here First |
|---------|----------------|
| Material completely black | Normal direction, light direction, whether NdotL is negative |
| Normal map seam | Tangent calculation method (Maya vs engine), whether texture is set to Linear |
| Colors shifted yellow/green | sRGB/Linear setting, tonemapper influence |
| Transparency artifacts | Depth sorting, whether Depth Write is disabled |
| Shader flickering | Z-fighting (two geometries with very close depth values), precision issue |
| Shadow acne | Shadow bias too small, shadow map resolution insufficient |
| GrabPass stutter (mobile) | Tile-based GPU framebuffer fetch issue |

---

## Learning Resources

- 📖 [RenderDoc Documentation](https://renderdoc.org/docs/)
- 🎥 [RenderDoc Getting Started (search "RenderDoc tutorial" on YouTube)]
- 📖 [NVIDIA Nsight Graphics](https://developer.nvidia.com/nsight-graphics) — deep PC analysis
- 📖 [Xcode GPU Frame Capture](https://developer.apple.com/documentation/metal/gpu_debugger) — Apple platform

---

## Hands-On Exercises (project-ideas)

1. **Complete frame analysis**: Choose one of your own scenes, capture a frame with RenderDoc, and document: (a) total Draw Call count (b) what percentage of total GPU time the Shadow pass takes (c) find the 3 most expensive Draw Calls.
2. **GBuffer visualization tool**: Write a switchable Debug View in Unreal that can display each GBuffer channel live — BaseColor/Normal/Roughness/Metallic/AO.
