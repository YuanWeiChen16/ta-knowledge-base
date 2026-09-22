# 00 — Foundations

**Stability Tag**: `[STABLE]` — The concepts here rarely change. Learn once, use forever.

---

## What This Section Is

The underlying language of TA work. Not a prerequisite — a reference to look up as needed.

Come back here when you hit something you don't understand, rather than trying to read it all before you start working.

---

## Sub-Sections

| Section | When to Look Here |
|---------|------------------|
| `linear-algebra/` | When you see transform, tangent space, normal map issues, or rotations that are wrong |
| `color-science/` | When colors look wrong, HDR overflow, display calibration, or ACES tonemap issues |
| `rendering-pipeline/` | When you want to understand why a particular effect needs to be done in a specific pass, or data flow issues |
| `gpu-architecture/` | When profiler output doesn't make sense, or when you want to understand draw call/bandwidth/latency |

---

## Highest-Leverage Investment

If you can only spend time on one foundational topic, in priority order:

1. **Rendering pipeline data flow** — understand what data exists at each stage: vertex → rasterize → fragment
2. **Color science** — the biggest hidden blind spot for mid-level TAs, and directly impacts visual output quality
3. **Linear algebra** — coordinate systems, normal maps, and tangent space issues all live here
4. **GPU architecture concepts** — makes profiler numbers meaningful
