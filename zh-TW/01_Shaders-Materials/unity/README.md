# Unity Shader 與材質系統

**穩定性標籤**：`[ENGINE-VERSIONED: Unity 6 / URP 17 / HDRP 17]`

> 引擎升版時需要驗證以下內容是否仍然有效。

---

## Unity 渲染管線選擇

| 管線 | 適用情境 | TA 重點 |
|------|---------|---------|
| **URP**（Universal RP） | 行動裝置、多平台、輕量級 | Shader Graph + 手寫 URP HLSL |
| **HDRP**（High Definition RP） | PC/主機高畫質 | 複雜材質系統、Probe Volume |
| **Built-in**（舊管線） | 維護舊專案 | 避免在新專案使用 |

**關鍵限制**：Shader 在不同管線間不能直接移植。URP shader ≠ HDRP shader。

---

## URP Shader 結構

```hlsl
Shader "Custom/MyURPShader" {
    Properties {
        _BaseMap ("Texture", 2D) = "white" {}
        _BaseColor ("Color", Color) = (1,1,1,1)
    }
    SubShader {
        Tags { "RenderType"="Opaque" "RenderPipeline"="UniversalPipeline" }
        
        Pass {
            Name "ForwardLit"
            Tags { "LightMode"="UniversalForward" }
            
            HLSLPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Core.hlsl"
            #include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Lighting.hlsl"
            
            TEXTURE2D(_BaseMap); SAMPLER(sampler_BaseMap);
            CBUFFER_START(UnityPerMaterial)
                float4 _BaseMap_ST;
                float4 _BaseColor;
            CBUFFER_END
            
            struct Attributes {
                float4 positionOS : POSITION;
                float2 uv : TEXCOORD0;
                float3 normalOS : NORMAL;
            };
            
            struct Varyings {
                float4 positionCS : SV_POSITION;
                float2 uv : TEXCOORD0;
                float3 normalWS : TEXCOORD1;
            };
            
            Varyings vert(Attributes IN) {
                Varyings OUT;
                OUT.positionCS = TransformObjectToHClip(IN.positionOS.xyz);
                OUT.uv = TRANSFORM_TEX(IN.uv, _BaseMap);
                OUT.normalWS = TransformObjectToWorldNormal(IN.normalOS);
                return OUT;
            }
            
            half4 frag(Varyings IN) : SV_Target {
                half4 texColor = SAMPLE_TEXTURE2D(_BaseMap, sampler_BaseMap, IN.uv);
                return texColor * _BaseColor;
            }
            ENDHLSL
        }
    }
}
```

---

## Shader Graph 要點

**適合用 Shader Graph 的情況：**
- 複雜的節點混合邏輯（用程式碼難以維護）
- 美術師需要自行調整的材質參數
- 快速 Prototype

**需要手寫 HLSL 的情況：**
- Shader Graph 沒有的節點功能
- 效能最佳化（精確控制指令數）
- Compute Shader
- 複雜的 Vertex 動畫

**混合做法（推薦）**：在 Shader Graph 裡用 `Custom Function` 節點嵌入 HLSL 程式碼。

---

## SRP Batcher（效能關鍵）

**原理**：把使用相同 shader variant 的物件批次處理，降低 CPU 設定 constant buffer 的開銷。

**啟用條件**：
1. Project Settings → URP Asset → SRP Batcher 打勾
2. Shader 必須使用 `CBUFFER_START(UnityPerMaterial)` 包裝材質屬性

**驗證方式**：Frame Debugger → 看 SRP Batch 數量

---

## GPU Instancing

```hlsl
// 在 Properties 加上
[Toggle(INSTANCING_ON)] _Instancing ("Instancing", Float) = 0

// Pragma
#pragma multi_compile_instancing

// 在 CBUFFER 裡用 UNITY_INSTANCING_BUFFER
UNITY_INSTANCING_BUFFER_START(UnityPerMaterial)
    UNITY_DEFINE_INSTANCED_PROP(float4, _BaseColor)
UNITY_INSTANCING_BUFFER_END(UnityPerMaterial)
```

---

## 常用 Unity ShaderLibrary 函數

```hlsl
// 座標轉換
TransformObjectToHClip(posOS)        // Object → Clip
TransformObjectToWorld(posOS)        // Object → World
TransformObjectToWorldNormal(normalOS) // 法線 Object → World
TransformWorldToView(posWS)          // World → View

// 光照
GetMainLight()                       // 取得主光源
InputData                            // 輸入資料結構（用於 PBR 計算）
UniversalFragmentPBR(inputData, ...)  // URP PBR 光照計算
```

---

## Adaptive Probe Volume (APV) — Unity 6

取代舊的 Light Probe Group，自動填充場景的 GI probe。

```
Window → Rendering → Lighting → Probe Volumes
GameObject → Light → Probe Volume (Auto)
```

---

## 學習資源

- 📖 [Unity URP ShaderLibrary Source](https://github.com/Unity-Technologies/Graphics) — 直接讀引擎源碼
- 🎥 [Catlike Coding URP Tutorials](https://catlikecoding.com/unity/tutorials/custom-srp/) — 最深入的 Unity 渲染教學
- 🎥 [Ben Cloward — Unity Shader Tutorials](https://www.youtube.com/c/BenCloward) — 實用 TA 向教學
- 📖 [Unity Graphics Documentation](https://docs.unity3d.com/6000.0/Documentation/Manual/render-pipelines.html)

---

## 實作練習 (project-ideas)

1. **URP Triplanar Shader**：不用 UV 展開，用 world position 做三軸貼圖投影。約束：支援 Normal map 的正確切線空間轉換、SRP Batcher 相容。
2. **ShaderGraph + Custom HLSL 混合材質**：在 ShaderGraph 裡用 Custom Function 節點實作一個 Voronoi crack 程序材質，暴露 crack density 和 edge glow 兩個參數給美術師。
