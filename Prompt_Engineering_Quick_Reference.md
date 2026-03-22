# ⚡ Prompt Engineering Quick Reference

## Decision Tree (What to Use When?)

```
START: Is the task simple?
  ├─ YES → Use ZERO-SHOT
  │         (Fast, cheap, good baseline)
  │
  └─ NO → Do you have good examples?
          ├─ YES → Use FEW-SHOT
          │         (Better accuracy)
          │
          └─ NO → Use ZERO-SHOT CoT
                   (Magic phrase: "let's think step by step")

Does accuracy matter critically?
  ├─ YES → Add SELF-CONSISTENCY
  │         (Vote across 3-5 runs)
  │
  └─ NO → Continue with current choice

Is this a multi-step workflow?
  ├─ YES → Use PROMPT CHAINING
  │         (Break into sequential steps)
  │
  └─ NO → Single prompt is fine

Does the task have multiple solution paths?
  ├─ YES → Use TREE OF THOUGHTS
  │         (Explore branches, select best)
  │
  └─ NO → Use CoT or chaining

Still not good enough?
  ├─ YES → Fine-tune or use RAG
  │
  └─ DONE: Deploy and monitor
```

\---

## Technique Comparison Matrix

|Technique|Speed|Cost|Accuracy|Use Case|Setup Time|
|-|-|-|-|-|-|
|Zero-shot|⚡⚡⚡|$|⭐⭐|Baseline, fast|5 min|
|Few-shot|⚡⚡|$$|⭐⭐⭐|Domain tasks|30 min|
|CoT|⚡⚡|$$|⭐⭐⭐|Reasoning|15 min|
|Self-Consistency|⚡|$$$|⭐⭐⭐⭐|Critical tasks|1 hour|
|Prompt Chaining|⚡⚡|$$|⭐⭐⭐⭐|Workflows|2 hours|
|Tree of Thoughts|⚡|$$$$|⭐⭐⭐⭐⭐|Planning|4 hours|
|RAG|⚡⚡|$$|⭐⭐⭐⭐|Grounding|1 day|
|Fine-tune|🐢|$$$$|⭐⭐⭐⭐⭐|Optimal|1 week|

\---

## 60-Second Explanations

### Zero-Shot

"Instruction only, no examples. Model uses pretrained knowledge. Fast and cheap but less accurate. Best for known tasks and baselines."

### Few-Shot

"Provide 2-5 examples showing what you want. Model learns from pattern. More accurate, costs more tokens. Great for domain-specific tasks."

### Chain-of-Thought

"Make model show reasoning before answering. Catches errors, makes output auditable. Better accuracy, more tokens. Good for complex logic."

### Self-Consistency

"Run same prompt multiple times, vote on best answer. Very accurate but 5x cost. Use when accuracy is critical."

### Prompt Chaining

"Break complex task into sequential steps. Each step feeds into next, like ETL. Better control, easier debugging. For workflows."

### Tree of Thoughts

"Explore multiple reasoning branches, evaluate, pick best. Most sophisticated, most expensive. For planning and optimization."

\---

## Production Checklist

```
BEFORE DEPLOYING:

□ Prompt clarity
  └─ Could a non-expert understand the task?

□ Format constraints
  └─ Output schema clearly defined?
  └─ Temperature set (0 for determinism)?

□ Examples (if using few-shot)
  └─ Cover edge cases?
  └─ Match actual data distribution?

□ Error handling
  └─ What if model returns wrong format?
  └─ Validation logic in place?

□ Fallback strategy
  └─ What if model fails?
  └─ Cached response ready?

□ Monitoring
  └─ Latency tracked?
  └─ Error rate tracked?
  └─ Cost tracked?

□ Token counting
  └─ Actual count ≤ budget?
  └─ Cost projections realistic?

□ Load testing
  └─ Works at expected volume?
  └─ Handles error spikes?

□ Documentation
  └─ Prompt versioned?
  └─ Future maintainers can understand?
```

\---

## Common Mistakes \& Fixes

### ❌ Mistake 1: Format Drift

```
Problem: Same prompt, different formats each run

Fix:
├─ temperature=0 (determinism)
├─ Explicit constraints ("Return JSON only")
├─ Validation + retry
└─ Strict parsing
```

### ❌ Mistake 2: Hallucinations

```
Problem: Model generates confident but false info

Fix:
├─ Use RAG (retrieval) for grounding
├─ Add verification step
├─ Combine with tool calling
└─ Confidence scoring
```

### ❌ Mistake 3: Token Explosion

```
Problem: Estimated 100 tokens, actual 500

Fix:
├─ Count with actual tokenizer
├─ Budget 2-3x your estimate
├─ Monitor token usage
└─ Reduce context aggressively
```

### ❌ Mistake 4: Few-Shot Bias

```
Problem: Examples don't match real distribution

Fix:
├─ Sample examples from actual data
├─ Match distribution (if 10% negative, use 1/10 negative)
├─ Cover edge cases
└─ Validate on holdout set
```

### ❌ Mistake 5: No Fallback

```
Problem: System breaks when model fails

Fix:
├─ Cached fallback response
├─ Retry logic with backoff
├─ Graceful degradation
└─ User notification
```

\---

##  Talking Points

### "What's your approach to prompt engineering?"

> "I start with a baseline—zero-shot prompt to understand if the task is solvable with pretraining alone. I measure accuracy precisely. If it's below my threshold, I upgrade: add examples (few-shot), then reasoning (CoT), then multiple paths (self-consistency). I only move to fine-tuning if prompting plateaus. The key is measuring at each step to justify the added complexity and cost."

### "How do you handle hallucinations?"

> "Hallucinations are a prompt design problem, not a model problem. If the task needs grounding in fact, I use RAG—retrieve documents, then ask the model to answer only from those documents. For high-stakes tasks, I add verification: ask the model to cite sources, then check those sources exist. I never trust the model alone on factual questions."

### "What's your biggest lesson learned?"

> "Over-engineering is the biggest trap. People jump to fine-tuning or complex chains before exhausting simpler techniques. A well-written zero-shot prompt often beats a mediocre few-shot setup. I've learned to start minimal, measure, then upgrade only when data proves it's necessary."

\---

## Cost Estimation

### Quick Formula

```
Estimated cost = (prompt\_tokens + response\_tokens) × token\_price × volume

Example:
- Prompt: 150 tokens
- Response: 100 tokens
- Price (Sonnet): $0.003 per 1K tokens
- Volume: 10,000 requests/day

Daily cost = (150 + 100) × (0.003 / 1000) × 10,000
           = 250 × 0.000003 × 10,000
           = $7.50/day
           = $225/month
```

### Token Counting Tips

```
Words: \~1.3 tokens
JSON: \~2 tokens per pair
Code: \~0.5 tokens per line
Numbers: \~0.3 tokens each

Always overestimate:
- Estimated: 100 tokens
- Actual: 130-150 tokens (30-50% overhead)
```

\---

## Parameter Tuning

### Temperature

```
0.0 = Deterministic (best for production)
0.3 = Slightly creative (good for QA)
0.7 = Normal (default)
1.0+ = Very creative (use rarely)

Rule: Set to 0 for pipelines, 0.3-0.7 for user-facing
```

### Max Tokens

```
Too low: Response gets cut off mid-sentence
Too high: Wastes tokens on padding
Best: Set to minimum needed for full response + 20% buffer

Example:
- Sentiment (Positive/Negative): max\_tokens=10
- Summary (100 words): max\_tokens=150
- Essay (500 words): max\_tokens=1000
```

### Top-p (Nucleus Sampling)

```
0.1 = Only most likely tokens (narrow)
0.9 = Full distribution (broad)
Default: 0.9

Usually leave at default. Only adjust if sampling is unstable.
```

\---

## Monitoring Dashboard (What to Track)

```python
# Core metrics
- Latency (p50, p95, p99)
- Error rate (failures / total)
- Cost per request
- Token usage distribution

# Quality metrics
- Task-specific accuracy (if measurable)
- User satisfaction (if available)
- Hallucination rate (manual sampling)
- Format compliance rate

# Business metrics
- Requests per day
- Total monthly cost
- Cost per transaction
- Time to first response

# Alerts
- Error rate > 5%
- Latency p95 > 5s
- Daily cost > budget
- Accuracy drop > 10%
```

\---

## Reading Order (Learn in This Sequence)

**Week 1-2: Fundamentals**

1. Zero-shot prompting basics
2. Few-shot learning with examples
3. Output validation \& constraints

**Week 3-4: Reasoning**
4. Chain-of-Thought
5. Self-Consistency
6. Cost considerations

**Week 5-6: Advanced**
7. Prompt chaining (pipelines)
8. Tree of Thoughts
9. Production deployment

**Week 7-8+: Optimization**
10. RAG (retrieval-augmented generation)
11. Fine-tuning basics
12. Monitoring \& iteration

## One-Liners (For Quick Reference)

```
Zero-shot   = "Figure it out from training"
Few-shot    = "Here's how, now copy"
CoT         = "Show your work"
Consistency = "Think multiple ways"
Chaining    = "Break it into steps"
ToT         = "Explore like a strategist"
RAG         = "Answer from documents"
Fine-tune   = "Learn my specific patterns"
```

\---

## Last-Minute Interview Tips

### If asked: "What would you do if prompting isn't working?"

Step back and think:

1. **Is the task inherently hard?**
→ Maybe the problem formulation is wrong
2. **Is it data distribution mismatch?**
→ Few-shot examples don't match real data
3. **Is accuracy bottleneck accuracy metrics?**
→ How are you measuring? Is 85% actually bad?
4. **Have you tried all prompting techniques?**
→ CoT → Self-Consistency → Chaining → RAG
5. **Is the context window too small?**
→ Maybe you're cutting off important info
6. **Should you fine-tune?**
→ Only if prompting plateaus after optimization

\---

## Final Wisdom

> "The best prompt engineers aren't the ones who know the most techniques.
> They're the ones who know when NOT to use them.
> 
> Start simple. Measure everything. Iterate relentlessly.
> When you hit a wall, add complexity only if data justifies it.
> 
> Your superpower isn't knowing CoT or ToT—it's being systematic."

\---

