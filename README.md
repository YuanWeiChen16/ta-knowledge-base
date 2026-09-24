# ta-knowledge-base

A structured Technical Artist knowledge base built from GDC, SIGGRAPH, and 80.lv publications — with hands-on implementation labs for Unreal Engine 5.

> Built by a programmer transitioning to Technical Artist. Organized around concepts, not tools.

---

## 語言 / Language

| 版本 | 路徑 |
|------|------|
| 繁體中文 | [`zh-TW/`](./zh-TW/) |
| English | [`en/`](./en/) |

---

## 內容概覽 / Contents

```
├── 00_Foundations/          線代、色彩科學、渲染管線、GPU 架構
├── 01_Shaders-Materials/    HLSL、PBR、Unity URP/HDRP、Unreal Material
├── 02_Rendering-Pipeline/   Deferred/Forward、Lumen、陰影、後處理、Vulkan 概念
├── 03_VFX-Systems/          VFX 原則、Unity VFX Graph、Unreal Niagara
├── 04_Performance-Profiling/ GPU 診斷方法論、Unity Profiler、Unreal Insights、行動裝置
├── 05_Pipeline-Tools/       Houdini VAT、Substance、Python 腳本、USD、Unreal PCG
├── 06_TA-Role-Mindset/      TA 角色與溝通模式
├── 07_Implementation-Labs/  11 個業界發表實作題（GDC / SIGGRAPH / 80.lv）
├── 08_Programmer-TA-Track/  程式師轉 TA 專屬路徑
└── Resources/               精選資源、外部 GitHub 索引、前沿技術追蹤
```

---

## 07 實作 Lab 清單 / Implementation Labs

| # | 名稱 | 來源 | 難度 |
|---|------|------|------|
| 01 | 程序水面 Shader | 80.lv 2025 | ⭐⭐ |
| 02 | 水晶多層材質（Substrate）| 80.lv 2026 | ⭐⭐⭐ |
| 03 | 即時天氣系統 | 80.lv 2025 | ⭐⭐⭐ |
| 04 | 風格化 VFX 爆炸 | 80.lv 2025 | ⭐⭐ |
| 05 | Niagara Ribbon 拖尾 | 80.lv 2025 | ⭐⭐ |
| 06 | Substrate 材質分層 | SIGGRAPH 2023 | ⭐⭐⭐ |
| 07 | MegaLights 大量動態光源 | SIGGRAPH 2025 | ⭐⭐⭐⭐ |
| 08 | 世界資訊驅動 Shader | GDC TA Summit | ⭐⭐ |
| 09 | 純 Shader 粒子系統 | GDC TA Summit 2024 | ⭐⭐⭐ |
| 10 | GPU 植被互動系統 | GDC TA Summit 2024 | ⭐⭐⭐⭐ |
| 11 | Nanite 植被 + PCG 程序森林 | GDC 2024 | ⭐⭐⭐ |

---

## 設計原則 / Design Principles

- **參考語料庫，不是課程** — 遇到問題來查，不是從頭讀到尾
- **概念優先，工具為葉** — 按概念層組織，引擎實作是葉節點
- **穩定性分層** — `[STABLE]` / `[ENGINE-VERSIONED]` / `[VOLATILE]`
- **作品集驅動** — 部分章節提供實作練習；`07_Implementation-Labs/` 集中列出具明確驗收標準的實作題

---

## 目標讀者 / Target Audience

- 程式師（C++/C#/Python）轉職 Technical Artist
- 已有美術背景、想加強技術深度的 TA
- 想系統性補強 Unreal Engine 渲染知識的開發者

---

## 工具版本 / Engine Versions

- Unreal Engine 5.4+（部分 Lab 需要 5.5 for MegaLights）
- Unity 6 / URP 17
- Houdini 20+
- Substance 3D（請依專案使用版本核對匯出預設）

---

## License

MIT — 自由使用、修改、分享。  
如果這個資料庫對你有幫助，歡迎 ⭐ Star 或提 PR 補充。
