# Color Science for TA

**Stability Tag**: `[STABLE]`

> The biggest hidden blind spot for mid-level TAs. Most "colors look wrong" problems have their root cause here.

---

## Quick Reference for Core Concepts

### Gamma vs Linear

| | Gamma Space | Linear Space |
|--|-------------|-------------|
| Storage | sRGB textures (perceptually uniform) | HDR/Linear textures |
| Calculations | **Wrong** (lighting calculations cannot be done here) | **Correct** |
| Display | Final output to screen | Intermediate calculations |

**Golden Rules**:
1. All lighting calculations happen in Linear space
2. Albedo/Diffuse textures are sRGB (require engine gamma correction)
3. Normal/Roughness/Metallic textures are Linear (no correction needed)

**Common mistake**: Setting Normal map to sRGB → incorrect normal calculations, lighting looks strange.

---

### Color Spaces

| Color Space | Usage | Engine Setting |
|-------------|-------|---------------|
| **sRGB / Rec.709** | Standard displays, SDR output | Final output |
| **ACEScg** | High dynamic range working space | UE default working space |
| **Linear sRGB** | Intermediate calculation space | Unity Linear rendering |
| **Rec.2020** | HDR displays | HDR output settings |

---

### Tonemapping

The process of compressing HDR (high dynamic range) down to the range a screen can display.

Common tonemappers:
- **ACES**: Film industry standard, UE default. Warm tones, strong contrast
- **Filmic**: Unity HDRP default. ACES-like but adjustable
- **Reinhard**: Simple, prone to overexposure
- **AgX** (Blender 3.6+): Better highlight preservation

**TA note**: A material's appearance before and after the tonemapper will differ. RenderDoc can capture the pre-tonemapper HDR buffer.

---

### White Balance & Color Temperature

- Color temperature expressed in Kelvin (K): ~3200K warm incandescent, ~6500K daylight
- The White Balance post-processing in UE/Unity adjusts the perceptual white point
- Affects the overall tonal cast of the entire scene, not just light sources

---

## Engine Settings Quick Reference

### Unity
```
Project Settings → Player → Color Space → Linear  ← must be set to Linear
Texture Import → sRGB (Color Texture) ← check for Albedo textures
Texture Import → sRGB (Color Texture) ← uncheck for Normal/Roughness
```

### Unreal Engine
```
Project Settings → Rendering → Working Color Space → ACEScg (default)
Texture → sRGB ← check for Albedo textures
Texture → sRGB ← uncheck for Normal/Roughness/Metallic
Post Process Volume → Tone Curve Amount (ACES intensity adjustment)
```

---

## Learning Resources

- 📖 [Filmic Worlds Blog](http://filmicworlds.com/blog/) — John Hable, in-depth articles on film/game color science
- 📖 [Color: From Hexcodes to Eyeballs](http://jamie-wong.com/post/color/) — comprehensive color science web article
- 🎥 [Acerola — Color Science Videos](https://www.youtube.com/@Acerola_t) — game rendering-oriented color science

---

## Hands-On Exercises (project-ideas)

1. **Color space comparison shader**: In the same scene, capture screenshots of Linear vs Gamma lighting calculations side by side. Goal: visually feel the difference.
2. **Tonemapper comparison tool**: Write a post-process shader in Unity/Unreal that can switch between ACES/Reinhard/None, and compare highlight handling differences in screenshots.
