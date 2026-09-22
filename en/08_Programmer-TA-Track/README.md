# 08 — Learning Path for Programmers Transitioning to TA

**This chapter is designed for people with a programming background (C++, C#, Python, systems programming).**

---

## Your Strengths and Areas to Develop

### Weapons You Already Have
- **Math foundation**: Matrices, vectors, and linear algebra are familiar — shader math clicks fast
- **Systems thinking**: You can read render pass architecture, data flow, and pipeline design
- **Debug instincts**: You can read GPU profiler output, understand execution models, and trace root causes
- **Automation intuition**: Python pipeline scripts are easy work for you
- **Abstraction ability**: You can quickly grasp new APIs (Vulkan, DX12 conceptual layer)

### Things You Need to Deliberately Build
- **Visual intuition**: What does "good-looking" actually mean, and what material feel does roughness 0.3 correspond to?
- **Engine UI muscle memory**: Fluency with Material Editor nodes and Niagara Graph operations
- **Artist communication language**: Translating technical costs into terms artists can understand
- **"Good enough" judgment**: Engineers chase correctness — TAs need to know when "visually acceptable is enough"

---

## Sub-Sections

| Section | Content |
|---------|---------|
| `shader-math-practice/` | LeetCode-style shader practice resources and exercises |
| `rendering-architecture/` | System Design-style deep resources on rendering architecture |
| `visual-intuition/` | How to deliberately train visual intuition (the programmer's weak spot) |
| `cpp-to-ta-bridge/` | How C++ / systems programming knowledge maps directly to TA work |

---

## Recommended Learning Order (8-Week Plan)

```
Week 1-2: Shader Math Muscle
  → Read The Book of Shaders cover to cover (with a code background, you can move fast)
  → Pick 10 effects on Shadertoy, break down the logic line by line
  → Goal: write a dissolve + noise effect from scratch on Shadertoy

Week 3-4: Engine Material Systems (the part most familiar to programmers)
  → Lab 08 (World-Driven Shader) — closest to the "read external data" systems mindset
  → Lab 09 (Pure Shader Particles) — the lab that leverages your programming background most
  → Read UE5 Material Editor's Custom node, embed HLSL directly into the engine

Week 5-6: Rendering Architecture Deep Dive (you'll love this)
  → Read "A Trip Through the Graphics Pipeline" (Fabian Giesen)
  → Read Jeremy Ong's getting-started-in-graphics recommendations
  → Work through Lab 07 (MegaLights) while reading the original SIGGRAPH paper

Week 7-8: Building Visual Intuition (deliberately practicing your weakness)
  → Grab one game screenshot daily and analyze the lighting and material settings
  → Do "beauty" exercises on Shadertoy only (not technical exercises)
  → Pick a game scene you love and try to recreate it in UE5

Ongoing:
  → Break down 1 Shadertoy effect per week
  → Read 1 SIGGRAPH / GDC paper per month
```
