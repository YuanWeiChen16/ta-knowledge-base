# Lab 05 — Niagara Ribbon 拖尾與 Flipbook 水花

**難度**：⭐⭐ Mid  
**預估時間**：4-8 小時  
**引擎版本**：UE5.2+

---

## 原始發表

- **來源**：80.lv — *"Wukong's Spinning Staff Water VFX Powered By UE5's Niagara & LiquiGen"* (2025)
- **URL**：https://80.lv/articles/wukong-s-spinning-staff-water-vfx-powered-by-ue5-s-niagara-liquigen
- **作者**：Vitaliy Lupul（Senior VFX Artist）

**閱讀重點**：Ribbon UV Tiling 設定做模糊拖尾、secondary ribbon 用 masked shader + dithering 防止 overdraw、8×8 flipbook + Motion Vector 貼圖讓低幀數動畫平滑。

---

## 你的任務

**製作一個武器揮擊拖尾 VFX，包含主拖尾、次拖尾和粒子飛濺，套用在 UE5 場景中的移動物件上。**

### 必須功能
1. **主 Ribbon 拖尾**：跟隨武器骨骼，Ribbon Renderer，UV Tiling 設定做視覺模糊感
2. **次 Ribbon（輪廓）**：第二條 Ribbon，用 masked shader，dithering 防止硬邊 overdraw
3. **Flipbook 飛濺**：水花或能量飛濺用 flipbook sprite sheet（至少 4×4），配合 Motion Vector Texture 做亞幀插值
4. **Ribbon 貼圖材質**：自製 ribbon 材質，包含 UV scroll + alpha erosion

### 加分功能
- Ribbon 寬度隨速度變化（速度快 = 拖尾更寬）
- 飛濺粒子數量隨揮擊速度動態調整
- 拖尾顏色隨骨骼位置改變（漸層）

---

## Ribbon Renderer 關鍵設定

```
Niagara → Ribbon Renderer：
  UV Distribution: Stretch (沿 Ribbon 延伸 UV，做 panning 效果)
  UV Tiling Distance: 控制 tile 間距（數值小 = tile 密 = 更多模糊感）
  Facing Mode: Screen (永遠朝向螢幕) 或 Custom (沿骨骼法線)
  
  Tessellation: 提高 ribbon 曲線平滑度（成本++）
  Segments Per Ribbon: 控制細分數量
```

---

## Flipbook Motion Vector 工作流

```
1. 製作 flipbook 動畫（JangaFX EmberGen / Houdini / 手繪）
2. 匯出為 8x8 sprite sheet（64 幀）
3. 同時匯出 Motion Vector texture（記錄每幀像素運動方向）
4. Niagara 材質裡：
   - Flipbook Player 節點（自動計算 SubUV 座標）
   - Motion Vector 採樣，和當前幀/下一幀之間 lerp
   結果：64 幀 flipbook 視覺上接近 128 幀流暢度
```

---

## Dithering 防 Overdraw 技術

```hlsl
// 材質 Graph：
// 1. 計算 Alpha
float alpha = tex2D(SpriteTex, uv).a;

// 2. Dither 矩陣（4x4 Bayer Matrix）
// 用 Custom 節點或 Dither Temporal AA 節點
// 讓半透明邊緣變成點狀 pattern，不做真正 alpha blend

// 3. Clip（Masked blend mode）
clip(alpha - ditherThreshold);

// 效果：視覺上像半透明，實際上是 Masked（不產生 overdraw）
// 代價：近看有 dithering 顆粒感（快速動的 VFX 幾乎看不出來）
```

---

## 驗收標準

| 項目 | 標準 |
|------|------|
| 視覺 | 主拖尾流暢，無明顯 segment 接縫 |
| 視覺 | Flipbook 有 Motion Vector 插值，無明顯幀跳動 |
| 視覺 | 兩條 Ribbon 有層次感（主拖尾 + 次輪廓）|
| 技術 | 次 Ribbon 材質用 Masked + Dithering，不是 Translucent |
| 技術 | Niagara Stat 顯示，Overdraw 層數合理 |
| 效能 | 整體 VFX < 0.5ms（單次觸發）|

---

## 關鍵問題（做完後回答）

1. Ribbon 的 `UV Tiling Distance` 和 `UV Distribution` 這兩個參數各自控制什麼？如何搭配做出模糊流動感？
2. Dithering + Masked 和直接用 Translucent 的效能差異主要來自哪裡？在行動裝置上差異會更大還是更小？
3. Motion Vector Flipbook 如何「插值」兩幀之間？它的限制是什麼（什麼情況下插值效果不好）？
4. Ribbon Tessellation 設太高會有什麼效能問題？如何決定合適的數值？
