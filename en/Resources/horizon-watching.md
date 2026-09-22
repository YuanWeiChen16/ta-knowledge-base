# Emerging Technology Watch

> **`[VOLATILE]` — Review quarterly.** The technologies here evolve rapidly. This doesn't mean they require immediate study — it means they're worth monitoring.
> Last updated: 2025 Q3

---

## Neural Rendering

### 3D Gaussian Splatting (3DGS)
- **What it is**: Represents scenes using millions of 3D Gaussian ellipsoids — 100× faster than NeRF
- **TA relevance**: Quickly generate visual assets from real-world scans, environment backgrounds
- **Tools**: [Luma AI](https://lumalabs.ai/), [Polycam](https://poly.cam/), [gaussian-splatting repo](https://github.com/graphdeco-inria/gaussian-splatting)
- **Engine support**: Unreal has community plugins, Unity has experimental support
- **Status**: Not yet suitable for real-time game rendering, but already practical as a scene scanning/reference tool

### AI Texture Generation
- **Stable Diffusion + ControlNet**: Generate tileable materials from reference images
- **Adobe Firefly (Substance)**: AI generation integrated inside Substance
- **Status**: Supplementary tool, does not replace Substance Designer workflows

---

## Upscaling & Frame Generation

| Technology | Vendor | Status |
|------------|--------|--------|
| DLSS 3.5 (Frame Generation) | NVIDIA | ✅ Production ready, RTX 40xx |
| FSR 3 (Fluid Motion Frames) | AMD | ✅ Cross-platform |
| XeSS | Intel | ✅ Cross-platform |
| TSR (built into UE5) | Epic | ✅ Production ready |

**TA impact**: Upscaling is now a performance budget tool, not just a late-stage optimization. Target resolution must account for this.

---

## Mesh Shaders / Task Shaders

- **What they are**: New pipeline replacing traditional Vertex + Geometry shaders
- **Core advantages**: GPU-side culling and LOD selection, flexible geometry processing
- **Nanite connection**: Nanite's meshlet system is built on mesh shaders
- **TA relevance**: Understanding meshlet concepts helps you understand Nanite limitations
- **Status**: Engines have adopted this; directly writing mesh shaders is still an advanced topic

---

## Motion Matching (ML-Driven Animation)

- **UE5 native support**: Motion Matching node (UE 5.4+)
- **Principle**: Searches a pose database for the best-matching animation pose for the current state — no hand-crafted state machines required
- **TA relevance**: Setting up pose databases, tuning cost functions, handling blend spaces
- **Unity**: Motion Matching available via third-party plugins (KinematicCharacterController + Motion Matching)
- **Status**: UE5 production ready, Unity still in development

---

## Path Tracing / Full Ray Tracing

- **UE5 Path Tracer**: Supports complete path tracing at offline quality, for screenshots/previews/Cinematics
- **Real-time Path Tracing**: Still in research phase, extremely demanding hardware requirements
- **TA uses**: Validate material physical correctness under path tracer mode
- **Status**: Use Lumen for real-time games, path tracer for reference/screenshots

---

## Substrate Material System (UE5.3+)

- **What it is**: Brand-new material architecture replacing the old Material system, supports true multi-layer materials
- **Advantages**: One material can have multiple BSDF layers (skin underlayer + surface oil + rain water)
- **Status**: UE 5.4 still Experimental; 5.5+ expected to become official
- **Learning resources**: [Unreal Substrate Documentation](https://docs.unrealengine.com/5.4/en-US/substrate-materials-in-unreal-engine/)

---

## Quarterly Review Protocol

Check this document each quarter (every 3 months):

1. **Check for status changes**: Which technologies have moved from Experimental to Production Ready?
2. **Remove outdated entries**: Move superseded technologies to `deprecated/` for archival
3. **Add new entries**: Update with latest technologies after SIGGRAPH / GDC
4. **Update the "Last updated" date**

> You don't need to track every detail — just know it exists, roughly what it is, and when it's worth going deeper.
