# Lab 09 — 純 Shader 粒子系統（No Niagara）

**難度**：⭐⭐⭐ Mid-Senior  
**預估時間**：1-2 天  
**引擎版本**：UE5.0+（Material Editor 即可）

---

## 原始發表

- **來源**：GDC Technical Artist Summit 2024
- **演講標題**：*"Technical Artist Summit: Build Your Own Particle System"*
- **演講者**：Ben Cloward（Technical Artist, Unity）
- **GDC Vault**：https://www.gdcvault.com/play/1034588/Technical-Artist-Summit-Build-Your
- **Ben Cloward YouTube**：https://www.youtube.com/@BenCloward（同系列教學影片）

**閱讀重點**：用純 Shader 節點（無 CPU 模擬）實現粒子效果的核心技術——Camera Facing、偽隨機數、UV 動畫、Depth Fade。這個方法極度省效能，因為所有計算在 GPU 上以 Vertex/Pixel Shader 完成。

---

## 核心概念：Shader 如何「假裝」有粒子

```
傳統 Niagara：CPU 追蹤每個粒子的位置/速度/生命週期
Shader 粒子：GPU 用數學產生「看起來像粒子」的視覺效果

關鍵工具：
  - Instance ID / Vertex ID → 每個 instance 有獨特的偽隨機種子
  - Frac(Time * Speed + Seed) → 0-1 的週期值，模擬粒子生命週期
  - 偽隨機數（Hash 函數）→ 讓每個粒子有不同的速度、大小、顏色
  - Camera-Facing Billboard → Mesh 永遠朝向攝影機
  - World Position Offset → 用數學驅動頂點位置（不需要 CPU）
```

---

## 你的任務

**在 UE5 的 Material Editor 裡，不使用 Niagara，純用 Shader 節點做一個完整的火花/星塵粒子效果。**

### 必須功能

1. **Camera-Facing Billboard**
   - 一個 Plane mesh 永遠面向攝影機
   - 用 World Position Offset + Camera Vector 實現
   - 不使用 Niagara 或 Sprite Renderer

2. **偽隨機每粒子差異化**
   - 每個 mesh instance 有不同的：大小、速度、起始相位
   - 使用 PerInstanceCustomData 或 Vertex Color 傳入種子值
   - 或使用 Object Position Hash 自動生成種子

3. **生命週期動畫**
   - 每個粒子有完整的 Born → Live → Die 週期
   - 大小：從 0 長到最大，再縮小到 0
   - Alpha：對應生命週期淡入淡出

4. **世界空間運動**
   - 粒子在世界空間中移動（向上漂浮、向外擴散等）
   - 純 WPO 驅動，無 CPU

5. **Depth Fade**
   - 粒子靠近其他幾何體時邊緣漸出
   - 防止硬邊 z-fighting

---

## 核心 HLSL / 節點邏輯

```hlsl
// 偽隨機數（在 Custom 節點裡）
float Hash(float seed) {
    return frac(sin(seed * 127.1 + 311.7) * 43758.5453);
}

// 每個粒子的獨特種子
float seed = dot(GetObjectWorldPosition(), float3(1, 1, 1));

// 生命週期（0→1→0 的三角波）
float lifetime = frac(Time * speed + Hash(seed));
float size = sin(lifetime * 3.14159); // 生命中段最大

// Camera-Facing WPO
float3 toCamera = normalize(CameraPosition - WorldPosition);
float3 up = float3(0, 0, 1);
float3 right = normalize(cross(up, toCamera));
// 用 right 和 up 重建面向攝影機的頂點偏移
```

---

## 驗收標準

| 項目 | 標準 |
|------|------|
| 視覺 | 每個 Plane instance 看起來都是獨立的粒子 |
| 視覺 | Billboard 在攝影機繞行時始終面向攝影機 |
| 視覺 | 粒子有完整生命週期（出生、移動、消失）|
| 技術 | 場景裡 500 個 instance，CPU 使用率幾乎不變 |
| 技術 | 有 Depth Fade，靠近地面時邊緣自然融合 |
| 技術 | Material 的 Draw Call = 1（所有 instance 共用）|

---

## 為什麼這個技術重要

| 比較 | Niagara 粒子 | Shader 粒子 |
|------|------------|-----------|
| CPU 成本 | 每粒子 CPU tick | 接近零 |
| GPU 成本 | 正常 overdraw | 正常 overdraw |
| 靈活度 | 非常高 | 中（受限於 shader math）|
| 最佳用途 | 需要物理/碰撞的複雜效果 | 大量環境背景粒子（塵埃、螢火蟲、星塵）|

**500 個 Niagara 粒子系統 vs 500 個 Shader 粒子 instance 的 CPU 成本差距可達 10-50 倍。**

---

## 關鍵問題（做完後回答）

1. 你的 Camera-Facing 實作和 Niagara 的 `Orient: Face Camera` 在數學上是否相同？有哪些邊界情況（如攝影機直接從上往下看）會出問題？
2. 偽隨機數（Hash 函數）和真正的 Random 有什麼差別？為什麼 Shader 裡「隨機」必須是偽隨機？
3. 這個方法的最大限制是什麼？什麼情況下你一定要用 Niagara 而不能用 Shader 粒子？
4. 500 個 Shader 粒子 instance 的 Overdraw 是多少？如何優化（hint：Depth Prepass、粒子大小、Additive blending）？

---

## 相關資源

- 🎥 [Ben Cloward YouTube — Shader-Based Particles](https://www.youtube.com/@BenCloward)
- 🎥 [GDC Vault 演講](https://www.gdcvault.com/play/1034588/Technical-Artist-Summit-Build-Your)
