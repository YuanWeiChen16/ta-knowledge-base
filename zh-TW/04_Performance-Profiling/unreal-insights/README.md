# Unreal Insights 與效能工具

**穩定性標籤**：`[ENGINE-VERSIONED: UE5.4]`

---

## stat 命令（最快速的第一步）

在 PIE（Play In Editor）或遊戲中按 `` ` `` 開啟 Console，輸入：

```
stat unit       ← 最重要！顯示 Frame/Game/Draw/GPU 時間
stat fps        ← FPS 顯示
stat gpu        ← 每個 GPU Pass 的時間（需要 r.GPUStatsEnabled 1）
stat scenerendering ← Draw Call、Triangle 數
stat memory     ← 記憶體使用
stat streaming  ← 貼圖 streaming 狀態

// 視覺化 Buffer
vis SceneColor          ← 最終顏色
r.VisualizeBuffer BaseColor    ← GBuffer BaseColor
r.VisualizeBuffer WorldNormal  ← GBuffer 法線
r.VisualizeBuffer Roughness    ← GBuffer 粗糙度
r.VisualizeBuffer SceneDepth   ← 深度
r.VisualizeOverdraw 1          ← Overdraw 視覺化（紅色越深越貴）
```

---

## GPU Visualizer

`Ctrl + Shift + ,` 或在 Console 輸入 `ProfileGPU`

### 如何讀取
1. 點擊 **Start** → 截取一幀
2. 左側：Pass 樹狀結構（按 GPU 時間排序）
3. 最貴的 Pass 通常是：Shadow、Translucency、PostProcess

### 關鍵 Pass 時間參考（60fps = 16.6ms 總預算）

| Pass | 健康值 | 警戒值 |
|------|--------|--------|
| Shadow Depths | < 2ms | > 4ms |
| Base Pass | < 4ms | > 8ms |
| Lighting | < 3ms | > 6ms |
| Translucency | < 1ms | > 3ms |
| Post Processing | < 2ms | > 4ms |

---

## Unreal Insights

獨立應用程式，比 in-editor 工具有更深度的追蹤：

```
啟動 UnrealInsights.exe（在引擎安裝目錄）
或從 Editor → Tools → Run Unreal Insights
```

### 連接遊戲
```
-trace=cpu,gpu,frame,log,memory,counters
// 加到遊戲啟動參數，或在 Editor Preferences 設定
```

### 主要使用情境
- **CPU Thread 分析**：找主線程和渲染線程的瓶頸
- **Hitch 分析**：找幀率卡頓的原因
- **Memory 追蹤**：追蹤記憶體分配時間點

---

## Nanite 效能視覺化

```
r.Nanite.Visualize overview    ← Nanite 總覽
r.Nanite.Visualize triangles   ← 實際渲染三角形密度
r.Nanite.Visualize clusters    ← Cluster 分布
r.Nanite.Visualize overdraw    ← Overdraw（Nanite 本身 overdraw 極少）
```

---

## Lumen 效能視覺化

```
r.Lumen.Visualize.Overview 1          ← Lumen 總覽
r.Lumen.DiffuseIndirect.Visualize 1   ← GI 視覺化
show LumenScene                        ← 顯示 Lumen 場景代理
```

---

## 學習資源

- 📖 [Unreal Engine Performance Guide](https://docs.unrealengine.com/5.4/en-US/performance-and-profiling-in-unreal-engine/)
- 📖 [Unreal Insights Documentation](https://docs.unrealengine.com/5.4/en-US/unreal-insights-in-unreal-engine/)
- 🎥 [GDC: Optimizing for Unreal Engine](https://gdcvault.com/) — 搜尋 "Unreal optimization"
