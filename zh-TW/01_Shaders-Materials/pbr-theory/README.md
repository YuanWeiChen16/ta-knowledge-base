# PBR 物理基礎渲染理論

**穩定性標籤**：`[STABLE]`

---

## 為什麼需要 PBR

傳統 Phong/Blinn-Phong 光照：美術調參數讓東西「看起來好」，但不同光照環境下表現不一致。

PBR（Physically Based Rendering）：基於物理原則，**在任何光照環境下都有一致且正確的行為**。

---

## 核心模型：微面元理論 (Microfacet Theory)

表面在微觀尺度由無數微小完美鏡面組成。宏觀的 Roughness 描述這些微面元的排列分散程度。

```
低 Roughness（光滑）→ 微面元對齊 → 清晰反射
高 Roughness（粗糙）→ 微面元分散 → 模糊漫反射
```

### 關鍵函數

**PBR BRDF** = D × F × G / (4 × NdotL × NdotV)

| 函數 | 名稱 | 描述 |
|------|------|------|
| **D** | Distribution Function | 法線分布，決定高光形狀（GGX 最常用）|
| **F** | Fresnel Term | 掠角反射增強（Schlick 近似）|
| **G** | Geometry Function | 微面元自遮擋（Smith GGX）|

---

## Metallic-Roughness 工作流（業界標準）

### Metallic（金屬度）0-1
- **0 = 非金屬（Dielectric）**：Albedo 有顏色，高光幾乎是白色
- **1 = 金屬（Conductor）**：Albedo 變成高光顏色，幾乎無漫反射
- **中間值**：用於過渡區（生鏽、塗層脫落）

### Roughness（粗糙度）0-1
- **0 = 完全光滑**：鏡面反射
- **1 = 完全粗糙**：完全漫反射
- **感知線性**：通常美術感覺 0.5 在視覺上真的是「中等粗糙」

### Albedo（基礎色）
- 非金屬：反射率 50-240 sRGB（避免純黑或純白）
- 金屬：高光顏色（鐵 = 灰，銅 = 橘，金 = 黃）

---

## Fresnel 效應

掠角（grazing angle）時所有表面反射率增加。這是物理現象，不是風格選擇。

```hlsl
// Schlick Fresnel 近似
float3 F_Schlick(float3 F0, float VdotH) {
    return F0 + (1.0 - F0) * pow(1.0 - VdotH, 5.0);
}
// F0 = 基礎反射率：非金屬約 0.04，金屬用 Albedo
```

---

## IBL（Image Based Lighting）

環境光照不是單一方向光，而是來自整個環境。

- **Diffuse IBL**：Irradiance Map（預計算的環境漫反射，用 SH 或低 mip cubemap）
- **Specular IBL**：Radiance Map（不同 mip = 不同 Roughness 的反射）+ BRDF LUT
- **引擎實作**：Unity Reflection Probe / Unreal Sky Light

---

## 常見 PBR 錯誤

| 錯誤 | 症狀 | 原因 |
|------|------|------|
| Albedo 太黑或太白 | 材質在任何光下都顯得不真實 | 違反能量守恆的 Albedo 值 |
| Metallic 用漸層灰階 | 邊緣區域材質看起來髒 | Metallic 應該是 0 或 1，過渡用 mask |
| Normal map 在 Gamma 空間 | 光照奇怪、法線計算錯誤 | Normal map 必須設為 Linear |
| Roughness 對比太強 | 高光分布不自然 | Roughness 感知非線性時需 perceptual roughness = roughness² |

---

## 學習資源

- 📖 [Substance PBR Guide](https://substance3d.adobe.com/tutorials/courses/the-pbr-guide-part-1) — Adobe/Allegorithmic 出品，業界標準入門
- 📖 [Filament PBR Documentation](https://google.github.io/filament/Filament.html) — Google Filament 引擎的 PBR 完整數學推導，免費
- 🎥 [GDC: Physically Based Shading in Theory and Practice](https://gdcvault.com/play/1024478) — SIGGRAPH 課程免費版
- 📖 [Real-Time Rendering Ch.9](https://www.realtimerendering.com/) — 完整 PBR 數學

---

## 實作練習 (project-ideas)

1. **材質球對比展示**：建立一個場景，同一個球體顯示 5×5 的 Roughness/Metallic 矩陣（0.0, 0.25, 0.5, 0.75, 1.0）。在 Unity 和 Unreal 各做，比較兩個引擎的 PBR 視覺差異。
2. **Fresnel 視覺化**：用純 HLSL 寫一個只顯示 Fresnel term 的 debug shader，確認掠角時反射增強行為正確。約束：要能調整 F0 值。
