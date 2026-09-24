# Deferred vs Forward 渲染架構

**穩定性標籤**：`[STABLE]`

---

## 核心差異

### Forward Rendering
```
For each object:
  For each light affecting this object:
    計算光照 → 輸出顏色
```
- 光照在每個物件的 Pass 中計算
- 多個光源 = 多個 Pass 或 shader 內 loop

### Deferred Rendering
```
Pass 1 (G-Buffer):  儲存幾何資訊（位置/法線/材質屬性）到多個 Render Target
Pass 2 (Lighting):  用 G-Buffer 資訊計算所有光源
```
- 幾何 Pass 和光照 Pass 分離
- 大量光源成本低（只影響 Lighting Pass）

---

## 比較表

| 特性 | Forward | Deferred |
|------|---------|---------|
| 大量動態光源 | 逐物件計算可能成本較高；Forward+ / clustered 可縮小受影響光源集合 | 光照與幾何分開；光照成本仍受解析度、光源與材質影響 |
| MSAA 抗鋸齒 | 常見支援路徑較直接；仍看引擎/平台 | 額外 G-Buffer 儲存與頻寬成本可能較高，支援度依引擎/平台 |
| 半透明物件 | 可用 forward shading；成本依 overdraw 和光照方式 | 通常使用獨立 forward/translucency pass，實際行為依引擎 |
| 行動裝置頻寬 | 可能減少額外 G-Buffer 頻寬 | G-Buffer 會增加頻寬與記憶體需求；裝置和解析度影響顯著 |
| 自訂光照模型 | 依 shader 路徑與引擎功能而定 | 可能受 G-Buffer 編碼與可用欄位限制 |
| 延伸：Tiled/Clustered Forward | ✅ 兼具優點 | — |

---

## G-Buffer 結構（Deferred 核心）

G-Buffer 通常保存後續光照所需的表面屬性與深度。Unreal 的實際 target 數量、格式、channel packing 會依 UE 版本、平台、渲染設定和 Substrate 格式改變；請用目標版本的 Buffer Visualization 或 RenderDoc 檢查，不要依賴固定欄位表。

| 常見資料 | 用途 |
|----------|------|
| Base Color、Normal、Roughness、Metallic 等材質屬性 | 延後著色時重建表面反射 |
| Shading Model / material flags | 決定材質著色路徑 |
| Depth（以及依管線可用的 Velocity 等資料） | 重建位置、深度測試或後處理 |

**TA 用途**：用 `r.VisualizeBuffer` 指令可以即時查看每個 G-Buffer channel，對材質除錯非常有用。

---

## Unity 的架構選擇

| 管線 | 架構 |
|------|------|
| URP | Forward、Forward+ 或 Deferred；依 Unity/URP 版本與 Renderer 設定 |
| HDRP | 可依 HDRP Asset 選 Forward 或 Deferred；效果支援與成本依設定 |

## Unreal 的架構

- **預設**：Deferred Rendering
- **Mobile**：可用 Mobile Forward 或 Mobile Deferred；預設和支援依目標平台/版本設定
- **半透明**：常使用獨立的 Forward shading pass；依材質 Lighting Mode 和引擎設定

---

## TA 實際影響

**半透明物件在 Deferred 管線中**：
- 不能接收 Deferred 光照（因為沒寫 G-Buffer）
- 通常用 Capsule Shadow 或 Forward 光照補充
- 成本高，要謹慎使用

**自訂 Shading Model（Deferred）**：
- 需要在 G-Buffer 裡預留欄位
- UE5：Substrate 材質系統重新設計了這個機制

---

## 實作練習 (project-ideas)

1. **G-Buffer 分析**：在目標 Unreal 版本使用 Buffer Visualization 或 RenderDoc 檢查實際 G-Buffer，記錄版本、平台、格式與主要資料用途。
