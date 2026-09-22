# 04 — Performance Profiling & Optimization

**Stability Tag**: Methodology `[STABLE]`, tool interfaces `[ENGINE-VERSIONED]`

> Optimization without profiler data is guessing. Measure first, then optimize.

---

## Sub-Sections

| Section | When to Look Here |
|---------|------------------|
| `gpu-methodology/` | Performance issue diagnosis approach, CPU/GPU bound determination |
| `unity-profiler/` | Unity Profiler, Frame Debugger usage |
| `unreal-insights/` | Unreal Insights, GPU Visualizer, console commands |
| `mobile/` | Mobile-specific performance issues |

---

## The Correct Order for Performance Optimization

1. **Define targets**: target platform, target fps, target frame budget (ms)
2. **Measure current state**: Profiler captures actual data
3. **Find the bottleneck**: CPU bound? GPU bound? Which pass?
4. **Minimal change**: Change only one thing, then measure again
5. **Verify improvement**: Confirm the numbers improved, visual quality wasn't lost
6. **Document decisions**: Why was this change made
