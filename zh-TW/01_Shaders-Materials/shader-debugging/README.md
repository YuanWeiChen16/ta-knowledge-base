# Shader 除錯工作流

**穩定性標籤**：`[STABLE]`（工具版本更新但工作流穩定）

> 越早建立除錯習慣，越少時間浪費在猜測。

---

## 黃金規則：先 Profile，再猜

**不要在沒有資料的情況下做優化。**  
打開 profiler → 找到真正的瓶頸 → 再動手。

---

## RenderDoc — 主要除錯工具

### 基本工作流
1. 啟動 RenderDoc，Attach 或 Launch 遊戲
2. `F12` 截取一個 Frame
3. Event Browser：看每個 Draw Call
4. Texture Viewer：看任意 texture/render target
5. Mesh Viewer：看 vertex data
6. Pipeline State：看每個 stage 的 shader 和 state

### 常用場景

**問題：材質顏色不對**
```
Event Browser → 找到問題物件的 Draw Call
→ Pipeline State → Pixel Shader → 點 "Debug" 按鈕
→ Shader Debugger：逐行執行，看每個變數的值
```

**問題：法線貼圖接縫**
```
Texture Viewer → 找 Normal map
→ 確認 Format 是 R8G8B8A8_UNORM（不是 sRGB）
→ Mesh Viewer → 看 tangent 和 bitangent 方向是否正確
```

**問題：透明物件排序問題**
```
Event Browser → 看 Translucency pass 的物件順序
→ Texture Viewer → 看各個 frame buffer 的深度值
```

### RenderDoc 截圖技巧
- `F12`：截取 frame
- 可以在 Texture Viewer 按 `S` 儲存 texture 為圖片
- Pipeline State 的每個 stage 都能看 shader source

---

## Unity Frame Debugger

`Window → Analysis → Frame Debugger`

比 RenderDoc 更整合、更易用，但資訊較少。

**用途：**
- 看 Pass 順序（Shadow → Depth → Opaque → Transparent → Post）
- 看 SRP Batcher 批次效果
- 看每個 Pass 的 Render Target
- **快速確認：** 「這個 pass 有沒有執行？」

---

## Unreal GPU Visualizer

`Shift + L` 或 `ProfileGPU` 命令

```
ProfileGPU → 點開 Frame → 看每個 pass 的 GPU 時間
```

**Unreal Console 命令（除錯用）：**
```
r.VisualizeBuffer BaseColor      // 看 GBuffer BaseColor
r.VisualizeBuffer WorldNormal    // 看 GBuffer 法線
r.VisualizeBuffer Roughness      // 看 GBuffer Roughness
r.VisualizeBuffer Metallic
r.VisualizeBuffer SceneDepth
stat GPU                         // 看 GPU 各 pass 時間
vis SceneColor                   // 看最終顏色 buffer
```

---

## Debug Shader 技巧

**視覺化任何中間值（最常用技巧）：**

```hlsl
// 用顏色顯示法線方向
return float4(normal * 0.5 + 0.5, 1.0); // 映射 -1~1 到 0~1

// 用顏色顯示 UV
return float4(uv.x, uv.y, 0, 1);

// 用顏色顯示深度
return float4(depth, depth, depth, 1);

// 用顏色顯示 vertex color
return vertexColor;

// 用顏色顯示 Mip level（需要 DDX/DDY）
float mip = log2(max(length(ddx(uv)), length(ddy(uv))));
return float4(mip/8.0, 0, 0, 1); // 紅色越深 = mip 越高
```

---

## 常見問題 Checklist

| 症狀 | 先查這裡 |
|------|---------|
| 材質全黑 | 法線方向、光源方向、NdotL 是否為負 |
| 法線貼圖接縫 | Tangent 計算方式（Maya vs 引擎差異）、貼圖是否設為 Linear |
| 顏色偏黃/偏綠 | sRGB/Linear 設定、tonemapper 影響 |
| 半透明穿幫 | 深度排序、Depth Write 是否關閉 |
| Shader 閃爍 | Z-fighting（兩個幾何體深度太接近）、精度問題 |
| 陰影 acne | Shadow bias 太小、shadow map 解析度不足 |
| GrabPass 卡頓（行動裝置）| Tile-based GPU framebuffer fetch 問題 |

---

## 學習資源

- 📖 [RenderDoc Documentation](https://renderdoc.org/docs/)
- 🎥 [RenderDoc 入門教學（YouTube 搜尋 "RenderDoc tutorial"）]
- 📖 [NVIDIA Nsight Graphics](https://developer.nvidia.com/nsight-graphics) — PC 深度分析
- 📖 [Xcode GPU Frame Capture](https://developer.apple.com/documentation/metal/gpu_debugger) — Apple 平台

---

## 實作練習 (project-ideas)

1. **Frame 完整分析**：選一個你自己的場景，用 RenderDoc 截取一幀，寫下：(a) 共有幾個 Draw Call (b) Shadow pass 佔總 GPU 時間幾% (c) 找出最貴的 3 個 Draw Call。
2. **GBuffer 視覺化工具**：在 Unreal 寫一個可切換的 Debug View，能即時顯示 BaseColor/Normal/Roughness/Metallic/AO 各個 GBuffer channel。
