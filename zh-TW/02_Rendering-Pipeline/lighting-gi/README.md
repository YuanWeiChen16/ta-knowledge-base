# 光照與全域光照 (GI)

**穩定性標籤**：概念 `[STABLE]`，引擎實作 `[ENGINE-VERSIONED: UE5.4 / Unity 6]`

---

## 光源類型

| 類型 | 描述 | 效能 |
|------|------|------|
| Directional Light | 無限遠平行光（太陽）| 低 |
| Point Light | 球狀範圍光 | 中 |
| Spot Light | 錐形範圍光 | 中 |
| Rect Light（Area）| 矩形面光源 | 高（實時）|
| Sky Light / IBL | 環境光照 | 低（預計算）|

---

## 全域光照（GI）方案比較

### 靜態 Baked GI（最便宜，不能動）
- 場景和光源都是靜態的
- 預計算 Lightmap，烘焙到貼圖
- **Unity**：Progressive Lightmapper
- **Unreal**：CPU/GPU Lightmass
- **適用**：建築、場景背景、靜態環境

### 動態 GI 方案

| 方案 | 引擎 | 原理 | 適用 |
|------|------|------|------|
| **Lumen** | Unreal 5 | 軟體光線追蹤 + SDF | PC/主機動態場景 |
| **RTXGI** | Unreal（RTX）| 硬體光線追蹤 probe | RTX 顯卡 |
| **APV**（Adaptive Probe Volume）| Unity 6 | 空間 probe 插值 | 動態物件 GI |
| **SSGI** | 兩者 | 螢幕空間 GI | 便宜但限螢幕範圍 |
| **LPV**（Light Propagation Volume）| 舊方案 | Voxel 傳播 | 幾乎已棄用 |

---

## Unreal Lumen 深入

**Lumen 原理**：
1. 用 SDF（有向距離場）追蹤光線
2. 在 Screen Space 補充高頻細節
3. 用 Radiance Cache 加速遠距離 GI

**Lumen 設定關鍵**：
```
Post Process Volume:
  Lumen Global Illumination → Scene Detail  (細節 vs 效能)
  Lumen Reflections → Quality              (反射品質)
  
r.Lumen.DiffuseIndirect.Allow 1            // 開啟 Lumen GI
r.Lumen.Reflections.Allow 1               // 開啟 Lumen 反射
```

**Lumen 限制**（TA 必知）：
- 半透明物件不能作為 GI 接收者/發射者
- Nanite mesh 的 WPO 對 Lumen SDF 有延遲
- 極低 Roughness 材質（< 0.1）的反射品質差，需要 Screen Space Reflections 補充

---

## Unity APV（Adaptive Probe Volume）

取代舊的 Light Probe Group，自動填充場景。

```
Hierarchy → 右鍵 → Light → Probe Volume (Auto)
Window → Rendering → Lighting → Probe Volumes Tab
```

**設定要點**：
- Subdivision Level 控制 probe 密度
- Max Subdivision Distance 防止 probe 穿透牆壁
- Dilation 填充 invalid probe（被幾何擋住的）

---

## Reflection 反射系統

| 方案 | 精確度 | 成本 | 動態支援 |
|------|--------|------|---------|
| Cubemap / IBL | 低（靜態）| 極低 | ❌ |
| Reflection Probe | 中 | 低 | 部分 |
| Screen Space Reflections | 高（螢幕內）| 中 | ✅ |
| Lumen Reflections | 高 | 高 | ✅ |
| Ray Traced Reflections | 極高 | 極高 | ✅ |

**TA 策略**：組合使用——IBL 做遠景基底，SSR 做近景高品質，Lumen 做動態物件。

---

## 學習資源

- 🎥 [GDC: Lumen in UE5](https://gdcvault.com/play/1027022) — Epic 官方深度講解
- 📖 [Unity APV Documentation](https://docs.unity3d.com/6000.0/Documentation/Manual/probevolumes.html)
- 📖 [SIGGRAPH: Advances in Real-Time Rendering](https://advances.realtimerendering.com/) — 年度 GI 技術進展

---

## 實作練習 (project-ideas)

1. **GI 方案效能對比**：同一個室內場景，分別測試 Baked Lightmap / Lumen / APV 的 frame time 和視覺品質，製作對比截圖和數據表。
2. **動態光照場景**：設計一個日夜循環場景，只用動態光（不烘焙），在 60fps 目標下調整 Lumen 設定達到可接受品質。記錄最終的設定值和理由。
