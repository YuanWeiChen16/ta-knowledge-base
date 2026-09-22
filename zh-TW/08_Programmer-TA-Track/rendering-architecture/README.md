# 渲染架構深度（類 System Design）

**程式師最能發揮優勢的領域——把渲染當成分散式系統來理解。**

---

## 核心類比：渲染 = 分散式系統

程式師熟悉的系統設計概念，在渲染裡都有對應：

| 系統設計概念 | 渲染對應 |
|------------|---------|
| 生產者/消費者佇列 | Command Buffer（CPU 生產，GPU 消費）|
| 快取 / Cache Invalidation | Texture Cache、Mip Map、Shader 編譯快取 |
| 並行與同步 | Resource Barrier、Pipeline Stage、Semaphore |
| 記憶體分層 | VRAM / L2 / L1 / Register File |
| 批次處理 | Draw Call Batching、GPU Instancing |
| 流量控制 | Texture Streaming、Virtual Geometry（Nanite）|
| 背壓（Backpressure）| CPU Bound vs GPU Bound |
| Pipeline 設計 | Render Pass、G-Buffer、Deferred vs Forward |

---

## 必讀文章（按深度排序）

### Level 1 — 建立正確心智模型

**"A Trip Through the Graphics Pipeline 2011"**
- 作者：Fabian Giesen（AMD/RAD Game Tools 工程師）
- URL：https://fgiesen.wordpress.com/2011/07/09/a-trip-through-the-graphics-pipeline-2011-index/
- 共 13 篇，從 DX11 Driver 到 Pixel Shader 的完整深度分析
- **這是程式師最應該先讀的一篇。** 像 Linux kernel deep dive，但主題是 GPU

**"Getting Started in Computer Graphics"**
- 作者：Jeremy Ong（資深 Graphics Programmer）
- URL：https://www.jeremyong.com/graphics/2024/05/19/getting-started-in-computer-graphics/
- 程式師視角的入門建議，包含學習路徑和應該問自己的問題

---

### Level 2 — 系統設計深度

**Real-Time Rendering 4th ed.**
- URL：https://www.realtimerendering.com/（部分免費）
- 程式師讀法：把每一章當成一個子系統的 design doc
  - Ch. 7 Shadow — Shadow Map 的各種設計 tradeoff
  - Ch. 11 Global Illumination — GI 快取的設計空間
  - Ch. 20 Pipelines — 現代 GPU pipeline 架構

**Physically Based Rendering (PBRT 4th ed.)**
- URL：https://pbr-book.org（完全免費）
- 把渲染當成 CS 問題：演算法、資料結構、數學推導全部嚴謹
- 程式師重點章節：Ch. 1（系統架構）、Ch. 4（採樣理論）、Ch. 9（材質模型）

---

### Level 3 — 底層 API 與硬體

**Vulkan Tutorial**
- URL：https://vulkan-tutorial.com
- 最好的 Vulkan 入門，從零開始建立一個三角形
- TA 讀到的深度：理解 Command Buffer、Render Pass、Barrier 概念即可（不需要全部實作）

**DirectX 12 Samples (Microsoft)**
- URL：https://github.com/microsoft/DirectX-Graphics-Samples
- HelloWorld 到 Advanced 有完整範例
- 對程式師：這些範例讓 Vulkan 概念更具體

**Arm Mali GPU Best Practices**
- URL：https://developer.arm.com/documentation/102444/latest/
- 行動裝置 TBDR 架構最好的官方說明
- 程式師讀法：理解「為什麼 tile-based 改變了所有 bandwidth 假設」

---

## System Design 風格的渲染問題

練習像回答 System Design 面試題一樣思考這些問題：

### Shadow System Design
```
問題：設計一個支援 100 個動態光源、各自有不同陰影的系統

考慮維度：
- 記憶體：100 個 Shadow Map @ 1024×1024 = 400MB，怎麼辦？
- 效能：每幀更新 100 個 Shadow Map 的成本？
- 品質：遠距離陰影解析度下降怎麼處理？
- 動態性：哪些光源需要每幀更新？哪些可以 cache？

現實解法：
→ MegaLights（SIGGRAPH 2025）用 Stochastic Sampling 解這個問題
→ 閱讀原論文，理解他們的 design decisions
```

### Texture Streaming Design
```
問題：一個 open world 遊戲有 50GB 的貼圖，VRAM 只有 8GB，怎麼管理？

考慮維度：
- 優先級：如何決定哪些貼圖要在 VRAM？（攝影機距離、螢幕佔比）
- 預載：玩家移動時如何預測接下來需要的貼圖？
- Mip Streaming：只載入需要的 Mip Level
- 驅逐策略：滿了怎麼辦？LRU？Priority Queue？

現實實作：
→ UE5 Virtual Texture
→ 讀 "Texture Streaming in Unreal Engine" 文件
```

### GI Cache Design
```
問題：設計一個支援動態光源、動態幾何的即時 GI 系統

考慮維度：
- 儲存格式：Irradiance Probe？SH？Radiance Cache？
- 更新策略：每幀全更新？增量更新？Async？
- Light Leaking：如何防止 probe 穿牆採樣到錯誤 GI？
- Scalability：如何在低端硬體降格？

現實實作：
→ Lumen（UE5）的設計可以在 GDC/SIGGRAPH 找到詳細說明
```

---

## 程式師的渲染 Debug 方法論

```
傳統程式 Debug：
  1. 看 stack trace
  2. 加 log
  3. 設 breakpoint

渲染 Debug：
  1. RenderDoc 截 Frame（等於 core dump）
  2. 逐 Pass 檢查（等於看 call stack）
  3. 視覺化中間值（等於 print debug）
  4. Shader Debugger 逐行執行（等於 breakpoint）

程式師優勢：
  - 你知道怎麼系統性地縮小問題範圍
  - 你不會「隨機改東西希望它好」
  - 你知道要找根因，不是症狀
```

---

## 學習里程碑

```
里程碑 1：能解釋一個完整 Frame 的資料流
  - 從 CPU Submit Draw Call 到螢幕輸出
  - 每個 Pass 的輸入輸出是什麼

里程碑 2：能診斷 CPU Bound vs GPU Bound
  - 能讀 profiler 數字
  - 知道哪個數字代表哪種瓶頸

里程碑 3：能評估渲染功能的實作成本
  - 能說「這個效果大概要多少 ms」
  - 能提出 3 種不同成本/品質 tradeoff 的實作方案

里程碑 4：能設計一個渲染子系統
  - 給定需求（X 個光源、Y ms 預算、Z 平台）
  - 能提出架構設計並解釋 tradeoff
```
