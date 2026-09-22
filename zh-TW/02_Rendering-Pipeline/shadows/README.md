# 陰影技術

**穩定性標籤**：概念 `[STABLE]`，引擎設定 `[ENGINE-VERSIONED]`

---

## 陰影 Map 基礎

**原理**：從光源視角渲染場景深度 → 主渲染時比較深度決定是否在陰影中。

```
Shadow Map 解析度 → 陰影邊緣清晰度
Shadow Distance   → 高品質陰影的最大距離
Cascades 數量     → 近距離精細 vs 遠距離粗糙的分段
```

---

## Cascaded Shadow Maps (CSM)

把陰影分成多個 Cascade，近處用高解析度、遠處用低解析度：

```
Cascade 0 (最近)：高解析度，小範圍
Cascade 1：中解析度
Cascade 2：低解析度
Cascade 3 (最遠)：最低解析度，大範圍
```

**TA 調整要點**：
- Cascade 數量：PC/主機 4，行動裝置 2-3
- Distribution（分布）：控制每個 cascade 的比例，通常近處要佔更多
- 過渡 Blend：在 cascade 邊界做平滑過渡，避免明顯切換

---

## 常見陰影問題與解法

### Shadow Acne（自陰影紋路）
**症狀**：物件表面出現條紋狀暗紋  
**原因**：Shadow Map 精度不足以區分物件和自身陰影  
**解法**：增加 Depth Bias / Normal Bias

```
// Unreal
Shadow Bias: 0.01 → 0.05  （增加直到 acne 消失）
Normal Shadow Bias: 1.0 → 2.0

// Unity (Light component)
Shadow Bias: 調整直到 acne 消失
Shadow Normal Bias: 避免 Peter-Panning
```

### Peter-Panning（陰影浮空）
**症狀**：陰影和物件之間有間隙，陰影像浮起來  
**原因**：Bias 設太大  
**解法**：降低 Bias，或使用 two-sided shadow caster

### Shadow Resolution 不足
**症狀**：陰影邊緣鋸齒明顯  
**解法**：
1. 增加 Shadow Map 解析度（成本高）
2. 使用 PCF（Percentage Closer Filtering）softening
3. 使用 PCSS（物理正確的軟陰影，隨距離變模糊）

---

## 陰影技術對比

| 技術 | 品質 | 成本 | 特點 |
|------|------|------|------|
| Hard Shadow | 低 | 極低 | 無 filtering |
| PCF | 中 | 低 | 固定寬度 soft |
| PCSS | 高 | 中 | 距離相關 soft |
| VSM (Variance SM) | 高 | 中 | 支援 blur，但有 light bleeding |
| Ray Traced Shadows | 極高 | 極高 | 物理正確 |

---

## Unreal 陰影設定

```
Directional Light:
  Dynamic Shadow Distance: 近距離高品質範圍
  Cascade Shadow Maps: 勾選
  Num Dynamic Shadow Cascades: 4 (PC) / 2 (Mobile)
  
Light → Advanced:
  Shadow Bias: 自陰影偏移
  Shadow Filter Sharpen: 邊緣清晰度
```

## Unity 陰影設定

```
Project Settings → Quality → Shadows:
  Shadow Distance: 高品質陰影最大距離
  Shadow Cascades: 4 Cascades (PC)
  Cascade Splits: 調整比例
  
Directional Light:
  Shadow Type: Soft Shadows
  Resolution: High/Very High
```

---

## 實作練習 (project-ideas)

1. **陰影 Bias 調整練習**：建立一個測試場景（各種角度的幾何體），系統性地測試不同 Bias 值的視覺效果。找出「acne 消失且 peter-panning 最小」的最優 Bias。記錄數值和截圖。
