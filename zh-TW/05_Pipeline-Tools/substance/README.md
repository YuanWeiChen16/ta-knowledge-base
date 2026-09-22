# Substance Designer & Painter

**穩定性標籤**：`[ENGINE-VERSIONED: Substance 3D 2024]`

---

## Designer vs Painter 職責分工

| 工具 | 用途 | TA 使用情境 |
|------|------|-----------|
| **Substance Designer** | 程序貼圖生成 | 製作可重複使用的材質庫、Trim Sheet、特殊效果貼圖 |
| **Substance Painter** | 3D 貼圖繪製 | 角色/道具手繪貼圖、ID Mask 烘焙 |

---

## Substance Designer TA 核心技能

### 程序 Tileable 材質
```
Graph 結構：
  Noise/Shape 節點 → Height Map
  Height → Normal（Normal node）
  Height → AO（Ambient Occlusion node）
  Height + Levels → Roughness
  Color Input + Variation → BaseColor
  最終輸出：BaseColor, Normal, Roughness, Metallic, AO
```

### Trim Sheet 製作工作流
Trim Sheet = 一張貼圖包含多個不同截面的材質條，用 UV 映射到建築/道具邊緣。

```
1. Designer 裡製作多個材質條（磚牆頂部/底部/轉角/窗框）
2. 用 Tile Sampler 或手動排列在一張 2048×512 貼圖
3. 引擎裡用 UV 映射配合模型 UV Layout
優點：少量貼圖覆蓋大量建築細節
```

### TA 必會的 Designer 節點
```
Tile Sampler         ← 程序分布圖案
Histogram Scan       ← 把灰階轉換成 mask（調整亮暗閾值）
Histogram Select     ← 選取特定灰階範圍
Warp                 ← 用一張貼圖扭曲另一張
Blend                ← 多貼圖混合（使用 Mask）
Normal Combine       ← 疊加多個 Normal map
Bevel                ← 從 Height 生成邊緣高光（倒角效果）
```

---

## Substance Painter TA 核心技能

### 烘焙（Baking）設定
烘焙是 Painter 工作流的起點，設定不對所有後續工作都會有問題：

```
Bake Mesh Maps:
  High Poly: 高模（帶細節）
  Low Poly: 低模（遊戲用）
  
重要貼圖：
  Normal (from mesh)  ← 從高模轉移細節到低模
  World Space Normal  ← 某些特殊 Shader 用
  Ambient Occlusion   ← 環境光遮蔽
  Curvature           ← 邊緣高光/凹槽暗部 Mask
  Position            ← 位置 Mask（用於程序材質定位）
  Thickness           ← 次表面散射用
  ID Map              ← 顏色 ID 快速選取分區
```

### ID Mask 工作流
```
1. 高模或低模用頂點色或多材質 ID 標記不同部位
2. 烘焙 ID Map（每個部位一個純色）
3. Painter 裡用 Color Selection 快速遮罩特定部位
4. 大幅提升多材質複雜物件的繪製效率
```

---

## 引擎匯出設定

### Unity URP 匯出
```
Output Template: Unity Universal Render Pipeline (Metallic)
輸出貼圖：
  BaseColor (+ Alpha for Opacity)
  Metallic + Roughness + AO（打包在 R G B channel）
  Normal (DirectX format → Unity 使用 DirectX)
```

### Unreal Engine 匯出
```
Output Template: Unreal Engine 4 (Metallic/Roughness)
輸出貼圖：
  BaseColor
  ORM (Occlusion-Roughness-Metallic 打包)
  Normal (DirectX format)
注意：Unreal 的 Normal 和 Unity 的 Y 軸方向相同（DirectX）
```

### Normal Map 方向慣例
```
DirectX（Unity、Unreal）：Y+ = 上（綠色偏上）
OpenGL（Blender、Maya 預設）：Y- = 上（綠色偏下）
匯出時確認選對格式，否則法線方向錯誤
```

---

## 學習資源

- 📖 [Adobe Substance 3D Documentation](https://helpx.adobe.com/substance-3d-designer/home.html)
- 🎥 [Stylized Station YouTube](https://www.youtube.com/@StylizedStation) — Substance Designer 程序材質
- 🎥 [Substance Academy](https://substance3d.adobe.com/tutorials) — Adobe 官方教學
- 📖 [The PBR Guide (Substance)](https://substance3d.adobe.com/tutorials/courses/the-pbr-guide-part-1) — PBR 和 Substance 整合
