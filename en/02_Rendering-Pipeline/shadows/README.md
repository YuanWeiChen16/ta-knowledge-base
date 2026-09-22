# Shadow Techniques

**Stability Tag**: Concepts `[STABLE]`, engine settings `[ENGINE-VERSIONED]`

---

## Shadow Map Fundamentals

**Principle**: Render scene depth from the light's perspective → during main rendering, compare depth to determine whether a pixel is in shadow.

```
Shadow Map resolution → sharpness of shadow edges
Shadow Distance      → maximum distance for high-quality shadows
Cascade count        → near-fine vs far-coarse segmentation
```

---

## Cascaded Shadow Maps (CSM)

Divide shadows into multiple Cascades — high resolution close up, low resolution at distance:

```
Cascade 0 (nearest):  high resolution, small range
Cascade 1:            medium resolution
Cascade 2:            low resolution
Cascade 3 (farthest): lowest resolution, large range
```

**TA tuning points**:
- Cascade count: PC/console 4, mobile 2-3
- Distribution: controls the ratio of each cascade; near range should typically take more
- Transition Blend: smooth transitions at cascade boundaries to avoid visible switching

---

## Common Shadow Issues and Solutions

### Shadow Acne (Self-Shadow Striping)
**Symptom**: Stripe-like dark patterns appear on object surfaces  
**Cause**: Shadow Map precision insufficient to distinguish the object from its own shadow  
**Fix**: Increase Depth Bias / Normal Bias

```
// Unreal
Shadow Bias: 0.01 → 0.05  (increase until acne disappears)
Normal Shadow Bias: 1.0 → 2.0

// Unity (Light component)
Shadow Bias: adjust until acne disappears
Shadow Normal Bias: prevent Peter-Panning
```

### Peter-Panning (Floating Shadow)
**Symptom**: Gap between shadow and object; shadow appears to float  
**Cause**: Bias set too high  
**Fix**: Reduce Bias, or use two-sided shadow caster

### Shadow Resolution Insufficient
**Symptom**: Jagged edges on shadow boundaries  
**Fix**:
1. Increase Shadow Map resolution (high cost)
2. Use PCF (Percentage Closer Filtering) softening
3. Use PCSS (physically correct soft shadows, blur increases with distance)

---

## Shadow Technique Comparison

| Technique | Quality | Cost | Notes |
|-----------|---------|------|-------|
| Hard Shadow | Low | Very low | No filtering |
| PCF | Medium | Low | Fixed-width soft |
| PCSS | High | Medium | Distance-dependent soft |
| VSM (Variance SM) | High | Medium | Supports blur, but has light bleeding |
| Ray Traced Shadows | Very high | Very high | Physically correct |

---

## Unreal Shadow Settings

```
Directional Light:
  Dynamic Shadow Distance: near-range high-quality range
  Cascade Shadow Maps: checked
  Num Dynamic Shadow Cascades: 4 (PC) / 2 (Mobile)
  
Light → Advanced:
  Shadow Bias: self-shadow offset
  Shadow Filter Sharpen: edge sharpness
```

## Unity Shadow Settings

```
Project Settings → Quality → Shadows:
  Shadow Distance: maximum distance for high-quality shadows
  Shadow Cascades: 4 Cascades (PC)
  Cascade Splits: adjust ratios
  
Directional Light:
  Shadow Type: Soft Shadows
  Resolution: High/Very High
```

---

## Hands-On Exercises (project-ideas)

1. **Shadow Bias tuning exercise**: Build a test scene (geometry at various angles) and systematically test the visual effect of different Bias values. Find the optimal Bias where "acne disappears and peter-panning is minimal." Document the values and screenshots.
