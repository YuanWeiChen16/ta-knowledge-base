# Houdini for Games

**穩定性標籤**：`[STABLE]` 概念，`[ENGINE-VERSIONED]` 引擎插件

---

## 為什麼 TA 需要 Houdini

Houdini 是程序生成的業界標準。對 TA 來說，最重要的三個用途：

1. **VAT（Vertex Animation Texture）**：把複雜模擬烘焙成貼圖，在引擎裡零成本播放
2. **程序資產**：可以根據參數生成變化無窮的場景元素
3. **Pipeline 工具**：用 Houdini Engine 把程序系統直接整合進 Unity/Unreal

---

## VAT（Vertex Animation Texture）深入

**原理**：把每個頂點在每一幀的位置/法線/顏色存進貼圖，vertex shader 讀取貼圖還原動畫。

**優點**：
- 零 CPU simulation 成本
- 完美的複雜流體/布料/破碎動畫
- 支援 GPU Instancing（不同時間點播放）

### VAT 類型

| 類型 | 適用 | Houdini SOP |
|------|------|------------|
| **Soft Body** | 布料、旗幟、植被 | Labs VAT Soft Body |
| **Rigid Body** | 破碎、剛體模擬 | Labs VAT Rigid Body |
| **Fluid / Spray** | 水、煙、火焰 | Labs VAT Fluid |
| **動態拓撲** | 粒子群、液態 | Labs VAT Dynamic Remesh |

### VAT 製作流程

```
1. Houdini 做模擬 (RBD / Cloth / Fluid SOP)
2. Labs VAT SOP → 烘焙輸出：
   - Position texture (RGB = XYZ 偏移)
   - Normal texture (法線方向)
   - 輸出 mesh (UV 在 TEXCOORD1，用於 VAT lookup)
3. 匯入引擎
4. 寫 Vertex Shader 讀取貼圖
```

### VAT Vertex Shader 邏輯（偽碼）
```hlsl
// 讀取 VAT Position 貼圖
float frame = _Time * _FPS;  // 當前幀數
float2 vatUV = float2(uv1.x, (frame + 0.5) / _TotalFrames);
float3 posOffset = SampleVATTexture(vatUV) * _BoundsSize + _BoundsMin;
float3 finalPos = basePos + posOffset;
```

---

## Houdini Engine（引擎整合）

把 Houdini 的程序系統直接在 Unity/Unreal 裡使用：

**Unity**：Houdini Engine for Unity Plugin  
**Unreal**：Houdini Engine for Unreal Plugin

**工作流**：
1. Houdini 裡建立一個帶參數的程序系統（HDA — Houdini Digital Asset）
2. 匯入引擎，參數在 Inspector/Details 面板暴露
3. 美術師直接在引擎裡調整參數，Houdini 在後台計算並更新 mesh

**適用情境**：程序地形、程序建築立面、程序植被分布

---

## Houdini for Games 學習路徑

```
Level 1（基礎）：
  → 理解 SOP（Surface Operator）節點流
  → 會用 Geometry VOP 做基礎程序 mesh
  → 了解 Attribute（@P, @N, @Cd, @uv）

Level 2（TA 核心）：
  → Labs VAT 完整工作流
  → Houdini Engine 引擎整合
  → Python SOP 腳本化程序系統

Level 3（進階）：
  → VEX 程序邏輯（Houdini 的 shader-like 語言）
  → FLIP Fluid 模擬
  → Custom HDA 設計給美術師使用
```

---

## 學習資源

- 📖 [SideFX Labs (免費工具包)](https://www.sidefx.com/products/houdini/houdini-labs/) — VAT 工具就在這裡
- 🎥 [Steven Knipping — Applied Houdini](https://www.appliedhoudini.com/) — 遊戲向 Houdini 教學
- 🎥 [SideFX Official YouTube](https://www.youtube.com/c/houdini3d)
- 🎥 [Rebelway Houdini for Games](https://www.rebelway.net/) — 付費但品質高
- 📖 [Houdini VAT 文件](https://www.sidefx.com/tutorials/vertex-animation-textures/)

---

## 實作練習 (project-ideas)

1. **布料 VAT**：用 Houdini Cloth SOP 模擬一面旗幟飄動（60幀），輸出 VAT，在 Unity URP 裡寫 vertex shader 播放。約束：支援 GPU Instancing，5個旗幟同時播放不同時間點，< 0.1ms。
2. **程序岩石 HDA**：建立一個可調整 detail/scale/variation 的程序岩石 HDA，用 Houdini Engine 匯入 Unreal，讓美術師能在場景裡直接調整參數生成不同形狀。
