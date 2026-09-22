# Lab 10 — GPU 植被互動系統（Ark: Survival Ascended）

**難度**：⭐⭐⭐⭐ Senior+  
**預估時間**：3-5 天  
**引擎版本**：UE5.1+

---

## 原始發表

- **來源**：GDC 2024 Technical Artist Summit
- **演講標題**：*"Technical Artist Summit: GPU-Based Foliage-Interaction for 'Ark: Survival Ascended'"*
- **演講者**：Studio Wildcard Technical Art Team
- **GDC Vault**：https://www.gdcvault.com/play/1034799/Technical-Artist-Summit-GPU-Based
- **備用（免費版）**：https://www.gdcvault.com/play/1034554/Technical-Artist-Summit-GPU-Based

**閱讀重點**：
- Custom Force-Injection Compute Shader 的架構
- 多個 Volumetric Render Target 的分層設計
- 如何把 Niagara 的力注入到 Render Target
- Nanite + WPO 植被的效能考量
- SpeedTree 9 + Houdini 的資料編碼工作流

---

## 你的任務

**建立一個 GPU 驅動的植被互動系統：玩家走過草地，草會即時彎曲避讓，離開後恢復。**

### 系統架構（簡化版）

```
玩家位置 → Blueprint 傳遞世界座標
    ↓
Render Target（記錄力場）
    ↓
草地材質讀取 RT → World Position Offset 彎曲
    ↓
彈性恢復（隨時間衰減）
```

### 必須功能

1. **Render Target 力場記錄**
   - 用一張 Render Target 記錄場景中的「力」（玩家位置 = 力的來源）
   - RT 格式：R16G16（記錄 XY 方向的力）
   - 每幀用 Blueprint 把玩家位置「繪製」到 RT 上

2. **材質讀取力場**
   - 草地材質讀取 RT 的對應像素
   - 根據力的大小和方向，用 WPO 彎曲草的頂點
   - 彎曲量需要考慮葉片高度（底部固定，頂部最大位移）

3. **彈性恢復**
   - RT 的力要隨時間衰減（玩家離開後草恢復直立）
   - 用 Render Target + Custom Resolve 或 Blueprint 的每幀衰減 pass 實現

4. **多物件支援**
   - 除了玩家，NPC 和大型 VFX 也能注入力
   - 至少支援 3 個同時的力源

---

## Render Target 工作流

```
Blueprint 設定：
1. 建立 Canvas Render Target 2D（解析度 512×512，覆蓋場景 50m×50m 範圍）
2. 每幀在 Event Tick：
   a. 計算玩家在 RT 上的 UV 座標
      UV = (PlayerWorldPos.XY - SceneOrigin) / SceneSize
   b. 用 Draw Material to Render Target 把「衝擊圓」畫到 RT
   c. 再用另一個 Material Pass 做全 RT 的衰減（每幀乘以 0.95）

材質讀取：
3. 草地材質：
   a. 計算當前頂點的世界 UV（同上公式）
   b. Texture Sample from RT
   c. 解碼 RG 通道為 XY 方向力
   d. 力 × 草高比例 → WPO
```

---

## 草地材質 WPO 邏輯

```hlsl
// 讀取力場
float2 sceneUV = (WorldPosition.xy - SceneOrigin) / SceneSize;
float2 force = DecodeForce(SampleRT(ForceRT, sceneUV));

// 高度遮罩（只有上半部分的草會彎曲）
float heightMask = saturate((VertexLocalZ - 0.1) / 0.9);

// 轉換為 WPO
float3 wpo = float3(force.x, force.y, 0) * heightMask * BendIntensity;

// 加入風吹動畫（疊加，不替換）
wpo += WindAnimation(WorldPosition, Time);

return wpo;
```

---

## 驗收標準

| 項目 | 標準 |
|------|------|
| 視覺 | 玩家走過，草明顯彎曲避讓 |
| 視覺 | 玩家離開後，草在 1-2 秒內回復直立 |
| 視覺 | 彎曲方向正確（朝玩家移動方向的反方向）|
| 技術 | 底部頂點不動，頂部頂點位移最大 |
| 技術 | 3 個同時的力源都有效果 |
| 效能 | 整體系統（RT 更新 + 草地渲染）< 2ms |

---

## 關鍵問題（做完後回答）

1. Ark 用的是 Volumetric Render Target（3D），你用的是 2D Render Target。3D 版本有什麼優點？什麼情況下 2D 不夠用（hint：多層地形、樓梯）？
2. RT 衰減用「每幀乘以 0.95」，在 30fps 和 60fps 下的恢復速度會不同。如何讓恢復速度與幀率無關？
3. Nanite 的 WPO 在 UE5.2+ 才支援，且有效能成本。這個系統在大規模森林場景（10,000 棵 Nanite 草）下如何優化？
4. 玩家快速衝刺 vs 緩慢走路，草的彎曲效果應該有什麼不同？你的 RT 設計能支援「速度感知」嗎？

---

## 相關資源

- 🎥 [GDC 2024 演講（免費版）](https://www.gdcvault.com/play/1034554/Technical-Artist-Summit-GPU-Based)
- 📖 [UE5 Render Target 文件](https://docs.unrealengine.com/5.4/en-US/render-targets-in-unreal-engine/)
- 🎥 [Minions Art — Interactive Grass RT Tutorial](https://www.youtube.com/@MinionsArt)（概念近似，Unity 版本但邏輯通用）
