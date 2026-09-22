# Lab 01 — 程序水面 Shader

**難度**：⭐⭐ Mid  
**預估時間**：4-8 小時  
**引擎版本**：UE5.4+

---

## 原始發表

- **來源**：80.lv — *"Crafting a Stylized Water Shader in UE5"* (2025)
- **URL**：https://80.lv/articles/how-to-build-stylized-water-shader-design-implementation-for-nimue
- **作者**：Kolja Bopp, Leanna Geideck, Stephan zu Münster（Hamburg University of Applied Sciences）

**閱讀重點**：他們如何用 Single Layer Water、三重 tiling 打破重複感、以及 Vertex Interpolator 優化。

---

## 你的任務

**在 UE5 裡重現一個程序風格化水面，不使用任何第三方外掛。**

### 必須功能
1. **波浪法線**：至少 2 層 panning normal map，方向相反、速度不同
2. **Tiling 打破**：至少 1 種減少貼圖重複的方法（noise 擾動 UV、距離 mask、macro variation）
3. **水深顏色**：Scatter + Absorption 根據水深改變顏色（SceneDepth - PixelDepth）
4. **邊緣泡沫**：用深度差值生成物件相交處的泡沫 mask
5. **WPO 波動**：World Position Offset 在 Z 軸做正弦波位移

### 加分功能
- 互動漣漪（Render Target 記錄角色行走）
- Camera distance LOD（遠處關閉 WPO 和部分 Normal）
- 焦散投影（ColorScaleBehindWater 輸入）

---

## 驗收標準

| 項目 | 標準 |
|------|------|
| 視覺 | 靜止畫面下不明顯看出貼圖重複 |
| 視覺 | 水深淺處顏色有明顯差異 |
| 視覺 | 物件相交處有泡沫線 |
| 效能 | GPU Visualizer 中水面 Pass < 1.5ms（1080p） |
| 技術 | Normal map 設定為 Linear（不是 sRGB） |
| 技術 | WPO 的 sine 計算移到 Vertex Shader（Vertex Interpolator 節點） |

---

## 關鍵問題（做完後回答）

1. Single Layer Water 和普通 Translucent 材質的根本效能差異是什麼？為什麼選前者？
2. 你用了什麼方法打破貼圖重複感？這個方法有什麼成本？
3. 在 UE5.4 中，Single Layer Water + Lumen Reflections 有一個已知 bug，你遇到了嗎？如何處理？
4. WPO 的 sine 波放在 Vertex Shader 而非 Pixel Shader 有什麼好處？代價是什麼？

---

## 需要用到的 UE5 節點 / 功能

```
Single Layer Water 材質 Domain
SceneDepth, PixelDepth        ← 計算水深
ColorScaleBehindWater         ← 焦散、水體顏色
Vertex Interpolator           ← 把計算移到 VS
DepthFade                     ← 邊緣消散
World Position Offset         ← 波動位移
Noise（Voronoi）              ← 焦散貼圖生成
```

---

## 參考截圖對比

做完後截圖，和原文比較：
- 正視角
- 30度斜視
- 物件入水邊緣特寫
- GPU Visualizer 截圖（含水面 pass 時間）
