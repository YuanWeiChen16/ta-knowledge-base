# TA 知識庫 — 使用說明

> **這不是課程。這是參考語料庫。**
> 
> 遇到生產問題時來這裡找答案，而不是把它當閱讀清單從頭讀到尾。

---

## 這個資料夾是什麼

這是一個 Technical Artist (TA) 知識參考庫，涵蓋：
- **Unity** 和 **Unreal Engine** 的 TA 工作流
- **底層圖形概念**（Vulkan/DX12 概念層）
- **效能優化、VFX、Pipeline 自動化**

內容經過三輪對抗式審查蒸餾，保留了真正可辯護的知識。

---

## 如何使用

### 情境 A：遇到具體問題
1. 判斷問題屬於哪個概念層（Shader？Lighting？Performance？）
2. 直接導航到對應章節
3. 查看 README 的「診斷指南」和「常見陷阱」

### 情境 B：系統性補強某個領域
1. 先看 `SKILL-MATRIX.md`，確認你的現狀
2. 找到你最弱的 1-2 個領域
3. 從該章節的 README「入門路徑」開始
4. 做 `project-ideas.md` 裡的實作練習

### 情境 C：剛開始學 TA
1. 從 `00_Foundations/` 開始，但**不要卡在這裡**
2. 同時在 `01_Shaders-Materials/` 動手做
3. 遇到不懂的數學或概念，回 `00_Foundations/` 查

---

## 穩定性標籤說明

每個章節的 README 都有穩定性標籤：

| 標籤 | 含義 | 更新頻率 |
|------|------|---------|
| `[STABLE]` | GPU 基礎、數學、PBR 理論。學一次，用一輩子 | 極少需要更新 |
| `[ENGINE-VERSIONED: UE5.x / Unity 6]` | 引擎特定內容，引擎升版時需檢查 | 引擎大版本升級時 |
| `[VOLATILE]` | ML/神經渲染、升級縮放技術。發展快速 | 季度評估 |

---

## 資料夾結構概覽

```
TA-Knowledge/
├── HOW-TO-USE.md              ← 你在這裡
├── SKILL-MATRIX.md            ← 自我評估表
├── LEARNING-PHILOSOPHY.md     ← 核心學習原則
│
├── 00_Foundations/            ← [STABLE] 最高槓桿，隨查隨用
├── 01_Shaders-Materials/      ← TA 技能核心
├── 02_Rendering-Pipeline/     ← 渲染管線深度
├── 03_VFX-Systems/            ← VFX 與粒子系統
├── 04_Performance-Profiling/  ← 效能優化方法論
├── 05_Pipeline-Tools/         ← 工具開發與自動化
├── 06_TA-Role-Mindset/        ← TA 思維與職涯
└── Resources/                 ← 精選外部資源
```

---

## 重要原則

**TA 的核心價值是翻譯**：在美術意圖和工程成本之間翻譯。  
這個資料庫的所有知識都服務於這個翻譯能力。
