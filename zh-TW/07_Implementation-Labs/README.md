# 07 — 實作測試室（基於業界發表）

每個 Lab 都對應一個在 GDC、SIGGRAPH 或 80.lv 上真實發表過的技術。  
**不是教你抄答案，是讓你從第一原則重現業界解法。**

---

## Lab 清單

| # | Lab 名稱 | 難度 | 技術來源 | 核心技能 |
|---|----------|------|---------|---------|
| 01 | 程序水面 Shader | ⭐⭐ Mid | 80.lv 2025 | Single Layer Water、WPO、Depth Fade |
| 02 | 水晶多層材質 | ⭐⭐⭐ Mid-Senior | 80.lv 2026 | Substrate、POM、Iridescence、Thin Film |
| 03 | 即時天氣系統 | ⭐⭐⭐ Senior | 80.lv 2025 | Blueprint 系統、MPC、Post Process 混合 |
| 04 | 風格化 VFX 爆炸 | ⭐⭐ Mid | 80.lv 2025 | Niagara、Flipbook、UberShader |
| 05 | Niagara Ribbon 拖尾 | ⭐⭐ Mid | 80.lv 2025 | Ribbon Renderer、Flipbook、Motion Vector |
| 06 | Substrate 材質分層 | ⭐⭐⭐ Senior | SIGGRAPH 2023 | Substrate Slab、能量守恆分層 |
| 07 | MegaLights 大量動態光源場景 | ⭐⭐⭐⭐ Senior+ | SIGGRAPH 2025 | MegaLights、Lumen、效能分析 |

---

## 使用方式

每個 Lab 有：
- **原始發表連結**：閱讀一次，理解作者的設計決策
- **你的任務**：不看答案，自己重現
- **驗收標準**：具體的視覺 + 效能目標
- **關鍵問題**：強迫你思考「為什麼」而非只是「怎麼做」

---

## 難度說明

| 符號 | 等級 | 預估時間 |
|------|------|---------|
| ⭐⭐ | Mid | 4-8 小時 |
| ⭐⭐⭐ | Senior | 1-3 天 |
| ⭐⭐⭐⭐ | Senior+ | 3-7 天 |
