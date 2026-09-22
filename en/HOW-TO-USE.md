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
3. Check the README's "Diagnostic Guide" and "Common Pitfalls"

### Scenario B: Systematically Strengthening a Domain
1. Start with `SKILL-MATRIX.md` to assess your current state
2. Identify your 1-2 weakest areas
3. Begin with that section's README "Getting Started Path"
4. Work through the hands-on exercises in `project-ideas.md`

### Scenario C: Just Starting Out in TA
1. Start with `00_Foundations/`, but **don't get stuck here**
2. Simultaneously work hands-on in `01_Shaders-Materials/`
3. When you hit something you don't understand (math or concepts), come back to `00_Foundations/` to look it up

---

## Stability Tag Reference

Every section's README has a stability tag:

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
└── Resources/                 ← Curated external resources
```

---

## Key Principle

**The core value of a TA is translation**: translating between artistic intent and engineering cost.  
All knowledge in this library serves that translation ability.
