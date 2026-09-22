# 線性代數 for TA

**穩定性標籤**：`[STABLE]`

---

## 核心概念快查

### 向量 (Vector)
- **點積 (Dot Product)**：`a · b = |a||b|cos(θ)` — 用於計算角度、光照方向
- **叉積 (Cross Product)**：`a × b` — 用於計算法線、切線空間建構
- **正規化**：`normalize(v) = v / |v|` — 方向向量必須正規化

### 矩陣 (Matrix)
- **TRS 矩陣**：Translation × Rotation × Scale — Unity/Unreal 的物件 transform
- **Model → World → View → Clip**：四個座標空間的轉換鏈
- **轉置 vs 逆矩陣**：法線向量用 `inverse(transpose(M))` 轉換，不是直接乘 M

### 四元數 (Quaternion)
- 避免萬向鎖 (Gimbal Lock) 的旋轉表示
- `q = (w, x, y, z)` 其中 w 是實部
- 插值：SLERP（球面線性插值）比 Euler 插值更平滑

### 座標空間 (Coordinate Spaces)
| 空間 | 定義 | TA 常見用途 |
|------|------|-----------|
| Object Space | 相對於物件原點 | Vertex shader 輸入 |
| World Space | 場景全局座標 | 光照計算 |
| View Space | 相對於攝影機 | 某些後處理效果 |
| Clip Space | 投影後 (-1 to 1) | 深度值來源 |
| Tangent Space | 相對於頂點法線 | Normal map 儲存空間 |
| UV Space | 0-1 貼圖座標 | 貼圖採樣 |

---

## Tangent Space 深入（最常踩坑的地方）

Normal map 儲存在 tangent space 中，需要 TBN 矩陣轉換：
- **T**angent（切線，沿 U 方向）
- **B**itangent（副切線，沿 V 方向）
- **N**ormal（法線）

**常見問題**：法線貼圖接縫 → 通常是 tangent 計算方式不一致（Maya vs 引擎差異）

---

## 學習資源

- 🎥 [3Blue1Brown — Essence of Linear Algebra](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab) — 最好的視覺化講解
- 📖 [Math for Game Developers (YouTube)](https://www.youtube.com/c/JorgeRodriguez) — 遊戲向應用
- 📖 [Graphics Codex](https://graphicscodex.courses.nvidia.com/) — NVIDIA 免費，含大量圖形數學

---

## 實作練習 (project-ideas)

1. **切線空間視覺化工具**：在 Unity/Unreal 寫一個 Debug shader，把 TBN 三個向量用顏色顯示出來。目標：能在任何 mesh 上檢查切線是否正確。
2. **手寫矩陣轉換**：不用引擎 API，純 HLSL 手寫 World to View 轉換，驗證結果和引擎一致。
