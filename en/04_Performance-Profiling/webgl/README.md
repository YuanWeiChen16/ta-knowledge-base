# Unity WebGL Platform Pitfalls

**Stability Tags**：`[ENGINE-VERSIONED: Unity 6]` `[VOLATILE: Browser support changes rapidly]`

> Source: First-hand experience porting a Unity game to WebGL in production. These are project cases, not platform guarantees; the source does not record the Unity patch, device, browser/OS versions, or server headers. Add that test environment before treating a row as reproducible.

---

## Platform Fundamentals (Read This First)

WebGL is not "native running in a browser" — it runs inside three layers of sandboxing:

```
Your game logic
    ↓
WASM (WebAssembly) — C# code runs on one thread; native C/C++ threads require opt-in and browser support
    ↓
Browser-provided WebGL API — capabilities vary by WebGL version, browser, and GPU
    ↓
Browser → OS → GPU
```

Each layer has its own constraints. The sections below are organized by problem category.

---

## Pitfall Catalog

### Runtime Environment

| Problem | Root Cause | Fix | Status (source project) |
|---------|-----------|-----|--------|
| C# `System.Threading` / `Thread` unavailable | Unity Web builds do not support C# multithreading | Split work across frames with coroutines or async/await; async/await does not move CPU work off the main thread. Unity 6 native C/C++ threading is opt-in and requires browser SharedArrayBuffer support and cross-origin isolation | ⚠️ Platform limitation |
| Multi-touch causes screen freeze | WebGL keyboard detection logic conflicts with touch events | Disable Unity's default WebGL keyboard detection; wire browser-native input API when text input is needed | ✅ Fixed |
| Video playback fails | WebGL Video Player support is limited; codec availability depends on browser | Use browser-supported formats (H.264 MP4 is most widely supported), or switch to sprite sheet / image sequence | ⚠️ Partial |

### Shader / Rendering

| Problem | Root Cause | Fix | Status (source project) |
|---------|-----------|-----|--------|
| Shader turns pink (missing material) | The shader, render pipeline, or feature is unsupported by the target browser/GPU, or a required variant is missing | Inspect Web build logs and device console; adapt shaders to the target device. Do not assume a fixed Shader Model ceiling | 📝 Reported fixed in source project |
| Shader effect disappears on iOS (object invisible) | The source project suspected a specific `clip()` / `discard` usage or device graphics implementation; reproduce with the actual device and shader variant first | Inspect build logs, reduce to a minimal repro, and validate on target hardware; do not generalize one case to all Metal or iOS devices | ✅ Reported fixed in source project |

> **Note**: WebGL extensions and shader behavior vary by browser, OS, GPU, and driver. The recorded iOS issue should be reproduced on its specific device and version; it does not describe all Safari/Metal devices.

### Audio

| Problem | Root Cause | Fix | Status (source project) |
|---------|-----------|-----|--------|
| Audio crackling / distortion | AudioClip was `Unload`ed too quickly while still playing, causing mid-playback resource release | Ensure AudioClip is not unloaded before playback ends; wait for `!audioSource.isPlaying` before setting `AudioSource.clip = null` | ✅ Fixed |

### Network / Asset Loading

| Problem | Root Cause | Fix | Status (source project) |
|---------|-----------|-----|--------|
| Bundle downloads consistently fail on specific entries | Network instability + oversized bundles increase timeout probability | ① Add retry logic (retry up to 3 times); ② Split bundles by size, keep individual bundle size within a reasonable limit | ✅ Fixed |
| Browser error popup appears | The Unity loader template or project JavaScript may display an error in browser UI; the source depends on version and configuration | Inspect the browser console and loader template; adjust error presentation while preserving useful diagnostics | ✅ Reported fixed in source project |

### Memory / Textures

| Problem | Root Cause | Fix | Status (source project) |
|---------|-----------|-----|--------|
| Texture format conflict (ETC2 vs ASTC) | Supported compressed texture formats vary by browser and GPU; desktop and iOS do not guarantee one common format | Use Unity format fallbacks or texture variants validated on target devices; test the actual browser/GPU combinations | 📝 Reported fixed in source project |
| iOS out-of-memory crash | Available memory varies by device, OS, browser, other tabs, and WebGL context; there is no universal fixed ceiling | Measure peak memory on target devices, reduce assets and concurrent loads, then tune **Initial Memory Size** / **Memory Growth** based on data | 📝 Reported fixed in source project |

---

## Unity WebGL Build Settings Evaluation

Option locations vary by Unity version; check the actual fields under WebGL Player Settings and Publishing Settings.

| Setting | Description | Recommendation |
|---------|-------------|----------------|
| **Compression: Brotli** | High compression; the server must serve precompressed files with the correct `Content-Encoding`. HTTPS is recommended for deployment, not a Brotli decompression requirement | Verify response headers, MIME type, and Unity loader configuration |
| **Compression: Gzip** | The server must serve files with the matching `Content-Encoding` | As with Brotli, verify response headers and loader configuration |
| **Native C/C++ Multithreading** | Unity 6 can opt into native C/C++ WebAssembly threads; this does not enable C# multithreading and requires SharedArrayBuffer and cross-origin isolation | Enable only when native threading is needed; verify browser support and server COOP/COEP headers, then profile |
| **Graphics Jobs / GPU Skinning** | Benefits vary by Unity version, content, device, and GPU | Treat as options to measure; compare the same build and scene on the same device rather than reusing one project's result |

---

## iOS WebGL Specifics

iOS Safari is where this project encountered issues; treat these as checks to validate by version, not as guarantees for all iOS devices:

- **Memory**: record device model, OS/Safari version, and peak usage; do not assume a fixed capacity
- **Graphics capabilities**: record WebGL version, extensions, and GPU/driver; test shaders on device
- **Native C/C++ threads**: if enabled, verify SharedArrayBuffer and cross-origin isolation; measure memory instead of assuming OOM
- **Audio context restriction**: iOS requires a user gesture (touch event) before audio can play — autoplay is blocked
- **Extension support**: Inspect extensions exposed by the target browser; do not assume `OES_texture_float`, `WEBGL_draw_buffers`, or others are available

---

## Performance Optimization (WebGL-Specific)

Measure CPU performance in the target browser. Unity Web build C# code runs on one thread; JS ↔ WASM overhead and other constraints vary by workload:

- **Draw call cost**: If the profiler shows CPU submission as a bottleneck, compare the gains from batching / GPU Instancing
- **GC allocs**: Record GC spikes and stalls in the target browser; avoid unnecessary per-frame allocations
- **Async asset loading**: Addressables async can avoid stalls while waiting for I/O, but does not move CPU work to a background thread
- **Texture resolution**: Adjust against image-quality goals and measured bandwidth/memory use

---

## Related Sections

- [Unity 6 WebGL Threads Support](https://docs.unity3d.com/6000.0/Documentation/ScriptReference/PlayerSettings.WebGL-threadsSupport.html) — native C/C++ threading support and C# limitations
- [Unity WebGL technical limitations](https://docs.unity3d.com/6000.0/Documentation/Manual/webgl-technical-overview.html) — platform limitations and deployment requirements

- [`../mobile/README.md`](../mobile/README.md) — Native mobile (iOS native / Android) performance optimization
- [`../../01_Shaders-Materials/shader-debugging/`](../../01_Shaders-Materials/shader-debugging/) — Shader debugging methods
