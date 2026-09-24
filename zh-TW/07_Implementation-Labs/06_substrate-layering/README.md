# Lab 06 — Substrate 材質分層系統

**難度**：⭐⭐⭐ Senior  
**預估時間**：1-2 天  
**引擎版本**：UE5.3+（需啟用 Substrate）

---

## 原始發表

- **來源**：SIGGRAPH 2023 Advances in Real-Time Rendering
- **論文標題**：*"Substrate: A New Material Shading Framework for Unreal Engine"*
- **PDF**：https://advances.realtimerendering.com/s2023/2023%20Siggraph%20-%20Substrate.pdf
- **作者**：Charles de Rousiers 等（Epic Games）

**閱讀重點**：Slab 的概念（matter building block）、Horizontal Mixing vs Vertical Layering 的語義差異、能量守恆分層、GBuffer packing 架構、Roughness tracking（上層粗糙度影響下層感知粗糙度）。

---

## 你的任務

**用 Substrate 製作一個「雪覆岩石」Master Material，展示 Vertical Layering 的物理正確分層。**

### 必須功能
1. **底層 Slab**：岩石（高 Roughness、無金屬度、有石頭 Normal）
2. **上層 Slab**：積雪（高 Roughness、白色 Albedo、不同 Normal）
3. **Vertical Layering**：用世界法線 Z 分量做 mask（UE 為 Z-up），水平面積雪、垂直面露岩石
4. **Roughness Tracking**：確認啟用後，雪層厚度影響岩石層的感知粗糙度
5. **厚度參數**：暴露 Snow Coverage（0-1）參數，控制積雪覆蓋程度

### 加分功能
- Wetness 參數：模擬濕雪（降低 Roughness、F0 調整）
- 第三層：薄冰層（半透明 Slab）覆蓋在積雪上
- 邊緣磨損遮罩（Curvature map 驅動）

---

## Substrate 核心節點

```
Substrate Slab BSDF
  ├── DiffuseAlbedo    ← 漫射顏色
  ├── F0               ← 基礎反射率（非金屬約 0.04）
  ├── Roughness        ← 粗糙度
  ├── Normal           ← 法線
  └── Thickness        ← 用於 SSS / 透射

Substrate Vertical Layering
  ├── Top (Slab)       ← 上層材質（雪）
  ├── Base (Slab)      ← 下層材質（岩石）
  └── Thickness        ← 上層厚度（影響 Roughness tracking）

Substrate Horizontal Mixing
  ├── A (Slab)
  ├── B (Slab)
  └── Mix (0-1)        ← lerp 兩個材質
```

---

## Vertical Layering vs Horizontal Mixing 差異

| 概念 | Vertical Layering | Horizontal Mixing |
|------|-------------------|-------------------|
| 物理比喻 | 塗層（上層蓋住下層）| 混合（A 和 B 並排）|
| 能量守恆 | ✅ 上層吸收光，下層接收剩餘 | ✅ 按比例混合 |
| Roughness Tracking | ✅（上層粗糙影響下層）| ❌ |
| 適用情境 | 雪/冰/漆/污垢覆蓋 | 材質混合（草地和泥土）|

---

## 世界法線 Mask（積雪分布）

```
// 積雪只出現在接近水平的表面
float3 worldNormal = normalize(TransformTangentVectorToWorld(
    Parameters.TangentToWorld, Normal
));
float snowMask = saturate(worldNormal.z);  // Z+ = 向上
snowMask = pow(snowMask, SnowSharpness);   // 控制邊緣硬度
snowMask *= SnowCoverage;                  // 全局覆蓋量
```

---

## 驗收標準

| 項目 | 標準 |
|------|------|
| 視覺 | 積雪只在水平面，垂直面露出岩石 |
| 視覺 | 積雪和岩石邊界有自然過渡 |
| 視覺 | SnowCoverage 從 0 到 1 變化時，過渡自然 |
| 技術 | 使用 Substrate Vertical Layering，不是手動 lerp |
| 技術 | Roughness Tracking 啟用（Settings 確認）|
| 技術 | 材質在 Lumen 場景下 GI 正確（雪反射更多光）|

---

## 關鍵問題（做完後回答）

1. Substrate Vertical Layering 的 Roughness Tracking 具體做了什麼？為什麼厚度影響底層的感知粗糙度？
2. 同樣的「雪覆岩石」效果，用標準材質 + 手動 lerp 兩套參數能做到嗎？差在哪裡？
3. Substrate 材質的 GBuffer 是怎麼打包的？為什麼複雜材質不會無限增加 GBuffer 大小？
4. Substrate 的成熟度會隨 UE 版本改變（UE5.5 起為 Beta；請查目標版本文件）。在生產環境你會怎麼評估它的使用風險？

---

## 相關資源

- 📄 [SIGGRAPH 2023 Substrate PDF](https://advances.realtimerendering.com/s2023/2023%20Siggraph%20-%20Substrate.pdf)
- 📖 [Unreal Substrate Documentation (UE5.8)](https://dev.epicgames.com/documentation/unreal-engine/substrate-materials-in-unreal-engine)
- 🎥 [GDC 2023 Electric Dreams Demo](https://www.youtube.com/watch?v=iX4SbL0XB00) — Substrate 首次公開展示
