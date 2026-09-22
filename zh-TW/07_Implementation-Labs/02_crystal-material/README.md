# Lab 02 — 水晶多層材質（Substrate）

**難度**：⭐⭐⭐ Mid-Senior  
**預估時間**：1-2 天  
**引擎版本**：UE5.3+（需啟用 Substrate）

---

## 原始發表

- **來源**：80.lv — *"Breakdown: How to Create an Optimized & Realistic Crystal Material"* (2026)
- **URL**：https://80.lv/articles/breakdown-how-to-create-an-optimized-and-realistic-crystal-material
- **作者**：Anastasia Gorban（Technical Lighting Artist）

**閱讀重點**：POM vs Bump Offset 的選擇、Iridescence 的 Sine 週期實作、為什麼用 Substrate 而不是標準材質、以及 instruction count 比較。

---

## 你的任務

**用 Substrate 在 UE5 裡製作一個可切換變體的水晶 Master Material。**

### 必須功能
1. **內部深度假造**：POM（Parallax Occlusion Mapping）或 Bump Offset，讓內部層有視差感
2. **雙層內部顏色**：兩個不同深度的顏色層，各自有 UV 偏移和擾動
3. **Iridescence（虹彩干涉）**：依據視角改變高光顏色（Sine 週期模擬薄膜干涉）
4. **外表面細節**：細微刮痕 Normal + 邊緣磨損 Normal 疊加
5. **內部發光**：Emissive pass 模擬光在水晶內部的散射

### 必須是 Substrate Slab 架構（不是標準 Default Lit）

---

## 啟用 Substrate

```
Project Settings → Rendering → Substrate (Experimental) → 勾選
重啟專案
```

---

## 驗收標準

| 項目 | 標準 |
|------|------|
| 視覺 | 旋轉時 Iridescence 顏色隨視角改變 |
| 視覺 | 水晶內部有明顯的深度層次（視差感）|
| 視覺 | 邊緣有更清晰的高光（Edge Normal）|
| 技術 | Substrate Slab 架構，不是 Standard Surface |
| 技術 | Instruction count 在合理範圍（原文：445-517）|
| 技術 | POM/Bump Offset 有 switch 可切換 |

---

## 關鍵問題（做完後回答）

1. 為什麼 Iridescence 用 `Sine` 函數？改變 Wave Scale 參數視覺上有什麼變化？物理意義是什麼？
2. Substrate 的 Slab BSDF 本身就有 ~326 指令的基礎成本。這個材質用標準 Surface 重現，能省多少指令？代價是什麼？
3. POM 和 Bump Offset 的視覺差異在什麼情況下最明顯？效能差異大嗎？
4. 在 Lumen Path Tracer 下，水晶的半透明效果和 Raster 模式下有什麼不同？

---

## Substrate 入門

```
材質圖裡搜尋 "Substrate" 節點：
  Substrate Slab BSDF  ← 主要 shading node
  Substrate Horizontal Mixing  ← 水平混合兩個材質
  Substrate Vertical Layering  ← 垂直分層（塗層效果）
  
輸出到 Substrate Material 而非舊的 Material Output
```

---

## 相關文件

- 📖 [Unreal Substrate Docs](https://docs.unrealengine.com/5.4/en-US/substrate-materials-in-unreal-engine/)
- 📄 [SIGGRAPH 2023 Substrate Paper](https://advances.realtimerendering.com/s2023/2023%20Siggraph%20-%20Substrate.pdf)
