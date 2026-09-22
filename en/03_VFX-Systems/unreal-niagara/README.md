# Unreal Niagara VFX System

**Stability Tag**: `[ENGINE-VERSIONED: UE5.4]`

---

## Niagara Architecture

```
Niagara System
  └── Emitter 1
  │     └── Emitter Properties
  │     └── Particle Spawn
  │     └── Particle Update
  │     └── Particle Render (Sprite / Mesh / Ribbon / Light)
  └── Emitter 2
  └── System Update (logic spanning multiple Emitters)
```

---

## Niagara vs Cascade (Legacy System)

| | Niagara | Cascade |
|--|---------|---------|
| Architecture | Data-driven, node graph | Module stack |
| Customizability | Very high (can write HLSL) | Limited |
| GPU particles | ✅ Full support | Partial support |
| Learning curve | High | Low |
| New project recommendation | ✅ Use | ❌ Migrate to Niagara |

---

## Common Niagara Modules

```
Spawn:
  Spawn Rate          ← spawn rate per second
  Spawn Burst Timed   ← burst spawn at a specific time
  Spawn Per Unit      ← spawn based on distance traveled (trail effects)

Initialize Particle:
  Initialize Particle  ← set initial position, velocity, color, size, lifetime

Update:
  Gravity Force       ← gravity
  Drag               ← drag
  Curl Noise Force    ← turbulence
  Update Age          ← update particle age (required)
  Solve Forces and Velocity ← integrate velocity (required)

Render:
  Sprite Renderer     ← quad billboard
  Mesh Renderer       ← 3D mesh particles
  Ribbon Renderer     ← connected particles (trails, lightning)
  Light Renderer      ← particle light emission
```

---

## Niagara Parameter Binding (Blueprint/C++ Control)

```cpp
// C++ control of Niagara parameters
#include "NiagaraFunctionLibrary.h"
#include "NiagaraComponent.h"

UNiagaraComponent* NiagaraComp = UNiagaraFunctionLibrary::SpawnSystemAtLocation(
    this, NiagaraSystem, SpawnLocation
);

NiagaraComp->SetFloatParameter(FName("Intensity"), 2.5f);
NiagaraComp->SetVectorParameter(FName("EmitDirection"), FVector(0, 0, 1));
NiagaraComp->SetColorParameter(FName("ParticleColor"), FLinearColor::Red);
```

---

## GPU Simulation vs CPU Simulation

| | GPU Simulation | CPU Simulation |
|--|----------------|----------------|
| Particle count | Hundreds of thousands to millions | Thousands to tens of thousands |
| Collision | Limited (depth collision) | Full physics collision |
| Read particle position | Difficult (needs GPU readback) | Easy |
| Best for | Pure visual effects (smoke, dust) | Needs game logic interaction |

**Setting**: Emitter Properties → Sim Target → GPU Compute Sim

---

## Niagara Fluids (UE5.1+)

Fluid simulation: Grid3D (3D fluid) and Grid2D (2D fluid/water surface).

High performance cost — suitable for Hero VFX or Cutscenes. Not suitable for real-time multi-instance use.

---

## Learning Resources

- 📖 [Unreal Niagara Documentation](https://docs.unrealengine.com/5.4/en-US/creating-visual-effects-in-niagara-for-unreal-engine/)
- 🎥 [Niagara Beginner Tutorial (UE5)](https://www.youtube.com/results?search_query=unreal+niagara+beginner+tutorial+UE5)
- 🎥 [Klemen Lozar — Niagara Advanced](https://www.youtube.com/@KlemenLozar)
- 📖 [GDC: Niagara VFX System Deep Dive](https://gdcvault.com/) — search "Niagara"
