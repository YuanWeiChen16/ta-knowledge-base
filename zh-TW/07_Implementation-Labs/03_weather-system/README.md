# Lab 03 — 即時天氣系統

**難度**：⭐⭐⭐ Senior  
**預估時間**：2-3 天  
**引擎版本**：UE5.3+

---

## 原始發表

- **來源**：80.lv — *"Crafting a Real-Time Sci-Fi Weather System with Multiple Moods in UE5"* (2025)
- **URL**：https://80.lv/articles/crafting-a-real-time-sci-fi-weather-system-with-multiple-moods-in-ue5
- **作者**：Marek Zaranski（Technical Artist, Drago Entertainment）

**閱讀重點**：Smooth Operator 組件架構、MPC（Material Parameter Collection）全域控制、兩個 Post Process Volume 混合的技術理由。

---

## 你的任務

**在 UE5 裡建立一個可程式化的天氣系統，支援至少 3 種天氣狀態的即時切換與混合。**

### 必須功能
1. **Weather Manager Blueprint**：控制天氣狀態切換、過渡時間、當前混合進度
2. **至少 3 種天氣**：如晴天、陰天、雨天（自行設計視覺風格）
3. **MPC 全域材質控制**：透過 Material Parameter Collection 同步調整所有場景材質的濕潤度、顏色偏移、飽和度
4. **天空/霧氣過渡**：Sky Atmosphere 或 Exponential Height Fog 參數隨天氣變化
5. **Post Process 混合**：至少 2 個天氣有不同的後處理效果（色調、曝光、霧氣濃度）
6. **Editor Utility Widget**：在 Editor 裡能即時切換天氣狀態預覽

### 加分功能
- 雨滴打濕地面材質（Ripple Render Target）
- 雨水 Niagara 粒子
- 閃電隨機觸發系統

---

## 系統架構建議

```
BP_WeatherManager
  └── Component: WeatherSmoothOperator
        ├── Module: SkyModule        (Sky Atmosphere 參數)
        ├── Module: FogModule        (Height Fog 參數)
        ├── Module: PostProcessModule (PP Volume weight 混合)
        ├── Module: MaterialModule   (MPC 參數推送)
        └── Module: VFXModule        (Niagara 系統開關)

MPC_WeatherGlobal
  ├── Wetness (0-1)
  ├── SkyTint (Color)
  ├── FogDensity (float)
  └── RainIntensity (0-1)
```

---

## 驗收標準

| 項目 | 標準 |
|------|------|
| 功能 | 天氣切換有平滑過渡（不是瞬間跳切）|
| 功能 | MPC 變化即時影響所有場景材質 |
| 功能 | Editor Widget 能預覽所有天氣狀態 |
| 技術 | Post Process 混合用 Volume Weight，不是直接改參數 |
| 技術 | 系統模組化，新增第 4 種天氣不需要修改核心邏輯 |
| 效能 | 天氣切換過渡期間 frame time 不應有明顯卡頓 |

---

## 關鍵問題（做完後回答）

1. 作者為什麼用「兩個獨立 Post Process Volume 混合 Weight」而非直接修改同一個 Volume 的參數？這個設計決策解決了什麼問題？
2. Material Parameter Collection 和直接在材質裡用 Dynamic Material Instance 設參數有什麼根本差異？什麼情況下用哪個？
3. 你的 Wetness 參數如何影響材質？PBR 上，濕潤的表面和乾燥的表面有什麼物理差異（Roughness? F0? Albedo?）？
4. 如果要讓這個系統在 Multiplayer 遊戲中同步，你會怎麼做？

---

## 相關節點 / API

```blueprint
// Blueprint 推送 MPC 參數
Set Scalar Parameter Value (MPC)
Set Vector Parameter Value (MPC)

// Post Process Volume 混合
Post Process Volume → Blend Weight (0-1)
Unbound Post Process Volume ← 確保全場景覆蓋

// Timeline 做平滑過渡
Timeline → Float Track → 推送到各模組
```
