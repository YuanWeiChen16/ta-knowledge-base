# Lab 11 — Nanite 植被 + PCG 程序森林

**難度**：⭐⭐⭐ Senior  
**預估時間**：1-2 天  
**引擎版本**：UE5.2+

---

## 原始發表

- **來源**：GDC 2024 — "Nanite for Artists"
- **演講者**：Epic Games Technical Art Team
- **YouTube**：https://www.youtube.com/watch?v=eoxYceDfKEM
- **相關演講**：GDC 2023 "New Tools for Building Photoreal Worlds in UE5.2"
  - YouTube：https://www.youtube.com/watch?v=dYk7byKHSRw

**閱讀重點**：
- 為什麼 Nanite 樹木的記憶體成本問題（同一棵樹大量 instance 時幾何重複）
- PCG 分解樹木為 Component（Trunk + Branch + Leaf Sprig）
- PCG to Point Data 轉換（把整棵樹的 layout 存成點資料）
- Nanite Foliage 的 Dicing Rate 調整
- Billboard LOD fallback 策略

---

## 你的任務

**用 PCG + Nanite 建立一個程序森林，每棵樹由組件拼接而成，記憶體效率比傳統烘焙樹木低 90%+。**

### 核心洞察（來自演講）

```
傳統做法：
  1棵樹 = 450萬三角形（trunk + branch instances 全部合併）= 150MB on disk
  
PCG 組件做法：
  Trunk mesh     = 2MB
  Branch sprig   = 3MB
  Leaf cluster   = 1MB
  PCG 點資料     = <1MB（只存 Transform + Mesh reference）
  
  整棵樹 = <7MB，比傳統少 95%
  原因：PCG 只存 transform，不重複儲存幾何體
```

---

## 系統架構

### 階段 1：製作樹木組件
```
Trunk mesh      ← 主幹（低面數，Nanite）
Branch mesh     ← 單根樹枝（可重複用）
Leaf Sprig mesh ← 葉片簇（Nanite Masked，小 mesh）

每個組件單獨匯入 UE5，保持小且可複用
```

### 階段 2：PCG 拼組樹木（Level 內）
```
PCG Graph — Tree Assembly:
  Sample Trunk Mesh Surface
    → Get Branch Attachment Points (by vertex color / bone positions)
    → Copy Branch Sprig meshes at attachment points
    → Scatter Leaf Sprigs along branches
    → Filter by Distance from Trunk (近處密，遠處稀)
    → Add Gradient LOD (近 trunk 的 branch 用完整 mesh，遠的用 billboard)
```

### 階段 3：PCG 轉點資料並大量散佈
```
1. 在 Level 裡把整棵 PCG 樹轉成 Point Data：
   右鍵 → Scripted Actions → Convert PCG Level to PCG Settings

2. 建立 Forest Scatter PCG：
   Landscape Sampler (取得地形點)
   → Filter by Slope (坡度 < 30度才長樹)
   → Filter by Height  
   → Copy Points (用剛才的樹 PCG 點資料)
   → Static Mesh Spawner

3. 大規模測試：1km × 1km 區域
```

### 階段 4：Billboard LOD Fallback
```
樹木 LOD 鏈：
  LOD0 (< 20m)：完整 PCG 組件樹
  LOD1 (20-50m)：Trunk + 簡化 Branch
  LOD2 (50m+)：Billboard（2D impostor）

Billboard 設定：
  Nanite → Disallow Nanite for billboard layer
  WPartition Layer Type → Instancing
```

---

## 驗收標準

| 項目 | 標準 |
|------|------|
| 記憶體 | 單棵 PCG 樹的組件總大小 < 10MB（Size on Disk）|
| 視覺 | 1km² 的森林，每棵樹看起來有自然差異 |
| 功能 | 調整 PCG 參數（密度、種類）能即時更新整片森林 |
| LOD | 遠距離自動切換到 Billboard，無明顯突變 |
| 效能 | 1km² 森林在 1080p 跑 60fps（PC mid-range）|
| Nanite | `r.Nanite.Visualize triangles` 顯示近處高密度，遠處低密度 |

---

## Nanite 植被關鍵設定

```
Mesh Import Settings：
  Build Nanite: ✅
  Two-Sided: ✅（葉片需要）

Project Settings → Rendering：
  Nanite Tessellation: ✅（如需位移）

Console：
  r.Nanite.DicingRate 1    ← 預設，最高品質
  r.Nanite.DicingRate 4    ← 降低解析度（效能優化）
  r.Nanite.Visualize overview  ← 總覽
  r.Nanite.Visualize triangles ← 三角形密度視覺化

WPO 設定（風吹）：
  Material → World Position Offset ← 風吹動畫
  Mesh → Nanite Settings → Evaluate WPO: ✅（UE5.2+，有成本）
  Mesh → Nanite Settings → Max WPO Displacement: 50  ← 設合理上限
```

---

## 關鍵問題（做完後回答）

1. 演講提到傳統合併樹木（4.5M tri, 150MB）vs PCG 組件（<7MB）的記憶體差異根本原因是什麼？Nanite 的壓縮在這裡扮演什麼角色？
2. PCG 轉換成 Point Data 後，如果原本的 Branch mesh 更新了，森林會自動更新嗎？這個工作流有什麼迭代陷阱？
3. `r.Nanite.DicingRate` 增加後，Nanite 的哪些視覺特性會降低？什麼情況下你應該調高這個值？
4. Billboard LOD 和 Nanite mesh 之間的視覺切換，有哪些技術可以讓它更平滑（hint：Dithering LOD transition, Cross-Fade LOD）？
5. 這個系統在 **行動裝置** 上能用嗎？Nanite 目前對行動平台的支援狀況是什麼？

---

## 相關資源

- 🎥 [GDC 2024 Nanite for Artists](https://www.youtube.com/watch?v=eoxYceDfKEM)
- 🎥 [GDC 2023 New Tools for Photoreal Worlds](https://www.youtube.com/watch?v=dYk7byKHSRw)
- 📖 [Unreal Nanite Documentation](https://docs.unrealengine.com/5.4/en-US/nanite-virtualized-geometry-in-unreal-engine/)
- 📖 [Unreal PCG Documentation](https://docs.unrealengine.com/5.4/en-US/procedural-content-generation-overview/)
