# GPU 效能診斷方法論

**穩定性標籤**：`[STABLE]`

---

## CPU Bound vs GPU Bound

**最重要的第一步**：確認你的瓶頸在哪裡。

```
CPU Bound：CPU 處理完一幀，GPU 在等 CPU
GPU Bound：GPU 渲染一幀，CPU 在等 GPU

判斷方式：
  Unity  → Profiler → CPU Usage → 看 Gfx.WaitForPresent 時間
  Unreal → stat unit → 看 Frame/Game/Draw/GPU 哪個最大
```

**常見 CPU Bound 原因**：
- 太多 Draw Call（CPU 設定狀態的開銷）
- 大量 C#/Blueprint Tick
- 物理模擬
- AI 計算

**常見 GPU Bound 原因**：
- 高 overdraw（半透明）
- 複雜 shader（過多 texture sample、複雜數學）
- 高解析度 render target
- 過多光源

---

## 診斷流程

```
Step 1: 開 profiler，截取有問題的 frame
  ↓
Step 2: CPU or GPU bound?
  ↓ GPU Bound
Step 3: 哪個 Pass 最貴？
  （Shadow? Lighting? Translucency? Post Process?）
  ↓
Step 4: 那個 Pass 裡哪個 Draw Call 最貴？
  ↓
Step 5: 是 Shader 複雜度? 貼圖頻寬? Overdraw? 幾何密度?
  ↓
Step 6: 只改一件事，再量測
```

---

## GPU 瓶頸類型

### Fillrate（填充率）限制
**症狀**：降低解析度 → 效能明顯提升  
**原因**：每幀要處理太多像素（高解析度 + 高 overdraw）  
**解法**：降低 render target 解析度、減少半透明 overdraw、使用 upscaling（DLSS/FSR）

### Bandwidth（頻寬）限制
**症狀**：降低貼圖大小 → 效能提升  
**原因**：GPU 讀寫記憶體太頻繁  
**解法**：貼圖壓縮（BC7/ETC2）、Mip Maps 正確設定、減少 render target 數量

### Compute（計算）限制
**症狀**：簡化 shader → 效能提升  
**原因**：shader 指令太複雜  
**解法**：簡化 shader 數學、預計算（bake）能預計算的值、half 代替 float（行動裝置）

### Vertex（頂點）限制
**症狀**：降低 polygon 數 → 效能提升  
**原因**：幾何體頂點數過多（非 Nanite 場景）  
**解法**：LOD 系統、減少 mesh 複雜度、Nanite（UE5）

---

## 常用效能指標

| 指標 | PC 目標（60fps）| 行動裝置目標（30fps）|
|------|--------------|-----------------|
| Frame Budget | 16.6ms | 33.3ms |
| Draw Calls | < 2000 | < 200 |
| Triangle Count | < 3M/frame | < 300K/frame |
| Shadow Map Passes | < 4ms | < 3ms |
| Translucency | < 2ms | < 1ms |
| Post Processing | < 3ms | < 2ms |

> 這些是參考值，實際目標因遊戲類型和硬體設定而異。

---

## 學習資源

- 📖 [NVIDIA GPU Performance Guide](https://developer.nvidia.com/blog/the-peak-performance-analysis-method-for-optimizing-any-gpu-workload/)
- 📖 [ARM Mali GPU Best Practices](https://developer.arm.com/documentation/102444/latest/)
- 🎥 [GDC: Performance Optimization talks](https://gdcvault.com/) — 搜尋 "GPU optimization"
