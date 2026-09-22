# USD (Universal Scene Description)

**穩定性標籤**：`[STABLE]` 概念，`[VOLATILE]` 工具鏈整合

---

## 為什麼 USD 重要

USD 是 Pixar 開源的場景描述格式，正在成為遊戲/電影/XR 業界的**通用交換標準**。

**核心價值**：非破壞性的分層（Layering）場景描述，多個部門可以同時修改場景的不同層面。

---

## USD 核心概念

### Prim（Primitive）
場景裡的每個物件都是一個 Prim：

```
/World                   ← Stage 根節點
  /World/Environment     ← Scope（群組）
  /World/Character       ← Xform（有 transform 的節點）
    /World/Character/Body  ← Mesh
  /World/Lighting        ← Scope
    /World/Lighting/Sun  ← DomeLight
```

### Layering（非破壞性分層）
```
Base Layer:    場景幾何 (geometry.usd)
Layout Layer:  位置擺放 (layout.usd)    ← 疊加在 Base 上
Lighting Layer: 燈光設定 (lighting.usd) ← 再疊加
FX Layer:      特效 (fx.usd)            ← 最上層

每層都可以獨立編輯，後層 override 前層的屬性
```

### Variant（變體）
同一個物件的不同版本（LOD、白天/夜晚版本、破損程度）：

```python
# 程式碼設定 variant
stage = Usd.Stage.Open("character.usd")
prim = stage.GetPrimAtPath("/Character")
variantSet = prim.GetVariantSets().GetVariantSet("costume")
variantSet.SetVariantSelection("armor")  # 切換到盔甲版本
```

---

## USD 在遊戲 Pipeline 的應用

### 資產交換
```
Maya/Blender/Houdini → 匯出 USD → 引擎匯入
不同 DCC 軟體間的高保真場景交換（比 FBX 更完整）
```

### 協作工作流
```
美術師 A 負責：character_geo.usd（幾何）
美術師 B 負責：character_materials.usd（材質）
燈光師負責：character_lighting.usd（燈光）
→ 合併成 character_final.usd（所有層疊加）
```

### Unreal Stage Actor
UE5 原生支援 USD：
```
File → Import → USD Stage
或在 Level 裡放 USD Stage Actor，直接載入 .usd 場景
```

---

## USDZ（Apple / Web 格式）

壓縮打包的 USD，用於 AR 預覽（iOS AR Quick Look）和 Web 3D 展示。

```python
# Python 轉換為 USDZ
from pxr import UsdUtils
UsdUtils.CreateNewUsdzPackage("scene.usd", "scene.usdz")
```

---

## 學習資源

- 📖 [USD官方文件](https://openusd.org/release/index.html)
- 📖 [Pixar USD Tutorials](https://openusd.org/release/tut_usd_tutorials.html)
- 🎥 [NVIDIA USD Workshop](https://developer.nvidia.com/usd)
- 📖 [Unreal USD Pipeline](https://docs.unrealengine.com/5.4/en-US/universal-scene-description-in-unreal-engine/)
