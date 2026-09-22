# Lab 02 — Crystal Multi-Layer Material (Substrate)

**Difficulty**: ⭐⭐⭐ Mid-Senior  
**Estimated Time**: 1–2 days  
**Engine Version**: UE5.3+ (Substrate must be enabled)

---

## Original Publication

- **Source**: 80.lv — *"Breakdown: How to Create an Optimized & Realistic Crystal Material"* (2026)
- **URL**: https://80.lv/articles/breakdown-how-to-create-an-optimized-and-realistic-crystal-material
- **Author**: Anastasia Gorban (Technical Lighting Artist)

**Reading focus**: The choice between POM vs Bump Offset, the Sine cycle implementation for Iridescence, why Substrate is used instead of the standard material, and instruction count comparisons.

---

## Your Task

**Create a switchable-variant crystal Master Material in UE5 using Substrate.**

### Required Features
1. **Fake Interior Depth**: POM (Parallax Occlusion Mapping) or Bump Offset to give the interior layers a sense of parallax
2. **Dual Interior Color Layers**: Two color layers at different depths, each with UV offset and distortion
3. **Iridescence (Thin-Film Interference)**: High light color that shifts based on view angle (Sine cycle simulating thin-film interference)
4. **Outer Surface Detail**: Subtle scratch Normal + edge wear Normal stacked together
5. **Interior Glow**: Emissive pass simulating light scattering inside the crystal

### Must Use Substrate Slab Architecture (not standard Default Lit)

---

## Enabling Substrate

```
Project Settings → Rendering → Substrate (Experimental) → Enable checkbox
Restart project
```

---

## Acceptance Criteria

| Category | Criteria |
|----------|----------|
| Visual | Iridescence color shifts with view angle when rotating |
| Visual | Crystal interior has clear depth layering (parallax feel) |
| Visual | Edges have sharper highlights (Edge Normal) |
| Technical | Substrate Slab architecture, not Standard Surface |
| Technical | Instruction count within reasonable range (reference article: 445–517) |
| Technical | POM/Bump Offset has a switch to toggle between them |

---

## Key Questions (Answer After Completing)

1. Why does Iridescence use a `Sine` function? What visual change does altering the Wave Scale parameter produce? What is the physical meaning?
2. The Substrate Slab BSDF has a base cost of ~326 instructions. How many instructions could you save by recreating this material with standard Surface? What would be the trade-off?
3. In what conditions is the visual difference between POM and Bump Offset most apparent? Is there a significant performance difference?
4. Under the Lumen Path Tracer, how does the crystal's translucent effect differ from Raster mode?

---

## Substrate Primer

```
Search for "Substrate" nodes in the material graph:
  Substrate Slab BSDF         ← primary shading node
  Substrate Horizontal Mixing ← horizontally blend two materials
  Substrate Vertical Layering ← vertical layering (coating effect)
  
Output to Substrate Material instead of the legacy Material Output
```

---

## Related Documentation

- 📖 [Unreal Substrate Docs](https://docs.unrealengine.com/5.4/en-US/substrate-materials-in-unreal-engine/)
- 📄 [SIGGRAPH 2023 Substrate Paper](https://advances.realtimerendering.com/s2023/2023%20Siggraph%20-%20Substrate.pdf)
