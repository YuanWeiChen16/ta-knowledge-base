# 前沿技術追蹤

> **`[VOLATILE]` — 每季評估一次。** 這裡的技術發展快速，不代表立即必學，代表值得關注。
> 最後更新：2025 Q3

---

## 神經渲染 (Neural Rendering)

### 3D Gaussian Splatting (3DGS)
- **是什麼**：用數百萬個 3D 高斯橢球體表示場景，比 NeRF 快 100x
- **TA 相關性**：掃描真實場景快速生成可視化資產、環境背景
- **工具**：[Luma AI](https://lumalabs.ai/)、[Polycam](https://poly.cam/)、[gaussian-splatting repo](https://github.com/graphdeco-inria/gaussian-splatting)
- **引擎支援**：Unreal 有社群插件，Unity 也有實驗性支援
- **狀態**：尚不適合即時遊戲渲染，但作為場景掃描/參考工具已實用

### AI 貼圖生成
- **Stable Diffusion + ControlNet**：從參考圖生成 tileable 材質
- **Adobe Firefly (Substance)**：在 Substance 內整合的 AI 生成
- **狀態**：輔助工具，不取代 Substance Designer 工作流

---

## 升解析度與幀生成

| 技術 | 廠商 | 狀態 |
|------|------|------|
| DLSS 3.5 (Frame Generation) | NVIDIA | ✅ 生產就緒，RTX 40xx |
| FSR 3 (Fluid Motion Frames) | AMD | ✅ 跨平台 |
| XeSS | Intel | ✅ 跨平台 |
| TSR (UE5 內建) | Epic | ✅ 生產就緒 |

**TA 影響**：upscaling 現在是效能預算工具，不只是後期優化。設計目標解析度時需考慮。

---

## Mesh Shaders / Task Shaders

- **是什麼**：取代傳統 Vertex + Geometry shader 的新管線
- **核心優勢**：GPU 端 culling 和 LOD 選擇，彈性幾何處理
- **Nanite 關係**：Nanite 的 meshlet 系統就是建立在 mesh shader 上
- **TA 相關性**：理解 meshlet 概念有助於理解 Nanite 限制
- **狀態**：引擎已採用，直接寫 mesh shader 仍是進階主題

---

## Motion Matching (ML 驅動動畫)

- **UE5 原生支援**：Motion Matching 節點（UE 5.4+）
- **原理**：從 pose database 搜尋最匹配當前狀態的動畫姿勢，不用手寫 state machine
- **TA 相關性**：設定 pose database、tuning cost function、處理 blend space
- **Unity**：Motion Matching 有第三方插件（KinematicCharacterController + Motion Matching）
- **狀態**：UE5 已生產就緒，Unity 尚在發展中

---

## Path Tracing / Full Ray Tracing

- **UE5 Path Tracer**：支援離線品質的完整 path tracing，用於截圖/預覽/Cinematic
- **Real-time Path Tracing**：仍處於研究階段，硬體要求極高
- **TA 用途**：在 path tracer 模式下驗證材質的真實物理正確性
- **狀態**：即時遊戲用 Lumen，path tracer 用於參考/截圖

---

## Substrate 材質系統（UE5.3+）

- **是什麼**：取代舊 Material 系統的全新材質架構，支援真正的多層材質
- **優勢**：一個材質可以有多個 BSDF layer（皮膚下層 + 上層油脂 + 雨水）
- **狀態**：UE 5.4 仍是 Experimental，5.5+ 預計轉正式
- **學習資源**：[Unreal Substrate Documentation](https://docs.unrealengine.com/5.4/en-US/substrate-materials-in-unreal-engine/)

---

## 季度評估協議

每季度（3個月）檢查這個文件：

1. **檢查狀態變化**：哪些從 Experimental 變成 Production Ready？
2. **移除過時條目**：已被取代的技術移到 `deprecated/` 備存
3. **新增條目**：SIGGRAPH / GDC 後更新最新技術
4. **更新「最後更新」日期**

> 不需要追蹤每個細節，只需要知道它存在、大概是什麼、何時值得深入。
