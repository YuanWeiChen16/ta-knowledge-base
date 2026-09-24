# HLSL 核心語法

**穩定性標籤**：`[STABLE]`

---

## 資料型別

```hlsl
float    // 32-bit 浮點，大多數計算用這個
half     // 16-bit 浮點，行動裝置效能優化用
int      // 32-bit 整數
uint     // 32-bit 無符號整數
bool     // 布林值

// 向量型別
float2   // (x, y)
float3   // (x, y, z)
float4   // (x, y, z, w)

// 矩陣型別
float4x4 // 4×4 矩陣（transform 用）
float3x3 // 3×3 矩陣（法線 transform 用）
```

---

## 內建函數快查

```hlsl
// 數學
dot(a, b)           // 點積
cross(a, b)         // 叉積
normalize(v)        // 正規化
length(v)           // 向量長度
lerp(a, b, t)       // 線性插值
saturate(x)         // clamp(x, 0, 1)
pow(x, n)           // x 的 n 次方
sqrt(x)             // 平方根
abs(x)              // 絕對值
sign(x)             // 正負符號 (-1, 0, 1)
step(edge, x)       // x >= edge ? 1 : 0
smoothstep(e0,e1,x) // 平滑 step
frac(x)             // 小數部分（0-1 循環）
floor(x)            // 無條件捨去
ceil(x)             // 無條件進位

// 三角函數
sin(x), cos(x), tan(x)
atan2(y, x)         // 四象限 arctangent

// 貼圖採樣
tex2D(sampler, uv)          // 基本採樣（舊式）
SAMPLE_TEXTURE2D(tex, s, uv) // Unity HLSL 巨集
texture.Sample(s, uv)        // HLSL 5.0 物件式

// 偏導數（用於 mip level 計算）
ddx(x), ddy(x)      // 相鄰像素的差值
```

---

## Vertex Shader 基本結構

```hlsl
struct VertexInput {
    float4 positionOS : POSITION;  // Object Space position
    float3 normalOS   : NORMAL;
    float4 tangentOS  : TANGENT;
    float2 uv         : TEXCOORD0;
};

struct VertexOutput {
    float4 positionCS : SV_POSITION; // Clip Space（必須輸出）
    float2 uv         : TEXCOORD0;
    float3 normalWS   : TEXCOORD1;
};

VertexOutput vert(VertexInput v) {
    VertexOutput o;
    // Object → Clip space transform
    o.positionCS = mul(UNITY_MATRIX_MVP, v.positionOS);
    o.uv = v.uv;
    // Object → World space 法線
    o.normalWS = UnityObjectToWorldNormal(v.normalOS); // 正確處理非等比縮放的法線轉換
    return o;
}
```

---

## Fragment Shader 基本結構

```hlsl
float4 frag(VertexOutput i) : SV_Target {
    // 採樣 Albedo 貼圖
    float4 albedo = SAMPLE_TEXTURE2D(_MainTex, sampler_MainTex, i.uv);
    
    // 簡易 Diffuse 光照
    float3 normalWS = normalize(i.normalWS);
    float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
    float NdotL = saturate(dot(normalWS, lightDir));
    
    float3 color = albedo.rgb * NdotL;
    return float4(color, 1.0);
}
```

---

## 常見 Semantic 語義

| Semantic | 用途 |
|----------|------|
| `POSITION` | 頂點位置輸入 |
| `SV_POSITION` | Clip space 位置輸出（必須） |
| `NORMAL` | 法線 |
| `TANGENT` | 切線 |
| `TEXCOORD0~7` | UV 座標或任意插值資料 |
| `COLOR` | 頂點色 |
| `SV_Target` | Fragment shader 顏色輸出 |
| `SV_Depth` | 自定義深度輸出 |

---

## 效能注意事項

```hlsl
// 🐢 慢：動態分支（GPU 兩個都執行）
if (condition) { expensiveOp(); }

// 🐇 快：數學替代分支
float result = lerp(a, b, step(threshold, value));

// 🐢 慢：pow(x, 2.2) 近似 gamma
float gamma = pow(color, 2.2);

// 🐇 快：已知指數用乘法
float sq = x * x; // pow(x, 2)

// 行動裝置：優先使用 half 而非 float
half3 color = (half3)albedo.rgb; // 節省 register 壓力
```

---

## 學習資源

- 📖 [The Book of Shaders](https://thebookofshaders.com/) — 最友善的 shader 入門（GLSL，概念通用）
- 🎥 [Ben Cloward YouTube](https://www.youtube.com/c/BenCloward) — Unity/Unreal shader 實作，業界 TA 出品
- 📖 [Catlike Coding](https://catlikecoding.com/unity/tutorials/) — Unity HLSL 深度教學
- 🎥 [Acerola YouTube](https://www.youtube.com/@Acerola_t) — 進階 shader 技術

---

## 實作練習 (project-ideas)

1. **純 HLSL 溶解效果**：用 noise 貼圖 + threshold 做一個 dissolve shader，加上邊緣發光。約束：在 Unity URP 和 Unreal 各做一次，比較語法差異。
2. **Vertex offset 草地**：純 vertex shader 讓草地根據世界座標做風吹搖擺，不用 animation。約束：需要支援 GPU instancing。
