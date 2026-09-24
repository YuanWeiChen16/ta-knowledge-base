# Unity WebGL 平台採坑紀錄

**穩定性標籤**：`[ENGINE-VERSIONED: Unity 6]` `[VOLATILE: 瀏覽器支援快速變動]`

> 來源：實際專案移植 Unity 遊戲至 WebGL 的第一手經驗整理。這些是專案案例，不是平台保證；原文未記錄 Unity patch、裝置、瀏覽器/OS 版本或伺服器標頭，重現前請補上測試環境。

---

## 平台本質限制（先讀這個）

WebGL 不是「跑在瀏覽器的 native」，而是跑在三層沙盒裡：

```
你的遊戲邏輯
    ↓
WASM（WebAssembly）— C# 程式在單一執行緒執行；原生 C/C++ 執行緒需額外啟用且受瀏覽器限制
    ↓
瀏覽器提供的 WebGL API — 功能依 WebGL 版本、瀏覽器與 GPU 而異
    ↓
瀏覽器 → OS → GPU
```

這三層各自有限制，下面按問題分類整理。

---

## 採坑清單

### 執行環境限制

| 問題 | 根因 | 解法 | 狀態（原專案記錄） |
|------|------|------|------|
| C# 無法使用 `System.Threading` / `Thread` | Unity Web builds 不支援 C# 多執行緒 | 將工作切成 coroutine / async-await 等跨幀工作；async/await 本身不會把 CPU 工作移出主執行緒。Unity 6 的 Native C/C++ threading 需另行啟用，並要求瀏覽器支援 SharedArrayBuffer 與跨來源隔離 | ⚠️ 平台限制 |
| 多指觸控導致畫面當機 | WebGL 的鍵盤開啟判斷與觸控事件互相干擾 | 關閉 Unity 預設的 WebGL 鍵盤偵測；需要輸入框時改串瀏覽器原生 input API | ✅ 已解 |
| 影片無法播放 | WebGL 對 Video Player 支援有限，codec 視瀏覽器而定 | 需使用瀏覽器支援的格式（H.264 MP4 最廣泛），或改用序列幀 / Sprite Sheet | ⚠️ 未完全解 |

### Shader / 渲染問題

| 問題 | 根因 | 解法 | 狀態（原專案記錄） |
|------|------|------|------|
| Shader 變紫色（材質 missing）| Shader、render pipeline 或所用功能不受目標瀏覽器/GPU 支援，或編譯變體缺失 | 查看 Web build 編譯記錄與實機 console，按目標裝置能力調整 shader；不要套用固定的 Shader Model 上限 | 📝 原專案回報已修正 |
| iOS 裝置上 shader 效果直接消失（物件不見）| 原專案懷疑與 `clip()` / `discard` 的特定用法或該裝置的圖形實作有關，需先用實機和 shader variant 重現 | 檢查編譯記錄、簡化最小重現案例並在目標裝置驗證；不要將單一案例推廣成 Metal 或 iOS 的普遍行為 | ✅ 原專案回報已解 |

> **注意**：WebGL extension 與 shader 行為依瀏覽器、OS、GPU/driver 而異。原專案記錄的 iOS 問題需在對應裝置與版本重現，不代表所有 Safari/Metal 裝置都有相同行為。

### 音效問題

| 問題 | 根因 | 解法 | 狀態（原專案記錄） |
|------|------|------|------|
| 音效破音、雜音 | 音效 clip 被太快 `Unload`，播放中途資源已釋放 | 確保 AudioClip 在播放完畢前不被 unload；使用 `AudioSource.clip = null` 前等待 `!audioSource.isPlaying` | ✅ 已解 |

### 網路 / 資源載入

| 問題 | 根因 | 解法 | 狀態（原專案記錄） |
|------|------|------|------|
| 下載 bundle 固定幾筆失敗 | 網路不穩定 + 單一 bundle 過大，逾時機率高 | ① 加入重試機制（retry 3 次）；② bundle 按大小切分，單包控制在合理上限 | ✅ 已解 |
| 網頁跳出錯誤彈窗（browser alert）| Unity loader template 或專案自訂 JavaScript 將錯誤呈現在瀏覽器 UI；實際來源依版本與設定而異 | 檢查 browser console 和 loader template，調整錯誤呈現方式並保留可診斷的錯誤記錄 | ✅ 原專案回報已解 |

### 記憶體 / 貼圖

| 問題 | 根因 | 解法 | 狀態（原專案記錄） |
|------|------|------|------|
| 電腦 + 手機貼圖格式衝突（ETC2 vs ASTC）| 瀏覽器/GPU 支援的壓縮格式不同；不能假設桌機或 iOS 一律支援同一格式 | 使用 Unity 的格式 fallback 或提供經目標裝置驗證的 texture variant；逐一測試實際瀏覽器與 GPU | 📝 原專案回報已修正 |
| iOS 裝置記憶體不足閃退 | 可用記憶體會因裝置、OS、瀏覽器、分頁與 WebGL context 而異，沒有通用固定上限 | 量測目標裝置的峰值記憶體；縮減資產與同時載入量，再依資料調整 **Initial Memory Size** / **Memory Growth** | 📝 原專案回報已修正 |

---

## Unity WebGL 建置設定評估

選項位置依 Unity 版本而異；請在 WebGL Player Settings 與 Publishing Settings 中確認實際可用的欄位。

| 設定 | 說明 | 建議 |
|------|------|------|
| **壓縮格式：Brotli** | 壓縮率高；伺服器必須以正確的 `Content-Encoding` 傳送預壓縮檔案。HTTPS 是部署建議，不是 Brotli 解壓縮本身的條件 | 確認伺服器回應標頭、MIME type 與 Unity 載入器設定一致 |
| **壓縮格式：Gzip** | 伺服器需正確傳送對應的 `Content-Encoding` | 與 Brotli 一樣，確認伺服器回應標頭與載入器設定 |
| **Native C/C++ Multithreading** | Unity 6 可選啟用原生 C/C++ WebAssembly threads；不會啟用 C# 多執行緒，且需要 SharedArrayBuffer 與跨來源隔離 | 只有確有原生多執行緒需求時才開啟，核對瀏覽器與伺服器 COOP/COEP 標頭並量測 |
| **Graphics Jobs / GPU Skinning** | 效益依 Unity 版本、內容、裝置及 GPU 而異 | 視為待測選項；用相同 build、場景與裝置比較，不沿用單一專案結論 |

---

## iOS WebGL 特別注意事項

iOS Safari 是本專案遇到問題的環境；以下為待逐版本驗證的檢查項，不代表所有 iOS 裝置：

- **記憶體**：記錄裝置型號、OS/Safari 版本與峰值；不要以固定容量推估
- **圖形能力**：記錄 WebGL 版本、extensions 與 GPU/driver，並在實機測 shader
- **原生 C/C++ threads**：若啟用，確認 SharedArrayBuffer 與跨來源隔離設定；另行量測記憶體，不推定必然 OOM
- **audio context 限制**：iOS 要求使用者互動後才能播放音效（touch event 觸發），不能自動播
- **Extension 支援**：檢查目標瀏覽器實際暴露的 extensions；不要預設 `OES_texture_float`、`WEBGL_draw_buffers` 等一定可用

---

## 效能優化方向（WebGL 特有）

WebGL 的 CPU 效能需在目標瀏覽器量測。Unity Web build 的 C# 程式在單一執行緒執行；JS ↔ WASM 成本和其他限制依工作負載而異：

- **Draw Call 成本**：若 profiler 顯示 CPU 提交成本是瓶頸，再比較 batching / GPU Instancing 的收益
- **GC Alloc**：記錄目標瀏覽器的 GC 峰值和卡頓；避免不必要的每幀配置
- **非同步資源載入**：用 Addressables async 等待 I/O 可避免同步載入造成停頓，但不會自動把 CPU 計算移到背景執行緒
- **貼圖解析度**：依畫質目標和實測頻寬/記憶體用量調整

---

## 相關章節

- [Unity 6 WebGL Threads Support](https://docs.unity3d.com/6000.0/Documentation/ScriptReference/PlayerSettings.WebGL-threadsSupport.html) — 原生 C/C++ threads 支援與 C# 限制
- [Unity WebGL technical limitations](https://docs.unity3d.com/6000.0/Documentation/Manual/webgl-technical-overview.html) — 平台限制與部署條件

- [`../mobile/README.md`](../mobile/README.md) — 原生行動裝置（iOS native / Android）效能優化
- [`../../01_Shaders-Materials/shader-debugging/`](../../01_Shaders-Materials/shader-debugging/) — Shader 除錯方法
