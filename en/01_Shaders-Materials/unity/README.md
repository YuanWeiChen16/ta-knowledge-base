# Unity Shader & Material Systems

**Stability Tag**: `[ENGINE-VERSIONED: Unity 6 / URP 17 / HDRP 17]`

> Validate the following content is still accurate when upgrading engine versions.

---

## Unity Render Pipeline Selection

| Pipeline | Use Case | TA Focus |
|----------|----------|---------|
| **URP** (Universal RP) | Mobile, multi-platform, lightweight | Shader Graph + hand-written URP HLSL |
| **HDRP** (High Definition RP) | PC/console high fidelity | Complex material system, Probe Volume |
| **Built-in** (legacy pipeline) | Maintaining old projects | Avoid for new projects |

**Key limitation**: Shaders cannot be directly ported between pipelines. URP shader ≠ HDRP shader.

---

## URP Shader Structure

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

## Shader Graph Key Points

**When Shader Graph is the right choice:**
- Complex node blending logic (hard to maintain in code)
- Material parameters that artists need to adjust themselves
- Rapid prototyping

**When hand-written HLSL is needed:**
- Functionality not available as Shader Graph nodes
- Performance optimization (precise instruction count control)
- Compute Shaders
- Complex vertex animation

**Mixed approach (recommended)**: Use `Custom Function` nodes inside Shader Graph to embed HLSL code.

---

## SRP Batcher (Performance Key)

**Principle**: Batches objects using the same shader variant to reduce CPU constant buffer setup overhead.

**Requirements to enable**:
1. Project Settings → URP Asset → SRP Batcher checked
2. Shader must wrap material properties with `CBUFFER_START(UnityPerMaterial)`

**Verification**: Frame Debugger → check the SRP Batch count

---

## GPU Instancing

```hlsl
// Add to Properties
[Toggle(INSTANCING_ON)] _Instancing ("Instancing", Float) = 0

// Pragma
#pragma multi_compile_instancing

// Use UNITY_INSTANCING_BUFFER in CBUFFER
UNITY_INSTANCING_BUFFER_START(UnityPerMaterial)
    UNITY_DEFINE_INSTANCED_PROP(float4, _BaseColor)
UNITY_INSTANCING_BUFFER_END(UnityPerMaterial)
```

---

## Common Unity ShaderLibrary Functions

```hlsl
// Coordinate transforms
TransformObjectToHClip(posOS)        // Object → Clip
TransformObjectToWorld(posOS)        // Object → World
TransformObjectToWorldNormal(normalOS) // Normal Object → World
TransformWorldToView(posWS)          // World → View

// Lighting
GetMainLight()                       // get main light
InputData                            // input data structure (used for PBR calculation)
UniversalFragmentPBR(inputData, ...)  // URP PBR lighting calculation
```

---

## Adaptive Probe Volume (APV) — Unity 6

Replaces the old Light Probe Group, automatically fills the scene with GI probes.

```
Window → Rendering → Lighting → Probe Volumes
GameObject → Light → Probe Volume (Auto)
```

---

## Learning Resources

- 📖 [Unity URP ShaderLibrary Source](https://github.com/Unity-Technologies/Graphics) — read the engine source directly
- 🎥 [Catlike Coding URP Tutorials](https://catlikecoding.com/unity/tutorials/custom-srp/) — the most in-depth Unity rendering tutorials
- 🎥 [Ben Cloward — Unity Shader Tutorials](https://www.youtube.com/c/BenCloward) — practical TA-oriented tutorials
- 📖 [Unity Graphics Documentation](https://docs.unity3d.com/6000.0/Documentation/Manual/render-pipelines.html)

---

## Hands-On Exercises (project-ideas)

1. **URP Triplanar Shader**: Without UV unwrapping, use world position for three-axis texture projection. Constraint: supports correct tangent space transforms for Normal maps, SRP Batcher compatible.
2. **ShaderGraph + Custom HLSL hybrid material**: Use a Custom Function node in ShaderGraph to implement a Voronoi crack procedural material, exposing crack density and edge glow as parameters for artists.
