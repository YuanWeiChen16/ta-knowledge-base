# HLSL Core Syntax

**Stability Tag**: `[STABLE]`

---

## Data Types

```hlsl
float    // 32-bit float, used for most calculations
half     // 16-bit float, used for mobile performance optimization
int      // 32-bit integer
uint     // 32-bit unsigned integer
bool     // boolean

// Vector types
float2   // (x, y)
float3   // (x, y, z)
float4   // (x, y, z, w)

// Matrix types
float4x4 // 4×4 matrix (used for transforms)
float3x3 // 3×3 matrix (used for normal transforms)
```

---

## Built-In Function Quick Reference

```hlsl
// Math
dot(a, b)           // dot product
cross(a, b)         // cross product
normalize(v)        // normalize
length(v)           // vector length
lerp(a, b, t)       // linear interpolation
saturate(x)         // clamp(x, 0, 1)
pow(x, n)           // x to the power of n
sqrt(x)             // square root
abs(x)              // absolute value
sign(x)             // sign (-1, 0, 1)
step(edge, x)       // x >= edge ? 1 : 0
smoothstep(e0,e1,x) // smooth step
frac(x)             // fractional part (0-1 cycling)
floor(x)            // floor
ceil(x)             // ceiling

// Trigonometry
sin(x), cos(x), tan(x)
atan2(y, x)         // four-quadrant arctangent

// Texture sampling
tex2D(sampler, uv)          // basic sampling (legacy)
SAMPLE_TEXTURE2D(tex, s, uv) // Unity HLSL macro
texture.Sample(s, uv)        // HLSL 5.0 object-style

// Partial derivatives (used for mip level calculation)
ddx(x), ddy(x)      // difference between adjacent pixels
```

---

## Basic Vertex Shader Structure

```hlsl
struct VertexInput {
    float4 positionOS : POSITION;  // Object Space position
    float3 normalOS   : NORMAL;
    float4 tangentOS  : TANGENT;
    float2 uv         : TEXCOORD0;
};

struct VertexOutput {
    float4 positionCS : SV_POSITION; // Clip Space (must output)
    float2 uv         : TEXCOORD0;
    float3 normalWS   : TEXCOORD1;
};

VertexOutput vert(VertexInput v) {
    VertexOutput o;
    // Object → Clip space transform
    o.positionCS = mul(UNITY_MATRIX_MVP, v.positionOS);
    o.uv = v.uv;
    // Object → World space normal
    o.normalWS = mul((float3x3)unity_ObjectToWorld, v.normalOS);
    return o;
}
```

---

## Basic Fragment Shader Structure

```hlsl
float4 frag(VertexOutput i) : SV_Target {
    // Sample Albedo texture
    float4 albedo = SAMPLE_TEXTURE2D(_MainTex, sampler_MainTex, i.uv);
    
    // Simple Diffuse lighting
    float3 normalWS = normalize(i.normalWS);
    float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
    float NdotL = saturate(dot(normalWS, lightDir));
    
    float3 color = albedo.rgb * NdotL;
    return float4(color, 1.0);
}
```

---

## Common Semantics

| Semantic | Usage |
|----------|-------|
| `POSITION` | Vertex position input |
| `SV_POSITION` | Clip space position output (required) |
| `NORMAL` | Normal |
| `TANGENT` | Tangent |
| `TEXCOORD0~7` | UV coordinates or arbitrary interpolated data |
| `COLOR` | Vertex color |
| `SV_Target` | Fragment shader color output |
| `SV_Depth` | Custom depth output |

---

## Performance Considerations

```hlsl
// 🐢 Slow: dynamic branching (GPU executes both)
if (condition) { expensiveOp(); }

// 🐇 Fast: math replaces branching
float result = lerp(a, b, step(threshold, value));

// 🐢 Slow: pow(x, 2.2) approximates gamma
float gamma = pow(color, 2.2);

// 🐇 Fast: use multiplication for known exponents
float sq = x * x; // pow(x, 2)

// Mobile: prefer half over float
half3 color = (half3)albedo.rgb; // saves register pressure
```

---

## Learning Resources

- 📖 [The Book of Shaders](https://thebookofshaders.com/) — the most beginner-friendly shader introduction (GLSL, concepts are universal)
- 🎥 [Ben Cloward YouTube](https://www.youtube.com/c/BenCloward) — Unity/Unreal shader implementation, produced by an industry TA
- 📖 [Catlike Coding](https://catlikecoding.com/unity/tutorials/) — in-depth Unity HLSL tutorials
- 🎥 [Acerola YouTube](https://www.youtube.com/@Acerola_t) — advanced shader techniques

---

## Hands-On Exercises (project-ideas)

1. **Pure HLSL dissolve effect**: Use a noise texture + threshold to make a dissolve shader with edge glow. Constraint: implement it once in Unity URP and once in Unreal, and compare syntax differences.
2. **Vertex offset grass**: Use only a vertex shader to make grass sway in the wind based on world coordinates — no animation. Constraint: must support GPU instancing.
