# GPU 架構概念

**穩定性標籤**：`[STABLE]`

> 讓 profiler 數字變得有意義的底層知識。TA 不需要寫 GPU driver，但需要理解 GPU 的決策邏輯。

---

## GPU vs CPU 的本質差異

| | CPU | GPU |
|--|-----|-----|
| 核心數 | 少（8-32） | 超多（數千） |
| 每核速度 | 快 | 慢 |
| 擅長 | 複雜邏輯、分支、串行任務 | 大量相同操作並行 |
| 記憶體 | 大容量、高延遲 cache | 高頻寬、特化架構 |

**TA 結論**：Shader 裡的 `if` 不像 CPU 那樣便宜。GPU 傾向執行兩個分支然後選一個結果。

---

## 記憶體階層（為什麼 Bandwidth 重要）

```
VRAM (GPU 顯示記憶體)
  ↓ 高頻寬但有限容量
L2 Cache
  ↓
L1 Cache / Shared Memory（每個 SM 一份）
  ↓ 極快但極小
Registers（每個 thread 自己的）
```

**TA 關注**：
- 貼圖採樣 → 從 VRAM 讀，cache miss 很貴
- 同一個 Draw call 的 fragment 如果 UV 跳太多 → cache 一直 miss → 慢
- 降低貼圖解析度或使用 Mip → 改善 cache locality

---

## 行動裝置 vs 桌機 GPU 架構差異（重要！）

### 桌機 GPU（Immediate Mode Rendering，IMR）
- 每個 primitive 立刻光柵化並輸出到 framebuffer
- VRAM 和顯示記憶體分離
- 頻寬充裕

### 行動裝置 GPU（Tile-Based Deferred Rendering，TBDR）
- 把螢幕分割成小塊 (Tile)，每個 Tile 的所有幾何先收集完，再一起光柵化
- 整個 Tile 的計算在晶片上的小型快取完成，**不用一直讀寫系統記憶體**
- 節省大量頻寬 = 節省電量

**TA 影響**：
- Blending 和 Depth test 在 TBDR 上幾乎免費（在 tile 快取內）
- 但「讀取當前 framebuffer 內容」的操作（GrabPass/SceneColor）在 TBDR 上**極貴**，需要把 tile flush 到記憶體
- Overdraw 懲罰在 TBDR 上比桌機低，但 Bandwidth 懲罰更嚴格

---

## Draw Call 和 Batching

**Draw Call**：CPU 告訴 GPU「畫這個物件」的指令。

**為什麼 Draw Call 有成本**：
- 每個 Draw Call 需要 CPU 設定狀態（材質、shader、transform）
- GPU 執行很快，但 CPU 準備資料有 overhead
- 大量小 Draw Call → CPU bound

**減少 Draw Call 的方法**：
- **GPU Instancing**：同一個 mesh + 材質，N 個物件只用 1 個 Draw Call
- **Static Batching**：合併靜態物件的幾何
- **SRP Batcher**（Unity）：降低每個 Draw Call 的 CPU 設定成本

---

## Vulkan/DX12 概念層（Track A — 所有 Senior TA）

不需要寫程式碼，但需要理解這些概念：

| 概念 | 為什麼 TA 需要知道 |
|------|-----------------|
| **Render Pass** | 解釋為什麼某些效果必須獨立一個 pass |
| **Resource Barrier** | 解釋為什麼「讀剛寫入的貼圖」需要特殊處理 |
| **Descriptor Sets** | 解釋材質「插槽」的成本來源 |
| **Command Buffer** | 解釋 Multi-threading rendering 怎麼工作 |
| **Memory Heaps** | 解釋 VRAM budget 和 upload heap 的差異 |

---

## 學習資源

- 📖 [Jasper St. Pierre — GPU Architecture](https://fgiesen.wordpress.com/2011/07/09/a-trip-through-the-graphics-pipeline-2011-index/) — Fabian Giesen 的 "A Trip Through the Graphics Pipeline"，最深度的免費資源
- 📖 [Arm Mali GPU Best Practices](https://developer.arm.com/documentation/102444/latest/) — 行動 GPU 必讀
- 🎥 [GDC: Tile-Based GPU Architectures](https://developer.apple.com/videos/play/wwdc2020/10602/) — Apple GPU 架構（代表所有 TBDR GPU）
- 📖 [GPU Gems 1-3](https://developer.nvidia.com/gpugems/gpugems/foreword) — 免費線上，GPU 技術經典

---

## 實作練習 (project-ideas)

1. **Bandwidth 測試**：在行動裝置目標平台（或模擬器）上，用 GrabPass vs 不用 GrabPass 做同一個效果，profiler 比較兩者的頻寬消耗。
2. **Instancing benchmark**：同場景，500 個物件分別測 No batching / Static batching / GPU Instancing 的 Draw Call 數量和 frame time。
