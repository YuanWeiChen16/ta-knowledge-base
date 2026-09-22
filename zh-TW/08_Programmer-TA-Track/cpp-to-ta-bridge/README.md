# C++/系統程式知識對應 TA 工作

**你的程式底子比你想的更直接對應 TA 工作。這裡是明確的對應關係。**

---

## 直接對應關係

### C++ → HLSL/GLSL

```cpp
// C++ 你熟悉的
struct Vertex {
    glm::vec3 position;
    glm::vec3 normal;
    glm::vec2 uv;
};

float dot(glm::vec3 a, glm::vec3 b) { return a.x*b.x + a.y*b.y + a.z*b.z; }
```

```hlsl
// HLSL 幾乎一樣
struct Vertex {
    float3 position : POSITION;
    float3 normal   : NORMAL;
    float2 uv       : TEXCOORD0;
};

// dot(), cross(), normalize() 全部內建，語法完全相同
float result = dot(a, b);  // ← 和 glm 一樣
```

**遷移成本：極低。** HLSL 語法比 C++ 簡單，沒有指標、沒有記憶體管理。

---

### 資料結構思維 → GPU 資料佈局

```cpp
// C++ 程式師的直覺
std::vector<Particle> particles;  // Array of Structures (AoS)

struct Particle {
    float3 position;
    float3 velocity;
    float  lifetime;
};
```

```hlsl
// GPU 偏好 Structure of Arrays (SoA)
// 因為 GPU 同時處理 32 個執行緒，SoA 讓記憶體存取連續

StructuredBuffer<float3> positions;   // 所有粒子的 position
StructuredBuffer<float3> velocities;  // 所有粒子的 velocity
StructuredBuffer<float>  lifetimes;   // 所有粒子的 lifetime

// 讀取連續，cache 友善
float3 pos = positions[threadId];
```

**你已有的知識**：cache locality、記憶體對齊——這些在 GPU 上更重要。

---

### 多執行緒 → Compute Shader

```cpp
// C++ 多執行緒
std::for_each(std::execution::par, particles.begin(), particles.end(),
    [](Particle& p) { p.position += p.velocity * deltaTime; });
```

```hlsl
// Compute Shader — 同樣的平行概念，但在 GPU 上
[numthreads(64, 1, 1)]
void UpdateParticles(uint3 id : SV_DispatchThreadID) {
    uint i = id.x;
    if (i >= particleCount) return;

    positions[i] += velocities[i] * deltaTime;
    lifetimes[i] -= deltaTime;
}

// 一次 Dispatch(N/64, 1, 1) 啟動 N 個執行緒
// 每組 64 個執行緒（一個 Warp/Wave）同時執行
```

**關鍵差異**：
- CPU 執行緒 = 數十個，每個很強大
- GPU 執行緒 = 數千個，每個很弱小，必須一起做相同的事

---

### 系統程式 → Render Pipeline 同步

```cpp
// C++ 同步原語
std::mutex mutex;
std::condition_variable cv;
std::atomic<int> counter;
```

```
Vulkan/DX12 對應：
  VkSemaphore    = 跨 Queue 同步（類似 OS Semaphore）
  VkFence        = CPU 等 GPU 完成（類似 cv.wait）
  VkBarrier      = 同一 Queue 內的資源狀態轉換
  VkEvent        = 細粒度的同步點

TA 需要理解的層面（不需要寫程式碼）：
  "為什麼我不能在 Shadow Pass 裡採樣當前幀的 Shadow Map？"
  → 因為同一幀的 Shadow Map 寫入還沒完成（需要 barrier）
  → 引擎自動處理，但你需要理解「順序依賴」的概念
```

---

### 編譯器知識 → Shader Compilation

```
C++ 編譯流程：
  Source → Preprocessor → Compiler → Linker → Binary

Shader 編譯流程：
  HLSL/GLSL → Shader Compiler (DXC/glslang)
            → Intermediate (SPIR-V / DXIL)
            → Driver Compiler → GPU 機器碼

你已理解的概念：
  - Shader Variant = 條件編譯（#define / multi_compile）
  - Shader 熱重載 = 類似動態載入
  - Shader 快取 = 避免重複編譯（PSO Cache）

UE5 的 shader 系統：
  - 每個 material + feature permutation = 一個 shader variant
  - 預編譯所有 variant = 打包時間長但執行時快
  - 動態編譯 = 執行中卡頓（shader compilation hitching）
```

---

## Python 在 TA Pipeline 中的直接應用

你的 Python 技能可以立刻產生價值：

```python
# 1. 批次資產驗證（最常見的 TA 工具）
import unreal

def validate_texture_naming():
    """
    掃描所有貼圖，找出不符合命名規範的。
    TA 日常：每週跑一次，報告給美術師。
    """
    assets = unreal.AssetRegistryHelpers.get_asset_registry()
    textures = assets.get_assets_by_class("Texture2D")
    
    issues = []
    for tex in textures:
        name = tex.asset_name
        if not (name.startswith("T_") or 
                name.endswith("_D") or name.endswith("_N")):
            issues.append(f"命名不符: {tex.package_name}")
    
    for issue in issues:
        unreal.log_warning(issue)
    
    unreal.log(f"掃描完成，發現 {len(issues)} 個問題")

# 執行
validate_texture_naming()
```

```python
# 2. 貼圖設定自動化（最節省時間的工具）
import unreal

def fix_normal_map_settings():
    """
    找到所有名稱包含 _N 的貼圖，確保它們設為 Normal Map 且 sRGB = False。
    手動做：每個貼圖要點 3 次。100 個貼圖 = 300 次點擊。
    這個腳本：3 秒。
    """
    factory = unreal.AssetRegistryHelpers.get_asset_registry()
    textures = factory.get_assets_by_class("Texture2D")
    
    fixed = 0
    for tex_data in textures:
        if "_N." in str(tex_data.package_name) or "_Normal." in str(tex_data.package_name):
            tex = unreal.load_asset(tex_data.package_name)
            if tex:
                tex.set_editor_property("compression_settings", 
                    unreal.TextureCompressionSettings.TC_NORMALMAP)
                tex.set_editor_property("srgb", False)
                unreal.EditorAssetLibrary.save_asset(str(tex_data.package_name))
                fixed += 1
    
    unreal.log(f"修正了 {fixed} 個 Normal Map 設定")
```

```python
# 3. LOD 自動設定
import unreal

def set_lod_settings_by_vertex_count():
    """
    根據頂點數自動設定 LOD 距離。
    > 10K 頂點 → 設 4 個 LOD
    1K-10K     → 設 3 個 LOD
    < 1K       → 設 2 個 LOD
    """
    meshes = unreal.AssetRegistryHelpers.get_asset_registry()\
                   .get_assets_by_class("StaticMesh")
    
    for mesh_data in meshes:
        mesh = unreal.load_asset(mesh_data.package_name)
        if not mesh:
            continue
        
        vertex_count = mesh.get_num_vertices(0)  # LOD 0
        
        lod_group = "LargeAsset" if vertex_count > 10000 else \
                    "MediumAsset" if vertex_count > 1000 else \
                    "SmallAsset"
        
        mesh.set_editor_property("lod_group", lod_group)
```

---

## 系統設計面試 → TA 技術設計思維

當 Art Director 說「我要一個能支援 50 種天氣狀態的系統」，你的反應應該像 System Design 面試：

```
1. 釐清需求
   "50 種天氣同時存在？還是切換？"
   "過渡時間需要多長？"
   "在什麼平台上跑？"

2. 估算規模
   "50 個 Post Process Volume × 每個 5 個參數 = 250 個參數要管理"
   "每秒插值一次 vs 每幀插值 = CPU 成本差 60 倍"

3. 提出架構選項
   Option A：每個天氣一個 Blueprint（50 個 BP，維護成本高）
   Option B：Data Table + 一個 Weather Manager BP（單一真相來源）
   Option C：Data Asset + Curve Assets（最靈活，可熱更新）

4. 分析 Tradeoff
   "Option B 實作快，但 Data Table 修改需要重新編譯"
   "Option C 最靈活，但初始設定複雜"

5. 推薦方案並說明理由
```

**這就是 Senior TA 和 Junior TA 的差距：** 能提出架構方案，而不只是「照你說的做」。

---

## 程式師轉 TA 的常見誤區

```
❌ 誤區 1：「我有程式底子，應該從 Vulkan 學起」
✓ 正確：先在引擎層面建立生產力，底層知識「按需」補充

❌ 誤區 2：「Shader 不就是個函數？應該很簡單」
✓ 正確：Shader 數學是 CS + 物理 + 視覺的交叉，需要刻意練習

❌ 誤區 3：「我會寫 Python，pipeline 工具不是問題」
✓ 正確：工具本身不難，難的是知道「什麼值得自動化、什麼不值得」

❌ 誤區 4：「效能優化是我的強項」
✓ 正確：CPU 優化 ≠ GPU 優化。GPU 的瓶頸模型完全不同，需要重新建立直覺

❌ 誤區 5：「先把技術學好，視覺感之後再說」
✓ 正確：視覺直覺要刻意訓練，越晚開始越難補，應該從第一天就並行練習
```
