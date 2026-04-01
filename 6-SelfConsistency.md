 # 🔁 Self-Consistency Prompting
### *A Complete, Intuitive, Interview-Ready Guide*

---

## 🎯 1. Crisp Definition

**Self-Consistency Prompting** is a technique where you ask the model the *same question multiple times*, let it reason independently each time, then pick the most common answer — because if multiple reasoning paths agree, they're probably right.

> Think: democratic voting, but for AI reasoning chains.

---

## 🧠 2. Build Intuition First

### The Analogy: The Expert Panel

Imagine you have a tricky legal question. Instead of asking one lawyer, you ask **five different lawyers** to think it through independently — no conferring, no peeking at each other's notes.

After they all respond:
- 4 out of 5 say "liable"
- 1 says "not liable"

You go with **liable** — because consensus across independent thinkers is more reliable than any single opinion.

**Self-Consistency Prompting does exactly this — except your "lawyers" are the same model, sampled multiple times with some randomness (temperature > 0).**

---

### Why Does Randomness Help?

LLMs are not deterministic at temperature > 0. Each run explores a **slightly different reasoning path** through the problem. Some paths are better than others. Majority voting finds the most reliable destination even when individual paths vary.

```
Same question, run 5 times:

Run 1: Path A ──► Answer: 42  ✅
Run 2: Path B ──► Answer: 42  ✅
Run 3: Path C ──► Answer: 40  ❌  (took a wrong turn)
Run 4: Path D ──► Answer: 42  ✅
Run 5: Path E ──► Answer: 42  ✅

Majority vote → Answer: 42  ✅✅✅✅
```

The wrong path in Run 3 gets outvoted. The noise cancels out.

---

## 📦 3. Structured Breakdown

### How It Works — 3 Phases

#### Phase 1: Sample
- Run the same prompt **N times** (typically 5–20)
- Use **temperature > 0** (usually 0.5–0.8) to introduce variation
- Each run produces an independent Chain-of-Thought reasoning trace + final answer

#### Phase 2: Aggregate
- Collect all N final answers
- Ignore the reasoning traces for voting purposes
- Group identical (or semantically equivalent) answers together

#### Phase 3: Select
- Pick the answer with the **highest frequency** (majority vote)
- In case of a tie: pick the answer supported by the highest-quality reasoning traces

---

### Self-Consistency vs Chain-of-Thought

| Feature                  | Chain-of-Thought (CoT)    | Self-Consistency           |
|--------------------------|---------------------------|----------------------------|
| Runs                     | 1                         | N (multiple)               |
| Temperature              | 0 (greedy/deterministic)  | > 0 (stochastic)           |
| Output                   | Single reasoning + answer | Multiple → majority vote   |
| Accuracy boost           | Moderate                  | Strong (especially on math)|
| Cost                     | Low (1 API call)          | Higher (N × API calls)     |
| Best for                 | Simple reasoning           | Complex, multi-step tasks  |

---

### When Self-Consistency Shines

- ✅ Arithmetic and math word problems
- ✅ Multi-step logical reasoning
- ✅ Commonsense reasoning benchmarks
- ✅ Any task where a wrong intermediate step can derail the answer
- ❌ Open-ended creative tasks (no single "right" answer to vote on)
- ❌ Simple factual lookups (overkill)
- ❌ Time-sensitive or budget-sensitive applications

---

## 🎨 4. Visual Thinking Elements

### The Full Flow

```
                    ┌─────────────┐
                    │   Question  │
                    └──────┬──────┘
                           │  (same prompt, N times)
          ┌────────────────┼─────────────────┐
          │                │                 │
          ▼                ▼                 ▼
   ┌────────────┐   ┌────────────┐   ┌────────────┐
   │  Run 1     │   │  Run 2     │   │  Run N     │
   │ Thought... │   │ Thought... │   │ Thought... │
   │ Answer: A  │   │ Answer: A  │   │ Answer: B  │
   └─────┬──────┘   └─────┬──────┘   └─────┬──────┘
         │                │                 │
         └────────────────▼─────────────────┘
                   ┌──────────────┐
                   │  Aggregator  │
                   │  A: 4 votes  │◄── majority
                   │  B: 1 vote   │
                   └──────┬───────┘
                          │
                          ▼
                   ┌──────────────┐
                   │ Final Answer │
                   │      A       │
                   └──────────────┘
```

---

### Reasoning Path Diversity (Why It Matters)

```
Problem: "If a train travels 60km/h for 2.5 hours, how far does it go?"

Run 1: 60 × 2.5 = 60 × 2 + 60 × 0.5 = 120 + 30 = 150km  ✅
Run 2: Distance = speed × time = 60 × 2.5 = 150km         ✅
Run 3: 60 per hour × 2 hours = 120, + half hour = 30 → 150km ✅
Run 4: 60 × 2.5... hmm, = 60 × 25 / 10 = 1500 / 10 = 150km ✅
Run 5: 2.5 hours = 150 minutes... 60km/h = 1km/min ❌ → 150km ✅ (lucky!)

All agree: 150km. Confidence: very high.
```

Even Run 5 took a confused path but got lucky. The **vote reinforces the right answer**, not the right reasoning.

---

### Confidence Scoring Visualised

```
Answer distribution across 10 runs:

  42  ████████░░  8 votes   → WINNER ✅
  40  ██░░░░░░░░  2 votes
  44  ░░░░░░░░░░  0 votes

Confidence = 8/10 = 80%
```

High agreement = high confidence. Use this as a signal for when to trust the output.

---

## 🧩 5. Memory Hooks

### 🔤 Mnemonic: **SAM**

```
S → Sample   (run it multiple times)
A → Aggregate (collect the answers)
M → Majority  (pick the most common one)

SAM always votes wisely.
```

---

### 📏 Short Rules

> **Rule 1:** One path can stumble. Many paths rarely stumble on the same spot.

> **Rule 2:** Self-Consistency needs temperature > 0. Greedy decoding gives the same answer every time — you'd just be paying N times for one result.

> **Rule 3:** Vote on the **final answer**, not the reasoning. Paths can differ wildly and still reach the same correct destination.

> **Rule 4:** More samples = more accuracy, but diminishing returns after ~20 samples.

---

### 💡 "Remember This" Lines

- *"Self-Consistency is CoT with democracy."*
- *"One model, many opinions, one vote."*
- *"The answer that survives multiple paths of attack is the answer worth trusting."*
- *"CoT is a solo climber. Self-Consistency is a team that votes on the summit route."*

---

## 😂 6. Light Humor

> Standard prompting is asking one friend for directions.  
> Chain-of-Thought is asking that friend to *think out loud* before answering.  
> Self-Consistency is asking five friends independently, then going with wherever four of them agree.  
> The one contrarian friend is still wrong. They always are.

---

> Asking the model once at temperature 0 is like consulting one very confident doctor.  
> Self-Consistency is getting a second, third, fourth, and fifth opinion.  
> The medical community calls this "best practice."  
> The AI community calls it "prompting technique."  
> Same energy.

---

## ⚡ 7. Practical Examples

### Example 1 — Math Word Problem

**Prompt (sent N=5 times):**
```
Q: A bookstore sells novels for $12 and textbooks for $28.
   Maya buys 3 novels and 2 textbooks. How much does she spend?
   Think step by step.
```

**Outputs:**

```
Run 1: 3×12 = 36, 2×28 = 56, total = 92       → Answer: $92 ✅
Run 2: novels cost 12 each × 3 = 36,
       textbooks 28 × 2 = 56, 36+56 = 92       → Answer: $92 ✅
Run 3: 12+12+12 = 36, 28+28 = 56, 92           → Answer: $92 ✅
Run 4: 3 novels = 36, 2 textbooks = 58 (error!) → Answer: $94 ❌
Run 5: total = 3(12) + 2(28) = 36 + 56 = 92    → Answer: $92 ✅

Vote: $92 → 4 votes ✅  |  $94 → 1 vote ❌

Final Answer: $92
```

Run 4 made an arithmetic slip. Majority vote catches and overrules it cleanly.

---

### Example 2 — Logical Reasoning

**Prompt (sent N=5 times):**
```
All mammals are warm-blooded.
Dolphins are mammals.
Snakes are not mammals.
Q: Are dolphins warm-blooded? Think step by step.
```

```
Run 1: Dolphins are mammals → mammals are warm-blooded → Yes  ✅
Run 2: Dolphins = mammal, mammals = warm-blooded → Yes         ✅
Run 3: Yes, by syllogism                                       ✅
Run 4: Yes                                                     ✅
Run 5: Yes                                                     ✅

Vote: Yes → 5/5. Confidence: 100%.
```

---

### Before vs After: Accuracy on GSM8K Math Benchmark

```
Technique                     Accuracy
─────────────────────────────────────
Standard prompting            ~17%
Chain-of-Thought (CoT)        ~56%
Self-Consistency (N=40)       ~74%     ← significant jump

Source: Wang et al., 2022 — "Self-Consistency Improves CoT Reasoning"
```

The jump from CoT to Self-Consistency is not from a smarter model — it is from smarter **aggregation**.

---

## 🚨 8. Common Mistakes

### ❌ Mistake 1 — Using Temperature = 0

```
WRONG:  temperature=0, N=10
Result: Same answer 10 times. You paid 10x for nothing.

RIGHT:  temperature=0.6–0.8, N=5–20
Result: Diverse reasoning paths, meaningful vote.
```

> Why it happens: People default to temperature=0 for "deterministic" outputs  
> and forget that Self-Consistency *needs* variance to work.

---

### ❌ Mistake 2 — Voting on Reasoning, Not Answers

```
WRONG:
  "Run 3 had the most detailed reasoning, so I'll use Run 3's answer."

RIGHT:
  Collect final answers from all runs → count → take majority.
  The reasoning traces are scaffolding, not the vote.
```

> Detailed reasoning ≠ correct reasoning. A longer wrong path is still wrong.

---

### ❌ Mistake 3 — Using It for Open-Ended Tasks

```
WRONG use case:
  "Write me a poem about autumn." × 10 → majority vote

Problem: There is no single "correct" poem. Voting produces
         incoherent averaged output or just picks one arbitrarily.

RIGHT use case: Tasks with a verifiable, discrete correct answer.
```

---

### ❌ Mistake 4 — Too Few Samples

```
N=2 example:
  Run 1 → Answer: A
  Run 2 → Answer: B
  Tie. No winner. Useless.

Use minimum N=5. N=10–20 for high-stakes tasks.
Accuracy gains plateau around N=20–40.
```

---

### ❌ Mistake 5 — Ignoring Cost

Each sample = one full API call. N=20 samples = 20× cost and latency.

```
Budget-aware approach:
  - Use N=5 for most tasks
  - Use N=10–20 only for high-stakes, high-complexity reasoning
  - If N=5 gives >80% agreement → stop early, no need for more
```

---

### ❌ Mistake 6 — Confusing with Ensemble Methods

| Feature       | Self-Consistency         | Model Ensembling          |
|---------------|--------------------------|---------------------------|
| Models used   | 1 model, N runs          | N different models        |
| Cost driver   | N × inference calls      | N × model deployments     |
| Variation source | Temperature/sampling  | Different architectures   |
| Easier to use | ✅ Yes                   | ❌ Much harder             |

> Self-Consistency is the poor man's ensemble — and often just as good.

---

## 🧠 9. Interview-Ready Summary

### One-liner Definition

> **Self-Consistency Prompting samples the same Chain-of-Thought prompt multiple times with non-zero temperature and selects the final answer by majority vote across all runs, exploiting the fact that correct reasoning paths converge while errors diverge.**

---

### 3 Key Points to Always Mention

1. **Mechanism:** Run same prompt N times → collect answers → majority vote wins
2. **Why it works:** Correct paths tend to converge on the right answer; errors are random and cancel each other out
3. **Key requirement:** Temperature must be > 0 to produce diverse reasoning paths — otherwise all runs are identical

---

### When to Use

| Use Self-Consistency ✅              | Skip Self-Consistency ❌           |
|--------------------------------------|------------------------------------|
| Math and arithmetic problems         | Creative / open-ended generation   |
| Multi-step logical reasoning         | Simple factual recall              |
| Commonsense reasoning tasks          | Real-time / low-latency required   |
| High-stakes decisions needing trust  | Severely budget-constrained tasks  |

---

### Quick Recall Card

```
┌─────────────────────────────────────────────────┐
│         Self-Consistency — Quick Card           │
│                                                 │
│  Mnemonic: SAM                                  │
│    S → Sample  (N runs, temp > 0)               │
│    A → Aggregate (collect final answers)        │
│    M → Majority (vote, highest count wins)      │
│                                                 │
│  Key rule: Vote on answers, not reasoning       │
│  Key risk: temp=0 → all identical, waste of $   │
│  Sweet spot: N = 5 to 20 samples                │
│                                                 │
│  vs CoT: CoT = 1 path. SC = N paths + vote.    │
│  Use when: discrete correct answer exists       │
└─────────────────────────────────────────────────┘
```

---

*Generated using the AI Educator prompt framework · Topic: Self-Consistency Prompting*
