# Lab 04 — 風格化 VFX 爆炸（Niagara）

**難度**：⭐⭐ Mid  
**預估時間**：4-8 小時  
**引擎版本**：UE5.2+

---

## 原始發表

- **來源**：80.lv — *"Creating League of Legends-Inspired Explosion VFX With UE5"* (2025)
- **URL**：https://80.lv/articles/creating-league-of-legends-inspired-explosion-vfx-with-ue5/
- **作者**：Laura Legras（VFX Artist）

**閱讀重點**：UberShader 設計理念（一個材質支援多種粒子用途）、buildup → launch → impact 三段式節奏設計、TwoSidedSign 技巧。

---

## 你的任務

**在 UE5 Niagara 裡製作一個三段式風格化爆炸 VFX，自訂主題（不需要跟原文一樣）。**

### 三段必須清楚可辨
1. **Buildup（蓄力）**：能量聚集感，元素向中心匯聚
2. **Impact（爆發）**：主要爆炸，元素向外擴散
3. **Dissipation（消散）**：餘韻，逐漸淡出

### 必須技術要求
1. **UberShader**：製作一個可重複使用的萬用粒子材質，包含：Panning texture、Dissolve mask、Distortion
2. **Flipbook**：至少一個元素使用 flipbook sprite sheet 動畫
3. **Mesh Particle**：至少一個元素使用 3D mesh（而非 sprite）
4. **曲線控制節奏**：用 Curve 控制大小/透明度，不要用線性插值

---

## 驗收標準

| 項目 | 標準 |
|------|------|
| 視覺 | 三段節奏清楚，有明顯的 contrast（大小、速度、明暗）|
| 視覺 | 沒有粒子「突然消失」，都有淡出 |
| 視覺 | 整體顏色有互補色對比（參考色彩理論）|
| 技術 | UberShader 在 Niagara 的 3 個以上 Emitter 中共用 |
| 技術 | Overdraw 合理（Niagara Stat 顯示，不超過 3 層疊加）|
| 技術 | Flipbook Player 節點設定正確（SubUV 行列數對應貼圖）|

---

## UberShader 基本結構

```
Inputs:
  MainTexture (Texture2D)
  MaskTexture (Texture2D)      ← Dissolve 用
  DistortionTexture (Texture2D)
  PanSpeed (Vector2)
  DissolveThreshold (float)    ← 0-1，控制溶解進度
  EmissiveIntensity (float)

Graph:
  DistortionTexture → 擾動 MainTexture 的 UV
  PanSpeed → MainTexture UV offset
  MaskTexture + DissolveThreshold → Step → Alpha mask
  MainTexture * EmissiveIntensity → Emissive
```

---

## Niagara 節奏控制技巧

```
Buildup（0-0.5s）：
  - 粒子向內移動（Attract to Point 模組，負 strength）
  - 大小從 0 漸增

Impact（0.5-0.8s）：
  - Burst Spawn（一次性大量生成）
  - 初速度向外爆發
  - Hit-stop 效果：主要粒子短暫停留 0.05s 再繼續

Dissipation（0.8-2.0s）：
  - Alpha over Life → Fade out
  - 加入 Drag 讓粒子減速
  - 加入小幅度 Turbulence
```

---

## 關鍵問題（做完後回答）

1. TwoSidedSign 節點在 VFX 裡的用途是什麼？哪類粒子最需要用它？
2. 你的 UberShader 的 instruction count 是多少？如果美術說「這個爆炸要同時出現 50 個」，你會怎麼優化？
3. Buildup 階段讓粒子向中心聚集，你用了什麼 Niagara 模組？有沒有其他方法？
4. 風格化 VFX 和寫實 VFX 在材質設計上的根本差異是什麼？
