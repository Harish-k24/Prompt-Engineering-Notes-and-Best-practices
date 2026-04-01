# 🌳 Tree of Thoughts (ToT) Prompting
### *A Complete, Intuitive Guide*

---

## 🎯 1. Crisp Definition

**Tree of Thoughts (ToT)** is a prompting technique where the model explores *multiple reasoning branches simultaneously*, evaluates each one, and navigates toward the best answer — like a chess player thinking several moves ahead instead of just the next one.

> Think: not a straight road, but a decision tree where bad paths get pruned before they waste your time.

---

## 🧠 2. Build Intuition First

### The Analogy: The Chess Grandmaster

A beginner chess player thinks one move at a time:
> *"I'll move this pawn. Done."*

A grandmaster thinks in **trees**:
> *"If I move this pawn, my opponent could do A, B, or C. If they do A, I can respond with X or Y. X leads to a strong position. Y leads to losing my queen. So I'll plan for X."*

The grandmaster is not smarter because of raw intelligence — they're smarter because they **explore branches, evaluate outcomes, and backtrack** from dead ends before committing.

**ToT gives your LLM the grandmaster's thinking structure.**

---

### The Progression: From Line to Tree

```
Standard Prompting:
  Q ──────────────────────────────► A
       (one shot, no exploration)


Chain-of-Thought (CoT):
  Q ──► T1 ──► T2 ──► T3 ──► A
       (one linear path, no branching)


Self-Consistency:
  Q ──► Path 1 ──► A1 ┐
  Q ──► Path 2 ──► A2 ├──► Vote ──► Best A
  Q ──► Path 3 ──► A3 ┘
       (multiple paths, no evaluation mid-way)


Tree of Thoughts:
        ┌──► Branch 1a ──► 💀 (pruned)
  Q ──► Node 1
        └──► Branch 1b ──► Node 2 ──► Branch 2a ──► ✅ Answer
                                  └──► Branch 2b ──► 💀 (pruned)
       (branches explored + evaluated + pruned at each step)
```

The key difference: **evaluation and pruning happen mid-reasoning**, not just at the end.

---

## 📦 3. Structured Breakdown

### The Four Pillars of ToT

#### 🌿 1. Thought Decomposition
- Break the problem into **intermediate reasoning steps** (thoughts)
- Each "thought" is a coherent chunk of reasoning — not a single word, not the full answer
- Example for a math problem: each thought = one algebraic manipulation

#### 🌱 2. Thought Generation
- At each node, generate **multiple candidate next thoughts** (branches)
- Two strategies:
  - **Sample:** generate k thoughts independently in parallel
  - **Propose:** generate k thoughts in one prompt using explicit enumeration

#### ⚖️ 3. State Evaluation
- Score or rank each candidate thought
- Two methods:
  - **Value:** ask the model to score each thought (e.g. 1–10, or sure/likely/impossible)
  - **Vote:** ask the model to vote on which thought is most promising across multiple samples

#### ✂️ 4. Search Algorithm
- Navigate the tree using a search strategy:
  - **BFS (Breadth-First Search):** explore all nodes at depth d before going deeper
  - **DFS (Depth-First Search):** follow one branch deep, backtrack if it fails
- Pruning: discard branches scored below a threshold — never explore them further

---

### ToT vs The Prompting Family

| Feature                 | CoT       | Self-Consistency | ReAct          | ToT                  |
|-------------------------|-----------|------------------|----------------|----------------------|
| Reasoning paths         | 1         | N (parallel)     | 1 (with tools) | Tree (branching)     |
| Mid-path evaluation     | ❌        | ❌               | Partial        | ✅ Yes               |
| Backtracking            | ❌        | ❌               | ❌             | ✅ Yes               |
| Tool use                | ❌        | ❌               | ✅             | Optional             |
| Best for                | Linear reasoning | Multi-step math | Agentic tasks | Complex planning     |
| Cost                    | Low       | Medium           | Medium         | High                 |
| Exploration depth       | Shallow   | Wide             | Sequential     | Deep + Wide          |

---

### When ToT Shines

- ✅ Planning problems (travel, scheduling, strategy)
- ✅ Mathematical puzzle solving (Game of 24, crosswords)
- ✅ Code generation requiring architectural decisions
- ✅ Creative writing with structural constraints
- ✅ Any task where **early wrong choices** compound into total failure
- ❌ Simple Q&A or factual lookup
- ❌ Tasks with no intermediate evaluation signal
- ❌ Latency-sensitive or heavily budget-constrained applications

---

## 🎨 4. Visual Thinking Elements

### The Full ToT Architecture

```
                         PROBLEM
                            │
                    ┌───────▼───────┐
                    │   Root Node   │
                    │  (initial     │
                    │   state)      │
                    └───────┬───────┘
                            │ Generate k thoughts
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
         [Thought A]   [Thought B]   [Thought C]
         Score: 3/10   Score: 8/10   Score: 5/10
              │             │             │
           PRUNED ✂️     KEEP ✅       PRUNED ✂️
                            │
                    Generate k thoughts
                  ┌─────────┴──────────┐
                  ▼                    ▼
           [Thought B1]          [Thought B2]
           Score: 9/10           Score: 4/10
                  │                    │
               KEEP ✅             PRUNED ✂️
                  │
           [Final Answer]
                  ✅
```

---

### BFS vs DFS Search in ToT

```
BFS (Breadth-First) — explore all branches at each depth:

Depth 1:  [A]  [B]  [C]          ← evaluate all
Depth 2:  [A1] [A2] [B1] [B2]   ← expand surviving nodes
Depth 3:  [B1a][B1b]             ← continue with best

Good for: Finding the globally best shallow path
Risk:     Expensive — explores many nodes

─────────────────────────────────────────────────

DFS (Depth-First) — follow one branch deep, backtrack if stuck:

Start ──► A ──► A1 ──► A1a ──► Dead end 💀
                               Backtrack ⬆️
          A ──► A1 ──► A1b ──► Solution ✅

Good for: Deep problems where early promising paths exist
Risk:     Can waste time going deep on a bad branch
```

---

### Thought Evaluation: Value vs Vote

```
VALUE method (single model scores each thought):

  Thought B: "Let x = the number of apples..."
  Prompt: "Rate this reasoning step: sure / likely / impossible"
  Model: "likely"  → keep

─────────────────────────────────────

VOTE method (model compares multiple thoughts):

  Thought A: "Convert to fractions first..."
  Thought B: "Let x = the number of apples..."
  Thought C: "The answer is clearly 7..."

  Prompt: "Which of A, B, C is the most promising next step?"
  Model samples × 5: B, B, A, B, B  → B wins (4/5 votes)
```

---

### Game of 24 — The Classic ToT Benchmark

```
Problem: Use numbers 4, 9, 10, 13 with +, -, ×, ÷
         to make exactly 24.

CoT (single path):
  13 - 9 = 4, 4 × 4 = 16, 16 + 10 = 26 ≠ 24 ❌
  (tries one path, fails, gives up or hallucinates)

ToT (tree exploration):
  Node 1 candidates:
    A: 13 - 9 = 4  → remaining: 4, 4, 10
    B: 10 - 9 = 1  → remaining: 4, 1, 13
    C: 4 × 9 = 36  → remaining: 36, 10, 13

  Evaluate: B seems unlikely (1 is limiting), prune
            A and C look promising, keep both

  Expand A (4, 4, 10):
    A1: 4 × 4 = 16, 16 + 10 = 26 ≠ 24 → prune
    A2: 4 + 4 = 8,  8 × 10 = 80 ≠ 24  → prune
    A3: 10 - 4 = 6, 6 × 4  = 24 ✅ FOUND IT

  Solution: (10 - 4) × 4 = 24
            [using 13: 13 - 9 = 4, so: (10 - (13-9)) × (13-9) = 24]
```

ToT solved it by exploring and pruning. CoT failed by walking one bad path.

---

## 🧩 5. Memory Hooks

### 🔤 Mnemonic: **TGES**

```
T → Thought decomposition  (break it into steps)
G → Generate candidates    (multiple next thoughts)
E → Evaluate each          (score or vote)
S → Search the tree        (BFS or DFS, prune bad branches)

"The Grandmaster Evaluates Strategically"
```

---

### 📏 Short Rules

> **Rule 1:** ToT = CoT + branching + evaluation + backtracking.  
> Remove any one of these and you have a weaker technique.

> **Rule 2:** Evaluation is what separates ToT from Self-Consistency.  
> SC votes at the end. ToT evaluates and prunes *during* reasoning.

> **Rule 3:** The thought granularity matters.  
> Too fine (one word per thought) → massive tree, too expensive.  
> Too coarse (whole solution per thought) → no branching benefit.  
> Sweet spot: one meaningful reasoning step per thought.

> **Rule 4:** BFS for broad exploration. DFS for deep commitment.  
> When in doubt, BFS — it finds globally better solutions.

---

### 💡 "Remember This" Lines

- *"CoT walks. ToT explores."*
- *"The power of ToT is not in thinking more — it's in knowing when to stop thinking down a bad path."*
- *"Backtracking is not failure. It is the algorithm working correctly."*
- *"ToT turns the LLM from a writer into an editor — it can reject its own drafts."*

---

## 😂 6. Light Humor

> Chain-of-Thought is a tourist with a map who follows one route and hopes for the best.  
> Tree of Thoughts is a tourist with a map who says: *"Let me check three routes, evaluate traffic on each, abandon the one with roadworks, and take the scenic path that still arrives on time."*  
> One arrives at a closed restaurant. The other has a reservation.

---

> CoT is autocomplete with extra steps.  
> ToT is autocomplete that stops mid-sentence, says *"actually no, let me try that differently,"*  
> and rewrites three times before committing.  
> Writers call this editing. Researchers call it a breakthrough.

---

> The paper introducing ToT benchmarked it on the Game of 24.  
> Standard prompting solved it 4% of the time.  
> ToT solved it 74% of the time.  
> The other 26% were probably just unlucky tree configurations.  
> (Or the model just really hates arithmetic. Relatable.)

---

## ⚡ 7. Practical Examples

### Example 1 — Creative Writing with Structural Constraint

**Task:** Write a coherent short story that must include: a lighthouse, a secret, and a reunion.

**CoT approach:**
```
Single path:
  "Maria returned to the lighthouse where she grew up.
   She found a secret letter. They reunited."
   → Technically correct but flat. No exploration of structure.
```

**ToT approach:**
```
Thought Generation (story structure candidates):
  Branch A: Secret is revealed before reunion → tension builds early
  Branch B: Reunion happens first, secret discovered after → twist ending
  Branch C: Secret drives the reunion → secret IS the reason they meet

Evaluate:
  A: Score 6/10 — predictable arc
  B: Score 8/10 — surprise factor, reader engagement
  C: Score 9/10 — tightly integrated, most satisfying

Select Branch C. Expand:
  C1: Secret = hidden letter proving they are siblings
  C2: Secret = lighthouse keeper knew where missing person was
  C3: Secret = the lighthouse itself is a signal only they understand

Evaluate C thoughts:
  C2: Score 9/10 — emotionally resonant, plot-complete

Write from C → C2:
  "For 20 years, Elara had sent ships past the old lighthouse —
   not for navigation, but hoping. The keeper had known all along
   where her brother was. The day he finally returned, the light
   blinked twice. Their old signal. He'd been watching too."
```

Structured exploration produced a richer, more intentional story.

---

### Example 2 — Multi-Step Planning Problem

**Task:** Plan a 3-day trip to a new city with a $500 budget, including flights already booked.

```
Root: $500 budget, 3 days, flights done → $320 remaining

Depth 1 — Accommodation strategy:
  Branch A: Hotel ($80/night × 3 = $240) → $80 left for food+activities
  Branch B: Hostel ($25/night × 3 = $75) → $245 left
  Branch C: Airbnb ($55/night × 3 = $165) → $155 left

Evaluate:
  A: $80 for 3 days food is very tight → Score 4/10, prune
  B: $245 is comfortable, flexibility → Score 9/10, keep
  C: Middle ground, less social → Score 7/10, keep B and C

Expand Branch B (Hostel, $245 remaining):
  Day budget: $245 / 3 ≈ $81/day

  Sub-branch B1: Museums + street food → culturally rich, low cost ✅
  Sub-branch B2: Tours + restaurants → higher cost, less flexibility ❌
  Sub-branch B3: Free walking tours + markets → maximises days ✅

Evaluate: B1 and B3 both score high → merge into final plan

Final Plan:
  Day 1: Free walking tour + market lunch + museum (free entry day)
  Day 2: Paid museum ($15) + street food lunch + local dinner ($30)
  Day 3: Day trip by transit ($12) + packed lunch + souvenir budget ($20)
  Total spent: ~$237 of $245 ✅
```

---

### Benchmark: Game of 24 (Original ToT Paper)

```
Method                    Success Rate
────────────────────────────────────────
Standard Prompting        4%
Chain-of-Thought          11%
Self-Consistency (k=100)  49%
Tree of Thoughts (BFS)    74%   ✅

Source: Yao et al., 2023 — "Tree of Thoughts: Deliberate Problem Solving with LLMs"
```

---

## 🚨 8. Common Mistakes

### ❌ Mistake 1 — Making Thoughts Too Granular

```
WRONG: Each "thought" = one word or one number
Result: Tree explodes in size, thousands of nodes, unusable cost

RIGHT: Each "thought" = one meaningful reasoning step
       (e.g. one equation, one structural decision, one sub-goal)
```

---

### ❌ Mistake 2 — Skipping the Evaluation Step

```
WRONG: Generate 3 branches → pick one randomly → continue
Result: You get branching without intelligence — just expensive CoT

RIGHT: Generate 3 branches → score each → prune low scorers → continue
       The evaluation IS the value of ToT. Without it, it's just noise.
```

---

### ❌ Mistake 3 — Using ToT for Simple Problems

```
WRONG use: "What is the capital of France?" → ToT with 5 branches
Result: Massive overkill. 10× cost. Same answer: Paris.

RIGHT mindset: ToT earns its cost only when:
  - Multiple valid intermediate paths exist
  - Early decisions significantly affect final quality
  - Backtracking is genuinely needed
```

---

### ❌ Mistake 4 — Confusing ToT with Self-Consistency

| Dimension         | Self-Consistency           | Tree of Thoughts              |
|-------------------|----------------------------|-------------------------------|
| When evaluated    | End only (majority vote)   | Mid-reasoning (each node)     |
| Backtracking      | ❌ No                      | ✅ Yes                        |
| Path relationship | Independent parallel paths | Connected tree structure      |
| Pruning           | ❌ No                      | ✅ Yes                        |

> The trap: both involve "multiple paths." The key differentiator is  
> **mid-path evaluation and backtracking** — only ToT has these.

---

### ❌ Mistake 5 — Ignoring Search Strategy Choice

```
Using DFS on a wide problem:
  → Goes deep on a mediocre branch
  → Misses a better branch at depth 2
  → Wrong answer, wasted compute

Using BFS on a very deep problem:
  → Explores every node at each level
  → Exponential growth, never reaches the answer
  → Times out or hits token limits

Match the strategy to the problem:
  Wide, shallow problems  → BFS
  Deep, narrow problems   → DFS
  Unknown depth           → BFS with depth limit
```

---

### ❌ Mistake 6 — Not Setting a Pruning Threshold

```
WRONG: Keep all branches at every level
Result: Tree grows exponentially, cost explodes

RIGHT: Set a minimum score threshold (e.g. "keep only thoughts
       rated 'likely' or above") and a max branching factor (k=2–5)

Rule of thumb: k=3 branches × depth=3 levels = 27 nodes maximum
               That is usually sufficient for most tasks.
```

---

## 🧠 9.  Summary

### One-liner Definition

> **Tree of Thoughts is a prompting framework that structures LLM reasoning as a tree where multiple candidate thoughts are generated, evaluated, and selectively expanded at each step — enabling deliberate exploration, mid-path evaluation, and backtracking toward complex problem solutions.**

---

### 3 Key Points to Always Mention

1. **Structure:** Problem broken into thought steps → multiple branches generated → each evaluated → low-scoring branches pruned → tree navigated via BFS or DFS
2. **Key advantage over CoT/SC:** Mid-reasoning evaluation and backtracking — the model can abandon a bad path before it reaches a wrong answer
3. **Cost trade-off:** ToT is significantly more expensive than CoT or SC — justified only for complex, multi-step tasks where reasoning path quality matters

---

### When to Use

| Use ToT ✅                               | Skip ToT ❌                          |
|------------------------------------------|--------------------------------------|
| Complex planning and scheduling          | Factual lookup or simple Q&A         |
| Mathematical puzzles (Game of 24, etc.)  | Creative tasks without clear scoring |
| Code architecture decisions              | Real-time / low-latency applications |
| Problems where early mistakes cascade   | Budget-sensitive inference            |
| Tasks needing deliberate exploration     | Tasks solved well by CoT already     |

---

### The Prompting Family — Where ToT Fits

```
Complexity & Power
      ▲
      │                              ╔══════════╗
      │                              ║   ToT    ║  ← explore + evaluate + backtrack
      │                    ╔═════════╩══════════╝
      │                    ║  Self-Consistency   ║  ← multiple paths + vote at end
      │          ╔═════════╩═════════════════════╝
      │          ║         ReAct                 ║  ← reason + act with tools
      │  ╔═══════╩═══════════════════════════════╝
      │  ║       Chain-of-Thought                ║  ← one linear reasoning path
      │  ╠═══════════════════════════════════════╝
      │  ║       Standard Prompting              ║  ← one shot answer
      │  ╚═══════════════════════════════════════╝
      └──────────────────────────────────────────► Cost & Complexity
```

---

### Quick Recall Card

```
┌──────────────────────────────────────────────────┐
│          Tree of Thoughts — Quick Card           │
│                                                  │
│  Mnemonic: TGES                                  │
│    T → Thought decomposition                     │
│    G → Generate k candidates per node            │
│    E → Evaluate each (value or vote)             │
│    S → Search (BFS or DFS) + prune               │
│                                                  │
│  Key rule: Evaluate MID-path, not just at end    │
│  Key power: Backtracking from dead ends          │
│  Key cost:  k branches × depth levels = nodes   │
│                                                  │
│  vs CoT: CoT walks one path. ToT explores many. │
│  vs SC:  SC votes at end. ToT prunes mid-way.   │
│                                                  │
│  Use when: planning, puzzles, cascading errors   │
│  Skip when: simple tasks, real-time, low budget  │
└──────────────────────────────────────────────────┘
```

---

*Generated using the AI Educator prompt framework · Topic: Tree of Thoughts (ToT) Prompting*
