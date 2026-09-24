# 06 — TA Role & Mindset

**Stability Tag**: `[STABLE]`

---

## The TA's Position in a Production Team

```
Art Department ←→ [Technical Artist] ←→ Engineering Department
              translate intent      translate cost
```

A TA is a bridge, not a subordinate to either side.

---

## The Three Core Work Modes of a TA

### 1. Firefighter
An artist hits a wall → TA finds a technical solution or alternative.  
**Mindset**: Understand the artistic intent first, then find a technical path. Don't immediately say "can't do it."

### 2. Toolsmith
Identify what artists are doing repeatedly → write tools to automate it.  
**Ask**: "How often does this happen, how long does each run take, and how error-prone is the current process?" Weigh the time saved against the cost of building and maintaining automation.

### 3. System Designer
Define material conventions, naming conventions, LOD specs, performance budgets in pre-production.  
**Greatest impact but least visible**. One hour of convention design early on can save 100 hours of revisions later.

---

## The Mindset Gap Between Junior and Senior TAs

| | Junior TA | Senior TA |
|--|-----------|-----------|
| Encounters a problem | Looks for who else has had the same problem | Reasons from first principles |
| Looks at a shader | How is this shader made | Where this shader sits in the entire pipeline |
| Work scope | Does what's assigned | Anticipates upcoming problems, proactively solves them |
| Shader flickering | Add bias (treat the symptom) | Find the precision root cause (treat the disease) |
| Receives a request | Implements as-is | Asks "why" first, proposes a better solution |

---

## Communication Patterns (TA's Most Underrated Skill)

### Communicating with Artists
- **Ask about intent first**: "What feeling do you want this material to convey?"
- **Communicate measurable evidence**: State the scene, device, resolution, and measurement method; for example, compare how transparent screen coverage affects GPU time on the target device
- **Give alternatives**: Explain visual differences, measured cost, and tradeoffs between the direct approach and alternatives

### Communicating with Engineers
- **Use numbers**: "This pass costs 0.8ms more" is more useful than "it's a bit slower"
- **List excluded options**: State what you've already tried and why it didn't work
- **Provide a minimal reproducible example**: The smallest scene that reproduces the problem

---

## Learning Resources

- 🎥 [GDC Technical Artist Talks](https://gdcvault.com/) — search "Technical Artist"
- 📖 [Tech Art Hub](https://www.techarthub.com/) — TA community resources
- 🌐 [Tech Art Aid Discord](https://discord.gg/techartaid) — active TA community
- 📖 [80.lv TA Articles](https://80.lv/) — industry TA case studies
