# Unreal Engine 材質系統

**穩定性標籤**：`[ENGINE-VERSIONED: UE5.4]`

---

## Material Editor 核心概念

### Material Domain
| Domain | 用途 |
|--------|------|
| Surface | 標準物件表面（最常用）|
| Deferred Decal | 貼花效果 |
| Light Function | 光源 Mask |
| Post Process | 後處理效果 |
| UI | HUD/介面 |

### Blend Mode
| Mode | 特性 | 效能 |
|------|------|------|
| Opaque | 不透明，最快 | ✅ 最佳 |
| Masked | 遮罩透明（clip），支援 Nanite | ✅ 好 |
| Translucent | 半透明，不支援大多數 GI | ⚠️ 貴 |
| Additive | 疊加混合（適合 VFX）| ⚠️ 貴 |

---

## Material Instance（最重要的工作流）

不要直接修改 Parent Material。建立 Material Instance：

```
右鍵 Material → Create Material Instance
```

**為什麼：**
- Parent Material 修改 → 所有 Instance 自動更新
- Instance 切換幾乎無成本（同一 shader variant）
- 美術師可以安全地調整 Instance 參數而不改 shader

**Material Instance Dynamic (MID)**：
```cpp
// Blueprint
UMaterialInstanceDynamic* MID = UMaterialInstanceDynamic::Create(Material, this);
MID->SetScalarParameterValue("Roughness", 0.5f);
MID->SetTextureParameterValue("BaseMap", MyTexture);
```

---

## Custom HLSL 節點

在 Material Editor 加入 Custom 節點，寫原生 HLSL：

```
Material Graph → 右鍵 → Custom
```

```hlsl
// Custom 節點範例：Voronoi 函數
float2 uv = Inputs[0].xy;  // Input 0
float cellSize = Inputs[1]; // Input 1

// 計算 Voronoi...
float2 cell = floor(uv / cellSize);
float minDist = 1.0;
// [邏輯...]
return minDist;
```

**限制：**
- Custom 節點不能定義函數，只能是 inline 程式碼
- 複雜邏輯建議用 `.ush` include 檔

---

## Nanite 相容性注意事項

Nanite（UE5 的虛擬幾何技術）有材質限制：

| 功能 | Nanite 支援 |
|------|-----------|
| Opaque | ✅ 完全支援 |
| Masked（硬邊 clip）| ✅ 支援（UE 5.1+）|
| Translucent | ❌ 不支援 |
| World Position Offset (WPO) | ✅ 支援（UE 5.2+，有效能成本）|
| Pixel Depth Offset | ❌ 不支援 |
| Two-Sided | ✅ 支援 |

**TA 黃金規則**：植被使用 WPO 風吹 + Masked 時，評估是否值得 Nanite 的 WPO 成本。

---

## Lumen 材質考量

Lumen（UE5 軟體光線追蹤 GI）對材質的影響：

- **Emissive 材質**：可以作為 Lumen 光源（需要 "Use Emissive for Static Lighting" 打勾）
- **Roughness 極低的材質**：Lumen 反射質量下降（改用 Screen Space Reflections 補充）
- **高 Roughness（>0.4）的金屬**：Lumen 表現最好

---

## Material Function（材質函數庫）

可重複使用的材質邏輯片段：

```
Content Browser → 右鍵 → Materials → Material Function
```

TA 工作流：把常用的邏輯（Tri-planar mapping、Rain wetness、Edge wear）封裝成 Material Function，供全專案複用。

---

## 常用內建材質函數

```
MF_Triplanar               // 三軸投影貼圖
MF_VertexInterpolator      // 把計算移到 Vertex Shader
MF_Desaturate              // 去飽和度
MF_HeightBlend             // 高度混合兩個材質
MF_CheapContrast           // 廉價對比度增強
```

---

## 學習資源

- 📖 [Unreal Engine Material Documentation](https://docs.unrealengine.com/5.4/en-US/unreal-engine-materials/)
- 🎥 [Ben Cloward — Unreal Shader Tutorials](https://www.youtube.com/c/BenCloward)
- 🎥 [William Faucher YouTube](https://www.youtube.com/@WilliamFaucher) — UE5 材質深度教學
- 📖 [Unreal Source: Engine/Shaders/](https://github.com/EpicGames/UnrealEngine) — 引擎 shader 源碼（需申請）

---

## 實作練習 (project-ideas)

1. **Substrate 多層材質**（UE5.3+）：用 Substrate 材質系統製作一個「雪覆蓋岩石」材質，底層岩石 + 頂層雪，根據世界法線方向混合。約束：支援 Nanite，不用 WPO。
2. **Material Function 庫**：建立 3 個可複用的 Material Function：(a) 程序邊緣磨損 (b) 雨水濕潤表面 (c) 程序 AO 假造。每個函數要有清楚的參數說明 comment。
