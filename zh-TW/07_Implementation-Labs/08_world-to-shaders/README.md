# Lab 08 — 世界資訊驅動 Shader（World-Driven Materials）

**難度**：⭐⭐ Mid  
**預估時間**：4-8 小時  
**引擎版本**：UE5.2+

---

## 原始發表

- **來源**：GDC Technical Artist Summit
- **演講標題**：*"Technical Artist Summit: Bringing the World to Your Shaders"*
- **演講者**：Matt Oztalay（Senior Developer Relations Technical Artist, Epic Games）
- **GDC Vault**：https://gdcvault.com/play/1027568/Technical-Artist-Summit-Bringing-the
- **備用連結**：https://gdcvault.com/play/1028009/Technical-Artist-Summit-Bringing-the

**閱讀重點**：如何把場景外部資訊（地形高度、景觀圖層、世界座標）傳入材質驅動動態效果；World Position 投影貼圖的設計模式；以及讓美術師能控制材質 variation 的工具思路。

---

## 你的任務

**在 UE5 裡建立一套「世界感知材質系統」，讓材質能讀取場景資訊自動適應環境。**

### 三個必須實作的功能

#### 功能 A：World Space 貼圖投影
- 用世界座標（World Position）做 UV，而非 mesh 本身的 UV
- 實現：不論 mesh 如何擺放，貼圖永遠對齊世界座標系
- 用途：大型地形細節、跨 mesh 的連續貼圖

#### 功能 B：Landscape 高度驅動材質混合
- 讀取 Landscape Layer Weight（草地、泥土、岩石）
- 用高度值自動在草地/泥土/岩石間混合
- 讓放在地形上的 Mesh 自動匹配周圍材質（RVT — Runtime Virtual Texture）

#### 功能 C：動態 Macro Variation
- 用世界座標採樣一張大尺度 Noise/Variation 貼圖
- 讓同一個材質在不同世界位置有顏色/Roughness 的細微變化
- 消除大面積同材質的「clone stamp」重複感

---

## RVT（Runtime Virtual Texture）快速入門

```
步驟：
1. 場景裡加入 RuntimeVirtualTextureVolume
2. 設定 VT 類型（Base Color + Normal + Roughness）
3. Landscape Material 裡：Write to RVT
4. 放在地形上的 Mesh Material：Read from RVT

效果：Mesh 底部自動混合地形材質，無縫融入環境
```

---

## 驗收標準

| 項目 | 標準 |
|------|------|
| 功能 A | 旋轉/移動 mesh，貼圖保持世界空間對齊 |
| 功能 B | 高度不同的地方，草地/泥土/岩石比例自然變化 |
| 功能 C | 同材質的 100 個物件放在場景中，沒有明顯重複感 |
| 技術 | World Position 計算在 Vertex Shader（Vertex Interpolator）|
| 技術 | RVT 讀寫正確（Mesh 底部有地形材質融合）|

---

## 關鍵問題（做完後回答）

1. World Space UV 投影和 Tri-Planar Mapping 有什麼關係？什麼情況下 Tri-Planar 比單軸投影更好？
2. RVT 把地形資訊「烙印」到 Mesh 上，這個技術的效能成本在哪裡？有什麼限制？
3. Macro Variation 貼圖的理想解析度和 Tiling 比例應該怎麼設定？太密或太疏分別有什麼視覺問題？
4. 你如何確保 World Space 材質在移動平台（TBDR）上不會造成過高的 Texture Bandwidth？

---

## 相關節點

```
World Position              ← 頂點世界座標
Vertex Interpolator         ← 把 WP 計算移到 VS
Runtime Virtual Texture     ← RVT 採樣
Landscape Layer Blend       ← 地形圖層混合
Absolute World Position     ← 不受 pivot 影響的世界座標
```
