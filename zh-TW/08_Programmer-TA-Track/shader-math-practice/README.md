# Shader 數學練習（類 LeetCode）

**給程式師的 Shader 練習資源——有即時視覺回饋的互動題目。**

---

## 核心資源

### 1. Shadertoy — 最像 LeetCode 的平台
**URL**：https://www.shadertoy.com

```
工作方式：
  - 線上 GLSL Fragment Shader 編輯器
  - 即時視覺回饋（改一行，立刻看到變化）
  - 龐大的社群作品庫（可以讀別人的 code）
  - 內建 iTime、iResolution、iMouse 等 uniform

程式師使用策略：
  1. 先看別人的作品
  2. 打開 code，逐行讀懂
  3. 修改一個變數，觀察視覺變化
  4. 嘗試自己重現（不看答案）
```

### 2. The Book of Shaders — 結構化入門
**URL**：https://thebookofshaders.com

有程式底子可以快速掃過，重點章節：
- **Ch. 5 — Shaping Functions**：`smoothstep`、`step`、`fract` 的視覺化理解
- **Ch. 9 — Patterns**：用數學生成重複圖案
- **Ch. 10 — Random**：Shader 裡的「隨機」為什麼必須是偽隨機
- **Ch. 11 — Noise**：Value Noise、Perlin Noise、FBM 從程式角度理解
- **Ch. 13 — Fractal Brownian Motion**：噪聲疊加的標準做法

### 3. Graphics Workshop (Harvard/MIT 風格作業)
**URL**：https://github.com/GongXiangShun/graphics-workshop

```
包含 5 個有明確 spec 的練習題：
  1. Quilt Patterns      ← Fragment Shader 入門，procedural 2D
  2. Landscape Gen       ← Simplex Noise 地形生成
  3. Rasterization       ← 實作 Phong shading（從零寫）
  4. Stylized Rendering  ← 卡通渲染、非寫實風格
  5. Ray Tracing         ← 純 Shader Ray Tracer

全部在瀏覽器 WebGL 執行，不需要安裝任何東西。
對程式師：這些題目就像 graphics LeetCode，有清楚的 input/output spec。
```

### 4. CIS 5660 Penn (University of Pennsylvania)
**URL**：https://github.com/CIS-5660-Fall-2024

```
賓州大學 Technical Art 課程，作業題目全部開放：
  HW04 Stylization：
    - 實作 Toon Shader（多光源支援）
    - Depth + Normal Buffer Outline（Sobel / Robert's Cross）
    - 全螢幕 Post-Process Effect
    - Unity URP Render Feature 自訂 Pass

這是真實大學 TA 課程的作業，難度適中，有完整說明。
```

---

## 練習題目清單（自訂難度排序）

### ⭐ Level 1 — Shader 基礎肌肉

```glsl
// 在 Shadertoy 上完成這些，每題 < 1 小時：
1. 畫一個會隨時間旋轉的色輪
2. 用 smoothstep 畫一個邊緣柔和的圓
3. 用 fract 製作無限重複的格子圖案
4. 用 sin/cos 製作波浪動畫
5. 實作 2D SDF（Signed Distance Field）的圓、矩形、星形
```

### ⭐⭐ Level 2 — 核心技術

```glsl
// 每題 1-3 小時：
6.  實作 Value Noise（從 hash 函數開始）
7.  實作 FBM（Fractional Brownian Motion，疊加多層 noise）
8.  Phong 光照模型（diffuse + specular + ambient）
9.  Dissolve 效果（noise threshold + discard）
10. Rim Light / Fresnel 效果
11. Triplanar Mapping（不用 UV 做貼圖投影）
```

### ⭐⭐⭐ Level 3 — 業界技術

```glsl
// 每題 3-8 小時：
12. Toon Shader（量化光照 + Sobel outline）
13. Parallax Occlusion Mapping（視差位移）
14. Screen Space Reflection（SSR）的近似實作
15. Voronoi Noise + Worley Noise
16. Ray Marching 基礎（SDF 場景 + 基本光照）
17. 用 Compute Shader 做 GPU 粒子模擬
```

---

## Shadertoy 的「拆解學習法」

程式師最適合的學習方式：

```
步驟：
1. 在 Shadertoy 搜尋你想學的效果
   （搜尋 "pbr" / "dissolve" / "voronoi" / "raymarching"）

2. 打開一個 star 數高的 shader，不要先看 code

3. 先自己嘗試實作（哪怕只是草稿）

4. 對照別人的 code：
   - 每個 function 的輸入輸出是什麼？
   - 每個 magic number 是從哪來的？
   - 有沒有更簡潔的寫法？

5. Fork 後修改，觀察視覺變化

6. 用自己的語言在程式碼裡加 comment
```

---

## GLSL → HLSL → UE5 對照表

程式師轉 TA 必備的語法對照：

```hlsl
// 型別
GLSL          HLSL (Unreal)
vec2          float2
vec3          float3
vec4          float4
mat4          float4x4
sampler2D     Texture2D + SamplerState

// 函數
mix(a,b,t)    lerp(a, b, t)
fract(x)      frac(x)
mod(a,b)      fmod(a, b)
texture(s,uv) s.Sample(ss, uv)  // HLSL 5.0

// 數學（相同）
dot, cross, normalize, length,
sqrt, pow, abs, sin, cos, clamp,
step, smoothstep, floor, ceil

// 特殊差異
GLSL: y+ = 上（OpenGL Normal Map）
HLSL: y- = 上（DirectX Normal Map）← 記住這個！

// UE5 Material Custom Node
// 不需要宣告型別，直接 return：
return dot(normalize(Normal), normalize(LightDir));
```

---

## 學習資源連結

| 資源 | URL | 用途 |
|------|-----|------|
| Shadertoy | shadertoy.com | 每日練習 |
| The Book of Shaders | thebookofshaders.com | 結構化入門 |
| Graphics Workshop | github.com/GongXiangShun/graphics-workshop | 有 spec 的作業 |
| CIS 5660 Penn | github.com/CIS-5660-Fall-2024 | 大學 TA 課作業 |
| Shader Story | github.com/degged/shaderstory | Unity URP 開源範例庫 |
| Khronos GLSL Docs | registry.khronos.org/OpenGL-Refpages | 函數查詢 |

---

## 程式師的 Shader Debug 心態轉換

```
程式師的 debug：
  print("value =", x)  ← 用文字輸出

Shader 的 debug：
  return float4(x, 0, 0, 1);  ← 用顏色輸出（紅色越亮 = x 越大）

常用技巧：
  // 視覺化法線
  return float4(Normal * 0.5 + 0.5, 1);

  // 視覺化 UV
  return float4(UV.x, UV.y, 0, 1);

  // 視覺化深度
  float d = SceneDepth / 1000.0;
  return float4(d, d, d, 1);

  // 確認某個 bool 條件
  return condition ? float4(0,1,0,1) : float4(1,0,0,1);  // 綠=true 紅=false
```
