# 渲染管線基礎

**穩定性標籤**：`[STABLE]`

> 理解資料怎麼從 3D 場景變成螢幕上的像素。這是所有 shader 工作的框架。

---

## 渲染管線資料流

```
CPU                          GPU
─────                        ─────────────────────────────────────────
Scene Data          →   [Vertex Shader]
(Mesh, Transform,       ↓ 每個頂點執行一次
 Materials)         →   [Primitive Assembly / Rasterization]
                        ↓ 把三角形填成像素片段
                    →   [Fragment / Pixel Shader]
                        ↓ 每個像素執行一次
                    →   [Output Merger / ROP]
                        ↓ 深度測試、混合
                    →   Framebuffer → 螢幕
```

---

## 各階段 TA 關注點

### Vertex Shader
- **輸入**：Position, Normal, Tangent, UV, VertexColor（每個頂點）
- **輸出**：Clip space position（必須），其他插值到 fragment
- **TA 用途**：Vertex animation（草地、布料簡易模擬）、Outline、Displacement

### Rasterization（光柵化）
- 把三角形邊緣插值填滿像素
- 生成 **Fragment**（候選像素）
- **TA 關注**：這裡決定 aliasing（鋸齒）從哪來

### Fragment / Pixel Shader
- **輸入**：插值後的頂點資料 + Texture samples
- **輸出**：顏色（RGBA）
- **TA 工作主場**：所有 PBR 計算、貼圖混合、特效都在這裡

### Depth Test & Blending
- **Depth Test**：比較深度 buffer，決定是否丟棄
- **Alpha Blending**：半透明混合（Order-dependent，性能殺手）
- **TA 陷阱**：半透明物件必須從後往前排序，不能用 depth write

---

## Forward vs Deferred（完整說明在 `02_Rendering-Pipeline/`）

| | Forward | Deferred |
|--|---------|---------|
| 光照計算 | 每個物件 × 每個光源 | 先存 G-Buffer，統一計算 |
| 半透明 | 容易 | 困難（需要特殊處理） |
| MSAA | 容易 | 困難/昂貴 |
| 行動裝置 | 通常更適合 | Bandwidth 很高 |

---

## Pass 的概念

現代渲染不是一次畫完，而是多個 Pass 疊加：

```
Shadow Pass     → 生成 Shadow Map
G-Buffer Pass   → 儲存幾何資訊（Deferred）
Lighting Pass   → 計算光照
Translucency    → 半透明物件
Post Process    → TAA, Bloom, Tone mapping
UI              → 最後疊加
```

**TA 關鍵問題**：「這個效果需要的資料，在這個 Pass 的時候存在嗎？」

---

## 學習資源

- 📖 [LearnOpenGL](https://learnopengl.com/) — 最好的入門，概念清晰，有互動範例
- 🎥 [Acerola YouTube](https://www.youtube.com/@Acerola_t) — 渲染管線視覺化講解
- 📖 [Real-Time Rendering 4th ed.](https://www.realtimerendering.com/) — 業界標準參考書（可線上免費瀏覽部分章節）

---

## 實作練習 (project-ideas)

1. **Pass 視覺化**：用 RenderDoc 截取一個 frame，逐 pass 檢查，寫下每個 pass 輸入/輸出是什麼。目標：能描述完整的 frame 生命週期。
2. **自製 Wireframe overlay**：不改 mesh，純用 shader 在 forward pass 疊加 wireframe。約束：不影響正常渲染、支援 skinned mesh。
