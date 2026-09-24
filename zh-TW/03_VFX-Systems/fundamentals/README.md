# VFX 基礎原則

**穩定性標籤**：`[STABLE]`

---

## VFX 設計原則

### 1. 讀取性優先（Readability First）
VFX 必須在 1/30 秒內讓玩家理解「發生了什麼」。  
- 形狀 > 細節（剪影要清楚）
- 顏色對比 > 寫實（在戰鬥中紅色 = 危險，藍色 = 友方）
- 動態曲線 > 靜態精緻（一個爆炸的 easing 比貼圖解析度更重要）

### 2. 效能預算先定義，再製作
每個 VFX 都要依目標裝置、畫面更新率與同時出現數量訂定 CPU/GPU 時間、粒子數和 overdraw 預算。下列數值只能當起始範例，需在目標平台量測調整：
- **Foreground / Hero VFX**（技能、爆炸）：例如先以 2ms、500 粒子作為單一效果的測量起點
- **Background / Ambient VFX**（環境煙塵、火焰）：例如先以 0.5ms、100 粒子/實例作為起點
- **UI VFX**：同樣消耗 GPU 與 fill rate；依解析度、混合方式與覆蓋範圍量測

### 3. 視覺衝擊 = Shape + Motion + Timing
- **Shape**：剪影清晰、有方向性
- **Motion**：不等速，用 easing curves（快入慢出或慢入快出）
- **Timing**：爆炸先有一個「壓縮」幀再展開（hit-stop 原則）

---

## VFX 效能關鍵指標

| 指標 | 影響 | 如何查看 |
|------|------|---------|
| **Overdraw** | 半透明粒子疊加 = 大量像素重繪 | RenderDoc / GPU Visualizer |
| **Particle Count** | 過多粒子 = CPU/GPU simulation 成本 | Profiler particle stats |
| **Texture Bandwidth** | 大型粒子 + 高解析度貼圖 | Bandwidth monitor |
| **Draw Calls** | 每個 VFX 系統的批次效率 | Frame Debugger |

### Overdraw 是行動裝置 VFX 殺手
半透明粒子每像素都要 blend：
- 1個大型粒子覆蓋 1920×1080 = 200萬像素的 blend
- 10個這樣的粒子疊加 = 2000萬 blend 操作/幀

**解法**：
1. 減少粒子的螢幕覆蓋範圍與彼此重疊；粒子數增加不一定能降低 overdraw
2. 使用 Depth Fade 改善與場景交界的視覺接縫；它不會自動減少 overdraw
3. 限制粒子最大尺寸（行動裝置通常 < 1/4 螢幕面積）

---

## Flipbook 動畫貼圖

**原理**：把動畫幀存在一張貼圖的網格裡，shader 裡切換 UV 播放動畫。

```
8×8 flipbook = 64 幀動畫
貼圖大小：512×512 → 每幀 64×64 像素
```

**製作工具**：
- Houdini → 最強的 flipbook 模擬輸出
- Substance Designer → 程序 flipbook 貼圖
- Unity VFX Graph 內建 Flipbook Player 節點

**Motion Vector Flipbook**：配合 motion vector 貼圖做亞幀插值，讓低幀數 flipbook 看起來更流暢。

---

## VAT（Vertex Animation Texture）

把頂點動畫（布料、流體、破碎）烘焙到貼圖，在 vertex shader 讀取還原：

**優點**：零 CPU simulation 成本，GPU 直接讀貼圖播放動畫  
**用途**：草地搖擺、旗幟飄動、破碎動畫、流體

**製作流程**：Houdini → VAT SOP → 輸出位置貼圖 + 法線貼圖 → 引擎 shader 讀取

---

## 學習資源

- 🎥 [Klemen Lozar — VFX Tutorials](https://www.youtube.com/@KlemenLozar) — Niagara/VFX Graph 實戰
- 🎥 [Tech Art Aid YouTube](https://www.youtube.com/@TechArtAid) — TA 向 VFX 技術
- 📖 [Realtimecolors VFX](https://realtimecolors.com/) — 色彩在 VFX 中的應用
- 🎥 [GDC: Technical Artist VFX talks](https://gdcvault.com/) — 搜尋 "VFX Technical Artist"

---

## 實作練習 (project-ideas)

1. **爆炸效果效能分析**：製作一個爆炸 VFX，用 profiler 測量 overdraw 和 GPU 時間。逐步優化直到在行動裝置目標下 < 1ms。記錄每步優化的前後數據。
2. **VAT 布料動畫**：用 Houdini 模擬一段旗幟飄動，輸出 VAT，在 Unity/Unreal 裡用 vertex shader 播放。約束：不用 Skinned Mesh，純貼圖驅動。
