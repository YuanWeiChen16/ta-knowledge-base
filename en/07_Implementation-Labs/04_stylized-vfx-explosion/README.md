# Lab 04 — Stylized VFX Explosion (Niagara)

**Difficulty**: ⭐⭐ Mid  
**Estimated Time**: 4–8 hours  
**Engine Version**: UE5.2+

---

## Original Publication

- **Source**: 80.lv — *"Creating League of Legends-Inspired Explosion VFX With UE5"* (2025)
- **URL**: https://80.lv/articles/creating-league-of-legends-inspired-explosion-vfx-with-ue5/
- **Author**: Laura Legras (VFX Artist)

**Reading focus**: The UberShader design philosophy (one material supporting multiple particle use cases), the three-phase timing design of buildup → launch → impact, and the TwoSidedSign technique.

---

## Your Task

**Create a three-phase stylized explosion VFX in UE5 Niagara with a custom theme (does not need to match the original article).**

### The Three Phases Must Be Clearly Distinguishable
1. **Buildup**: A sense of energy gathering, elements converging toward the center
2. **Impact**: The main explosion, elements bursting outward
3. **Dissipation**: The aftermath, gradually fading out

### Required Technical Specifications
1. **UberShader**: Create one reusable all-purpose particle material containing: Panning texture, Dissolve mask, Distortion
2. **Flipbook**: At least one element uses a flipbook sprite sheet animation
3. **Mesh Particle**: At least one element uses a 3D mesh (not a sprite)
4. **Curve-Driven Timing**: Use Curves to control size/opacity — no linear interpolation

---

## Acceptance Criteria

| Category | Criteria |
|----------|----------|
| Visual | Three phases are clearly distinct, with strong contrast (size, speed, brightness) |
| Visual | No particles "pop" out of existence — all fade out |
| Visual | Overall color palette uses complementary color contrast (apply color theory) |
| Technical | UberShader is shared across 3 or more Emitters in Niagara |
| Technical | Overdraw is reasonable (Niagara Stat shows no more than 3 stacked layers) |
| Technical | Flipbook Player node configured correctly (SubUV rows/columns match the texture) |

---

## UberShader Basic Structure

```
Inputs:
  MainTexture (Texture2D)
  MaskTexture (Texture2D)      ← for Dissolve
  DistortionTexture (Texture2D)
  PanSpeed (Vector2)
  DissolveThreshold (float)    ← 0-1, controls dissolve progress
  EmissiveIntensity (float)

Graph:
  DistortionTexture → distort MainTexture UV
  PanSpeed → MainTexture UV offset
  MaskTexture + DissolveThreshold → Step → Alpha mask
  MainTexture * EmissiveIntensity → Emissive
```

---

## Niagara Timing Control Techniques

```
Buildup (0–0.5s):
  - Particles move inward (Attract to Point module, negative strength)
  - Size grows from 0

Impact (0.5–0.8s):
  - Burst Spawn (large one-shot spawn)
  - Initial velocity bursts outward
  - Hit-stop effect: main particles pause briefly for 0.05s before continuing

Dissipation (0.8–2.0s):
  - Alpha over Life → Fade out
  - Add Drag to decelerate particles
  - Add subtle Turbulence
```

---

## Key Questions (Answer After Completing)

1. What is the purpose of the TwoSidedSign node in VFX? Which types of particles need it most?
2. What is the instruction count of your UberShader? If an art director says "this explosion needs to appear 50 times simultaneously," how would you optimize it?
3. What Niagara module did you use to make particles converge toward the center during the Buildup phase? Are there other approaches?
4. What is the fundamental difference between stylized VFX and realistic VFX in terms of material design?
