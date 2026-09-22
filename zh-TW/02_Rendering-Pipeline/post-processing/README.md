# 後處理管線

**穩定性標籤**：概念 `[STABLE]`，引擎設定 `[ENGINE-VERSIONED]`

---

## 後處理執行順序

```
Scene Color (HDR)
  ↓
Temporal Anti-Aliasing (TAA)    ← 累積歷史幀做抗鋸齒
  ↓
Depth of Field                  ← 景深模糊
  ↓
Motion Blur                     ← 動態模糊
  ↓
Bloom                           ← 高光溢出
  ↓
Lens Flare / Dirt Mask          ← 鏡頭效果
  ↓
Eye Adaptation (Auto Exposure)  ← 自動曝光
  ↓
Tone Mapping                    ← HDR → LDR 壓縮
  ↓
Color Grading (LUT)             ← 最終色調調整
  ↓
FXAA / SMAA (可選)              ← 快速抗鋸齒補充
  ↓
UI Overlay
```

---

## 抗鋸齒技術

| 技術 | 品質 | 成本 | 問題 |
|------|------|------|------|
| **MSAA** | 高 | 高 | 不支援 Deferred |
| **TAA** | 高 | 中 | 鬼影（ghosting）、模糊 |
| **FXAA** | 低 | 極低 | 過度模糊 |
| **SMAA** | 中 | 低 | 比 FXAA 好但有限 |
| **DLSS** (NVIDIA) | 極高 | 低（AI）| 需要 RTX 顯卡 |
| **FSR** (AMD) | 高 | 低 | 跨平台 |
| **XeSS** (Intel) | 高 | 低 | 跨平台 |

**TAA Ghosting 解決**：
- 增加 Motion Vector 精確度
- 降低 TAA History Weight（反應更快但更閃）
- 確保所有動態物件都有正確的 Motion Vector

---

## Bloom

**原理**：把高於閾值的亮度像素模糊擴散。

```
Unreal:
  Post Process Volume → Bloom → Intensity + Threshold
  Bloom Method: Convolution (品質高) vs Standard (效能好)
  
Unity HDRP:
  Post Process → Bloom → Threshold + Intensity + Scatter
```

**TA 陷阱**：Bloom 在 Linear 空間計算。如果材質的 Emissive 值設得太低，Bloom 不會觸發。Emissive 強度需要超過 1.0 才能有 Bloom 效果。

---

## Color Grading 與 LUT

**LUT（Look-Up Table）**：把輸入顏色映射到輸出顏色的 3D 貼圖。

工作流：
1. 在引擎截取 **Neutral LUT**（未調色的基準）
2. 匯入 DaVinci Resolve / Photoshop 調色
3. 匯出修改後的 LUT（通常 32×32×32）
4. 匯入引擎，在 Post Process Volume 套用

```
Unreal: Post Process Volume → Color Grading → Color Grading LUT
Unity HDRP: Volume → Color Adjustments → Color Filter (或 Tonemapping LUT)
```

---

## Auto Exposure（自動曝光）

模擬人眼在明暗環境的適應。

```
Unreal:
  Min/Max EV100: 控制曝光範圍（-4 到 10 常用）
  Speed Up/Down: 進入亮/暗環境的適應速度
  
Unity HDRP:
  Exposure → Mode: Automatic
  Compensation: 整體曝光偏移
```

**TA 注意**：過激的 Auto Exposure 讓玩家從室外進入室內時有不舒適的閃爍感。Speed Down（暗→亮）通常比 Speed Up（亮→暗）需要更慢。

---

## 自訂後處理效果

**Unreal**：Material Domain 設為 Post Process
**Unity URP**：實作 `ScriptableRenderPass` + `RendererFeature`

```csharp
// Unity URP 自訂後處理 Renderer Feature
public class MyPostProcessFeature : ScriptableRendererFeature {
    public override void Create() {
        m_Pass = new MyPostProcessPass();
    }
    public override void AddRenderPasses(ScriptableRenderer renderer, ref RenderingData renderingData) {
        renderer.EnqueuePass(m_Pass);
    }
}
```

---

## 學習資源

- 🎥 [Unreal Post Process Deep Dive (YouTube)](https://www.youtube.com/results?search_query=unreal+post+process+deep+dive)
- 📖 [Unity HDRP Post Processing](https://docs.unity3d.com/Packages/com.unity.render-pipelines.high-definition@latest/index.html)
- 🎥 [Color Grading with LUT Tutorial](https://www.youtube.com/results?search_query=game+engine+LUT+color+grading)
