# 01 — Shaders & Material Systems

**Stability Tag**: Core concepts `[STABLE]`, engine implementation `[ENGINE-VERSIONED]`

---

## This Is the Core of TA Skills

Shaders and materials are the most central skills in a TA's daily work. All visual effects, material expression, and VFX ultimately land here.

---

## Sub-Sections

| Section | When to Look Here | Stability |
|---------|------------------|-----------|
| `hlsl-core/` | Writing shader code, not understanding HLSL syntax | `[STABLE]` |
| `pbr-theory/` | PBR material expression is wrong, understanding roughness/metallic behavior | `[STABLE]` |
| `unity/` | Unity ShaderGraph, URP/HDRP material issues | `[ENGINE-VERSIONED: Unity 6]` |
| `unreal/` | Unreal Material Editor, material instances, HLSL nodes | `[ENGINE-VERSIONED: UE5.4]` |
| `shader-debugging/` | Shader looks wrong, need a debugging workflow | `[STABLE]` |

---

## Recommended Learning Order (For Beginners)

1. `hlsl-core/` — understand HLSL syntax and data flow first
2. `pbr-theory/` — understand the physical foundation, then move to engine implementation
3. Choose your primary engine: `unity/` or `unreal/`
4. `shader-debugging/` — the sooner you build debugging habits, the better

> **Note**: Visual nodes (ShaderGraph/Material Editor) and HLSL code are complementary, not replacements.  
> Use nodes to understand data flow first, then use code to do what nodes can't.
