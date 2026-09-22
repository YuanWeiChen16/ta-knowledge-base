# Post-Processing Pipeline

**Stability Tag**: Concepts `[STABLE]`, engine settings `[ENGINE-VERSIONED]`

---

## Post-Processing Execution Order

```
Scene Color (HDR)
  ↓
Temporal Anti-Aliasing (TAA)    ← accumulates historical frames for anti-aliasing
  ↓
Depth of Field                  ← depth blur
  ↓
Motion Blur                     ← motion blur
  ↓
Bloom                           ← highlight glow
  ↓
Lens Flare / Dirt Mask          ← lens effects
  ↓
Eye Adaptation (Auto Exposure)  ← automatic exposure
  ↓
Tone Mapping                    ← HDR → LDR compression
  ↓
Color Grading (LUT)             ← final tone adjustment
  ↓
FXAA / SMAA (optional)         ← fast anti-aliasing supplement
  ↓
UI Overlay
```

---

## Anti-Aliasing Techniques

| Technique | Quality | Cost | Issues |
|-----------|---------|------|--------|
| **MSAA** | High | High | Doesn't support Deferred |
| **TAA** | High | Medium | Ghosting, blurriness |
| **FXAA** | Low | Very low | Over-blurring |
| **SMAA** | Medium | Low | Better than FXAA but limited |
| **DLSS** (NVIDIA) | Very high | Low (AI) | Requires RTX GPU |
| **FSR** (AMD) | High | Low | Cross-platform |
| **XeSS** (Intel) | High | Low | Cross-platform |

**Solving TAA Ghosting**:
- Improve Motion Vector accuracy
- Reduce TAA History Weight (reacts faster but flickers more)
- Ensure all dynamic objects have correct Motion Vectors

---

## Bloom

**Principle**: Blurs and spreads pixels with brightness above a threshold.

```
Unreal:
  Post Process Volume → Bloom → Intensity + Threshold
  Bloom Method: Convolution (higher quality) vs Standard (better performance)
  
Unity HDRP:
  Post Process → Bloom → Threshold + Intensity + Scatter
```

**TA pitfall**: Bloom is calculated in Linear space. If a material's Emissive value is set too low, Bloom won't trigger. Emissive intensity must exceed 1.0 for Bloom to take effect.

---

## Color Grading and LUT

**LUT (Look-Up Table)**: A 3D texture that maps input colors to output colors.

Workflow:
1. Capture a **Neutral LUT** in-engine (unadjusted baseline)
2. Import to DaVinci Resolve / Photoshop for color grading
3. Export the modified LUT (usually 32×32×32)
4. Import to engine, apply in Post Process Volume

```
Unreal: Post Process Volume → Color Grading → Color Grading LUT
Unity HDRP: Volume → Color Adjustments → Color Filter (or Tonemapping LUT)
```

---

## Auto Exposure

Simulates the human eye adapting to bright and dark environments.

```
Unreal:
  Min/Max EV100: controls exposure range (-4 to 10 is common)
  Speed Up/Down: adaptation speed when entering bright/dark environments
  
Unity HDRP:
  Exposure → Mode: Automatic
  Compensation: global exposure offset
```

**TA note**: Overly aggressive Auto Exposure causes uncomfortable flicker when players move from outdoors to indoors. Speed Down (dark→bright) usually needs to be slower than Speed Up (bright→dark).

---

## Custom Post-Processing Effects

**Unreal**: Set Material Domain to Post Process  
**Unity URP**: Implement `ScriptableRenderPass` + `RendererFeature`

```csharp
// Unity URP custom post-processing Renderer Feature
public class MyPostProcessFeature : ScriptableRendererFeature {
    public override void Create() {
        m_Pass = new MyPostProcessPass();
    }
    public override void AddRenderPasses(ScriptableRenderer renderer, ref RenderingData renderingData) {
        renderer.EnqueuePass(m_Pass);
    }
}
```

---

## Learning Resources

- 🎥 [Unreal Post Process Deep Dive (YouTube)](https://www.youtube.com/results?search_query=unreal+post+process+deep+dive)
- 📖 [Unity HDRP Post Processing](https://docs.unity3d.com/Packages/com.unity.render-pipelines.high-definition@latest/index.html)
- 🎥 [Color Grading with LUT Tutorial](https://www.youtube.com/results?search_query=game+engine+LUT+color+grading)
