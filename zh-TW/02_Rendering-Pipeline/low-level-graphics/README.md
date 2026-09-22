# 底層圖形概念 (Vulkan / DX12 概念層)

**穩定性標籤**：`[STABLE]`

> TA 不寫 Vulkan 程式碼，但理解這些概念讓你能讀懂 profiler 輸出，理解引擎的設計決策。

---

## Track A — 概念層（所有 Senior TA 必須）

### Render Pass 與 Attachment

**概念**：Render Pass 定義一組渲染操作和它們使用的 Render Target（Attachment）。

**為什麼 TA 需要知道**：
- 理解為什麼某些效果必須在獨立的 Pass 才能做
- 理解 G-Buffer 為什麼是「多個 Render Target」
- 理解 GrabPass 在 Tile-based GPU 為什麼貴（需要結束當前 pass）

```
Render Pass 1: Shadow Map pass
  Attachment: Depth buffer
  
Render Pass 2: G-Buffer pass  
  Attachments: Color RT0 (BaseColor), RT1 (Normal), RT2 (Roughness), Depth
  
Render Pass 3: Lighting pass
  Attachment: HDR Color RT
  Input: 讀取 G-Buffer（如果在同一 Render Pass 內 = 免費的 Tile 操作）
```

---

### Resource Barrier（同步屏障）

**概念**：告訴 GPU「這個資源從 X 狀態轉換到 Y 狀態」。

**為什麼需要**：GPU 高度並行。你剛寫完的 Shadow Map，下一個 Pass 要讀它時，GPU 需要確保寫入已完全完成。

**TA 實際影響**：
- 複雜的後處理 chain（效果 A 的輸出是效果 B 的輸入）需要正確的 barrier
- 不正確的 barrier → 視覺錯誤（讀到未完成的資料）或效能損失（等待不必要）
- 在 Unreal 的 RDG（Render Dependency Graph）中，這些 barrier 是自動管理的

---

### Descriptor Sets / Binding（資源綁定）

**概念**：GPU 在執行 shader 時如何「找到」要用的貼圖和 buffer。

**Descriptor Set**：一組資源的綁定表。切換材質 = 切換 Descriptor Set。

**TA 實際影響**：
- 每個材質 / shader variant 切換都有綁定成本
- 這就是為什麼 Material Instance 比切換 Parent Material 便宜
- Bindless Rendering（現代技術）：把所有貼圖放在一個巨大的 array，shader 用 index 存取，降低切換成本

---

### 記憶體階層

```
System RAM (CPU)
  ↑ PCIe / Unified Memory
VRAM / GPU Memory
  ↑ L2 Cache
  ↑ L1 Cache (per SM)
  ↑ Registers (per thread)
```

**TA 實際影響**：
- 貼圖 streaming：從 System RAM 按需載入 VRAM
- VRAM budget：超過 budget → 開始 streaming → 卡頓
- Unified Memory（Apple Silicon, PS5）：CPU/GPU 共享記憶體，影響 streaming 策略

---

### Command Buffer / Multi-threaded Rendering

**概念**：CPU 把 GPU 指令預先錄製到 Command Buffer，然後提交給 GPU 執行。

**為什麼 TA 需要知道**：
- 解釋為什麼 "CPU Bound" 和 "GPU Bound" 是不同的問題
- Multi-threaded rendering：多個 CPU 核心同時錄製 Command Buffer → 降低 CPU bound
- Unreal 的 RHI Thread 就是在做這個

---

## Track B — 實作層（引擎開發 / 特定工作室）

> 只有在工作室自研引擎或非常底層的 rendering 工作才需要。大多數 TA 不需要這層。

如果你需要深入這層：
- 📖 [Vulkan Tutorial](https://vulkan-tutorial.com/) — 最好的 Vulkan 入門
- 📖 [Vulkan Guide](https://vkguide.dev/) — 更現代的 Vulkan 教學（Descriptor Sets、Render Pass 2）
- 📖 [D3D12 HelloWorld Samples](https://github.com/microsoft/DirectX-Graphics-Samples) — Microsoft 官方 DX12 範例

---

## GPU-Driven Rendering（現代技術概念）

Nanite 和現代高端引擎的核心技術：

**傳統方式**：CPU 決定哪些物件要畫 → 每個物件一個 Draw Call
**GPU-Driven**：CPU 上傳所有物件資料 → GPU 自己決定要畫哪些（Culling 在 GPU 上）→ 用 Indirect Draw Call 批次執行

**TA 影響**：
- Nanite 的 meshlet 系統是 GPU-Driven 的一部分
- 理解為什麼 Nanite 幾何體的 Draw Call 數量不再是主要效能指標
- GPU Occlusion Culling 如何工作（HZB — Hierarchical Z Buffer）

---

## 學習資源

- 📖 [Fabian Giesen — A Trip Through the Graphics Pipeline](https://fgiesen.wordpress.com/2011/07/09/a-trip-through-the-graphics-pipeline-2011-index/) — 最深度免費資源
- 🎥 [GDC: GPU-Driven Rendering Pipelines](https://gdcvault.com/) — 搜尋 "GPU Driven Rendering"
- 📖 [Sascha Willems Vulkan Examples](https://github.com/SaschaWillems/Vulkan) — Vulkan 實際範例（即使不寫，讀這個能理解概念）
