# TA 技能訪談式自評指南

> 這份指南把 `SKILL-MATRIX.md` 的 36 個子技能，每一項都附上**具體的判定標準**。
> 
> 比起直接填數字，用「描述你的具體經驗」來判定分數，結果更準確。

---

## 評估方法

對每個子技能，回答：「我在這個技能上**實際做過什麼**？」  
根據做到的程度對照下方標準，選最符合的等級。

| 等級 | 定義 |
|------|------|
| **0** | 完全沒接觸過 |
| **1** | 知道工具怎麼用，但遇到邊緣案例就卡住 |
| **2** | 理解「為什麼」，能獨立診斷大多數問題 |
| **3** | 從第一原則推理，能設計系統、制定策略 |

評估完成後，計算每個**領域的平均分**，找出最低的 1–2 個，優先補強。

---

## 評分標準

### Shader 編寫

**HLSL / GLSL 語法**
- 0：從沒碰過 shader 程式碼
- 1：改過別人的 shader，能看懂大部分語法
- 2：能從空白寫出 UV 動畫、簡單光照、noise 函數
- 3：寫過 Compute Shader、vertex deformation、自訂 lighting model

**UV 操作與貼圖採樣**
- 0：不知道 UV 是什麼
- 1：知道 UV 是 0–1 座標，用過 `tex2D` / `SAMPLE_TEXTURE2D` 採樣
- 2：做過 UV scroll、tiling、`frac()` 重複、用 UV 做 mask 或 gradient
- 3：做過 UV 扭曲（distortion）、triplanar mapping、flowmap、自訂 UV 投影

**Vertex Shader 應用**
- 0：不知道 vertex shader 是什麼
- 1：知道 vertex shader 負責座標轉換，但沒有自己改過
- 2：做過頂點位移（波浪、風吹草動）、用 vertex color 傳資料
- 3：做過 GPU skinning、自訂 morphing、複雜的 world-space deformation

**Compute Shader**
- 0：完全沒碰過
- 1：知道概念（thread group、SRV/UAV），但沒實際寫過
- 2：寫過簡單的 compute pass，例如 texture 處理、buffer 計算
- 3：寫過複雜的 GPU simulation、間接繪製、自訂 dispatch pipeline

---

### 材質系統

**PBR 微面元理論**
- 0：只知道 PBR 是「物理真實的材質」
- 1：知道 Metallic / Roughness / Albedo 三個參數的意義，會調材質球
- 2：理解 roughness 影響 NDF、菲涅耳（Fresnel）效應、能量守恆的概念
- 3：能解釋 Cook-Torrance BRDF 的每個項、知道 Specular 和 Diffuse 的能量分配

**Unity ShaderGraph / URP / HDRP**
- 0：沒用過 Unity
- 1：用過 ShaderGraph 拉節點，或改過 URP 的材質設定
- 2：自訂過 URP Render Feature、或在 HDRP 做過 Custom Pass
- 3：寫過 URP 的自訂 shader pass、熟悉 SRP Batcher 的限制與條件

**Unreal Material Editor / Instances**
- 0：沒用過 UE
- 1：開過 Material Editor，會連節點、改 Base Color / Roughness
- 2：用過 Material Instance 覆蓋參數、Dynamic Material Instance、或 Custom 節點嵌入 HLSL
- 3：設計過 Master Material 架構、管理過大量 Instance 的參數策略、或做過 Material Layer

**Shader 除錯（RenderDoc）**
- 0：沒用過任何 frame capture 工具
- 1：知道 RenderDoc 是什麼，開過、截過一個 frame
- 2：能看 draw call 清單、inspect 某個 pass 的輸入貼圖和輸出結果
- 3：能追蹤 shader resource binding、對比 vertex buffer、分析具體 pixel 的 shader 執行結果

---

### 光照 & GI

**即時光照原理**
- 0：只知道場景裡有燈光物件
- 1：知道光照的基本分類（直接光 / 間接光 / 環境光），會調燈光參數
- 2：理解 Phong / Blinn-Phong 模型、知道 GI 為何需要預計算或近似
- 3：能解釋 Radiance / Irradiance 的定義、理解 Light Probe 和 Reflection Probe 的原理

**Lightmap Baking 工作流**
- 0：不知道 lightmap 是什麼
- 1：知道 lightmap 是預計算的靜態光照，按過 bake 按鈕
- 2：設定過 UV2 unwrap、調過 texel density、處理過 lightmap seam 問題
- 3：做過大型場景的 lightmap 策略（LOD lightmap、cluster baking、atlas 管理）

**Unity APV / HDRP Probe Volume**
- 0：不知道這是什麼
- 1：知道 Probe Volume 是用來做動態物件 GI 的，但沒實際設定過
- 2：設定過 APV、調過 probe density、處理過漏光問題
- 3：做過複雜場景的 probe 分層策略、用過 Streaming APV 或自訂 probe 權重

**Unreal Lumen**
- 0：沒用過 UE，不知道 Lumen 是什麼
- 1：知道 Lumen 是 UE5 的動態 GI，但沒實際操作過
- 2：在專案裡啟用過 Lumen、調過品質設定、處理過 Lumen 和靜態物件的相容問題
- 3：理解 Surface Cache 和 Ray Tracing fallback 機制、能針對效能做 Lumen 策略決策

**陰影技術（CSM, VSM, PCSS）**
- 0：只知道物件會投影，沒思考過技術實作
- 1：知道 Shadow Map 的基本概念，調過陰影距離或解析度
- 2：理解 CSM 分層原理、知道 PCF 和 PCSS 的差異（軟硬邊）
- 3：能解釋 VSM 的 variance 計算、處理過陰影 acne / peter-panning 的根本原因

---

### VFX & 粒子

**粒子系統設計原則**
- 0：沒做過任何 VFX，不知道粒子系統怎麼運作
- 1：用過引擎的粒子系統，做過簡單的爆炸、煙霧
- 2：理解 spawn rate、lifetime、velocity、force 的交互關係，能拆解並重現看到的效果
- 3：能從美術需求反推技術策略（效能預算、LOD 策略、overdraw 控制）

**Unity VFX Graph**
- 0：沒用過 VFX Graph
- 1：開過 VFX Graph、連過幾個節點、能做出簡單的粒子噴射
- 2：用過 GPU Event、做過粒子碰撞、或用 Blackboard 參數從外部控制效果
- 3：寫過自訂 HLSL Block、做過大量粒子模擬（10萬+）且維持效能預算

**Unreal Niagara**
- 0：沒用過 UE，不知道 Niagara 是什麼
- 1：開過 Niagara System、做過基本粒子效果
- 2：用過 Niagara Module Script、Parameter Binding、或做過 GPU Simulation
- 3：設計過複雜的 Niagara 架構（多 Emitter 協作、自訂 HLSL Module、與 Blueprint 雙向通訊）

**VFX 效能預算**
- 0：從來沒想過 VFX 的效能問題
- 1：知道粒子太多會影響效能，會限制數量或關掉複雜效果
- 2：理解 overdraw、fillrate、GPU particle 的成本來源，能針對平台設定效能預算
- 3：建立過 VFX budget 系統、能量化每個效果的 GPU cost 並做優先級決策

---

### 效能分析

**GPU 效能瓶頸診斷方法論**
- 0：遇到效能問題只會降低畫質設定
- 1：知道要用 profiler 看，能分辨 CPU bound 還是 GPU bound
- 2：有完整診斷流程：確認瓶頸在哪個 stage（vertex / fragment / memory bandwidth），再針對性優化
- 3：能從 GPU counter 數據推斷根本原因，設計過系統級的效能監控與預算管理

**Unity Profiler / Frame Debugger**
- 0：沒用過
- 1：開過 Profiler，看過 CPU/GPU 時間軸，知道怎麼找耗時的 call
- 2：用過 Frame Debugger 逐一檢查 draw call、看過 GBuffer 的各個 pass
- 3：能結合 Memory Profiler + GPU Usage + Frame Debugger 做完整 frame 分析並提出針對性優化

**Unreal Insights / GPU Visualizer**
- 0：沒用過 UE
- 1：用過 `stat fps` 或 `stat unit` 看過基本數字
- 2：用過 GPU Visualizer 看各 pass 耗時、或開過 Unreal Insights 追蹤 frame
- 3：能用 Unreal Insights 做完整的 CPU/GPU 相關性分析、追蹤過 RHI thread 瓶頸

**行動裝置效能（Tile-based GPU）**
- 0：沒做過行動裝置專案
- 1：做過行動裝置專案，知道要降解析度、減少 draw call
- 2：理解 tile-based GPU 的 on-chip memory 特性、知道 framebuffer fetch 和 overdraw 對行動裝置的特殊影響
- 3：能針對 TBDR 架構設計 render pass 策略、用過 Arm Mobile Studio 或 Xcode GPU Frame Capture 做深度分析

**RenderDoc 完整工作流**
（同「Shader 除錯」，共用評分標準）

---

### Pipeline & 工具

**Python DCC 腳本（Maya / Blender / Houdini）**
- 0：沒用過 DCC 工具，也沒寫過相關腳本
- 1：在 Maya 或 Blender 裡執行過 Python 指令，改過簡單的屬性
- 2：寫過完整的工具（批次匯出、自動命名、LOD 生成），有 UI 或 shelf button
- 3：設計過可維護的 DCC 工具框架、處理過跨 DCC 工作流整合或 pipeline 自動化

**Houdini for Games（VAT）**
- 0：沒用過 Houdini
- 1：開過 Houdini、知道節點式工作流，但沒做過 games 相關輸出
- 2：做過 VAT 輸出（rigid / soft body / fluid），知道怎麼在引擎端重建動畫
- 3：設計過完整的 Houdini → 引擎 VAT pipeline、處理過 UE/Unity 兩端的 shader 對接

**Substance Designer / Painter 進階**
- 0：沒用過 Substance
- 1：用 Painter 上過色、貼過貼圖，基本 PBR 材質製作
- 2：在 Designer 裡做過程序材質、設計過自訂 graph、了解 noise + warp 的組合邏輯
- 3：設計過可重用的 Substance template 系統、做過 bake 優化或自訂 filter

**USD 格式與交換工作流**
- 0：沒聽過 USD
- 1：知道 USD 是場景交換格式，知道它用在大型 pipeline
- 2：在專案裡用過 USD 做資產匯入匯出、了解 Layer、Prim、Override 的概念
- 3：設計過 USD-based pipeline、處理過 variant set、自訂過 composition arc

**Unreal PCG Framework**
- 0：沒用過 UE
- 1：知道 PCG 是 UE5 的程序化生成工具，但沒實際操作
- 2：做過 PCG Graph、用過 Sampler / Filter / Spawner 節點做植被或物件散佈
- 3：設計過複雜的 PCG 系統、自訂過 PCG 節點、或整合過 PCG 與 Nanite / Lumen

---

### 底層圖形概念

**Render Pass / G-Buffer 架構**
- 0：不知道 Deferred 和 Forward 的差異
- 1：知道 Deferred 是先把幾何資訊存進 G-Buffer，再做光照計算
- 2：知道 G-Buffer 裡存了哪些資料（Normal、Albedo、Roughness、Depth），理解 render pass 的執行順序
- 3：能解釋 Deferred 在多光源場景的 complexity 優勢、知道 Tile-based Deferred 的原理

**Draw Call Batching / GPU Instancing**
- 0：不知道 draw call 是什麼
- 1：知道 draw call 多會影響 CPU 效能，做過合併 mesh 或靜態 batching
- 2：理解 Static Batching / Dynamic Batching / GPU Instancing 的適用條件與限制
- 3：做過自訂的 instanced draw 或 indirect draw、處理過 SRP Batcher 的相容性問題

**記憶體階層（VRAM / 系統 RAM）**
- 0：只知道顯示卡有 VRAM，容量不夠會卡
- 1：知道 VRAM 和系統 RAM 的差異，知道貼圖上傳 GPU 的基本流程
- 2：理解 texture streaming、mipmap 的記憶體管理、知道 bandwidth 是 GPU 的主要瓶頸之一
- 3：能分析 memory budget、理解 cache hierarchy 對 shader 效能的影響

**同步 Barrier 概念**
- 0：不知道 GPU 同步是什麼
- 1：知道 GPU 執行是非同步的，聽過 pipeline barrier 或 memory barrier
- 2：理解 resource state transition（D3D12 ResourceBarrier、Vulkan image layout transition），知道為什麼需要它
- 3：能設計正確的 barrier 策略、處理過 hazard（read-after-write）導致的 artifact

**Vulkan / DX12 概念層**
- 0：完全沒接觸過低階圖形 API
- 1：讀過教學或文件，知道它們比 OpenGL/DX11 更底層、需要手動管理資源
- 2：實際寫過 Vulkan 或 DX12 的程式，建立過 render pass、command buffer、descriptor set
- 3：設計過完整的 renderer、處理過 multi-frame in flight、memory aliasing

---

### 數學基礎

**線性代數（向量、矩陣）**
- 0：不熟線代，看到矩陣就頭痛
- 1：知道向量的點積、叉積，理解矩陣做座標轉換的概念
- 2：能手算 TRS 矩陣、理解 model/view/projection 三個空間的轉換、會用向量做幾何判斷
- 3：能用矩陣分解、理解齊次座標的 w 分量意義、自己實作過線代工具庫

**四元數與旋轉**
- 0：不知道四元數是什麼，只用過 Euler angles
- 1：知道四元數用來表示旋轉、避免 Gimbal Lock，會用引擎 API 做旋轉插值（Slerp）
- 2：理解四元數的 `w, x, y, z` 分量意義、能手算簡單的四元數乘法、知道和旋轉矩陣的轉換
- 3：能從第一原則推導四元數旋轉公式、理解雙重覆蓋（q 和 -q 代表同一旋轉）

**色彩科學（Gamma, ACES, sRGB）**
- 0：不知道 gamma 是什麼，只知道顏色有 RGB 值
- 1：知道 gamma correction 的存在，知道 sRGB 和 Linear 的差異，會在引擎裡正確設定貼圖的色彩空間
- 2：理解為什麼要在 linear space 做光照計算、知道 ACES tonemapping 的用途、能解釋 HDR 和 LDR 的工作流差異
- 3：能解釋 CIE XYZ 色彩空間、理解 color gamut 和 white point 的轉換、設計過完整的色彩管理 pipeline

**渲染管線資料流**
- 0：不清楚渲染管線的執行流程
- 1：知道大致流程：頂點處理 → 光柵化 → fragment shader → 輸出
- 2：能解釋每個 stage 的輸入輸出（Vertex Buffer → VS → Rasterizer → PS → RenderTarget）、知道 depth test 在哪個階段
- 3：能解釋 early-z、geometry shader、stream output、以及各 stage 在硬體上的對應執行單元

---

## 結果分析

### 計算領域平均分

```
領域平均 = 各子技能分數加總 ÷ 子技能數量
```

| 領域 | 子技能數 |
|------|---------|
| Shader 編寫 | 4 |
| 材質系統 | 4 |
| 光照 & GI | 5 |
| VFX & 粒子 | 4 |
| 效能分析 | 5 |
| Pipeline & 工具 | 5 |
| 底層圖形概念 | 5 |
| 數學基礎 | 4 |

### 找出最弱領域

1. 把 8 個領域的平均分由低到高排列
2. 取最低的 1–2 個
3. 在最弱領域內，找分數為 0 的子技能 — 那是最優先的補強點

### 補強策略

- **0 分子技能**：先從知識庫對應章節建立基本概念，再找一個 Lab 動手做
- **1 分子技能**：找邊緣案例練習；閱讀對應章節的進階小節
- **2 分子技能**：做 Implementation Lab、讀 SIGGRAPH/GDC 原始論文
- **3 分子技能**：寫作品集、做複雜系統、分享給別人

---

## 提示

你可以把這份評分標準貼給任何 AI 助手，請它用**逐項訪談**的方式引導你評估：

> 「請根據 SKILL-ASSESSMENT-GUIDE.md 的評分標準，用逐項訪談的方式問我具體的工作經驗，幫我判定每個子技能的分數，然後找出最弱的 1–2 個領域。」
