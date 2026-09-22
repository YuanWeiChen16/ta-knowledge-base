# Unity VFX Graph

**穩定性標籤**：`[ENGINE-VERSIONED: Unity 6 / VFX Graph 17]`

---

## VFX Graph vs Particle System（Shuriken）

| | VFX Graph | Particle System (ShurikanS) |
|--|-----------|--------------------------|
| 執行位置 | GPU（Compute Shader）| CPU |
| 粒子數上限 | 數百萬 | 數萬 |
| 相依性 | 需要 URP 或 HDRP | 所有管線 |
| 視覺節點 | ✅ | ❌ |
| 物理碰撞 | 有限 | 完整 |

**選擇原則**：大量粒子 + 視覺效果 → VFX Graph。需要物理碰撞互動 → Particle System。

---

## VFX Graph 核心架構

```
Context (上下文，定義生命週期階段)：
  Initialize    → 粒子誕生時執行一次
  Update        → 每幀更新
  Output        → 渲染輸出（Quad/Mesh/Strip）

Block (積木)：
  每個 Context 裡放 Block 定義行為
  例：Set Velocity Random、Set Color over Life、Flipbook Player

Operator (運算節點)：
  數學運算、貼圖採樣、Noise 等，連線到 Block 的屬性
```

---

## 常用 Block 快查

```
Initialize:
  Set Position (Shape: Sphere/Box/Mesh)  ← 誕生位置
  Set Velocity Random                     ← 初始速度
  Set Lifetime Random                     ← 生命週期
  Set Size Random                         ← 初始大小

Update:
  Gravity                                 ← 重力
  Drag                                    ← 阻力（讓粒子減速）
  Turbulence                              ← 亂流（用 noise 驅動）
  Conform to Sphere/Signed Distance Field ← 讓粒子貼合表面
  Update Position                         ← 根據速度更新位置（必須）

Output Particle Quad:
  Set Color over Life                     ← 顏色隨生命變化
  Set Alpha over Life                     ← 透明度隨生命變化
  Set Size over Life                      ← 大小隨生命變化
  Flipbook Player                         ← 動畫貼圖播放
  Orient: Face Camera                     ← 永遠朝向攝影機
```

---

## Blackboard（參數暴露）

把 VFX 內部的值暴露給外部 C# 控制：

```csharp
// 在 C# 裡控制 VFX 參數
VisualEffect vfx = GetComponent<VisualEffect>();
vfx.SetFloat("Intensity", 2.5f);
vfx.SetVector3("SpawnPosition", transform.position);
vfx.SendEvent("OnHit"); // 觸發 VFX 裡的 Event
```

---

## 效能最佳化

1. **Capacity**：設定最大粒子數量，VFX Graph 預先分配記憶體
2. **Output Mesh vs Quad**：Mesh 粒子更複雜，謹慎使用
3. **Texture 格式**：VFX 貼圖通常不需要最高品質，ETC2/BC7 壓縮
4. **GPU Event**：讓 GPU 上的粒子生成子粒子（避免 CPU→GPU roundtrip）

---

## 學習資源

- 📖 [Unity VFX Graph Documentation](https://docs.unity3d.com/Packages/com.unity.visualeffectgraph@latest)
- 🎥 [Gabriel Aguiar — VFX Graph Tutorials](https://www.youtube.com/@GabrielAguiarProd)
- 🎥 [Unity官方 VFX Graph Playlist](https://www.youtube.com/playlist?list=PLX2vGYjWbI0RlZMAbKWC2-kVUcEJ7Pq7L)
