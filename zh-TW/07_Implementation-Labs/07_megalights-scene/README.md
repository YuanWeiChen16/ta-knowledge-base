# Lab 07 — MegaLights 大量動態光源場景

**難度**：⭐⭐⭐⭐ Senior+  
**預估時間**：3-5 天  
**引擎版本**：UE5.5+（功能狀態與設定依版本變動；UE5.5 為 Experimental，開始前查閱目標版本文件）

---

## 原始發表

- **來源**：SIGGRAPH 2025 Advances in Real-Time Rendering
- **論文標題**：*"MegaLights: Stochastic Direct Lighting in Unreal Engine 5"*
- **PDF**：https://advances.realtimerendering.com/s2025/content/MegaLights_Stochastic_Direct_Lighting_2025.pdf
- **YouTube**：https://www.youtube.com/watch?v=dmmN8_c8Tb0
- **作者**：Krzysztof Narkowicz, Tiago Costa（Epic Games）

**閱讀重點**：Weighted Reservoir Sampling 光源選擇原理、為什麼 MegaLights 比傳統 Shadow Map 方案更適合大量光源、Area Light Guiding（2×2 bitmask）如何減少無效 ray、以及 Tile Classification 如何降低 register pressure。

> MegaLights 面向大量動態光源，但不保證所有場景都更快。UE5.5 文件要求 Hardware Ray Tracing 與 SM6；請依目標 UE 版本確認需求與可用光源/陰影設定。

---

## 你的任務

**設計並建立一個展示 MegaLights 威力的場景，量化傳統方案 vs MegaLights 的視覺和效能差異。**

### 場景設計要求
一個室內場景（如工廠、地下城、霓虹街道），包含：
- **至少 50 個動態點光源 / 區域光源**（用來比較不同光源數量下的成本；不要預設傳統方案必定無法使用）
- **混合光源類型**：Point Light、Spot Light、Rect Light 各至少 5 個
- **大量陰影要求**：所有光源都有動態陰影（這正是 MegaLights 的設計目標）
- **動態元素**：場景中有移動物件（NPC、旋轉機械等），驗證動態陰影正確性

---

## 三階段實驗流程

### 階段 1：基準線（傳統方案）

在 Project Settings → Rendering → Direct Lighting 關閉 MegaLights，並確認 Post Process Volume 覆寫未重新啟用。記錄：

- 光源數量 vs GPU 時間（從 5 個開始，每次 +5，記到 50 個）
- GPU Visualizer 截圖（Shadow Depths Pass 時間）
- 視覺品質截圖

### 階段 2：啟用 MegaLights
在 Project Settings → Rendering → Direct Lighting 啟用 MegaLights；記錄專案、光源與 Post Process Volume 設定。

記錄相同指標，與階段 1 對比。

### 階段 3：MegaLights 品質調優
依目標 UE 版本可用的專案、Post Process Volume 與 Light Component 設定調整品質；每次只改一項，記錄設定名稱、GPU 時間與噪點截圖。不要假設各版本都有相同的 Console Variable。

---

## TA 視角的技術分析

做完實驗後，需要回答以下 TA 實際工作中會遇到的問題：

### 材質設定影響
- 極低 Roughness 材質（< 0.05）的陰影品質如何？
- Masked 材質（植被、欄杆）的陰影品質如何？需要 fallback 到 Shadow Map 嗎？
- Substrate 多層材質在 MegaLights 下表現如何？

### Nanite 整合
- Nanite mesh 在 MegaLights 的 ray tracing 中使用 proxy mesh，視覺誤差有多大？
- 什麼幾何體最容易出現 proxy mismatch？

### 效能預算制定
```
根據你的實驗數據，填寫：

| 光源數量 | 傳統陰影 GPU 時間 | MegaLights GPU 時間 | 品質比較 |
|---------|----------------|-------------------|---------|
| 10 | ___ms | ___ms | |
| 25 | ___ms | ___ms | |
| 50 | ___ms | ___ms | |
| 100 | ___ms | ___ms | |
```

---

## 驗收標準

| 項目 | 標準 |
|------|------|
| 場景 | 至少 50 個動態帶陰影光源 |
| 場景 | 3 種以上光源類型 |
| 實驗 | 完整的傳統 vs MegaLights 效能對比數據表 |
| 分析 | 識別出 MegaLights 在你的場景中的限制（至少 2 個）|
| 技術 | 知道如何用 `r.VisualizeBuffer` 查看 MegaLights 中間 buffer |

---

## 關鍵問題（做完後回答）

1. MegaLights 用 Weighted Reservoir Sampling 選擇要採樣的光源。「Logarithmic perceptual weighting」是什麼？為什麼用 log 而不是線性權重？
2. 你的場景中 50 個光源的情況下，MegaLights 比傳統方案快多少倍？在什麼條件下傳統方案反而更快？
3. 論文提到 Directional Light 需要特殊處理（限制 sample budget）。你在測試場景中有 Directional Light 嗎？它的 shadow 品質如何？
4. MegaLights 是否適合行動裝置？從論文的設計假設推斷你的答案。
5. 如果你的 Art Director 說「50 個光源的場景陰影太噪了」，你有哪些旋鈕可以調？各自的效能代價是什麼？

---

## 效能分析工具

```
stat GPU
ProfileGPU
```
MegaLights 的開關、陰影方法與可用品質設定請使用目標 UE 版本文件列出的 Project Settings、Post Process Volume 和 Light Component 選項；不要假設跨版本 Console Variable 相同。

---

## 相關資源

- 📄 [SIGGRAPH 2025 MegaLights PDF](https://advances.realtimerendering.com/s2025/content/MegaLights_Stochastic_Direct_Lighting_2025.pdf)
- 🎥 [SIGGRAPH 2025 MegaLights 演講影片](https://www.youtube.com/watch?v=dmmN8_c8Tb0)
- 📖 [Unreal MegaLights Documentation (UE5.8)](https://dev.epicgames.com/documentation/unreal-engine/megalights-in-unreal-engine)
- 📖 [Unreal Engine 5.5 Release Notes](https://dev.epicgames.com/documentation/unreal-engine/unreal-engine-5-5-release-notes)
- 📄 [ReSTIR 論文（MegaLights 的對比方案）](https://research.nvidia.com/publication/2020-07_spatiotemporal-reservoir-resampling-real-time-ray-tracing-dynamic-direct)
