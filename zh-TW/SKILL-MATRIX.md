# TA 技能自評矩陣

用這個表格評估你在每個領域的現狀。**TA 的成長是非線性的**——你可以在某個領域是 Senior，在另一個領域是 Junior。誠實評估比假裝全能更有價值。

---

## 評估等級定義

| 等級 | 定義 |
|------|------|
| **0 — 空白** | 完全沒有接觸過 |
| **1 — Junior** | 知道工具怎麼用，但遇到邊緣案例就卡住 |
| **2 — Mid** | 理解「為什麼」，能獨立診斷大多數問題 |
| **3 — Senior** | 從第一原則推理，能設計系統、制定策略 |

---

## 技能矩陣

| 領域 | 子技能 | 你的等級 (0-3) | 目標等級 | 參考章節 |
|------|--------|--------------|---------|---------|
| **Shader 編寫** | HLSL/GLSL 語法 | | | `01_Shaders-Materials/hlsl-core` |
| | UV 操作與貼圖採樣 | | | `01_Shaders-Materials/hlsl-core` |
| | Vertex shader 應用 | | | `01_Shaders-Materials/hlsl-core` |
| | Compute shader | | | `01_Shaders-Materials/hlsl-core` |
| **材質系統** | PBR 微面元理論 | | | `01_Shaders-Materials/pbr-theory` |
| | Unity ShaderGraph / URP / HDRP | | | `01_Shaders-Materials/unity` |
| | Unreal Material Editor / Instances | | | `01_Shaders-Materials/unreal` |
| | Shader 除錯 (RenderDoc) | | | `01_Shaders-Materials/shader-debugging` |
| **光照 & GI** | 即時光照原理 | | | `02_Rendering-Pipeline/lighting-gi` |
| | Lightmap baking 工作流 | | | `02_Rendering-Pipeline/lighting-gi` |
| | Unity APV / HDRP Probe Volume | | | `02_Rendering-Pipeline/lighting-gi` |
| | Unreal Lumen | | | `02_Rendering-Pipeline/lighting-gi` |
| | 陰影技術（CSM, VSM, PCSS）| | | `02_Rendering-Pipeline/shadows` |
| **VFX & 粒子** | 粒子系統設計原則 | | | `03_VFX-Systems/fundamentals` |
| | Unity VFX Graph | | | `03_VFX-Systems/unity-vfx-graph` |
| | Unreal Niagara | | | `03_VFX-Systems/unreal-niagara` |
| | VFX 效能預算 | | | `03_VFX-Systems/fundamentals` |
| **效能分析** | GPU 效能瓶頸診斷方法論 | | | `04_Performance-Profiling/gpu-methodology` |
| | Unity Profiler / Frame Debugger | | | `04_Performance-Profiling/unity-profiler` |
| | Unreal Insights / GPU Visualizer | | | `04_Performance-Profiling/unreal-insights` |
| | 行動裝置效能 (tile-based GPU) | | | `04_Performance-Profiling/mobile` |
| | RenderDoc 完整工作流 | | | `01_Shaders-Materials/shader-debugging` |
| **Pipeline & 工具** | Python DCC 腳本 (Maya/Blender/Houdini) | | | `05_Pipeline-Tools/python-scripting` |
| | Houdini for Games (VAT) | | | `05_Pipeline-Tools/houdini` |
| | Substance Designer/Painter 進階 | | | `05_Pipeline-Tools/substance` |
| | USD 格式與交換工作流 | | | `05_Pipeline-Tools/usd` |
| | Unreal PCG Framework | | | `05_Pipeline-Tools/unreal-pcg` |
| **底層圖形概念** | Render pass / G-Buffer 架構 | | | `02_Rendering-Pipeline/low-level-graphics` |
| | Draw call batching / GPU instancing | | | `02_Rendering-Pipeline/low-level-graphics` |
| | 記憶體階層 (VRAM / 系統 RAM) | | | `02_Rendering-Pipeline/low-level-graphics` |
| | 同步 barrier 概念 | | | `02_Rendering-Pipeline/low-level-graphics` |
| | Vulkan/DX12 概念層 (Track A) | | | `02_Rendering-Pipeline/low-level-graphics` |
| **數學基礎** | 線性代數（向量、矩陣） | | | `00_Foundations/linear-algebra` |
| | 四元數與旋轉 | | | `00_Foundations/linear-algebra` |
| | 色彩科學 (gamma, ACES, sRGB) | | | `00_Foundations/color-science` |
| | 渲染管線資料流 | | | `00_Foundations/rendering-pipeline` |

---

## 使用建議

1. **每 3 個月重新評估一次**
2. **找出你最薄弱且最影響日常工作的領域** — 優先補那個
3. **不要追求全部 3 分** — 大多數 TA 在 2-3 個領域有深度特化，其他領域夠用即可
4. **作品集反映你的 3 分領域** — 把最強的技能做成可展示的東西

---

## Senior TA 的非線性路徑

真實的 Senior TA 技能分布通常長這樣（不是全部拉滿）：

```
Shader Writing:      ███████████ 3
Material Systems:    ███████████ 3  
Lighting & GI:       ███████░░░░ 2
VFX & Particles:     ███████░░░░ 2
Performance:         ███████████ 3
Pipeline & Tools:    ███████░░░░ 2
Low-Level Concepts:  ███████░░░░ 2
Math Foundations:    ███████░░░░ 2
```

目標是**深度特化 + 廣度夠用**，不是均勻拉滿。
