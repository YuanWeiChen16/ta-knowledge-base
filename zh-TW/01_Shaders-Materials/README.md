# 01 — Shader 與材質系統

**穩定性標籤**：核心概念 `[STABLE]`，引擎實作 `[ENGINE-VERSIONED]`

---

## 這是 TA 技能的核心

Shader 和材質是 TA 日常工作最核心的技能。所有視覺效果、材質表現、VFX 最終都落地在這裡。

---

## 子章節

| 章節 | 何時來查 | 穩定性 |
|------|---------|--------|
| `hlsl-core/` | 寫 shader 程式碼、看不懂 HLSL 語法 | `[STABLE]` |
| `pbr-theory/` | PBR 材質表現不對、理解 roughness/metallic 行為 | `[STABLE]` |
| `unity/` | Unity ShaderGraph、URP/HDRP 材質問題 | `[ENGINE-VERSIONED: Unity 6]` |
| `unreal/` | Unreal Material Editor、材質 instance、HLSL 節點 | `[ENGINE-VERSIONED: UE5.4]` |
| `shader-debugging/` | shader 看起來不對、需要除錯工作流 | `[STABLE]` |

---

## 推薦學習順序（針對新手）

1. `hlsl-core/` — 先理解 HLSL 語法和資料流
2. `pbr-theory/` — 理解物理基礎，再進入引擎實作
3. 選擇你主要的引擎：`unity/` 或 `unreal/`
4. `shader-debugging/` — 越早建立除錯習慣越好

> **注意**：視覺節點（ShaderGraph/Material Editor）和 HLSL 程式碼互補，不是替代。  
> 先用節點理解資料流，再用程式碼做節點做不到的事。
