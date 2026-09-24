# Unity Profiler 工具使用

**穩定性標籤**：`[ENGINE-VERSIONED: Unity 6]`

---

## Unity Profiler

`Window → Analysis → Profiler`

### 主要 Module
| Module | 查什麼 |
|--------|--------|
| **CPU Usage** | 每幀各系統的 CPU 時間 |
| **GPU Usage** | 各 Pass 的 GPU 時間（需要 Graphics Jobs 設定）|
| **Memory** | 記憶體分配、GC Alloc |
| **Rendering** | Draw Call 數、Batch 數、三角形數 |

### 關鍵指標位置
```
CPU Usage → 展開 Rendering：
  Camera.Render          ← 整個渲染的 CPU 成本
  Gfx.WaitForPresentOnGfxThread ← 可能在等 VSync 或 GPU；不能單獨判定 GPU Bound
  
Rendering Module：
  Batches                ← Draw Call 數（越少越好）
  SetPass Calls          ← 材質切換次數
  Triangles              ← 本幀三角形總數
  Vertices               ← 本幀頂點總數
```

---

## Frame Debugger

`Window → Analysis → Frame Debugger`

最快速的「這個 Pass 在做什麼」工具。

### 使用方式
1. 打開 Frame Debugger
2. 點 **Enable** 暫停並截取當前幀
3. 左側樹狀結構展開 Pass
4. 點擊任意 Draw Call → 右側看 Render Target 和狀態

### 關鍵用途
- **確認 SRP Batcher 效果**：`SRP Batch` 群組裡的 Draw Call 被批次了
- **找半透明 Pass**：`Transparent` 或 `Translucency` 下的 Draw Call
- **確認 Shadow Pass**：`ShadowCaster` Pass 的成本

---

## Memory Profiler（Unity 6 Package）

`Window → Analysis → Memory Profiler`

安裝：`Package Manager → Memory Profiler`

### 用途
- 查看 VRAM 使用量
- 找最大的貼圖（按 Size 排序）
- 追蹤記憶體洩漏

---

## 常用 Debug 命令（Edit Mode）

```csharp
// 即時顯示 Draw Call 統計（需要 Stats 面板）
// Game View → Stats 按鈕

// 程式碼中強制開啟/關閉某個渲染功能
// URP RendererFeature 的 SetActive()

// 查看特定物件的 shader variant
// Project Settings → Graphics → Shader Stripping → Log
```

---

## Unity 效能優化工具清單

| 問題 | 工具 |
|------|------|
| 找最貴的 Draw Call | Frame Debugger |
| CPU vs GPU bound | Profiler Timeline + GPU Usage；搭配目標平台 profiler 判讀 |
| 記憶體用量 | Memory Profiler |
| Draw Call 數量 | Profiler Rendering Module |
| Shader 複雜度 | RenderDoc + Shader Inspector |
| 行動裝置效能 | Android GPU Inspector / Xcode GPU Debugger |

---

## 實作練習 (project-ideas)

1. **效能基線建立**：在你的場景拍一次 Profiler 快照，記錄 Draw Calls、Triangle Count、GPU 時間分布。設定優化目標並追蹤改善過程。
