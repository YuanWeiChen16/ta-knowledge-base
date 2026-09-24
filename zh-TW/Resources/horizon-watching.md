# 前沿技術追蹤

> **`[VOLATILE]` — 每季評估一次。** 這裡的技術發展快速，不代表立即必學，代表值得關注。
> 最後檢視：2026 Q3。此文件是技術觀察清單，不是完整的產品支援矩陣；功能、版本與平台支援請以連結的官方文件為準。

---

## 神經渲染 (Neural Rendering)

### 3D Gaussian Splatting (3DGS)
- **是什麼**：以 3D 高斯表示和渲染場景；和 NeRF 的速度、品質比較取決於資料集與實作，不宜用單一倍數概括
- **TA 相關性**：掃描真實場景快速生成可視化資產、環境背景
- **工具**：[Luma AI](https://lumalabs.ai/)、[Polycam](https://poly.cam/)、[gaussian-splatting repo](https://github.com/graphdeco-inria/gaussian-splatting)
- **引擎支援**：依引擎版本與插件而異；正式導入前核對維護狀態、授權和平台支援
- **狀態**：適合作為場景擷取/參考的候選方案；即時執行能力須按資產與目標硬體驗證

### AI 貼圖生成
- **Stable Diffusion + ControlNet**：從參考圖生成 tileable 材質
- **Adobe Firefly / Substance AI 功能**：功能與可用性依產品版本、授權及地區而異，使用前查閱產品文件
- **狀態**：可作為輔助工具評估，不取代程序材質工作流

---

## 升解析度與幀生成

| 技術 | 廠商 | 狀態 |
|------|------|------|
| DLSS | NVIDIA | 升頻、光線重建與影格生成依 DLSS 版本、GPU、遊戲整合而異 |
| FSR | AMD | 升頻與影格生成依 FSR 版本、GPU、遊戲整合而異 |
| XeSS | Intel | 可用功能和硬體路徑依版本、GPU 與遊戲整合而異 |
| TSR | Epic | Unreal Engine 內建時域升頻；品質與成本依版本、解析度及設定而異 |

**TA 影響**：upscaling 現在是效能預算工具，不只是後期優化。設計目標解析度時需考慮。

---

## Mesh Shaders / Task Shaders

- **是什麼**：取代傳統 Vertex + Geometry shader 的新管線
- **核心優勢**：GPU 端 culling 和 LOD 選擇，彈性幾何處理
- **Nanite 關係**：Nanite 使用虛擬化幾何與 cluster-based 處理；不要將 Nanite 等同於一般 mesh shader 管線
- **TA 相關性**：理解 meshlet 概念有助於理解 Nanite 限制
- **狀態**：引擎已採用，直接寫 mesh shader 仍是進階主題

---

## Motion Matching (ML 驅動動畫)

- **UE5 原生支援**：Motion Matching 節點（UE 5.4+）
- **原理**：從 pose database 搜尋最匹配當前狀態的動畫姿勢，不用手寫 state machine
- **TA 相關性**：設定 pose database、tuning cost function、處理 blend space
- **Unity**：Motion Matching 有第三方插件（KinematicCharacterController + Motion Matching）
- **狀態**：功能與支援依引擎版本/外掛而異；導入前查閱目標版本文件

---

## Path Tracing / Full Ray Tracing

- **UE5 Path Tracer**：支援離線品質的完整 path tracing，用於截圖/預覽/Cinematic
- **Real-time Path Tracing**：仍處於研究階段，硬體要求極高
- **TA 用途**：在 path tracer 模式下驗證材質的真實物理正確性
- **狀態**：即時遊戲用 Lumen，path tracer 用於參考/截圖

---

## Substrate 材質系統（UE5.3+）

- **是什麼**：UE5 的模組化材質架構，可組合多層 BSDF；與既有材質系統並存
- **優勢**：一個材質可以有多個 BSDF layer（皮膚下層 + 上層油脂 + 雨水）
- **狀態**：UE5.5 起為 Beta；UE5.8 文件仍標示 Beta，正式出貨前依目標版本評估風險
- **學習資源**：[Unreal Substrate Documentation (UE5.8)](https://dev.epicgames.com/documentation/unreal-engine/substrate-materials-in-unreal-engine) · [UE5.5 Release Notes](https://dev.epicgames.com/documentation/unreal-engine/unreal-engine-5-5-release-notes)

---

## 季度評估協議

每季度（3個月）檢查這個文件：

1. **檢查狀態變化**：哪些從 Experimental 變成 Production Ready？
2. **移除過時條目**：已被取代的技術移到 `deprecated/` 備存
3. **新增條目**：SIGGRAPH / GDC 後更新最新技術
4. **更新「最後更新」日期**

> 不需要追蹤每個細節，只需要知道它存在、大概是什麼、何時值得深入。
