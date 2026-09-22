# 08 — 程式師轉 TA 專屬學習路徑

**這個章節專為有程式背景（C++、C#、Python、系統程式）的人設計。**

---

## 你的優勢與補強方向

### 已有的武器
- **數學底子**：矩陣、向量、線性代數不陌生，Shader 數學上手快
- **系統思維**：能看懂 render pass 架構、data flow、pipeline 設計
- **Debug 能力**：能讀 GPU profiler 輸出、理解執行模型、追根究柢
- **自動化直覺**：Python pipeline 腳本對你來說是輕鬆工作
- **抽象能力**：能快速理解新 API（Vulkan、DX12 概念層）

### 需要刻意建立的
- **視覺直覺**：什麼是「好看」、roughness 0.3 對應什麼材質感
- **引擎 UI 肌肉記憶**：Material Editor 節點、Niagara Graph 的操作熟練度
- **美術溝通語言**：把技術成本翻譯成美術聽得懂的語言
- **「夠好」的判斷**：工程師傾向追求正確，TA 需要知道何時「視覺上可接受就夠了」

---

## 子章節

| 章節 | 內容 |
|------|------|
| `shader-math-practice/` | 類 LeetCode 的 Shader 練習資源與題目 |
| `rendering-architecture/` | 類 System Design 的渲染架構深度資源 |
| `visual-intuition/` | 如何刻意訓練視覺直覺（程式師的弱點）|
| `cpp-to-ta-bridge/` | C++/系統程式知識如何直接對應 TA 工作 |

---

## 建議學習順序（8 週計劃）

```
Week 1-2：Shader 數學肌肉
  → The Book of Shaders 全讀（有 code 底子，可快速過）
  → Shadertoy 上選 10 個效果，逐一拆解每行邏輯
  → 目標：能在 Shadertoy 從空白寫出 dissolve + noise 效果

Week 3-4：引擎材質系統（程式師最親切的部分）
  → Lab 08（World-Driven Shader）— 最接近「讀取外部資料」的系統思維
  → Lab 09（純 Shader 粒子）— 最能發揮程式底子的 Lab
  → 讀 UE5 Material Editor 的 Custom 節點，把 HLSL 嵌入引擎

Week 5-6：渲染架構深度（你會愛這個）
  → 讀 "A Trip Through the Graphics Pipeline"（Fabian Giesen）
  → 讀 Jeremy Ong 的 graphics 入門建議
  → Lab 07（MegaLights）同步讀 SIGGRAPH 論文原文

Week 7-8：視覺直覺建立（刻意練習弱點）
  → 每天截一張遊戲截圖，分析光照和材質設定
  → Shadertoy 上只做「美觀」練習（不是技術練習）
  → 找一個你喜歡的遊戲場景，嘗試在 UE5 重現

持續進行：
  → 每週 1 個 Shadertoy 效果拆解
  → 每月讀 1 篇 SIGGRAPH / GDC 論文原文
```
