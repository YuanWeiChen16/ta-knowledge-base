# Lab 11 — Nanite 植被 + PCG 程序森林

**難度**：⭐⭐⭐ Senior  
**預估時間**：1-2 天  
**引擎版本**：UE5.2+（歷史工作流；PCG 節點名稱與操作依版本變動）

---

## 原始發表

- **來源**：GDC 2024 — "Nanite for Artists"
- **演講者**：Epic Games Technical Art Team
- **YouTube**：https://www.youtube.com/watch?v=eoxYceDfKEM
- **相關演講**：GDC 2023 "New Tools for Building Photoreal Worlds in UE5.2"
  - YouTube：https://www.youtube.com/watch?v=dYk7byKHSRw

> 本 Lab 記錄 GDC 2024 的 PCG 組件拼樹與傳統 LOD/billboard 思路，不等同 UE5.8 的 Nanite Foliage 功能。UE5.8 Nanite Foliage 為 Experimental，採用 Nanite Assemblies、Voxels 與 Skinning 等系統；正式使用前請查目標版本文件並自行量測。

**閱讀重點**：
- 為什麼 Nanite 樹木的記憶體成本問題（同一棵樹大量 instance 時幾何重複）
- PCG 分解樹木為 Component（Trunk + Branch + Leaf Sprig）
- PCG to Point Data 轉換（把整棵樹的 layout 存成點資料）
- 以 PCG 組件拼接與散佈樹木
- 傳統 LOD/billboard fallback 的取捨（不是目前 Nanite Foliage 的遠距離方案）

---

## 你的任務

**用 PCG + Nanite 建立一個程序森林，比較組件拼接與傳統樹木資產的磁碟大小、串流記憶體、GPU/CPU 時間和畫面品質。**

### 核心洞察（以目標版本與資產量測）

演講或網路範例中的資產大小不能直接當作通用基準。用相同內容、引擎版本、品質設定與平台比較兩種方案；分開記錄磁碟大小、載入/串流記憶體、CPU/GPU 時間與視覺差異。PCG 是否重用幾何也取決於輸出資料與資產引用方式。

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

此階段保留傳統 billboard LOD 作為比較方案；UE5.8 Nanite Foliage 的遠距離幾何流程不同，請勿把此步驟當成其設定方式。
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
| 記憶體 | 記錄資產磁碟大小與執行時串流記憶體；不套用固定門檻 |
| 視覺 | 在已記錄的目標區域與視距下，樹木具有自然差異 |
| 功能 | 調整 PCG 參數（密度、種類）能即時更新整片森林 |
| LOD | 記錄所選 LOD/billboard 策略及切換瑕疵；若使用 Nanite Foliage，依該版本文件驗證其幾何轉換 |
| 效能 | 先定義硬體、解析度與目標幀率，再提供可重現的 CPU/GPU 數據 |
| Nanite | 使用目標 UE 版本提供的 Nanite Visualization 檢查密度與成本 |

---

## Nanite 植被關鍵設定

```
Mesh Import Settings：
  Build Nanite: ✅
  Two-Sided: ✅（葉片需要）

使用目標 UE 版本的 Nanite Visualization 檢查幾何密度與渲染狀態；設定名稱、可用檢視模式及位移支援依版本而異。

WPO 設定（風吹）：
  Material → World Position Offset ← 風吹動畫
  Mesh → Nanite Settings → Evaluate WPO: ✅（UE5.2+，有成本）
  Mesh → Nanite Settings → Max WPO Displacement: 50  ← 設合理上限
```

---

## 關鍵問題（做完後回答）

1. 你的兩種樹木資產在磁碟大小與串流記憶體上差多少？幾何重用、Nanite 壓縮和 PCG 輸出各自有什麼影響？
2. PCG 轉換成 Point Data 後，如果原本的 Branch mesh 更新了，森林會自動更新嗎？這個工作流有什麼迭代陷阱？
3. 用 Nanite Visualization 觀察不同距離下的幾何與 overdraw；哪些資產設定或樹葉材質對畫面和效能影響最大？
4. Billboard LOD 和 Nanite mesh 之間的視覺切換，有哪些技術可以讓它更平滑（hint：Dithering LOD transition, Cross-Fade LOD）？
5. 這個系統在 **行動裝置** 上能用嗎？Nanite 目前對行動平台的支援狀況是什麼？

---

## 相關資源

- 🎥 [GDC 2024 Nanite for Artists](https://www.youtube.com/watch?v=eoxYceDfKEM)
- 🎥 [GDC 2023 New Tools for Photoreal Worlds](https://www.youtube.com/watch?v=dYk7byKHSRw)
- 📖 [Unreal Nanite Documentation](https://docs.unrealengine.com/5.4/en-US/nanite-virtualized-geometry-in-unreal-engine/)
- 📖 [Unreal PCG Documentation](https://docs.unrealengine.com/5.4/en-US/procedural-content-generation-overview/)
- 📖 [Nanite Foliage (UE5.8, Experimental)](https://dev.epicgames.com/documentation/unreal-engine/nanite-foliage)
