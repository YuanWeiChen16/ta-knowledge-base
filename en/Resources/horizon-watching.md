# Emerging Technology Watch

> **`[VOLATILE]` — Review quarterly.** The technologies here evolve rapidly. This doesn't mean they require immediate study — it means they're worth monitoring.
> Last reviewed: 2026 Q3. This is a watchlist, not a complete product support matrix; verify feature, version, and platform support in the linked official documentation.

---

## Neural Rendering

### 3D Gaussian Splatting (3DGS)
- **What it is**: Represents and renders scenes using 3D Gaussians; speed and quality comparisons with NeRF depend on the dataset and implementation, so avoid a single blanket multiplier
- **TA relevance**: Quickly generate visual assets from real-world scans, environment backgrounds
- **Tools**: [Luma AI](https://lumalabs.ai/), [Polycam](https://poly.cam/), [gaussian-splatting repo](https://github.com/graphdeco-inria/gaussian-splatting)
- **Engine support**: Varies by engine version and plugin; verify maintenance, licensing, and platform support before adoption
- **Status**: A candidate for scene capture/reference workflows; validate real-time use with the assets and target hardware

### AI Texture Generation
- **Stable Diffusion + ControlNet**: Generate tileable materials from reference images
- **Adobe Firefly / Substance AI features**: Availability and functionality vary by product version, subscription, and region; check current product documentation
- **Status**: Evaluate as an optional aid; it does not replace procedural material workflows

---

## Upscaling & Frame Generation

| Technology | Vendor | Status |
|------------|--------|--------|
| DLSS | NVIDIA | Upscaling, ray reconstruction, and frame generation vary by DLSS version, GPU, and game integration |
| FSR | AMD | Upscaling and frame generation vary by FSR version, GPU, and game integration |
| XeSS | Intel | Available features and hardware paths vary by version, GPU, and game integration |
| TSR | Epic | Temporal upscaling built into Unreal Engine; quality and cost vary by version, resolution, and settings |

**TA impact**: Upscaling is now a performance budget tool, not just a late-stage optimization. Target resolution must account for this.

---

## Mesh Shaders / Task Shaders

- **What they are**: New pipeline replacing traditional Vertex + Geometry shaders
- **Core advantages**: GPU-side culling and LOD selection, flexible geometry processing
- **Nanite connection**: Nanite uses virtualized geometry and cluster-based processing; it should not be equated with a general mesh-shader pipeline
- **TA relevance**: Understanding meshlet concepts helps you understand Nanite limitations
- **Status**: Engines have adopted this; directly writing mesh shaders is still an advanced topic

---

## Motion Matching (ML-Driven Animation)

- **UE5 native support**: Motion Matching node (UE 5.4+)
- **Principle**: Searches a pose database for the best-matching animation pose for the current state — no hand-crafted state machines required
- **TA relevance**: Setting up pose databases, tuning cost functions, handling blend spaces
- **Unity**: Motion Matching available via third-party plugins (KinematicCharacterController + Motion Matching)
- **Status**: Features and support vary by engine version/plugin; check the target version's documentation

---

## Path Tracing / Full Ray Tracing

- **UE5 Path Tracer**: Supports complete path tracing at offline quality, for screenshots/previews/Cinematics
- **Real-time Path Tracing**: Still in research phase, extremely demanding hardware requirements
- **TA uses**: Validate material physical correctness under path tracer mode
- **Status**: Use Lumen for real-time games, path tracer for reference/screenshots

---

## Substrate Material System (UE5.3+)

- **What it is**: UE5's modular material framework for composing layered BSDFs; it coexists with the existing material system
- **Advantages**: One material can have multiple BSDF layers (skin underlayer + surface oil + rain water)
- **Status**: Beta starting in UE5.5; UE5.8 documentation still labels it Beta. Assess risk for the target version before shipping.
- **Learning resources**: [Unreal Substrate Documentation (UE5.8)](https://dev.epicgames.com/documentation/unreal-engine/substrate-materials-in-unreal-engine) · [UE5.5 Release Notes](https://dev.epicgames.com/documentation/unreal-engine/unreal-engine-5-5-release-notes)

---

## Quarterly Review Protocol

Check this document each quarter (every 3 months):

1. **Check for status changes**: Which technologies have moved from Experimental to Production Ready?
2. **Remove outdated entries**: Move superseded technologies to `deprecated/` for archival
3. **Add new entries**: Update with latest technologies after SIGGRAPH / GDC
4. **Update the "Last updated" date**

> You don't need to track every detail — just know it exists, roughly what it is, and when it's worth going deeper.
