# Unreal PCG Framework

**穩定性標籤**：`[ENGINE-VERSIONED: UE5.4]`

---

## PCG 是什麼

PCG（Procedural Content Generation）Framework 是 UE5.2+ 內建的程序生成系統，讓 TA 能在 Editor 和運行時動態生成場景內容。

---

## PCG 核心架構

```
PCG Graph：節點圖，定義生成邏輯
  Input Nodes:
    Surface Sampler      ← 在表面上採樣點
    Landscape Sampler    ← 在地形上採樣點
    Spline Sampler       ← 沿樣條線採樣點
    Volume Sampler       ← 在體積內採樣點

  Filter Nodes:
    Filter by Slope      ← 根據斜率過濾（岩石只長在陡坡）
    Filter by Density    ← 密度過濾
    Difference           ← 排除重疊區域（道路上不長草）
    Intersection         ← 只保留重疊區域

  Transform Nodes:
    Transform Points     ← 隨機旋轉/縮放
    Project Points       ← 投影到表面（讓物件貼合地形）
    Copy Points          ← 複製點資料

  Output Nodes:
    Static Mesh Spawner  ← 生成 Static Mesh
    Actor Spawner        ← 生成 Actor
    Spline Mesh Spawner  ← 沿樣條生成 Mesh
```

---

## 基本工作流

### 場景植被生成
```
Landscape Sampler
  → Filter by Slope (0-30度 = 平地 = 草地)
  → Filter by Height (海拔限制)
  → Add Random Offset (隨機位置抖動)
  → Transform Points (隨機旋轉Y軸，隨機縮放 0.8-1.2)
  → Project to Terrain (貼合地形)
  → Static Mesh Spawner (草/小花/蘑菇)
```

### 沿路徑生成欄杆
```
Spline Input
  → Sample Spline (間距 = 欄杆寬度)
  → Align to Spline Direction (對齊方向)
  → Static Mesh Spawner (欄杆 mesh)
```

---

## PCG 屬性系統

每個 Point 有 Attributes，可以在節點圖中傳遞和修改：

```
內建 Attributes：
  Position (float3)    ← 世界座標
  Rotation (rotator)  ← 旋轉
  Scale (float3)       ← 縮放
  Density (float)      ← 密度（0-1，用於過濾）
  Color (FLinearColor) ← 顏色（可傳給 Mesh）

自訂 Attributes：
  Add Attribute → 定義自己的屬性
  例：Slope, BiomeType, WetnessFactor
  這些可以傳給 Material Parameter（動態材質變化）
```

---

## PCG vs Foliage Tool

| | PCG | Foliage Tool |
|--|-----|-------------|
| 更新 | 動態（參數改→立即重算）| 靜態（手動繪製）|
| 規則 | 完整程序邏輯 | 簡單筆刷密度 |
| 記憶體 | 可以不儲存（運行時生成）| 永久儲存 |
| 適合 | 程序生成的自然場景 | 手工精細控制的場景 |

---

## 學習資源

- 📖 [Unreal PCG Documentation](https://docs.unrealengine.com/5.4/en-US/procedural-content-generation-overview/)
- 🎥 [PCG Framework Tutorial (Official)](https://www.youtube.com/watch?v=Ol5Z9Tpz5gQ)
- 🎥 [Unreal Sensei — PCG Tutorials](https://www.youtube.com/@UnrealSensei)

---

## 實作練習 (project-ideas)

1. **程序森林場景**：用 PCG 在地形上生成樹木/岩石/草地，規則：樹木只長在坡度 < 25度，岩石只長在坡度 > 30度，道路 3m 範圍內無植被。約束：1km² 區域要能即時生成，不能影響遊戲執行效能。
