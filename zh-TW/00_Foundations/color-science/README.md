# 色彩科學 for TA

**穩定性標籤**：`[STABLE]`

> 中階 TA 最大的隱藏盲點。很多「顏色不對」的問題根源都在這。

---

## 核心概念快查

### Gamma vs Linear

| | Gamma 空間 | Linear 空間 |
|--|-----------|-----------|
| 儲存 | sRGB 貼圖（人眼感知均勻） | HDR/Linear 貼圖 |
| 計算 | sRGB 等非線性編碼值需先轉成 Linear，才能正確進行光照與混色運算 | 光照與顏色混合常在 Linear 空間計算 |
| 顯示 | 最終輸出到螢幕 | 中間計算 |

**基本規則**：
1. 所有光照計算在 Linear 空間進行
2. Albedo/Diffuse 貼圖是 sRGB（需要引擎 gamma 校正）
3. Normal/Roughness/Metallic 貼圖是 Linear（不需要校正）

**常見錯誤**：把 Normal map 設為 sRGB → 法線計算錯誤，光照看起來奇怪。

---

### 色彩空間

| 色彩空間 | 用途 | 引擎設定 |
|---------|------|---------|
| **sRGB / Rec.709** | 標準顯示器、SDR 輸出 | 最終輸出 |
| **ACEScg** | 寬色域線性工作空間，常用於電影與合成流程 | UE 可選工作空間；預設為 sRGB / Rec.709 |
| **Linear sRGB** | 中間計算空間 | Unity Linear rendering |
| **Rec.2020** | HDR 顯示器 | HDR 輸出設定 |

---

### Tonemapping

把 HDR（高動態範圍）壓縮到螢幕可顯示範圍的過程。

常見 Tonemapper：
- **Unreal 預設 Tonemapper**：使用以 ACES 為基礎的色調映射曲線；這不代表專案工作色彩空間是 ACEScg
- **ACES**：常見的色調映射方法；實際外觀取決於引擎版本與設定
- **Filmic**：不同軟體可能指不同曲線，使用時請確認實作與設定
- **Reinhard**：簡單，容易過曝
- **AgX**（Blender 3.6+）：更好的高光保留

**TA 關注點**：材質在 tonemapper 前後的外觀會不同。RenderDoc 可以截取 tonemapper 前的 HDR buffer。

---

### 白平衡 & 色溫

- 色溫以 Kelvin (K) 表示：~3200K 暖白熾燈，~6500K 日光
- UE/Unity 的 White Balance 後處理調整的是感知白點
- 影響整個場景的整體色調，不只是光源

---

## 引擎設定快查

### Unity
```
Project Settings → Player → Color Space → Linear  ← 必須設為 Linear
Albedo/Base Color ← 以色彩貼圖匯入，啟用 sRGB
Normal ← 設為 Normal Map；資料貼圖如 Roughness/Metallic/AO 不做 sRGB 解碼
```

### Unreal Engine
```
Project Settings → Rendering → Working Color Space → sRGB / Rec.709 (預設；依 UE 版本確認)
ACEScg 可作為專案工作色彩空間，但不是預設值
Texture → sRGB ← Albedo 貼圖打勾
Texture → sRGB ← Normal/Roughness/Metallic 不打勾
Post Process Volume → Color Grading / Tone Curve 相關設定（依 UE 版本確認）
```

---

## 學習資源

- 📖 [Filmic Worlds Blog](http://filmicworlds.com/blog/) — John Hable，電影/遊戲色彩科學深度文章
- 📖 [Unreal Engine Working Color Space](https://dev.epicgames.com/documentation/unreal-engine/working-color-space-in-unreal-engine) — UE 工作色彩空間與預設
- 📖 [Color: From Hexcodes to Eyeballs](http://jamie-wong.com/post/color/) — 完整的色彩科學網頁文章
- 🎥 [Acerola — Color Science Videos](https://www.youtube.com/@Acerola_t) — 遊戲渲染向色彩科學

---

## 實作練習 (project-ideas)

1. **色彩空間對比 shader**：在同一個場景，分別截圖 Linear/Gamma 計算的光照結果，做並排比較。目標：直觀感受差異。
2. **Tonemapper 比較工具**：在 Unity/Unreal 寫一個可切換 ACES/Reinhard/None 的後處理 shader，截圖比較高光處理差異。
