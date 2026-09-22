# 04 — 效能分析與優化

**穩定性標籤**：方法論 `[STABLE]`，工具介面 `[ENGINE-VERSIONED]`

> 沒有 profiler 數據支持的優化是猜測。先量測，再優化。

---

## 子章節

| 章節 | 何時來查 |
|------|---------|
| `gpu-methodology/` | 效能問題診斷思路、CPU/GPU bound 判斷 |
| `unity-profiler/` | Unity Profiler、Frame Debugger 使用 |
| `unreal-insights/` | Unreal Insights、GPU Visualizer、Console 命令 |
| `mobile/` | 行動裝置特有的效能問題 |

---

## 效能優化的正確順序

1. **定義目標**：目標平台、目標 fps、目標 frame budget (ms)
2. **量測現狀**：Profiler 截取實際數據
3. **找出瓶頸**：CPU bound? GPU bound? 哪個 pass?
4. **最小化修改**：只改一件事，再量測
5. **驗證改善**：確認數字變好，視覺沒有損失
6. **記錄決策**：為什麼做這個改動
