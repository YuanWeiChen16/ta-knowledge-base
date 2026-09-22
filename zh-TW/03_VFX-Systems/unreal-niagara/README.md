# Unreal Niagara VFX 系統

**穩定性標籤**：`[ENGINE-VERSIONED: UE5.4]`

---

## Niagara 架構

```
Niagara System
  └── Emitter 1
  │     └── Emitter Properties
  │     └── Particle Spawn
  │     └── Particle Update
  │     └── Particle Render (Sprite / Mesh / Ribbon / Light)
  └── Emitter 2
  └── System Update (跨 Emitter 的邏輯)
```

---

## Niagara vs Cascade（舊系統）

| | Niagara | Cascade |
|--|---------|---------|
| 架構 | 資料驅動，節點圖 | 模組堆疊 |
| 自訂性 | 極高（可寫 HLSL）| 有限 |
| GPU 粒子 | ✅ 完整支援 | 部分支援 |
| 學習曲線 | 高 | 低 |
| 新專案建議 | ✅ 使用 | ❌ 遷移到 Niagara |

---

## 常用 Niagara 模組

```
Spawn:
  Spawn Rate          ← 每秒生成速率
  Spawn Burst Timed   ← 指定時間點爆發式生成
  Spawn Per Unit      ← 移動時根據距離生成（拖尾效果）

Initialize Particle:
  Initialize Particle  ← 設定初始位置、速度、顏色、大小、生命週期

Update:
  Gravity Force       ← 重力
  Drag               ← 阻力
  Curl Noise Force    ← 亂流
  Update Age          ← 更新粒子年齡（必須）
  Solve Forces and Velocity ← 積分速度（必須）

Render:
  Sprite Renderer     ← 四邊形 billboard
  Mesh Renderer       ← 3D mesh 粒子
  Ribbon Renderer     ← 連線粒子（拖尾、閃電）
  Light Renderer      ← 粒子發光
```

---

## Niagara 參數綁定（Blueprint/C++ 控制）

```cpp
// C++ 控制 Niagara 參數
#include "NiagaraFunctionLibrary.h"
#include "NiagaraComponent.h"

UNiagaraComponent* NiagaraComp = UNiagaraFunctionLibrary::SpawnSystemAtLocation(
    this, NiagaraSystem, SpawnLocation
);

NiagaraComp->SetFloatParameter(FName("Intensity"), 2.5f);
NiagaraComp->SetVectorParameter(FName("EmitDirection"), FVector(0, 0, 1));
NiagaraComp->SetColorParameter(FName("ParticleColor"), FLinearColor::Red);
```

---

## GPU 模擬 vs CPU 模擬

| | GPU 模擬 | CPU 模擬 |
|--|---------|---------|
| 粒子數 | 數十萬~數百萬 | 數千~數萬 |
| 碰撞 | 有限（深度碰撞）| 完整物理碰撞 |
| 讀取粒子位置 | 困難（需 GPU readback）| 容易 |
| 適用 | 純視覺效果（煙、塵）| 需要遊戲邏輯互動 |

**設定**：Emitter Properties → Sim Target → GPU Compute Sim

---

## Niagara Fluids（UE5.1+）

流體模擬：Grid3D（3D 流體）和 Grid2D（2D 流體/水面）。

效能成本高，適合 Hero VFX 或 Cutscene。不適合即時多實例使用。

---

## 學習資源

- 📖 [Unreal Niagara Documentation](https://docs.unrealengine.com/5.4/en-US/creating-visual-effects-in-niagara-for-unreal-engine/)
- 🎥 [Niagara Beginner Tutorial (UE5)](https://www.youtube.com/results?search_query=unreal+niagara+beginner+tutorial+UE5)
- 🎥 [Klemen Lozar — Niagara Advanced](https://www.youtube.com/@KlemenLozar)
- 📖 [GDC: Niagara VFX System Deep Dive](https://gdcvault.com/) — 搜尋 "Niagara"
