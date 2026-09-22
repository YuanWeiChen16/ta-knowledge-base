# 外部同類資源索引

**其他人在 GitHub 和網路上建立的類似知識庫、課程和程式碼倉庫。**

> 和本資料夾互補使用：本資料夾是「概念 + 方法論」，下面的資源是「可直接執行的程式碼範例」。

---

## GitHub 知識庫（最接近本資料夾的結構）

### frepiso/TechArt-Guide ⭐ 最推薦
**URL**：https://github.com/frepiso/TechArt-Guide  
**複本（fork）**：https://github.com/joelRVC/TechArt-Guide

```
內容覆蓋：
  Houdini（VEX、USD、LOP）
  OpenUSD（完整概念和腳本）
  Python、C++、Linux/Docker/Git
  渲染數學（向量、矩陣、座標系）
  PBR（Normal Map、MaterialX）
  Vulkan API（完整概念層）
  相機（快門、光圈、景深）

特點：
  - 配備 Docker DevContainer，Python + USD 開箱即用
  - 有可執行的 Houdini VEX 範例程式碼
  - 涵蓋面廣，適合程式師背景（有 C++ 和 Linux 章節）
  - 持續更新中
```

**和本資料夾的差異**：TechArt-Guide 偏 Houdini/USD/工具鏈，本資料夾偏 Unreal/Shader/GDC 實作。

---

## GitHub 程式碼倉庫（可直接執行的 Shader 範例）

### csdjk/LearnUnrealShader ⭐ Unreal 必看
**URL**：https://github.com/csdjk/LearnUnrealShader  
**引擎**：Unreal Engine 5.3.2

```
包含的 Shader 範例：
  Surface Shaders：
    - TriPlanar Projection（三軸投影）
    - Ice（折射 + SSS 半透明）
    - InteriorCube（假室內空間 Cubemap Raymarching）

  Post-Process Effects：
    - Outline（深度 + 法線 Edge Detection）
    - PixelationFilter（UV 量化像素化）
    - ActionLines（程序射線動畫）
    - VHS（老式電視效果）

架構模式展示：
  - Material Instance Pattern
  - Material Function Pattern
  - Parameter Collection Pattern
```

### PacktPublishing/UE5-Shaders-Cookbook
**URL**：https://github.com/PacktPublishing/Unreal-Engine-5-Shaders-and-Effects-Cookbook-2nd-Edition  
**書籍**：Unreal Engine 5 Shaders and Effects Cookbook (2nd ed.) by Brais Brenlla

```
50+ 個 UE5 材質和效果的完整範例：
  - Lumen / Nanite 整合材質
  - PBR pipeline 完整流程
  - 多平台優化技術
  - 進階特效（Subsurface、Translucency）
書中程式碼 = 本資料夾 Lab 的參考答案之一
```

### DeGGeD/ShaderStory ⭐ Unity 版本
**URL**：https://github.com/degged/shaderstory  
**引擎**：Unity 6.0+ URP

```
免費開放的 Unity HLSL 教學資源庫：
  - 常用 HLSL 函數範例
  - 邊緣過渡技術
  - 圖案與形狀生成
  - 光照模型實作
Creative Commons 授權，可自由學習改用
```

### Toffee-Meow/Shader-Grimoire ⭐ 中文 TA 學習記錄
**URL**：https://github.com/Toffee-Meow/Shader-Grimoire  
**引擎**：Unreal Engine 5.x、Unity 2022

```
一位正在學習 TA 的中文使用者的 Shader 學習筆記倉庫：
  重點方向：卡通渲染（Toon/NPR）
  包含：角色卡渲、通用材質、VFX、後處理、風格化渲染
  語言：HLSL、GLSL、ShaderLab
  特點：有詳細的中文說明和渲染效果截圖
  對你的意義：和你的學習路徑相似，可以看別人如何記錄和組織知識
```

### SaiiPrashanth/Shader_Dump
**URL**：https://github.com/SaiiPrashanth/Shader_Dump  
**引擎**：Unity Built-in / URP / HDRP

```
個人 Unity HLSL 片段收集庫：
  Surface/：Toon、PBR 變體、Dissolve
  PostProcess/：Outline、Vignette、Scanline
  Particles/：Soft Particle、Distortion VFX
  Utility/：Noise 函數、Blend 工具
每個 Shader 都有管線標記和 comment
```

---

## 進階：Unreal 底層圖形程式（程式師向）

### heyx3/ExtendedGraphicsProgramming ⭐ 程式師必看
**URL**：https://github.com/heyx3/ExtendedGraphicsProgramming  
**系列文章**：https://medium.com/@manning.w27/advanced-graphics-programming-in-unreal-part-1-10488f2e17dd

```
7 篇系列文章 + 配套 Plugin：

文章主題：
  Part 1: 介紹
  Part 2: Rendering Abstractions（RHI、RDG）和執行緒
  Part 3: Scenes、Views、SceneViewExtension
  Part 4: Global Shaders
  Part 5: Simple Material Shaders
  Part 6: Advanced Material Shaders
  Part 7: Mesh-Material Shaders 和 Mesh Passes

Plugin 功能：
  - Custom Post-Process Material Shaders
  - Screen-Space Passes（Compute 或 Vertex+Pixel）
  - Custom Render Passes + Mesh Passes
  - Material Shader Compilation API

適合你的原因：
  - 作者明確說「需要 C++ 多執行緒知識」
  - 把 Unreal 渲染系統當成 C++ 工程問題來解
  - 是目前網路上把 UE5 自訂渲染 Pass 解釋最清楚的資源
  
這是從 TA 過渡到 Graphics Programmer 的橋樑資源。
```

---

## 比較表

| 資源 | 引擎 | 類型 | 適合程度 | 特色 |
|------|------|------|---------|------|
| frepiso/TechArt-Guide | 引擎無關 | 知識庫 | ⭐⭐⭐⭐⭐ | 最完整的 TA 知識庫，有 Docker |
| csdjk/LearnUnrealShader | UE5 | 程式碼範例 | ⭐⭐⭐⭐⭐ | 直接在 UE5 跑的 Shader 範例 |
| UE5 Shaders Cookbook | UE5 | 書+程式碼 | ⭐⭐⭐⭐ | 50+ 配方，有書可對照 |
| DeGGeD/ShaderStory | Unity | 程式碼範例 | ⭐⭐⭐ | Unity URP HLSL 免費資源 |
| Toffee-Meow/Shader-Grimoire | UE5+Unity | 學習記錄 | ⭐⭐⭐⭐ | 中文，卡通渲染專注 |
| heyx3/ExtendedGraphicsProgramming | UE5 | Plugin+文章 | ⭐⭐⭐⭐⭐ | 程式師必看，底層渲染 |

---

## 如何與本資料夾搭配使用

```
本資料夾：          外部資源：
概念理解         →  csdjk/LearnUnrealShader（直接看 UE5 範例）
Lab 實作參考     →  UE5 Shaders Cookbook（找類似題目的解法）
Houdini/USD     →  frepiso/TechArt-Guide（比本資料夾更完整）
底層渲染深入     →  heyx3/ExtendedGraphicsProgramming（程式師橋樑）
Unity 參考      →  ShaderStory / Shader_Dump
```
