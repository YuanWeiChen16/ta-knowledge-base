# Lab 10 — GPU Foliage Interaction System (Ark: Survival Ascended)

**Difficulty**: ⭐⭐⭐⭐ Senior+  
**Estimated Time**: 3–5 days  
**Engine Version**: UE5.1+

---

## Original Source

- **Source**: GDC 2024 Technical Artist Summit
- **Talk Title**: *"Technical Artist Summit: GPU-Based Foliage-Interaction for 'Ark: Survival Ascended'"*
- **Speaker**: Studio Wildcard Technical Art Team
- **GDC Vault**: https://www.gdcvault.com/play/1034799/Technical-Artist-Summit-GPU-Based
- **Alternate (free version)**: https://www.gdcvault.com/play/1034554/Technical-Artist-Summit-GPU-Based

**Key Takeaways**:
- Architecture of the custom Force-Injection Compute Shader
- Multi-layer design using multiple Volumetric Render Targets
- How Niagara forces are injected into Render Targets
- Performance considerations for Nanite + WPO foliage
- SpeedTree 9 + Houdini data encoding workflow

---

## Your Task

**Build a GPU-driven foliage interaction system: when the player walks through grass, the grass bends in real time to avoid them, then springs back after they leave.**

### System Architecture (Simplified)

```
Player position → Blueprint passes world coordinates
    ↓
Render Target (records force field)
    ↓
Grass material reads RT → World Position Offset bends vertices
    ↓
Elastic recovery (decays over time)
```

### Required Features

1. **Render Target Force Field Recording**
   - Use a Render Target to record "forces" in the scene (player position = force source)
   - RT format: R16G16 (stores XY directional force)
   - Each frame, Blueprint "paints" the player's position onto the RT

2. **Material Reads Force Field**
   - Grass material samples the corresponding pixel from the RT
   - Uses the force magnitude and direction to bend grass vertices via WPO
   - Bend amount must account for blade height (base is fixed, top has maximum displacement)

3. **Elastic Recovery**
   - Forces in the RT decay over time (grass returns upright after the player leaves)
   - Implemented with Render Target + Custom Resolve, or a per-frame decay pass in Blueprint

4. **Multi-Object Support**
   - NPCs and large VFX can also inject forces, not just the player
   - Support at least 3 simultaneous force sources

---

## Render Target Workflow

```
Blueprint setup:
1. Create Canvas Render Target 2D (resolution 512×512, covering 50m×50m scene area)
2. Each frame in Event Tick:
   a. Calculate the player's UV coordinate on the RT:
      UV = (PlayerWorldPos.XY - SceneOrigin) / SceneSize
   b. Use Draw Material to Render Target to paint an "impact circle" onto the RT
   c. Run another Material Pass over the full RT for decay (multiply by 0.95 each frame)

Material reading:
3. Grass material:
   a. Calculate the current vertex's world UV (same formula as above)
   b. Texture Sample from RT
   c. Decode RG channels as XY directional force
   d. Force × grass height ratio → WPO
```

---

## Grass Material WPO Logic

```hlsl
// Read force field
float2 sceneUV = (WorldPosition.xy - SceneOrigin) / SceneSize;
float2 force = DecodeForce(SampleRT(ForceRT, sceneUV));

// Height mask (only the upper portion of the grass bends)
float heightMask = saturate((VertexLocalZ - 0.1) / 0.9);

// Convert to WPO
float3 wpo = float3(force.x, force.y, 0) * heightMask * BendIntensity;

// Add wind animation (additive, does not replace)
wpo += WindAnimation(WorldPosition, Time);

return wpo;
```

---

## Acceptance Criteria

| Item | Standard |
|------|----------|
| Visual | Player walks through — grass visibly bends out of the way |
| Visual | After player leaves, grass returns upright within 1–2 seconds |
| Visual | Bend direction is correct (opposite to the player's movement direction) |
| Technical | Base vertices don't move; top vertices have maximum displacement |
| Technical | All 3 simultaneous force sources produce visible effects |
| Performance | Entire system (RT update + grass rendering) < 2ms |

---

## Key Questions (Answer After Completing the Lab)

1. Ark uses Volumetric Render Targets (3D); you're using a 2D Render Target. What are the advantages of the 3D version? In what situations is 2D insufficient (hint: multi-layer terrain, staircases)?
2. The RT decay uses "multiply by 0.95 per frame" — recovery speed will differ between 30fps and 60fps. How do you make recovery speed frame-rate independent?
3. Nanite's WPO support only arrived in UE5.2+, and it carries a performance cost. How would you optimize this system for a large-scale forest scene (10,000 Nanite grass instances)?
4. When a player sprints vs walks slowly, how should the grass bending effect differ? Can your RT design support "velocity-awareness"?

---

## Related Resources

- 🎥 [GDC 2024 Talk (free version)](https://www.gdcvault.com/play/1034554/Technical-Artist-Summit-GPU-Based)
- 📖 [UE5 Render Target Documentation](https://docs.unrealengine.com/5.4/en-US/render-targets-in-unreal-engine/)
- 🎥 [Minions Art — Interactive Grass RT Tutorial](https://www.youtube.com/@MinionsArt) (similar concept, Unity version but logic is universal)
