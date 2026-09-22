# TA Learning Philosophy

Core principles that survived three rounds of adversarial review.

---

## 1. This Is a Reference Corpus, Not a Course

**The biggest mistake**: treating this folder as a textbook to read from beginning to end.

Correct usage: encounter a problem → come here for answers → read with the context of your problem.  
One hour of reading with a real problem beats ten hours of passive reading.

---

## 2. Visual Nodes First, HLSL Code Second

Start with ShaderGraph / Material Editor. Nodes make mental models visible.  
After understanding "how data flows," then write HLSL — you'll be faster and make fewer mistakes.

**The counterintuitive truth**: People who start learning shaders through code can often write code but don't understand what it's doing.  
People who start with nodes often understand what's happening underneath much faster.

---

## 3. Profile First, Then Optimize

Don't touch any shader optimization before opening RenderDoc or Unreal Insights.  
The bottleneck you think exists is almost never the real bottleneck.

**Rule**: Optimization without profiler data is guessing, not engineering.

---

## 4. Fundamentals Are a Lookup Resource, Not Prerequisites

Linear algebra, color science, rendering pipeline basics — these are important,  
but they are **not prerequisites you must complete before you start working**.

Correct mindset: look things up as you go. Hit a tangent space problem, look up the matrix.  
Colors look wrong, look up gamma/linear. Foundational knowledge is absorbed fastest when you have context.

**The only exception**: Before you're completely stuck and can't make progress, spend 1-2 hours reading the core concepts in the fundamentals section.

---

## 5. Concepts Before Tools, Tools Are Leaf Nodes

"I learned Unity ShaderGraph" is not a skill.  
"I understand the PBR microfacet model and implemented it in ShaderGraph" is a skill.

Engines change; concepts remain. Engine knowledge is a replaceable leaf node. Concepts are the root.

---

## 6. Vulkan Is a Conceptual Language, Not a Required Programming API

Most TAs don't need to write Vulkan code.  
But the **concepts** that Vulkan exposes (render pass, barrier, descriptor set cost, memory hierarchy)  
let you read profiler output and understand why the engine makes its choices.

**The boundary**: Understanding the concepts → yes, necessary. Writing `VkCreateInstance` code → only needed in specific contexts.

---

## 7. The Core TA Competency Is Translation

Translating between artistic intent and engineering cost.  

This means:
- Being able to translate "this shader costs 0.3ms extra per frame" into terms an artist can understand
- Being able to translate "I want this material to look like wet stone" into technical requirements an engineer can work with
- Being able to say "is this effect worth this cost," not just "can this be done"

---

## 8. Portfolio-Driven Learning

Every time you learn a concept, ask yourself: "What demonstrable thing can I make with this?"  
Check each section's `project-ideas.md` — the exercises there have real production constraints (platform, ms budget, polygon limits).

Without a portfolio, knowledge is just knowledge.
