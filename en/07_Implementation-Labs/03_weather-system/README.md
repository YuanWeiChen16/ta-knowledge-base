# Lab 03 — Real-Time Weather System

**Difficulty**: ⭐⭐⭐ Senior  
**Estimated Time**: 2–3 days  
**Engine Version**: UE5.3+

---

## Original Publication

- **Source**: 80.lv — *"Crafting a Real-Time Sci-Fi Weather System with Multiple Moods in UE5"* (2025)
- **URL**: https://80.lv/articles/crafting-a-real-time-sci-fi-weather-system-with-multiple-moods-in-ue5
- **Author**: Marek Zaranski (Technical Artist, Drago Entertainment)

**Reading focus**: The Smooth Operator component architecture, MPC (Material Parameter Collection) global control, and the technical rationale for blending two Post Process Volumes.

---

## Your Task

**Build a programmable weather system in UE5 that supports real-time switching and blending between at least 3 weather states.**

### Required Features
1. **Weather Manager Blueprint**: Controls weather state switching, transition duration, and current blend progress
2. **At Least 3 Weather States**: e.g., sunny, overcast, rainy (design the visual style yourself)
3. **MPC Global Material Control**: Use a Material Parameter Collection to synchronously adjust wetness, color shift, and saturation across all scene materials
4. **Sky/Fog Transition**: Sky Atmosphere or Exponential Height Fog parameters change with weather state
5. **Post Process Blending**: At least 2 weather states have distinct post-processing effects (color grading, exposure, fog density)
6. **Editor Utility Widget**: Ability to switch and preview weather states in real time within the Editor

### Bonus Features
- Wet ground material driven by a Ripple Render Target
- Rain Niagara particle system
- Randomized lightning trigger system

---

## Suggested System Architecture

```
BP_WeatherManager
  └── Component: WeatherSmoothOperator
        ├── Module: SkyModule        (Sky Atmosphere parameters)
        ├── Module: FogModule        (Height Fog parameters)
        ├── Module: PostProcessModule (PP Volume weight blending)
        ├── Module: MaterialModule   (MPC parameter push)
        └── Module: VFXModule        (Niagara system on/off)

MPC_WeatherGlobal
  ├── Wetness (0-1)
  ├── SkyTint (Color)
  ├── FogDensity (float)
  └── RainIntensity (0-1)
```

---

## Acceptance Criteria

| Category | Criteria |
|----------|----------|
| Functional | Weather transitions are smooth (no instant snapping) |
| Functional | MPC changes instantly affect all scene materials |
| Functional | Editor Widget can preview all weather states |
| Technical | Post Process blending uses Volume Weight, not direct parameter changes |
| Technical | System is modular — adding a 4th weather state requires no changes to core logic |
| Performance | No noticeable frame time spikes during weather transition |

---

## Key Questions (Answer After Completing)

1. Why does the author blend "two independent Post Process Volume Weights" instead of directly modifying parameters on the same Volume? What problem does this design decision solve?
2. What is the fundamental difference between Material Parameter Collection and using Dynamic Material Instances to set parameters directly in a material? When should you use each?
3. How does your Wetness parameter affect the material? In PBR terms, what are the physical differences between a wet and a dry surface (Roughness? F0? Albedo?)?
4. If you needed to synchronize this system in a multiplayer game, how would you approach it?

---

## Relevant Nodes / API

```blueprint
// Blueprint: push MPC parameters
Set Scalar Parameter Value (MPC)
Set Vector Parameter Value (MPC)

// Post Process Volume blending
Post Process Volume → Blend Weight (0-1)
Unbound Post Process Volume ← ensures full scene coverage

// Timeline for smooth transitions
Timeline → Float Track → push to each module
```
