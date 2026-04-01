# ⚛️ React Prompting Techniques
### *A Complete, Intuitive Guide*

---

## 🎯 1. Crisp Definition

**React Prompting** is a structured technique where you guide an AI model to *reason step-by-step* before giving a final answer — like asking it to "show its work" instead of just blurting out an answer.

> Think: less "magic 8-ball", more "show me your working out."

---

## 🧠 2. Build Intuition First

### The Analogy: The Brilliant-but-Impulsive Student

Imagine a student who is incredibly smart but tends to **answer too fast** — they skip steps, make careless errors, and sometimes confidently give the wrong answer.

Now imagine you tell them:  
> *"Before you answer, write down every step of your reasoning."*

Suddenly — they slow down, catch their own mistakes, and give a much better answer.

**That student is your LLM. React Prompting is the teacher's instruction.**

---

### The Mental Model

```
Without React Prompting:
  Question ──────────────────────────────► Answer
                    (black box)

With React Prompting:
  Question ──► Thought 1 ──► Action 1 ──► Observation 1
                    │
                    ▼
               Thought 2 ──► Action 2 ──► Observation 2
                    │
                    ▼
               Final Answer  ✅
```

The model **reasons, acts, observes** — then repeats until it reaches the answer.

---

## 📦 3. Structured Breakdown

### What "ReAct" Actually Stands For

| Letter | Stands For  | What It Means                              |
|--------|-------------|--------------------------------------------|
| **Re** | Reasoning   | The model thinks out loud                  |
| **Act** | Acting      | The model takes an action (search, calc, code run) |

Together → **ReAct** = Reasoning + Acting interleaved.

---

### The Three Core Components

#### 🧠 Thought
- The model's internal reasoning step
- Starts with: *"I need to...", "Let me think...", "The user wants..."*
- Catches errors **before** they become answers

#### ⚡ Action
- Something the model *does* based on the thought
- Could be: search the web, run code, call an API, look up a value
- Format: `Action: [tool_name]("[input"])`

#### 👁️ Observation
- The **result** of the action
- Fed back into the model as new context
- Format: `Observation: [result]`

---

### The Full Loop

```
┌────────────────────────────────────────────┐
│                                            │
│   Thought ──► Action ──► Observation       │
│       ▲                       │            │
│       └───────────────────────┘            │
│              (repeat until done)           │
│                                            │
│              ▼ Final Answer                │
└────────────────────────────────────────────┘
```

---

## 🎨 4. Visual Thinking Elements

### Side-by-Side: Standard vs ReAct Prompting

```
STANDARD PROMPTING
──────────────────
User:   "What is the population of Tokyo multiplied by the
         GDP per capita of Japan?"

Model:  "The answer is approximately $214 trillion."
         ← No reasoning shown. Hard to verify. Often wrong.


REACT PROMPTING
───────────────
User:   Same question.

Model:
  Thought: I need two pieces of data — Tokyo's population
           and Japan's GDP per capita. Let me look them up.

  Action: search("Tokyo population 2024")
  Observation: Tokyo population ≈ 13.96 million

  Thought: Now I need Japan's GDP per capita.
  Action: search("Japan GDP per capita 2024")
  Observation: Japan GDP per capita ≈ $33,800 USD

  Thought: Now I multiply: 13,960,000 × $33,800
  Action: calculate(13960000 * 33800)
  Observation: 471,848,000,000

  Final Answer: Approximately $471.8 billion.
                ← Transparent. Verifiable. Trustworthy.
```

---

### ReAct in an Agent Architecture

```
User Query
    │
    ▼
┌─────────────┐
│  LLM Core   │◄──────────────────────────────┐
│  (Thinker)  │                               │
└──────┬──────┘                               │
       │ generates Thought + Action           │
       ▼                                      │
┌─────────────┐    ┌──────────────────────┐   │
│  Tool Layer │───►│  External World      │   │
│  Dispatcher │    │  (Search/API/DB/Code)│   │
└─────────────┘    └──────────┬───────────┘   │
                              │ Observation    │
                              └───────────────►┘
                                (fed back in)
```

---

## 🧩 5. Memory Hooks

### 🔤 Mnemonic: **T-A-O**

```
T → Thought      (plan)
A → Action       (do)
O → Observation  (learn)

TAO — like the philosophy: think, move, observe the world.
```

### 📏 Short Rules

> **Rule 1:** If the model can think out loud, it will be right more often.

> **Rule 2:** ReAct = reasoning trace + tool use. Without tools, it's just Chain-of-Thought.

> **Rule 3:** Observations are not optional — they close the feedback loop.

### 💡 "Remember This" Lines

- *"ReAct gives the model a scratchpad AND hands."* (scratchpad = Thought, hands = Action)
- *"Chain-of-Thought thinks. ReAct thinks AND does."*
- *"Every Action must produce an Observation — otherwise it's just daydreaming."*

---

## 😂 6. Light Humor

> Standard prompting asks the model to be a genius.  
> ReAct prompting asks the model to be a genius **who shows their work**.  
> The difference in output quality is roughly the same as the difference between  
> "trust me bro" and a peer-reviewed paper.

---

> The model without ReAct is like a chef who plates the food before tasting it.  
> Confident. Aesthetic. Occasionally inedible.

---

## ⚡ 7. Practical Examples

### Example 1 — Simple Question Answering with ReAct

**Prompt given to the model:**

```
Answer the following question using the ReAct format.
Use Thought, Action, and Observation steps.
Available tools: search(query), calculator(expression)

Question: Is the Eiffel Tower taller than the Empire State Building?
```

**Model output:**

```
Thought: I need the heights of both structures.

Action: search("Eiffel Tower height metres")
Observation: Eiffel Tower is 330 metres tall.

Thought: Now I need the Empire State Building's height.
Action: search("Empire State Building height metres")
Observation: Empire State Building is 443 metres tall (to roof).

Thought: 443 > 330, so Empire State Building is taller.
Final Answer: No. The Empire State Building (443m) is taller than
              the Eiffel Tower (330m).
```

---

### Example 2 — Multi-step Coding Agent

**Prompt:**

```
You are a coding assistant using ReAct format.
Tools available: run_code(code), read_file(path), write_file(path, content)

Task: Read the file 'data.csv', count the number of rows, and write
      the result to 'result.txt'.
```

**Model output:**

```
Thought: I should first read the file to understand its structure.
Action: read_file("data.csv")
Observation: [header row + 1,432 data rows returned]

Thought: I can count the rows from the observation. There are 1,432
         data rows. I'll write that to result.txt.
Action: write_file("result.txt", "Row count: 1432")
Observation: File written successfully.

Final Answer: Done. The file had 1,432 data rows.
              Result written to result.txt.
```

---

### Before vs After: ReAct Impact on Accuracy

```
Task: Multi-hop reasoning question (3 lookups needed)

WITHOUT ReAct:   ~38% accuracy   (model guesses, compounds errors)
WITH ReAct:      ~71% accuracy   (each step verified before next)

Source: Original ReAct paper — Yao et al., 2022
```

---

## 🚨 8. Common Mistakes

### ❌ Mistake 1 — Confusing ReAct with Chain-of-Thought (CoT)

| Feature          | Chain-of-Thought     | ReAct                        |
|------------------|----------------------|------------------------------|
| Reasoning        | ✅ Yes               | ✅ Yes                        |
| Tool use         | ❌ No                | ✅ Yes                        |
| External data    | ❌ No                | ✅ Yes (via Actions)           |
| Self-correction  | Limited              | Strong (via Observations)    |

> **Why it happens:** Both involve "thinking out loud" so they get conflated.  
> **Remember:** CoT = thinking only. ReAct = thinking + doing.

---

### ❌ Mistake 2 — Forgetting the Observation step

```
WRONG:
  Thought: I'll search for the answer.
  Action: search("climate change causes")
  Thought: Based on the search...   ← skipped Observation!

RIGHT:
  Thought: I'll search for the answer.
  Action: search("climate change causes")
  Observation: [actual search results here]
  Thought: Based on these results...
```

> Without Observation, the model is just pretending to use a tool.

---

### ❌ Mistake 3 — Using ReAct for simple, single-hop questions

```
OVERKILL:
  Question: "What colour is the sky?"

  Thought: I need to determine the colour of the sky...
  Action: search("sky colour")
  Observation: Blue
  Final Answer: Blue.

BETTER: Just answer directly. ReAct adds overhead.
```

> **Rule:** Use ReAct when 2+ steps or external data are needed. Not for trivia.

---

### ❌ Mistake 4 — Not defining available tools in the prompt

If you do not tell the model what tools it can use, it will invent tools — and then hallucinate their outputs.

```
Always include in your prompt:
  "Available tools: search(query), calculator(expr), ..."
```

---

### ❌ Mistake 5 — Infinite loops

The model can get stuck repeating the same Thought → Action → unhelpful Observation cycle.

Fix: Set a **maximum iteration limit** (typically 5–10 steps) and add a fallback:

```python
max_steps = 8
if steps_taken >= max_steps:
    return "I was unable to complete this in the allowed steps."
```

---

## 🧠 9. Interview-Ready Summary

### One-liner Definition

> **ReAct Prompting is a technique that interleaves the model's reasoning (Thought) with grounded actions (Action + Observation), enabling it to solve complex, multi-step problems using external tools.**

---

### 3 Key Points to Always Mention

1. **Structure:** Thought → Action → Observation loop, repeated until Final Answer
2. **Advantage over CoT:** ReAct can use real external tools; CoT only reasons in isolation
3. **Best use case:** Agentic tasks — anything requiring search, code execution, or API calls

---

### When to Use ReAct

| Use ReAct ✅                              | Skip ReAct ❌                        |
|------------------------------------------|--------------------------------------|
| Multi-hop questions needing lookups      | Single-fact questions                |
| Tasks requiring tool use (search, code)  | Creative writing                     |
| Agent pipelines (LangChain, AutoGPT)     | Simple classification                |
| Debugging or verification workflows     | Conversational chitchat              |

---

### Quick Recall Card

```
┌─────────────────────────────────────────────┐
│            ReAct — Quick Card               │
│                                             │
│  Re = Reasoning (Thought)                   │
│  Act = Acting (Action + Observation)        │
│                                             │
│  Loop: T → A → O → T → A → O → Answer      │
│                                             │
│  vs CoT: CoT thinks. ReAct thinks + does.  │
│                                             │
│  Use when: 2+ steps, tools needed           │
│  Avoid when: simple, no tools needed        │
│                                             │
│  Key risk: infinite loops → set max steps  │
└─────────────────────────────────────────────┘
```

---

*Generated using the AI Educator prompt framework · Topic: React Prompting Techniques*
