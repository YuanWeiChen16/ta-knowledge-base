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
| 大量動態光源 | ❌ 貴（每光源 × 每物件）| ✅ 便宜 |
| MSAA 抗鋸齒 | ✅ 便宜 | ❌ 貴/不相容 |
| 半透明物件 | ✅ 直接支援 | ❌ 需要 Forward pass 補充 |
| 行動裝置頻寬 | ✅ 較低 | ❌ G-Buffer 頻寬高 |
| 自訂光照模型 | ✅ 容易 | ⚠️ 需要 G-Buffer 欄位支援 |
| 延伸：Tiled/Clustered Forward | ✅ 兼具優點 | — |

---

## G-Buffer 結構（Deferred 核心）

典型的 G-Buffer layout（以 Unreal 為例）：

| Render Target | 儲存內容 |
|---------------|---------|
| RT0 (RGBA8) | BaseColor (RGB) + Shading Model ID (A) |
| RT1 (RGBA8) | Metallic + Specular + Roughness + AO |
| RT2 (RGB10A2) | World Normal (RGB) + Per-object data |
| RT3 (R11G11B10)| Emissive / VelocityBuffer |
| Depth | Scene Depth |

**TA 用途**：用 `r.VisualizeBuffer` 指令可以即時查看每個 G-Buffer channel，對材質除錯非常有用。

---

## Unity 的架構選擇

| 管線 | 架構 |
|------|------|
| URP | Forward+ (Tiled Forward，URP 14+) |
| HDRP | Deferred（預設） + Forward 補充半透明 |

## Unreal 的架構

- **預設**：Deferred Rendering
- **Mobile**：Forward Rendering（可選）
- **半透明**：永遠是 Forward pass（疊加在 Deferred 之後）

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

1. **G-Buffer 分析**：在 Unreal 裡用 `r.VisualizeBuffer` 逐一截圖所有 G-Buffer channel，寫一份說明文件，標注每個 channel 儲存什麼資訊及其用途。
