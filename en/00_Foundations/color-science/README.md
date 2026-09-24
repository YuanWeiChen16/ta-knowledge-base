# Color Science for TA

**Stability Tag**: `[STABLE]`

> The biggest hidden blind spot for mid-level TAs. Most "colors look wrong" problems have their root cause here.

---

## Quick Reference for Core Concepts

### Gamma vs Linear

| | Gamma Space | Linear Space |
|--|-------------|-------------|
| Storage | sRGB textures (perceptually uniform) | HDR/Linear textures |
| Calculations | Nonlinear encoded values such as sRGB must be converted to Linear before lighting or color-mixing operations | Lighting and color mixing are commonly calculated in Linear space |
| Display | Final output to screen | Intermediate calculations |

**Basic Rules**:
1. All lighting calculations happen in Linear space
2. Albedo/Diffuse textures are sRGB (require engine gamma correction)
3. Normal/Roughness/Metallic textures are Linear (no correction needed)

**Common mistake**: Setting Normal map to sRGB → incorrect normal calculations, lighting looks strange.

---

### Color Spaces

| Color Space | Usage | Engine Setting |
|-------------|-------|---------------|
| **sRGB / Rec.709** | Standard displays, SDR output | Final output |
| **ACEScg** | Wide-gamut linear working space, common in film and compositing | Optional UE working space; default is sRGB / Rec.709 |
| **Linear sRGB** | Intermediate calculation space | Unity Linear rendering |
| **Rec.2020** | HDR displays | HDR output settings |

---

### Tonemapping

The process of compressing HDR (high dynamic range) down to the range a screen can display.

Common tonemappers:
- **Unreal default tonemapper**: Uses an ACES-based tone curve; this does not mean the project working color space is ACEScg
- **ACES**: A common tone-mapping approach; appearance depends on engine version and settings
- **Filmic**: The name can refer to different curves across software; verify the implementation and settings
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
Albedo/Base Color ← import as a color texture with sRGB enabled
Normal ← set the texture type to Normal Map; data maps such as Roughness/Metallic/AO should not be sRGB-decoded
```

### Unreal Engine
```
Project Settings → Rendering → Working Color Space → sRGB / Rec.709 (default; verify for your UE version)
ACEScg is an optional project working color space, not the default
Texture → sRGB ← check for Albedo textures
Texture → sRGB ← uncheck for Normal/Roughness/Metallic
Post Process Volume → Color Grading / tone-curve settings (names and behavior vary by UE version)
```

---

## Learning Resources

- 📖 [Filmic Worlds Blog](http://filmicworlds.com/blog/) — John Hable, in-depth articles on film/game color science
- 📖 [Unreal Engine Working Color Space](https://dev.epicgames.com/documentation/unreal-engine/working-color-space-in-unreal-engine) — UE working color spaces and defaults
- 📖 [Color: From Hexcodes to Eyeballs](http://jamie-wong.com/post/color/) — comprehensive color science web article
- 🎥 [Acerola — Color Science Videos](https://www.youtube.com/@Acerola_t) — game rendering-oriented color science

---

## Hands-On Exercises (project-ideas)

1. **Color space comparison shader**: In the same scene, capture screenshots of Linear vs Gamma lighting calculations side by side. Goal: visually feel the difference.
2. **Tonemapper comparison tool**: Write a post-process shader in Unity/Unreal that can switch between ACES/Reinhard/None, and compare highlight handling differences in screenshots.
