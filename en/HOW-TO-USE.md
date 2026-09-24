# TA Knowledge Base — How to Use

> **This is not a course. This is a reference corpus.**
> 
> Come here to find answers when you hit a production problem — don't treat it as a reading list to go through from cover to cover.

---

## What This Folder Is

This is a Technical Artist (TA) knowledge reference library covering:
- TA workflows for **Unity** and **Unreal Engine**
- **Low-level graphics concepts** (Vulkan/DX12 conceptual layer)
- **Performance optimization, VFX, and pipeline automation**

The content has been distilled through three rounds of adversarial review, retaining only genuinely defensible knowledge.

---

## How to Use

### Scenario A: You Have a Specific Problem
1. Identify which conceptual layer your problem belongs to (Shader? Lighting? Performance?)
2. Navigate directly to the corresponding section
3. Read the relevant sections in that README. For performance issues, start with the diagnosis flow in `04_Performance-Profiling/`

### Scenario B: Systematically Strengthening a Domain
1. Start with `SKILL-MATRIX.md` to assess your current state
2. Identify your 1-2 weakest areas
3. Start with the core concepts or learning resources in that README
4. Work through its "Hands-On Exercises" section. `07_Implementation-Labs/` contains full implementation briefs and acceptance criteria

### Scenario C: Just Starting Out in TA
1. Start with `00_Foundations/`, but **don't get stuck here**
2. Simultaneously work hands-on in `01_Shaders-Materials/`
3. When you hit something you don't understand (math or concepts), come back to `00_Foundations/` to look it up

---

## Stability Tag Reference

Sections with version-sensitive material include stability tags. Labs and resource indexes list engine versions or source links separately. Use the version named in a section when following tool instructions:

| Tag | Meaning | Update Frequency |
|-----|---------|-----------------|
| `[STABLE]` | GPU fundamentals, math, PBR theory. Learn once, use forever | Rarely needs updating |
| `[ENGINE-VERSIONED: UE5.x / Unity 6]` | Engine-specific content, check when engine version upgrades | On major engine version upgrades |
| `[VOLATILE]` | ML/neural rendering, upscaling techniques. Rapidly evolving | Quarterly review |

---

## Folder Structure Overview

```
TA-Knowledge/
├── HOW-TO-USE.md              ← You are here
├── SKILL-MATRIX.md            ← Self-assessment table
├── LEARNING-PHILOSOPHY.md     ← Core learning principles
│
├── 00_Foundations/            ← [STABLE] Highest leverage, look up anytime
├── 01_Shaders-Materials/      ← Core TA skills
├── 02_Rendering-Pipeline/     ← Rendering pipeline depth
├── 03_VFX-Systems/            ← VFX and particle systems
├── 04_Performance-Profiling/  ← Performance optimization methodology
├── 05_Pipeline-Tools/         ← Tool development and automation
├── 06_TA-Role-Mindset/        ← TA mindset and career
├── 07_Implementation-Labs/    ← Industry-inspired labs and acceptance criteria
├── 08_Programmer-TA-Track/    ← Learning path for programmers moving into TA
└── Resources/                 ← Curated external resources
```

---

## Key Principle

**The core value of a TA is translation**: translating between artistic intent and engineering cost.  
All knowledge in this library serves that translation ability.
