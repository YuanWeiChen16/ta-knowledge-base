# 00 — 基礎層

**穩定性標籤**：`[STABLE]` — 這裡的概念極少改變。學一次，用一輩子。

---

## 這個章節是什麼

TA 工作的底層語言。不是前置課程——是隨查隨用的參考。

遇到不懂的地方回來查，而不是試圖在動手之前把全部讀完。

---

## 子章節

| 章節 | 何時來查 |
|------|---------|
| `linear-algebra/` | 看到 transform、tangent space、法線貼圖問題、旋轉不對時 |
| `color-science/` | 顏色看起來不對、HDR overflow、顯示器校正、ACES tonemap 問題時 |
| `rendering-pipeline/` | 想理解為什麼某個效果要在特定 pass 做、資料流問題時 |
| `gpu-architecture/` | 看不懂 profiler 輸出、想理解 draw call/bandwidth/latency 時 |

---

## 最高槓桿的投入

如果你只能花時間在一個基礎主題，按優先序：

1. **渲染管線資料流** — 理解 vertex→rasterize→fragment 的每個階段存在什麼資料
2. **色彩科學** — 中階 TA 最大的隱藏盲點，也最直接影響視覺輸出品質
3. **線性代數** — 座標系、法線貼圖、切線空間問題都在這
4. **GPU 架構概念** — 讓 profiler 數字變得有意義
