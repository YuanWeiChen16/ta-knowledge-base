# 行動裝置效能優化

**穩定性標籤**：概念 `[STABLE]`，平台細節 `[VOLATILE]`

---

## Tile-Based GPU 的本質差異（必讀）

行動裝置 GPU（Qualcomm Adreno、ARM Mali、Apple GPU）都是 **TBDR（Tile-Based Deferred Rendering）**架構，和桌機 GPU 根本不同。

```
螢幕分割成 Tile（通常 16×16 或 32×32 像素）
每個 Tile 的所有幾何先收集 → 在晶片上的快取計算完 → 才寫回記憶體
```

**對 TA 的核心影響**：

| 操作 | 桌機成本 | 行動裝置成本 |
|------|---------|-----------|
| Alpha Blend | 中 | 低（在 tile 快取內）|
| Depth Test | 中 | 低（在 tile 快取內）|
| **讀取 framebuffer（GrabPass）** | 中 | **極高**（強制 tile flush）|
| 大量 Draw Calls | 高 | 高（更嚴重）|
| 高 Bandwidth 貼圖 | 中 | **極高**（電量殺手）|

---

## 行動裝置效能黃金規則

### 1. 永遠不用 GrabPass / SceneColor（除非必要）
強制把 tile 裡的資料 flush 到系統記憶體再讀回來。

**替代方案**：
- Depth Fade 代替折射的邊緣混合
- Distortion Pass 代替 GrabPass 折射
- Render Feature 用 copy color pass（有些情況可接受）

### 2. 貼圖頻寬是第一效能殺手
- 每張貼圖都要壓縮（Android：ETC2 / ASTC，iOS：ASTC）
- Mip Map 永遠要開啟（無 Mip = 強制讀最高解析度）
- Texture Atlas 減少 Draw Call 和 texture switch

### 3. 控制 Overdraw
行動裝置的 pixel fill rate 遠低於桌機：
- 半透明物件 < 3層疊加
- Particle 系統的最大尺寸限制（通常螢幕 1/4）
- Early-Z Rejection：確保不透明物件能 early reject

### 4. Draw Call 預算更嚴格
桌機 2000 Draw Call 正常，行動裝置目標 < 200：
- Static Batching
- GPU Instancing
- Atlas Texture + 合併材質

---

## 行動裝置常見效能陷阱

| 陷阱 | 症狀 | 解法 |
|------|------|------|
| Depth Prepass 缺失 | Overdraw 高 | 確保不透明物件有 depth prepass |
| 非壓縮貼圖 | 記憶體暴增、bandwidth 爆 | 所有貼圖強制壓縮格式 |
| 太多 shader variant | 載入時間長、記憶體高 | Shader stripping、減少 keyword |
| 全螢幕後處理疊加 | frame time 暴增 | 行動裝置版本簡化後處理 |
| Realtime Light > 1 | Draw Call 倍增 | 行動裝置通常只用 1 個 directional |

---

## 行動裝置 Profiling 工具

| 工具 | 平台 | 用途 |
|------|------|------|
| **Android GPU Inspector** | Android | 深度 GPU 分析（Adreno/Mali）|
| **Snapdragon Profiler** | Android (Qualcomm) | Adreno 細節分析 |
| **Xcode GPU Frame Capture** | iOS | Apple GPU 完整分析 |
| **Mali Graphics Debugger** | Android (ARM) | Mali GPU 分析 |
| Unity Remote + Profiler | iOS/Android | 基本 Unity Profiler 遠端 |

---

## 學習資源

- 📖 [ARM Mali GPU Best Practices](https://developer.arm.com/documentation/102444/latest/)
- 📖 [Qualcomm Adreno Optimization Guide](https://developer.qualcomm.com/software/adreno-gpu-sdk/gpu)
- 📖 [Apple Metal Best Practices](https://developer.apple.com/documentation/metal/resource_fundamentals/reducing_the_memory_footprint_of_metal_apps)
- 📖 [Unity Mobile Optimization](https://docs.unity3d.com/Manual/MobileOptimizationGraphicsMethods.html)

---

## 實作練習 (project-ideas)

1. **GrabPass 成本測試**：同一個水面效果，分別用 GrabPass 和 Depth Fade 近似，在實際行動裝置（或 Android GPU Inspector）測量兩者的 bandwidth 差異。
2. **Draw Call 優化挑戰**：場景從 > 500 Draw Call 優化到 < 150，不能降低視覺品質超過 10%。記錄每個優化步驟和節省的 Draw Call 數。
