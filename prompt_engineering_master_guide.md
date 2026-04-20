# Prompt Engineering Master Guide

### From Zero to Production — Plain English, Real Examples, No Fluff

\---

> \\\*\\\*Who this is for:\\\*\\\* Anyone who types instructions into an AI and wonders why it gives weird answers. Beginners, developers, analysts, HR folks, consultants — everyone who talks to AI for work.

> \\\*\\\*What you'll get:\\\*\\\* 15 techniques, 10 ready-to-use prompt templates, debugging playbook, enterprise survival guide, and 30  discussion pointers. Plain language throughout. No PhD required.

\---

## Table of Contents

* [Part 1 — What Is Prompt Engineering?](#part-1)
* [Part 1A — Prompt Anatomy](#part-1a)
* [Part 2 — The 15 Techniques](#part-2)
* [Part 3 — Comparison Tables](#part-3)
* [Part 4 — Learning Path](#part-4)
* [Part 5 — Combining Techniques](#part-5)
* [Part 6 — Ready-to-Use Prompt Library](#part-6)
* [Part 7 — Debugging Bad Prompts](#part-7)
* [Part 8 — Enterprise Prompt Engineering](#part-8)
* [Part 9 —  Prep](#part-9)

\---

<a name="part-1"></a>

# Part 1 — What Is Prompt Engineering?

## The Simple Answer

Prompt engineering is the skill of writing better instructions for AI so it gives you better answers.

That's it. You're not coding. You're not doing math. You're crafting instructions — like writing a really precise email to a very literal colleague who does exactly what you say, nothing more, nothing less.

## Why Prompts Matter More Than You Think

Imagine you hire a world-class chef. You say: "Make me food." They could make sushi, or a five-course French dinner, or a bowl of cereal. Technically, all of them are food. But none of them are what you actually wanted.

Prompts work the same way. The AI isn't lazy or stupid — it just does what you asked. If you asked poorly, you get a poor answer.

Here's what a bad prompt costs you in a real system:

|What Breaks|Real Impact|
|-|-|
|Vague task|AI guesses wrong, you redo the work|
|No output format|Can't plug response into your app|
|Missing context|AI invents facts (hallucination)|
|Too long a prompt|Higher API costs, slower responses|
|Inconsistent phrasing|Unreliable results across runs|

## How Prompts Affect Four Things That Actually Matter

**Quality** — Better prompts = more accurate, relevant, useful answers. A prompt that includes context, examples, and a clear task will outperform a one-liner 90% of the time.

**Cost** — Every word you send costs tokens (the AI's billing unit). Poorly designed prompts often send too much fluff or trigger the AI to repeat itself. In production, this multiplies into real money.

**Latency** — Longer prompts take longer to process. Prompts that ask for step-by-step thinking take longer than prompts that ask for direct answers. Sometimes the slowness is worth it. Often it isn't.

**Reliability** — A well-structured prompt gives you the same quality of answer on run 100 as it did on run 1. Vague prompts are inconsistent — great today, garbage tomorrow.

## Common Beginner Mistakes

These are the top things people do wrong when they first start using AI:

1. **Writing like a Google search.** "Best way fix SQL error" — this isn't a search box, it's a conversation. Write in sentences.
2. **Expecting mind-reading.** "Write the report." What report? For who? In what tone? How long? The AI doesn't know your context unless you tell it.
3. **Giving the task but not the format.** You ask for analysis, you get an essay. You needed a table. You didn't say table.
4. **One massive wall of text.** Dumping 2,000 words of background when 100 well-chosen words would work better.
5. **Not iterating.** They try once, it fails, they give up. Prompt engineering is a loop — try, see what broke, fix it, try again.
6. **Over-constraining.** Adding 20 rules that contradict each other. The AI gets paralysed or starts ignoring rules.
7. **Forgetting examples.** The single biggest quality upgrade you can make to almost any prompt is adding one good example of what you want.

## How Professionals Design Prompts in Real Systems

When prompt engineering moves from "me trying ChatGPT" to "a company running 10,000 AI calls a day," it looks different:

* **Prompts live in version control** — just like code. Every change is tracked.
* **Prompts are tested before deployment** — with real inputs, compared to expected outputs.
* **Prompts are modular** — a system prompt sets the stage, a user prompt handles the variable part.
* **Prompts have fallbacks** — if the AI returns something unexpected, the system has a plan.
* **Prompts are monitored** — cost, latency, and quality are tracked over time.
* **Prompts are reviewed by humans** — especially in sensitive domains like healthcare, finance, legal.

In short: professionals treat prompts like software. They write them carefully, test them, version them, and improve them over time.

\---

<a name="part-1a"></a>

# Part 1A — Prompt Anatomy

## The 7 Building Blocks of a Great Prompt

You don't need all seven in every prompt. But knowing each one helps you diagnose why a prompt is failing — and fix it fast.

\---

### 1\. Role — Who Is the AI Being?

**What it is:** You tell the AI to behave like a specific type of expert.

**Why it matters:** AI is a generalist. Giving it a role focuses its style, vocabulary, assumptions, and level of detail. A "senior tax accountant" answers very differently from "a friendly assistant."

**Weak:** *(no role given)*

**Strong:** `You are a senior data engineer with 10 years of experience in SQL optimisation and ETL pipeline design.`

**Real-world example:** A customer service chatbot built for a telecom company gives better, more empathetic responses when told "You are a patient, professional customer support agent for a mobile network" instead of nothing.

**Mistake to avoid:** Don't make the role too exotic or fictional unless you need creativity. "You are a medieval wizard" won't help you write a SQL query.

\---

### 2\. Task — What Exactly Needs to Happen?

**What it is:** The specific job you want done. Clear, concrete, one thing at a time.

**Why it matters:** Vague tasks produce vague outputs. "Write something about marketing" is a task. "Write a 150-word LinkedIn post announcing our new product launch to B2B decision-makers" is a task that will actually work.

**Weak:** `Write about our product.`

**Strong:** `Write a 150-word LinkedIn post announcing the launch of our inventory management software. The audience is operations managers at mid-size manufacturers. Tone: professional but energetic.`

**Mistake to avoid:** Giving two tasks at once without clear separation. "Write the summary AND suggest improvements AND reformat it" — pick one, or chain them properly (see Prompt Chaining).

\---

### 3\. Context — What Does the AI Need to Know?

**What it is:** Background information the AI doesn't have but needs to do the job well.

**Why it matters:** Without context, the AI fills in the blanks. Often wrong. Context reduces hallucination and improves relevance.

**Weak:** `Summarise this document.`

**Strong:** `Summarise this document. It is a quarterly financial report for a retail company. The audience is non-finance executives who need a 5-bullet summary of key risks.`

**Mistake to avoid:** Dumping your entire company wiki as context. Be selective — give only what's needed for this specific task.

\---

### 4\. Constraints — What Are the Rules?

**What it is:** The limits, boundaries, and rules the AI must follow.

**Why it matters:** Without constraints, AI tends to be verbose, use technical jargon, go off-topic, or produce outputs you can't use. Constraints shape the output.

**Weak:** *(no constraints)*

**Strong:**

```
Constraints:
- Maximum 200 words
- No technical jargon
- Do not mention competitors
- Use British English spelling
```

**Mistake to avoid:** Contradictory constraints. "Be concise but cover every detail" is a contradiction. The AI will pick one or oscillate inconsistently.

\---

### 5\. Examples — Show, Don't Just Tell

**What it is:** One or more examples of the output you want.

**Why it matters:** This is the single most powerful quality booster available to you. Showing the AI exactly what good looks like removes ambiguity completely.

**Weak:** `Write a professional email.`

**Strong:**

```
Write a professional email. Here is the style I want:

Example:
Subject: Q3 Pipeline Review — Action Required
Hi \\\[Name], I wanted to follow up on the Q3 pipeline numbers before Thursday's leadership meeting...
```

**Mistake to avoid:** Using bad examples. If your example is vague or poorly written, the AI will imitate that.

\---

### 6\. Output Format — What Should It Look Like?

**What it is:** The structure, format, or presentation of the answer.

**Why it matters:** If your system needs JSON, you need to say JSON. If your user needs bullet points, say bullet points. Without this, you get prose when you needed a table, or a table when you needed a paragraph.

**Weak:** `Analyse this data.`

**Strong:**

```
Return your analysis in this exact JSON format:
{
  "summary": "one sentence",
  "key\\\_findings": \\\["finding 1", "finding 2"],
  "risk\\\_level": "low | medium | high",
  "recommended\\\_action": "one sentence"
}
```

**Mistake to avoid:** Asking for a format but not giving an example of it. "Return as JSON" is weaker than showing the schema.

\---

### 7\. Evaluation Criteria — What Does Good Look Like?

**What it is:** How you or the AI itself should judge whether the response is successful.

**Why it matters:** This is underused but powerful. Telling the AI what "good" means helps it self-correct and improves quality, especially for longer tasks.

**Weak:** *(nothing)*

**Strong:** `A good response will: (1) directly answer the question, (2) stay under 100 words, (3) avoid assumptions not supported by the data provided.`

**Mistake to avoid:** Making criteria too abstract. "Be excellent" is not a criterion. "Contains at least 3 specific data points from the report" is a criterion.

\---

### Putting It All Together

Here's what a well-built prompt looks like using all seven components:

```
ROLE: You are an experienced HR manager at a global tech company.

TASK: Write a rejection email for a job candidate who interviewed for a Software Engineer role.

CONTEXT: The candidate had good technical skills but the role was filled internally. We want to keep the door open for future applications.

CONSTRAINTS:
- 150 words maximum
- Warm and respectful tone
- Do not mention the internal hire
- End with an invitation to apply again

EXAMPLE OPENING: "Dear \\\[Name], Thank you so much for taking the time to..."

OUTPUT FORMAT: Plain email with Subject line, greeting, body, and sign-off.

EVALUATION CRITERIA: The email should feel personal, not like a mass template. It should leave the candidate feeling respected.
```

That prompt will produce a dramatically better result than: "Write a rejection email."

\---

<a name="part-2"></a>

# Part 2 — The 15 Prompt Techniques

\---

## Technique 1 — Zero-Shot Prompting

\---

### 1\. Simple Explanation

You just ask. No examples, no warm-up, no hand-holding. You give the AI a task and trust it knows how to do it from its training.

### 2\. 100-Word Deep Explanation

Zero-shot prompting means you give the model a task with zero examples. You rely entirely on what the model already knows from being trained on billions of documents. For common, well-defined tasks — summarisation, translation, classification, simple Q\&A — this works well. The model has seen enough of these tasks during training that it can figure out what you want. For niche, specialised, or unusual tasks, it starts to struggle because it has no reference point for your specific format or expectation. Zero-shot is your default starting point. If it works, great — you save tokens and complexity. If it doesn't, you move to few-shot.

### 3\. Why It Works

The model has been trained on enormous amounts of human text. Many common tasks have clear linguistic patterns the model has seen thousands of times. When you say "summarise this," the model has a strong prior on what summarisation looks like and does it.

### 4\. Best Use Cases

* Translation between common languages
* Simple summarisation
* Sentiment classification (positive/negative/neutral)
* Answering factual questions
* Basic data extraction
* Standard email drafting

### 5\. When Not to Use

* When output format is very specific and unusual
* When the task is niche or domain-specific
* When you need consistent structure across many runs
* When accuracy is critical and there's no room for interpretation

### 6\. Real-World Example

**Customer Support:** A travel company uses zero-shot to classify incoming support tickets into categories: flight, hotel, refund, baggage. Simple classification, no examples needed.

### 7\. Sample Prompt

```
Classify the following customer message into one of these categories:
\\\[Flight Issue / Hotel Issue / Refund Request / Baggage / Other]

Customer message: "My bag never arrived at the carousel after my flight from Dubai."

Return only the category name.
```

### 8\. Prompt Breakdown

* `Classify` — clear action verb
* `one of these categories` — constrained output set (no free-form answers)
* `Customer message:` — delimiter separating instruction from data
* `Return only the category name` — output format constraint to prevent verbose answers

### 9\. Expected Output Example

```
Baggage
```

### 10\. Common Mistakes

* Not constraining the output — AI writes a paragraph when you wanted one word
* Using zero-shot for tasks that need domain knowledge the model doesn't have
* Expecting perfect consistency across runs on ambiguous tasks

### 11\. Pro Tips

* Always constrain the output format even in zero-shot
* If you get wrong answers, don't just retry — add an example (go to few-shot)
* Zero-shot works best when your task has a clear "right answer" the model can identify

### 12\. Memory Trick

**"Just Ask, Don't Babysit"** — Zero examples, zero hand-holding. If the model was trained on it, it can do it alone.

### 13\. Insight

"Zero-shot prompting relies on the model's pre-trained knowledge to complete a task without any examples. It's the baseline technique — fast, cheap, and effective for well-defined tasks. Its limitation is that it provides no guidance on format or domain-specific expectations, which can lead to inconsistent outputs for specialised use cases."

### 14\. Before vs After

**Before (weak):**

```
What's the sentiment of this review?
"The food was cold and the service was rude."
```

**After (better zero-shot):**

```
Classify the sentiment of the following customer review.
Choose exactly one: Positive / Negative / Neutral

Review: "The food was cold and the service was rude."

Return only the classification label.
```

**Why better:** Constrains the output, specifies the label options, prevents a paragraph-long answer.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|Low|
|Latency Impact|Fast|
|Reliability|Medium (depends on task clarity)|

**Worth it when:** Task is simple and well-defined. No need to waste tokens on examples.
**Optimise by:** Constraining output format to get short, usable responses.

\---

## Technique 2 — Few-Shot Prompting

\---

### 1\. Simple Explanation

You show the AI a few examples of what you want before giving it the real task. You're teaching by demonstration.

### 2\. 100-Word Deep Explanation

Few-shot prompting gives the model 2–5 examples of the input-output pattern you expect, then presents the real task. The examples calibrate the model's understanding of your specific format, tone, vocabulary, and logic. It's like showing a new employee three sample reports before asking them to write one. The model doesn't learn permanently — each session is fresh — but within the prompt, those examples act as immediate references. Few-shot dramatically improves consistency and quality for tasks that have unusual formats, specific styles, or domain-specific logic that the model wouldn't nail from scratch.

### 3\. Why It Works

Language models predict the next most likely token. When you show examples of a pattern, the model's prediction engine recognises that pattern and continues it. It's essentially pattern completion — the model sees "given this kind of input, the output looks like this" and applies that to your real task.

### 4\. Best Use Cases

* Custom data extraction with a specific schema
* Tone/style matching for brand voice
* Domain-specific classification
* Code generation for a proprietary framework
* Structured output in a non-standard format

### 5\. When Not to Use

* Simple tasks the model handles fine zero-shot (wasting tokens)
* When you have bad examples — they'll make things worse
* When tasks vary too wildly from example to example
* When context window limits are tight

### 6\. Real-World Example

**Data Engineering:** A team uses few-shot to teach the model their internal error classification system, which has 8 custom categories not found in standard documentation. Three examples teach the model the classification logic.

### 7\. Sample Prompt

```
Classify these pipeline error messages into categories.
Categories: \\\[TIMEOUT / AUTH\\\_FAILURE / DATA\\\_FORMAT / MISSING\\\_FILE / UNKNOWN]

Examples:
Message: "Connection to database timed out after 30s"
Category: TIMEOUT

Message: "Invalid credentials for user svc\\\_account\\\_etl"
Category: AUTH\\\_FAILURE

Message: "Expected CSV but received JSON payload"
Category: DATA\\\_FORMAT

Now classify:
Message: "File orders\\\_2024\\\_Q3.csv not found in /data/input/"
Category:
```

### 8\. Prompt Breakdown

* **Categories list** — defines the valid output set
* **Three examples** — shows the pattern with enough variety
* **Consistent format** — `Message:` / `Category:` used identically in examples and task
* **"Now classify:"** — clean separator between examples and real task

### 9\. Expected Output Example

```
MISSING\\\_FILE
```

### 10\. Common Mistakes

* Using only one example — not enough pattern signal
* Examples that contradict each other
* Examples formatted differently than the real task
* Picking unrepresentative examples that don't cover edge cases

### 11\. Pro Tips

* Use 3–5 examples — that's the sweet spot. More than 5 rarely helps.
* Cover diverse cases in your examples, not just easy ones
* Format examples identically to the real task — exact same structure
* If you want edge case handling, include an edge case in your examples

### 12\. Memory Trick

**"Show, Don't Just Tell"** — You're not describing what you want, you're demonstrating it.

### 13\. Insight

"Few-shot prompting provides in-context learning examples that calibrate the model's output format and logic. It's particularly effective when the task has a non-standard output structure or domain-specific classification logic. The key is example quality and consistency — the model will imitate exactly what you show, so your examples must be your best work."

### 14\. Before vs After

**Before:**

```
Classify this error: "File orders\\\_2024\\\_Q3.csv not found"
```

**After:**

```
\\\[Full few-shot prompt as shown above]
```

**Why better:** With no examples, the model might say "File not found error" or write a paragraph. With examples, it returns exactly `MISSING\\\_FILE` — machine-parseable, consistent, right.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|Medium (examples add tokens)|
|Latency Impact|Slightly slower|
|Reliability|High|

**Worth it when:** Output format needs to be precise and consistent across many runs.
**Optimise by:** Using the minimum number of examples that achieve reliable results. Start with 2, test, add more only if needed.

\---

## Technique 3 — Chain of Thought (CoT)

\---

### 1\. Simple Explanation

You ask the AI to think through its answer step by step before giving the final answer. Like asking someone to "show their work" in maths class.

### 2\. 100-Word Deep Explanation

Chain of Thought (CoT) prompting instructs the model to reason through a problem in visible steps rather than jumping directly to a conclusion. This matters because language models generate text one token at a time — if you ask for the answer immediately, the model commits to it before "thinking." By asking it to reason first, you force it to build up logic that the final answer draws from. CoT dramatically improves performance on maths, logic, multi-step analysis, and anything where the answer depends on intermediate reasoning. The classic trigger is adding "Let's think step by step" to your prompt, but explicit step structure works even better.

### 3\. Why It Works

When the model generates step-by-step reasoning, each step becomes context for the next. This builds up a logical chain that keeps the final answer grounded. Without CoT, the model might confidently state a wrong answer because it "guessed" rather than "reasoned." With CoT, errors in the chain become visible and the final answer is more likely to be correct.

### 4\. Best Use Cases

* Multi-step maths and financial calculations
* Logical reasoning and deduction
* Troubleshooting and root cause analysis
* Complex classification with nuanced criteria
* Legal or policy analysis
* Medical triage logic

### 5\. When Not to Use

* Simple factual lookups ("What's the capital of France?")
* Tasks where step-by-step reasoning adds no value
* When speed and low cost are the priority
* Creative writing (reasoning steps break flow)

### 6\. Real-World Example

**Finance:** An analyst uses CoT to have AI check whether a transaction should be flagged for compliance review, walking through each relevant rule in sequence before reaching a conclusion.

### 7\. Sample Prompt

```
A customer made 5 international transactions in 3 days, totalling £47,500.
Their account average monthly transaction is £3,200.

Step through this analysis:
Step 1 — Calculate how this compares to their monthly average
Step 2 — Identify whether the pattern fits any common fraud indicators
Step 3 — State your confidence level (Low / Medium / High) that this is suspicious
Step 4 — Give your final recommendation: \\\[Flag for Review / No Action]

Work through each step explicitly before giving the final recommendation.
```

### 8\. Prompt Breakdown

* **Concrete numbers** — gives the model real data to reason over
* **Explicit step labels** — forces structured reasoning, not a guess
* **Confidence level** — adds nuance to the output
* **Final recommendation** — constrained to two options, comes after reasoning

### 9\. Expected Output Example

```
Step 1: £47,500 over 3 days vs. £3,200 monthly average. This is approximately 15x their normal monthly volume compressed into 3 days. Highly anomalous.

Step 2: Pattern indicators — rapid succession of international transfers, volume spike, short timeframe. These match structuring and account takeover patterns.

Step 3: Confidence: High — multiple indicators align with fraud patterns.

Step 4: Flag for Review
```

### 10\. Common Mistakes

* Asking for steps but not enforcing them (model skips to answer)
* Steps that don't logically build on each other
* Forgetting to ask for a final conclusion after the reasoning
* Using CoT when the task is simple — wastes tokens

### 11\. Pro Tips

* For critical decisions, add "Before finalising, check if your reasoning is consistent" at the end
* Numbered steps work better than "step by step" — they're harder to skip
* Combine with role prompting for domain accuracy (e.g., "As a compliance officer, step through…")

### 12\. Memory Trick

**"Show Your Work or Lose the Marks"** — Just like school maths. No working shown = likely wrong answer.

### 13\. Insight

"Chain of Thought prompting improves model accuracy on complex reasoning tasks by making intermediate steps explicit. It works because language models are token-by-token generators — they can't 'go back' and reconsider. Forcing step-by-step output means each step informs the next, building logically grounded conclusions. The tradeoff is token cost and latency — it's not appropriate for simple, fast-lookup tasks."

### 14\. Before vs After

**Before:**

```
Should this transaction be flagged? Customer made 5 international transactions totalling £47,500 in 3 days. Normal monthly average is £3,200.
```

Output might be: "Yes, this looks suspicious." (No reasoning, low confidence)

**After:** Full CoT prompt as shown above.

Output is detailed, reasoned, auditable. In a compliance context, the reasoning trail matters as much as the answer.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|High (reasoning steps add output tokens)|
|Latency Impact|Slow|
|Reliability|Very High for reasoning tasks|

**Worth it when:** The task involves logic, calculation, or multi-step decisions where errors have real consequences.
**Optimise by:** Only using CoT for genuinely complex reasoning. Use zero-shot for simple tasks.

\---

## Technique 4 — Reflection Prompting

\---

### 1\. Simple Explanation

You ask the AI to look at its own answer and critique or improve it. The AI checks its own work.

### 2\. 100-Word Deep Explanation

Reflection prompting is a two-stage process: first the model produces an answer, then it's asked to review, critique, or improve that answer. This is powerful because language models, like humans, can spot errors in their own writing more easily when reviewing it separately from generating it. The model shifts from "creator" to "editor" mode. This can happen in one prompt (ask for answer then critique) or in a follow-up prompt. Reflection catches logical inconsistencies, missing considerations, factual gaps, and formatting issues. It's especially valuable in high-stakes outputs: reports, decisions, code, or any output that will be acted on.

### 3\. Why It Works

The model's response to "review this" activates different patterns than the response to "write this." When reviewing, the model is essentially comparing the output against known standards — grammar rules, logical consistency, completeness. It's a different "mode" with a different objective function.

### 4\. Best Use Cases

* Quality-checking generated reports or documents
* Code review and bug detection
* Legal or policy document review
* Improving draft communications
* Self-correcting reasoning in multi-step tasks
* Any high-stakes output before human review

### 5\. When Not to Use

* Quick, low-stakes tasks (doubles the cost and time)
* When the model consistently fails to find its own errors (know your model's limits)
* Creative tasks where subjective quality is the goal

### 6\. Real-World Example

**HR:** An HR team generates job descriptions with AI, then uses a reflection step to check for unconscious bias, legal compliance issues, and unclear requirements before publishing.

### 7\. Sample Prompt

```
STEP 1 — Write a job description for a Data Analyst role at a fintech company.

STEP 2 — Review the job description you just wrote and check for:
- Any language that might unintentionally exclude diverse candidates
- Requirements that seem unnecessarily restrictive
- Missing information a candidate would need to apply
- Anything that doesn't match a modern, inclusive workplace

STEP 3 — Output the improved version of the job description.
```

### 8\. Prompt Breakdown

* **Labelled steps** — forces the model to complete each stage
* **Specific review criteria** — gives the reflection stage clear things to look for
* **Final output step** — produces the improved version, not just a critique

### 9\. Expected Output Example

The model produces a first draft, then a critique noting "Requirements listed '5+ years experience' which may discourage qualified candidates with non-linear paths," then an improved version removing that restriction.

### 10\. Common Mistakes

* Asking for reflection without giving criteria — the model reflects too generally
* Stopping at critique without asking for the improved version
* Using reflection on tasks where the original was already good (wastes tokens)

### 11\. Pro Tips

* Give the reflection step specific things to check — not just "review your answer"
* For critical outputs, run reflection twice: once for substance, once for format
* Combine with role prompting: "Now review this as a senior editor would"

### 12\. Memory Trick

**"Draft, Critique, Polish"** — It's the professional writing process, automated.

### 13\.  Insight

"Reflection prompting implements a self-correction loop within the model's output. By separating generation from evaluation, it leverages the model's ability to detect inconsistencies and gaps in existing text — a different cognitive mode than generation. This improves output quality, particularly for high-stakes documents where first-draft errors are unacceptable."

### 14\. Before vs After

**Before:**

```
Write a job description for a Data Analyst role.
```

Output may contain vague requirements, biased language, or missing key information.

**After:** Full reflection prompt as shown. Output is self-reviewed and improved.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|High (draft + review + revised output)|
|Latency Impact|Slow|
|Reliability|High|

**Worth it when:** Output will be published, acted on, or reviewed by stakeholders. The extra cost pays for itself in avoided errors.

\---

## Technique 5 — Role Prompting

\---

### 1\. Simple Explanation

You tell the AI to pretend it's a specific type of expert. "You are a senior financial analyst" — and suddenly the answer comes from that perspective.

### 2\. 100-Word Deep Explanation

Role prompting assigns an identity, expertise, and perspective to the model before it answers. This isn't just decoration — it genuinely shifts vocabulary, detail level, assumptions, and reasoning style. An AI answering as a data engineer uses different terminology and prioritises different concerns than the same AI answering as a product manager. Roles also manage formality: "friendly customer service agent" vs "clinical medical reviewer" set very different tones. Role prompting is foundational and combines powerfully with almost every other technique. It's one of the cheapest upgrades you can make to any prompt — adding a role costs almost no tokens.

### 3\. Why It Works

The model's training data contains millions of texts written by different types of professionals. When you invoke a role, the model selects from a distribution of patterns, vocabulary, and reasoning styles associated with that role. It's not magic — it's pattern matching to a specific professional persona in the training data.

### 4\. Best Use Cases

* Technical writing that needs domain accuracy
* Communication that needs a specific tone
* Decision support that needs professional framing
* Any task where expertise framing improves quality
* Customer-facing interactions that need specific personality

### 5\. When Not to Use

* When the role conflicts with the task ("You are a children's book author — now write a compliance report")
* When you want a neutral, multi-perspective answer
* When role specificity introduces unwanted bias

### 6\. Real-World Example

**Healthcare:** A hospital uses "You are an experienced nurse communicating with anxious patients" as the system prompt for their patient pre-appointment information system. Empathetic, clear, non-alarming language results.

### 7\. Sample Prompt

```
You are a senior SQL developer with deep expertise in performance optimisation for PostgreSQL databases handling millions of rows.

A junior developer has written the following query and it's running very slowly:

\\\[query here]

Review this query and explain:
1. Why it's slow (identify the specific issues)
2. How to fix each issue
3. The optimised version of the query

Write your explanation for an audience that knows SQL basics but hasn't studied query optimisation.
```

### 8\. Prompt Breakdown

* **Detailed role** — "senior," "deep expertise," specific database — precision matters
* **Context** — junior developer, slow query — frames the situation
* **Three-part task** — structured and clear
* **Audience note** — adjusts the explanation level without changing the role

### 9\. Expected Output Example

Explanation of why indexes aren't being used, how to add a composite index, and a rewritten query — all explained at an intermediate level without jargon overload.

### 10\. Common Mistakes

* Vague roles: "expert" or "professional" — say what kind
* Role that conflicts with the task
* Forgetting to maintain the role across a multi-turn conversation

### 11\. Pro Tips

* Specify seniority level — "junior" vs "senior" produces noticeably different outputs
* Add domain specialisation: not just "lawyer" but "contract lawyer specialising in SaaS licensing"
* For customer-facing AI, always include communication style in the role: "calm, empathetic, professional"

### 12\. Memory Trick

**"Costume Changes the Character"** — Put the right costume on the AI and it plays the part.

### 13\.  Insight

"Role prompting shifts the model's output distribution toward patterns associated with a specific professional persona in its training data. It affects vocabulary, reasoning depth, assumptions, and tone simultaneously. It's one of the highest-leverage, lowest-cost prompt improvements available — almost always worth adding."

### 14\. Before vs After

**Before:**

```
Explain why this SQL query is slow.
```

**After:**

```
You are a senior SQL developer specialising in PostgreSQL performance...
```

**Why better:** The role shifts the answer from a generic explanation to an expert-level diagnosis with specific, actionable fixes — same question, massively different quality.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|Very Low (a sentence added to the prompt)|
|Latency Impact|None|
|Reliability|Improves significantly for domain-specific tasks|

**Worth it when:** Almost always. One sentence, high returns.

\---

## Technique 6 — Constraint Prompting

\---

### 1\. Simple Explanation

You set rules the AI must follow. Word limits, tone restrictions, what to avoid, what to always include.

### 2\. 100-Word Deep Explanation

Constraint prompting defines the boundaries the model must operate within when completing a task. Constraints shape length, format, tone, content restrictions, and logical rules. Without constraints, AI tends toward verbosity, assumptions, and free-form outputs that may not fit your use case. Constraints are what turn "write an email" into "write a 100-word email in a professional but warm tone that doesn't mention pricing." They're most powerful when specific and measurable — "under 100 words" beats "be brief." Constraints also prevent safety or compliance issues in enterprise use, acting as guardrails within the prompt itself.

### 3\. Why It Works

Constraints reduce the model's output space. Instead of considering all possible responses, it filters for responses that satisfy the constraints. Clear constraints create clearer outputs — the model isn't choosing between many interpretations, it's fulfilling a specification.

### 4\. Best Use Cases

* Any output that has a word or character limit
* Brand communication with tone guidelines
* Compliance-sensitive content (legal, medical, financial)
* Systems where output feeds into another process
* Customer-facing text that must avoid certain topics

### 5\. When Not to Use

* Creative brainstorming where constraints stifle ideas
* Exploratory analysis where you want breadth
* When you have so many constraints they contradict each other

### 6\. Real-World Example

**Legal/Finance:** A wealth management firm constrains their AI to never give specific investment advice, never mention competitor products, always recommend professional consultation, and stay under 250 words per response.

### 7\. Sample Prompt

```
You are a financial wellness assistant.

Write a response to this customer question about pension contributions.

Customer question: "Should I contribute more to my pension or pay off my credit card debt?"

Constraints:
- Maximum 150 words
- Do not recommend specific financial products
- Do not give personalised investment advice
- Always end with: "For personalised advice, speak to a certified financial adviser."
- Tone: warm, informative, non-alarming
```

### 8\. Prompt Breakdown

* **Role** — sets the domain and persona
* **Context** — real customer question
* **Explicit constraints list** — numbered or bulleted for clarity
* **Mandatory closing line** — ensures legal compliance every time

### 9\. Expected Output Example

A 140-word balanced explanation of both options, ending with the required disclaimer — no specific advice given, warm tone maintained.

### 10\. Common Mistakes

* Too many constraints — model gets confused and breaks some
* Vague constraints: "be professional" needs defining
* Constraints that contradict each other
* No constraints at all on outputs that need them

### 11\. Pro Tips

* Rank your constraints implicitly by listing the most important ones first
* Test removing constraints one by one to see which ones are actually doing work
* For enterprise use, keep a library of standard constraint blocks that get appended to all prompts in a category

### 12\. Memory Trick

**"Rules Before the Game Starts"** — You set the rules once, the AI plays by them every time.

### 13\.  Insight

"Constraint prompting is essential for enterprise use cases where compliance, tone, and output format are non-negotiable. It reduces the model's search space by specifying what not to do as much as what to do. The key failure mode is constraint conflict — too many competing rules produce inconsistent outputs. Prioritise constraints and test them individually."

### 14\. Before vs After

**Before:**

```
Answer this pension question.
```

**After:** Full constraint prompt as shown. Output is compliant, bounded, and safe to publish.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|Low (constraints add input tokens)|
|Latency Impact|None|
|Reliability|Very High|

**Worth it when:** Any time output compliance, length, or tone matters — which is most enterprise contexts.

\---

## Technique 7 — Output Format Prompting

\---

### 1\. Simple Explanation

You tell the AI exactly what shape the answer should take — JSON, table, bullet points, numbered list, a specific paragraph structure.

### 2\. 100-Word Deep Explanation

Output format prompting specifies the presentation structure of the model's response. This is critical when the output will be parsed, displayed, or used by another system. An API expecting JSON crashes on prose. A dashboard needing a table can't use an essay. Format prompting tells the model not just what to say but how to present it. The most powerful version combines a format instruction with an example schema — showing the model the exact structure rather than just naming it. This technique is foundational for any AI integrated into a product, pipeline, or workflow.

### 3\. Why It Works

The model's training included structured text of all kinds — JSON files, tables, bullet lists, code. When you request a format and show an example, the model recognises the pattern and replicates it. The more specific your schema, the more consistent the output.

### 4\. Best Use Cases

* API integrations that parse AI output
* Data extraction into structured formats
* Dashboard feeds
* Document templates
* Any repeatable output that feeds a downstream process

### 5\. When Not to Use

* Casual conversation or one-off questions
* When format flexibility produces better creative results
* When the format you specify is wrong for the content

### 6\. Real-World Example

**Analytics:** A data team uses output format prompting to extract structured data from unstructured customer feedback — the model returns a JSON object per review with sentiment, topics, urgency score, and suggested action.

### 7\. Sample Prompt

```
Extract the key information from the following customer complaint and return it as JSON.

Use this exact schema:
{
  "customer\\\_id": "string or null",
  "complaint\\\_category": "billing | technical | delivery | other",
  "severity": "low | medium | high",
  "summary": "max 50 words",
  "suggested\\\_action": "string"
}

Customer complaint:
"I've been charged twice for my subscription this month. This is the third time this has happened. I'm cancelling if this isn't fixed today."
```

### 8\. Prompt Breakdown

* **Schema with types** — tells the model exactly what each field should contain
* **Constrained enum values** — `billing | technical | delivery | other` — no creative labelling
* **Word limit on summary** — prevents the summary field from becoming a paragraph
* **Data clearly separated** — instruction above, data below

### 9\. Expected Output Example

```json
{
  "customer\\\_id": null,
  "complaint\\\_category": "billing",
  "severity": "high",
  "summary": "Customer charged twice this month, third occurrence. Threatening to cancel.",
  "suggested\\\_action": "Process refund and escalate to retention team."
}
```

### 10\. Common Mistakes

* Naming a format without showing the schema ("return as JSON" is weaker than showing the schema)
* Fields with ambiguous types — specify string, number, boolean
* Forgetting null handling for optional fields
* Mixing format instructions with task instructions in confusing ways

### 11\. Pro Tips

* Always show an example schema even if brief
* Include data types and constraints in the schema (`"max 50 words"`, `"one of: low | medium | high"`)
* For mission-critical parsing, add: "Return only the JSON. No explanation, no markdown."
* Test with edge cases: empty data, ambiguous data, long data

### 12\. Memory Trick

**"The Container Shapes the Content"** — Tell the AI what container to pour the answer into.

### 13\.  Insight

"Output format prompting is critical for AI systems integration. When AI feeds data to downstream processes, format reliability isn't a nice-to-have — it's a hard requirement. Showing the exact schema rather than just naming the format type significantly improves consistency. This technique pairs naturally with few-shot examples showing correctly formatted outputs."

### 14\. Before vs After

**Before:**

```
Summarise this complaint.
```

Output: Three paragraphs of prose you can't parse.

**After:** Full output format prompt as shown. Output: Clean JSON your system can immediately use.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|Low-Medium (schema adds input, structured output is concise)|
|Latency Impact|Fast|
|Reliability|Very High when schema is clear|

**Worth it when:** Output connects to any system, tool, or process. Which is most of the time in production.

\---

## Technique 8 — Delimiter Prompting

\---

### 1\. Simple Explanation

You use special markers — like triple quotes, XML tags, or dashes — to clearly separate different parts of your prompt. The AI knows where instructions end and data begins.

### 2\. 100-Word Deep Explanation

Delimiter prompting uses clear, consistent markers to separate distinct sections of a prompt: the instruction, the data, examples, constraints, and output format. Without delimiters, long prompts become ambiguous — the model isn't always sure what it should process vs. what it should follow as instruction. Delimiters are especially important when data contains natural language that might look like instructions. For example, if you're asking the model to summarise a document that contains the phrase "now write a report," without delimiters the model might try to follow that phrase. Delimiters prevent this kind of confusion and make long prompts reliable.

### 3\. Why It Works

Delimiters create structure that the model can parse. Triple quotes, XML-style tags, or labelled sections act like markers that help the model understand: "this is what I should do" vs "this is what I should process." The clearer the separation, the less room for misinterpretation.

### 4\. Best Use Cases

* Prompts with large amounts of data being passed in
* When data might contain instruction-like language
* Multi-part prompts with distinct sections
* Document processing
* Any production prompt where reliability across thousands of runs matters

### 5\. When Not to Use

* Short, simple prompts where structure is obvious
* Conversational queries
* When overhead of formatting isn't worth the reliability gain

### 6\. Real-World Example

**Document Processing:** A legal tech company processes contracts with AI. They use XML tags to separate the instruction from the contract text, preventing the model from accidentally "following" contract clauses as instructions.

### 7\. Sample Prompt

```
<instruction>
Summarise the following meeting transcript. 
Focus on: decisions made, action items, and owners.
Return as a structured list.
</instruction>

<transcript>
John: OK so the Q3 report deadline is confirmed for Friday. 
Sarah: I'll handle the financial section. John, can you do the executive summary?
John: Sure. Also we agreed to move the client demo to next Wednesday.
Mark: I'll send calendar invites for the demo today.
</transcript>

<output\\\_format>
## Decisions
- \\\[list]

## Action Items
| Owner | Task | Deadline |
|---|---|---|
</output\\\_format>
```

### 8\. Prompt Breakdown

* **`<instruction>` tags** — clearly wraps what the model should do
* **`<transcript>` tags** — clearly wraps the data to process
* **`<output\\\_format>` tags** — defines the expected structure of the response
* No ambiguity about what's instruction vs. what's data vs. what's the output template

### 9\. Expected Output Example

```markdown
## Decisions
- Q3 report deadline confirmed: Friday
- Client demo moved to next Wednesday

## Action Items
| Owner | Task | Deadline |
|---|---|---|
| Sarah | Write financial section of Q3 report | Friday |
| John | Write executive summary of Q3 report | Friday |
| Mark | Send calendar invites for client demo | Today |
```

### 10\. Common Mistakes

* Inconsistent delimiter style (mixing XML tags with triple quotes in the same prompt)
* Not using delimiters when the data contains instruction-like language
* Forgetting to close XML tags
* Using delimiters on simple prompts that don't need them

### 11\. Pro Tips

* Be consistent across your entire prompt library — pick a delimiter style and stick to it
* XML-style tags work especially well because they're explicit and self-labelling
* Triple backticks (` `) work well for code sections
* For very large data inputs, always use delimiters — they're non-negotiable

### 12\. Memory Trick

**"Fences for the Sections"** — Like fences in a field, delimiters tell the model exactly where one thing ends and another begins.

### 13\.  Insight

"Delimiter prompting is a reliability technique for complex prompts. When instructions and data coexist in the same prompt, the model can conflate them — especially when data contains natural language. Delimiters create explicit parsing boundaries. This is particularly important in production systems where prompt injection attacks might try to hide instructions inside data payloads."

### 14\. Before vs After

**Before:**

```
Summarise this meeting transcript. Focus on decisions and action items. John: OK so the Q3 report deadline is Friday...
```

Model might lose track of where instruction ends and data begins.

**After:** XML-tagged version as shown. Clean, reliable, parseable.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|Very Low (tags add minimal tokens)|
|Latency Impact|None|
|Reliability|Significantly improved for complex prompts|

**Worth it when:** Any prompt with distinct data sections, or when data might contain instruction-like content.

\---

## Technique 9 — Step-Back Prompting

\---

### 1\. Simple Explanation

Before answering the specific question, you ask the AI to take a step back and think about the general principles or broader context first. Then it applies those principles to the specific problem.

### 2\. 100-Word Deep Explanation

Step-back prompting instructs the model to first identify the broader concept, principle, or framework relevant to a question, then use that to answer the specific question. It's the opposite of diving straight in. For narrow, domain-specific questions, this technique grounds the answer in solid principles rather than guessing from limited context. It's like asking a doctor: "What category of condition does this symptom pattern suggest?" before asking "What's the diagnosis?" The broader first step prevents premature conclusions and ensures the model's answer is informed by relevant frameworks, not just pattern-matching on surface features.

### 3\. Why It Works

Specific questions can be answered many ways. By first establishing the general principle, the model creates a filter: "Given this principle, what's the correct specific answer?" This reduces hallucination and increases logical consistency, especially for technical or specialised domains.

### 4\. Best Use Cases

* Diagnosing complex technical problems
* Policy or legal interpretation
* Scientific or medical reasoning
* Instructional design (teaching a concept by principle first)
* Strategic advice that needs grounding in theory

### 5\. When Not to Use

* Simple, direct questions with factual answers
* When you need speed and the extra reasoning step isn't warranted
* Creative tasks where principles might constrain imagination

### 6\. Real-World Example

**IT Support:** An enterprise helpdesk AI uses step-back prompting to first identify the category of network problem before attempting specific diagnostics — avoiding the common failure of jumping to the wrong solution.

### 7\. Sample Prompt

```
A developer is getting intermittent 502 Bad Gateway errors on their microservice API. 
The errors happen about 10% of the time and increase under load.

STEP 1 — Step back: What are the general categories of causes for intermittent 502 errors under load in microservice architectures?

STEP 2 — Apply: Given those categories, which are most likely given this specific pattern (intermittent, load-dependent), and what diagnostic steps would you prioritise?
```

### 8\. Prompt Breakdown

* **Specific problem** — enough detail for the step-back to be relevant
* **Step 1 prompt** — asks for general principles first
* **Step 2 prompt** — applies those principles to the specific case
* **Pattern specification** — "intermittent, load-dependent" gives the model key diagnostic signals

### 9\. Expected Output Example

Step 1 lists categories: upstream service unavailability, timeout mismatches, resource exhaustion, connection pool limits, load balancer health check failures.

Step 2 prioritises connection pool exhaustion and timeout mismatches for this pattern, recommends specific diagnostic commands.

### 10\. Common Mistakes

* Skipping Step 1 mentally and just getting Step 2's answer
* Making the step-back too broad ("What are all possible errors?")
* Not linking Step 1 to Step 2 properly

### 11\. Pro Tips

* Make Step 1 targeted to the problem domain, not completely general
* Explicitly instruct the model to use Step 1's output in Step 2
* This works well combined with Chain of Thought for complex diagnostics

### 12\. Memory Trick

**"Zoom Out to Zoom In"** — Take a step back to get a better view before focusing.

### 13\.  Insight

"Step-back prompting improves accuracy by grounding specific answers in general principles first. It mirrors expert human reasoning — professionals categorise before diagnosing. This reduces the model's tendency to jump to pattern-matched but wrong specific answers, particularly in technical domains."

### 14\. Before vs After

**Before:**

```
How do I fix intermittent 502 errors on my API?
```

Gets a generic answer that may not match the specific pattern.

**After:** Step-back prompt. Gets a principled diagnosis specific to the load-dependent, intermittent pattern.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|Medium|
|Latency Impact|Medium|
|Reliability|High for diagnostic/reasoning tasks|

\---

## Technique 10 — Self-Consistency Prompting

\---

### 1\. Simple Explanation

You ask the AI the same question multiple times (or with slightly different phrasings), get multiple answers, and then go with the most common or most consistent answer.

### 2\. 100-Word Deep Explanation

Self-consistency prompting generates multiple independent responses to the same question — often with slight variations — and aggregates the results. The idea is that a correct answer will appear consistently across runs, while incorrect or hallucinated answers will vary. It's a voting mechanism for AI outputs. This is particularly powerful for factual questions, calculations, or reasoning tasks where you can't easily verify the answer yourself. The downside is obvious: it multiplies your cost by the number of samples you take. But for high-stakes decisions, having a "majority vote" from five AI runs is far more reliable than trusting a single output.

### 3\. Why It Works

Language models have some inherent randomness (controlled by the "temperature" setting). This means the same question can produce different answers on different runs. Correct answers are more stable — they'll appear in the majority of runs. Hallucinated or uncertain answers vary wildly. The most frequent answer across runs is therefore more likely to be correct.

### 4\. Best Use Cases

* High-stakes factual questions you can't easily verify
* Numerical calculations where errors matter
* Medical or legal reasoning where single-run errors are unacceptable
* Any situation where a "second opinion" improves confidence
* Ensemble logic in automated pipelines

### 5\. When Not to Use

* When speed or cost are the priority
* For simple, clear-cut questions with obvious answers
* Creative tasks where variation is the goal
* When a single confident run is sufficient

### 6\. Real-World Example

**Healthcare:** A medical information system runs clinical guidelines queries three times and only presents information that appears consistently — flagging responses where runs disagree for human clinical review.

### 7\. Sample Prompt Structure

Run this prompt three times independently:

```
Given the following patient symptoms, what is the most likely diagnosis category?
Symptoms: 38.5°C fever, productive cough, chest pain on breathing, fatigue for 4 days.

Think through the differential diagnosis systematically.
Final answer: \\\[Most likely category]
```

Then compare outputs across three runs and use the consistent answer.

### 8\. Prompt Breakdown

* **Consistent prompt** across runs — no variation in the instruction
* **Reasoning step** included to surface disagreement in logic, not just conclusions
* **Structured final answer** — makes comparison across runs easy

### 9\. Expected Output Example

Run 1: Lower respiratory tract infection  
Run 2: Lower respiratory tract infection  
Run 3: Viral pneumonia

Majority: Lower respiratory tract infection — flag for clinical review since Run 3 diverged.

### 10\. Common Mistakes

* Running the same prompt just twice (not enough for reliable consensus)
* Ignoring divergent answers instead of flagging them
* Using self-consistency for tasks where all runs will produce the same wrong answer (it doesn't fix fundamental knowledge gaps)

### 11\. Pro Tips

* Run at least 3 times, ideally 5 for high-stakes decisions
* Track disagreement rates — high disagreement on a question type is a signal to change your approach
* Vary temperature slightly between runs to increase diversity
* Build automatic flagging for human review when runs disagree

### 12\. Memory Trick

**"Three Doctors, Same Diagnosis"** — If three independent opinions agree, you're more confident. If they disagree, investigate.

### 13\.  Insight

"Self-consistency prompting is an ensemble technique that trades cost for reliability. By sampling multiple outputs and selecting the most frequent answer, it effectively reduces the variance in model responses. It's appropriate for high-stakes, hard-to-verify tasks where single-run reliability is insufficient. The cost multiplier makes it impractical for high-volume, low-stakes queries."

### 14\. Before vs After

**Before:** Run once, get one answer, trust it.

**After:** Run three times, all three agree → high confidence. Two agree, one differs → moderate confidence, note the divergence. All differ → escalate to human review.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|Very High (multiplied by number of runs)|
|Latency Impact|Very Slow (parallel or sequential runs)|
|Reliability|Very High|

**Worth it when:** Decisions have significant consequences if wrong. Medical, legal, financial contexts. Not worth it for routine tasks.

\---

## Technique 11 — ReAct Prompting

\---

### 1\. Simple Explanation

The AI combines Reasoning and Acting — it thinks about what to do, does it (like searching, calculating, checking a database), looks at the result, then decides what to do next. Think, Act, Observe, repeat.

### 2\. 100-Word Deep Explanation

ReAct (Reasoning + Acting) is a prompting technique for agentic AI systems — where the model doesn't just generate text but takes actions, observes results, and responds accordingly. The model interleaves thought (reasoning steps), action (tool calls, queries, API calls), and observation (interpreting what came back). This loop continues until the task is complete. ReAct is the backbone of most AI agent systems. It allows the model to handle tasks that require real-world information — web search, database queries, calculator use — rather than relying solely on training data. The explicit reasoning before each action also reduces errors.

### 3\. Why It Works

By making the model explain its reasoning before acting, you reduce impulsive actions based on misunderstanding. The observation step grounds the model in real results rather than assumptions. The loop continues until the task is actually complete, not just until the model thinks it's done.

### 4\. Best Use Cases

* AI agents that use tools (search, databases, APIs, calculators)
* Multi-step research tasks
* Dynamic data retrieval and analysis
* Customer service bots that need to check real-time account info
* Any workflow where the AI needs to gather information to answer

### 5\. When Not to Use

* Static knowledge tasks where no external data is needed
* Simple Q\&A
* When tool access isn't available
* Latency-critical tasks (the loop is slow)

### 6\. Real-World Example

**Customer Support:** An e-commerce AI agent uses ReAct to handle "Where is my order?" — Thought: I need to check the order database. Action: Query order system with customer ID. Observation: Order is in transit, estimated delivery tomorrow. Response: provides accurate, real-time answer.

### 7\. Sample Prompt

```
You are a helpful analyst assistant with access to these tools:
- search\\\_database(query) — searches the internal sales database
- calculate(expression) — evaluates a mathematical expression
- get\\\_report(report\\\_name) — retrieves a specific report

Use this format for each step:
Thought: \\\[your reasoning]
Action: \\\[tool\\\_name(parameters)]
Observation: \\\[result of the action]
... (repeat as needed)
Final Answer: \\\[your answer]

Task: What was the percentage increase in sales for the Western region between Q2 and Q3 this year?
```

### 8\. Prompt Breakdown

* **Tool list with descriptions** — the model knows what's available
* **Strict format** — Thought/Action/Observation pattern ensures structured reasoning
* **Clear task** — specific, answerable with the available tools
* **"Final Answer:"** — explicit ending condition

### 9\. Expected Output Example

```
Thought: I need Q2 and Q3 Western region sales data.
Action: get\\\_report("Q2\\\_Sales\\\_Regional")
Observation: Western Region Q2 Sales: £2.4M

Thought: Now I need Q3 data.
Action: get\\\_report("Q3\\\_Sales\\\_Regional")
Observation: Western Region Q3 Sales: £2.9M

Thought: Now calculate the percentage increase.
Action: calculate("(2.9 - 2.4) / 2.4 \\\* 100")
Observation: 20.83

Final Answer: The Western region saw a 20.83% increase in sales from Q2 to Q3.
```

### 10\. Common Mistakes

* Not defining tools clearly — model tries to use tools that don't exist
* No observation format — model makes up results instead of waiting for them
* Not specifying the end condition — model loops forever
* Mixing reasoning and action in the same step

### 11\. Pro Tips

* Always define the tool list explicitly with parameters
* Include a maximum number of loops to prevent infinite reasoning
* Log all Thought/Action/Observation sequences for debugging
* Validate tool outputs before passing them back as observations

### 12\. Memory Trick

**"Think, Do, Look, Repeat"** — ReAct is a human problem-solving loop encoded in a prompt.

### 13\.  Insight

"ReAct prompting enables AI agents by interleaving reasoning and action in an explicit, observable loop. The key insight is that making reasoning visible before each action reduces errors and makes the system auditable. It's the foundational pattern for building reliable AI agents that use external tools — the observe-reason-act loop mirrors how experts approach unknown problems."

### 14\. Before vs After

**Before:**

```
What was the sales increase for the Western region?
```

The AI guesses from training data (probably wrong, certainly outdated).

**After:** ReAct prompt with database access. Gets real, current data and does the actual calculation.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|High (multi-step loops, all reasoning is in context)|
|Latency Impact|Slow (multiple tool calls)|
|Reliability|High for dynamic data tasks|

**Worth it when:** The task genuinely requires real-time or external data. Static knowledge? Use something simpler.

\---

## Technique 12 — Tree of Thoughts (ToT)

\---

### 1\. Simple Explanation

Instead of the AI following one line of thinking, you ask it to consider multiple different approaches to a problem simultaneously — like exploring multiple branches of a tree — and then evaluate which branch leads to the best answer.

### 2\. 100-Word Deep Explanation

Tree of Thoughts (ToT) extends Chain of Thought by exploring multiple reasoning paths in parallel rather than following a single chain. The model generates several possible approaches to a problem, evaluates each, and selects (or combines) the best. This mirrors how expert problem-solvers actually think — they consider alternatives before committing to one solution. ToT is computationally expensive but produces significantly better results for complex problems with multiple valid approaches. It's particularly powerful for planning, strategy, puzzle-solving, and any domain where the first approach isn't always the best approach.

### 3\. Why It Works

Single-path reasoning can get "locked in" to a suboptimal direction early. By exploring multiple branches, you avoid premature commitment. The evaluation step forces comparison, which surfaces better options. It also provides natural explainability — you can see why one path was chosen over another.

### 4\. Best Use Cases

* Strategic planning with multiple options
* Complex debugging where the bug might be in several places
* Design decisions with genuine trade-offs
* Creative problems with multiple valid approaches
* Scenario planning and risk analysis

### 5\. When Not to Use

* Problems with a clear single solution
* Time or cost-sensitive tasks
* Simple tasks that CoT handles fine
* When you don't have the budget for multi-branch exploration

### 6\. Real-World Example

**Operations:** A supply chain team uses ToT to evaluate three different approaches to resolving a supplier shortage — exploring cost, speed, and risk trade-offs of each before recommending one.

### 7\. Sample Prompt

```
We need to improve customer response time from 48 hours to 24 hours with no budget increase.

Generate THREE different approaches to solving this problem.
For each approach:
- Describe the solution
- List 2 key advantages
- List 2 key risks or drawbacks
- Rate feasibility: Low / Medium / High

Then evaluate all three approaches and recommend the best one with justification.
```

### 8\. Prompt Breakdown

* **Clear problem** with specific constraints (no budget increase)
* **"THREE different approaches"** — forces breadth
* **Consistent evaluation criteria** — makes comparison fair
* **"Recommend the best one"** — forces convergence to a conclusion

### 9\. Expected Output Example

Three distinct approaches (e.g., triage prioritisation system, AI first-response automation, staff scheduling changes) each with structured pros/cons/feasibility ratings, followed by a justified recommendation.

### 10\. Common Mistakes

* Asking for alternatives but letting the model produce variations of the same idea
* No evaluation step — you get options but no recommendation
* Too many branches (five+) — quality degrades
* Not forcing distinct approaches — prompt must push for variety

### 11\. Pro Tips

* Push back if the branches look similar: "These approaches are too similar — give me more distinct options"
* Have the model rate approaches on multiple dimensions (cost, speed, risk, feasibility)
* Combine with self-consistency: run ToT twice and see if the same branch wins

### 12\. Memory Trick

**"Don't Follow the First Path"** — Explore the branches before choosing one.

### 13\.  Insight

"Tree of Thoughts extends Chain of Thought by exploring multiple reasoning paths rather than committing to one. This mirrors expert problem-solving behaviour and reduces premature commitment to suboptimal solutions. The tradeoff is cost — multiple reasoning chains multiplied by evaluation steps. It's appropriate for complex strategic decisions where the cost of a wrong choice exceeds the cost of extra computation."

### 14\. Before vs After

**Before:**

```
How can we improve customer response time?
```

Gets one idea, maybe the first obvious one.

**After:** ToT prompt. Gets three distinct, evaluated approaches with a justified recommendation.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|Very High|
|Latency Impact|Slow|
|Reliability|High for complex decisions|

**Worth it when:** The decision being made has significant strategic or financial consequences.

\---

## Technique 13 — Retrieval-Augmented Prompting (RAG Prompting)

\---

### 1\. Simple Explanation

Instead of asking the AI to rely on its training knowledge (which might be outdated or wrong), you first retrieve relevant, current documents and inject them directly into the prompt. The AI answers from the documents you gave it.

### 2\. 100-Word Deep Explanation

RAG prompting solves one of AI's biggest problems: knowledge cutoffs and hallucination. The model's training data has a cutoff date and doesn't contain your company's internal documents, latest reports, or proprietary data. RAG retrieves relevant documents from an external knowledge base — a database, document store, or search system — and injects them into the prompt as context. The model then answers based on those documents rather than its training data. This dramatically reduces hallucination (because the answer is in the documents) and keeps knowledge current without retraining the model.

### 3\. Why It Works

The model's instructions say "answer based on this context." When you provide relevant, accurate documents, the model has both the capability to understand and synthesise them AND the instruction to use them as the source of truth. This anchors the answer to real, current information.

### 4\. Best Use Cases

* Internal knowledge base chatbots
* Policy and compliance Q\&A
* Customer support with product documentation
* Research assistants with access to papers
* Legal review with case law
* Any domain requiring current, proprietary, or specialised knowledge

### 5\. When Not to Use

* General knowledge questions the model handles well from training
* When retrieval quality is poor (bad retrieval → worse answer than no retrieval)
* When documents are too long to fit in context meaningfully
* Highly creative tasks that don't need factual grounding

### 6\. Real-World Example

**HR/Internal Policy:** An employee HR chatbot retrieves the relevant sections of the company's HR handbook before answering questions about leave policy, benefits, or procedures — giving accurate company-specific answers instead of generic HR advice.

### 7\. Sample Prompt

```
<instruction>
Answer the employee's question using ONLY the information provided in the retrieved documents below.
If the answer is not in the documents, say: "I don't have that information — please contact HR directly."
Do not use general knowledge. Cite which document your answer comes from.
</instruction>

<retrieved\\\_documents>
Document 1 — Annual Leave Policy (updated March 2024):
"Full-time employees are entitled to 28 days annual leave per year, including bank holidays. Leave must be requested via the HR portal with a minimum of 2 weeks' notice..."

Document 2 — Remote Work Policy (updated January 2024):
"Employees may work remotely up to 3 days per week with manager approval..."
</retrieved\\\_documents>

<employee\\\_question>
How many days of annual leave do I get, and how do I request them?
</employee\\\_question>
```

### 8\. Prompt Breakdown

* **Strict instruction** — "ONLY the information in the documents" prevents hallucination
* **Explicit fallback** — tells the model what to say when the answer isn't in documents
* **Source citation instruction** — builds trust and auditability
* **Delimiters** — clearly separate instruction, documents, and question
* **Documents clearly labelled** — with dates, so the model knows they're current

### 9\. Expected Output Example

```
Based on the Annual Leave Policy (updated March 2024): 
Full-time employees receive 28 days annual leave per year including bank holidays. 
To request leave, use the HR portal with at least 2 weeks' notice.
```

### 10\. Common Mistakes

* Injecting irrelevant documents — noise degrades quality
* Not instructing the model to only use the documents — it falls back to training data
* Providing no fallback instruction — model invents answers when documents don't cover the question
* Documents too long for context window — truncation causes missing info

### 11\. Pro Tips

* Always retrieve the most relevant chunks, not entire documents
* Include document metadata (date, source name) so the model can cite accurately
* Test with questions not covered by any document to ensure the fallback works
* For production systems, track which retrieved chunks are actually used — this tells you if your retrieval is working

### 12\. Memory Trick

**"Find First, Then Answer"** — Retrieve the facts, then form the response from them.

### 13\.  Insight

"RAG prompting separates retrieval from generation, combining a search system with an LLM. The model acts as an intelligent synthesiser of retrieved content rather than a knowledge store. Key success factors are retrieval quality — garbage in, garbage out — and clear instructions to stay grounded in the retrieved context. RAG is now the standard approach for any enterprise knowledge base application."

### 14\. Before vs After

**Before:**

```
How many days of annual leave do I get?
```

Model gives generic legal minimum or makes up company policy.

**After:** RAG prompt with actual policy. Model gives the correct company-specific answer.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|High (documents add significant context tokens)|
|Latency Impact|Medium-High (retrieval step + longer context)|
|Reliability|Very High when retrieval is good|

**Worth it when:** Accuracy is non-negotiable and information is domain-specific, proprietary, or time-sensitive. Almost always in enterprise contexts.

\---

## Technique 14 — Prompt Chaining

\---

### 1\. Simple Explanation

You break a complex task into smaller steps and use the output of one prompt as the input to the next. Like an assembly line, but for AI tasks.

### 2\. 100-Word Deep Explanation

Prompt chaining orchestrates multiple prompts in sequence, where each prompt handles one specific sub-task and its output feeds the next. Instead of cramming a complex, multi-step task into one massive prompt (which confuses the model and reduces quality), you break it into clean stages. This mirrors professional workflows — a research assistant gathers data, a writer drafts, an editor reviews. Each stage is optimised for its specific task. Chaining also makes systems more maintainable: you can update, test, and swap individual steps without redesigning the whole pipeline.

### 3\. Why It Works

Complex tasks have multiple distinct cognitive steps. Trying to do all of them in one prompt dilutes focus and degrades quality at each step. Individual prompts focused on one task perform that task better. Chaining lets each prompt be the best possible version of itself.

### 4\. Best Use Cases

* Content production pipelines (research → outline → draft → edit → format)
* Data processing workflows
* Multi-step analysis (collect data → analyse → interpret → recommend)
* Document transformation (extract → classify → summarise → route)
* Any workflow with distinct stages that each require different expertise or format

### 5\. When Not to Use

* Simple tasks that don't need multiple steps
* When latency is critical (chains are slower)
* When errors in early steps cascade into later ones without validation
* When each step is so dependent on the full context that splitting doesn't help

### 6\. Real-World Example

**Content Marketing:** A marketing team chains: Prompt 1 — Extract key themes from a product update document. Prompt 2 — Write social media posts for each theme. Prompt 3 — Check posts against brand guidelines. Prompt 4 — Format final approved posts by platform.

### 7\. Sample Prompt Chain

**Prompt 1 — Extract:**

```
Extract the 5 most important facts from the following press release.
Return as a numbered list. Facts only, no commentary.
\\\[press release text]
```

**Prompt 2 — Draft (uses output of Prompt 1):**

```
Using these 5 facts:
\\\[output from Prompt 1]

Write a LinkedIn post announcing this news. 
Audience: B2B technology decision-makers. 
Tone: Professional, confident. 150 words maximum.
```

**Prompt 3 — Review (uses output of Prompt 2):**

```
Review the following LinkedIn post for brand voice compliance:
\\\[output from Prompt 2]

Check against these brand guidelines:
- Never use the word "innovative" or "revolutionary"
- Always mention the customer benefit, not just the feature
- Must include a call to action

Return: \\\[APPROVED] or \\\[NEEDS REVISION: list specific issues]
```

### 8\. Prompt Breakdown

Each prompt is clean and focused. Each step has one job. Outputs are formatted to be useful as inputs for the next step.

### 9\. Expected Output Example

Chain produces a reviewed, approved LinkedIn post that's accurate (came from real press release facts), well-written (dedicated drafting step), and brand-compliant (dedicated review step).

### 10\. Common Mistakes

* Passing raw, unformatted output between steps — clean up between chains
* No error handling — a bad output at step 2 cascades to ruin step 3
* Chain steps that are too granular (unnecessary overhead) or too broad (defeats the purpose)
* No validation checkpoint between steps

### 11\. Pro Tips

* Add a validation step between critical stages to catch errors before they propagate
* Log inputs and outputs at each step for debugging
* Make early steps produce clean, structured outputs that are easy for later steps to consume
* Consider running steps in parallel where they don't depend on each other

### 12\. Memory Trick

**"Assembly Line, Not One Big Machine"** — Each station does one thing well.

### 13\.  Insight

"Prompt chaining decomposes complex tasks into discrete, optimisable steps where each prompt has a single, clear responsibility. This improves quality at each stage, makes the pipeline debuggable, and allows independent optimisation and testing of each step. Error isolation is the key operational benefit — when a chain fails, you know exactly which step broke."

### 14\. Before vs After

**Before:**

```
Read this press release and write a compliant LinkedIn post that I can publish today.
```

One massive, confused prompt that tries to extract, draft, and validate simultaneously.

**After:** Three-step chain. Each step nails its specific job.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|Medium-High (multiple API calls)|
|Latency Impact|Medium-High (sequential steps)|
|Reliability|Very High (errors are isolated)|

**Worth it when:** Tasks are complex enough that quality matters more than speed. Most professional content and data pipelines qualify.

\---

## Technique 15 — Meta Prompting

\---

### 1\. Simple Explanation

You use AI to write or improve prompts for AI. You prompt the AI to build better prompts for you.

### 2\. 100-Word Deep Explanation

Meta prompting is the practice of using a language model to generate, critique, or improve prompts rather than directly producing end-user content. Instead of hand-crafting every prompt, you describe the task and ask the model to create the optimal prompt for it. Or you give it a weak prompt and ask it to make it better. This creates a feedback loop — you use AI expertise to improve your AI instructions. Meta prompting is powerful for prompt engineering at scale, where you need many high-quality prompts across many tasks, and for discovering prompt formulations you wouldn't have thought of yourself.

### 3\. Why It Works

The model has seen millions of examples of good and bad prompting in its training data. When asked to generate or critique prompts, it applies this understanding. It essentially leverages its training on human communication patterns to generate optimised instructions for… itself. Slightly circular, but genuinely useful.

### 4\. Best Use Cases

* Creating prompt templates at scale
* Improving existing prompts that aren't performing well
* Learning prompt engineering by seeing the model's suggestions
* Bootstrapping a prompt library for a new domain
* A/B testing prompt variations
* Prompt governance — having the model check prompts for compliance

### 5\. When Not to Use

* When you need a specific, precisely controlled prompt (generate it yourself)
* When the meta-prompt process itself introduces errors
* When cost is constrained and meta-prompting adds unnecessary overhead

### 6\. Real-World Example

**Enterprise AI Team:** A company uses meta prompting to generate first drafts of prompts for each of their 20 business use cases, which their prompt engineers then refine — cutting initial development time significantly.

### 7\. Sample Prompt

```
You are an expert prompt engineer.

I need to create a prompt for the following business task:
Task: Help customer service agents quickly summarise a customer call and extract follow-up actions.
Audience using the prompt: Customer service agents (not technical)
System: The output will be pasted into a CRM system
Quality bar: Must be consistent, structured, and fast

Generate an optimised prompt for this task. 
The prompt should:
- Be clear for a non-technical user
- Produce structured output suitable for a CRM
- Take less than 2 minutes for the AI to process
- Include role, task, constraints, and output format

Also explain your design choices.
```

### 8\. Prompt Breakdown

* **Role: expert prompt engineer** — activates the model's knowledge of prompt design
* **Detailed task spec** — the model needs to understand the context to build a good prompt
* **Specific requirements** — audience, system, quality bar give the model design constraints
* **"Explain your choices"** — turns this into a learning exercise, not just a tool

### 9\. Expected Output Example

The model produces a full, structured prompt with role, task, constraints, and a CRM-formatted output schema — plus an explanation of why each component was included. You edit and deploy.

### 10\. Common Mistakes

* Being vague about the task — the model builds a generic prompt
* Not specifying the audience or system — misses critical design decisions
* Using the generated prompt without testing it
* Not asking for explanation of design choices (miss the learning opportunity)

### 11\. Pro Tips

* Always test meta-generated prompts with real examples before deploying
* Use meta prompting iteratively: generate, test, critique the prompt, regenerate
* Ask the model to generate multiple prompt versions, then select the best
* Use meta prompting to maintain prompt libraries: "Review this existing prompt and suggest improvements"

### 12\. Memory Trick

**"Ask the AI to Write the Instructions for the AI"** — Let the expert write the spec.

### 13\.  Insight

"Meta prompting leverages the model's training on prompt patterns to generate or improve prompts. It accelerates prompt development at scale and can surface formulations that human engineers wouldn't discover manually. The key limitation is that meta-generated prompts must be tested — the model doesn't have access to your specific data or quality bar, so generated prompts are starting points, not finished products."

### 14\. Before vs After

**Before:** An engineer spends 2 hours crafting a prompt by trial and error.

**After:** Meta prompt generates a strong starting point in 2 minutes. Engineer tests and refines. Total time: 30 minutes.

### 15\. Cost Awareness

|Factor|Rating|
|-|-|
|Token Usage|Medium (meta call + generated prompt)|
|Latency Impact|Medium|
|Reliability|Medium (requires testing)|

**Worth it when:** You need to create many prompts or your current prompts are underperforming and you're not sure why.

\---

<a name="part-3"></a>

# Part 3 — Technique Comparison Tables

## 1\. Easiest to Hardest

|Rank|Technique|Difficulty|
|-|-|-|
|1|Zero-Shot|⭐ Beginner|
|2|Role Prompting|⭐ Beginner|
|3|Constraint Prompting|⭐ Beginner|
|4|Output Format Prompting|⭐ Beginner|
|5|Delimiter Prompting|⭐⭐ Easy|
|6|Few-Shot|⭐⭐ Easy|
|7|Chain of Thought|⭐⭐ Easy|
|8|Reflection Prompting|⭐⭐ Easy|
|9|Step-Back Prompting|⭐⭐⭐ Medium|
|10|Prompt Chaining|⭐⭐⭐ Medium|
|11|Meta Prompting|⭐⭐⭐ Medium|
|12|RAG Prompting|⭐⭐⭐⭐ Advanced|
|13|Self-Consistency|⭐⭐⭐⭐ Advanced|
|14|ReAct|⭐⭐⭐⭐ Advanced|
|15|Tree of Thoughts|⭐⭐⭐⭐⭐ Expert|

## 2\. Lowest to Highest Token Cost

|Rank|Technique|Token Cost|
|-|-|-|
|1|Zero-Shot|Very Low|
|2|Role Prompting|Very Low|
|3|Delimiter Prompting|Very Low|
|4|Constraint Prompting|Low|
|5|Output Format Prompting|Low-Medium|
|6|Few-Shot|Medium|
|7|Step-Back Prompting|Medium|
|8|Prompt Chaining|Medium-High|
|9|Meta Prompting|Medium-High|
|10|Chain of Thought|High|
|11|Reflection Prompting|High|
|12|RAG Prompting|High|
|13|ReAct|High|
|14|Tree of Thoughts|Very High|
|15|Self-Consistency|Very High (×N runs)|

## 3\. Best for Different Goals

|Goal|Best Techniques|
|-|-|
|Beginners|Zero-Shot, Role, Constraint, Few-Shot|
|Enterprise systems|RAG, Prompt Chaining, Output Format, Delimiter|
|AI agents|ReAct, Prompt Chaining, Tree of Thoughts|
|Complex reasoning|CoT, Tree of Thoughts, Step-Back, Reflection|
|Structured outputs|Output Format, Delimiter, Few-Shot|
|Highest reliability|Self-Consistency, Reflection, RAG|
|Lowest cost|Zero-Shot, Role, Constraint, Output Format|
|Fastest results|Zero-Shot, Role, Output Format|

\---

<a name="part-4"></a>

# Part 4 — Learning Path

## Beginner Level — Start Here

**Goal:** Get useful, consistent answers for common tasks.

**Learn first:**

1. Zero-Shot — understand the baseline
2. Role Prompting — instant quality upgrade
3. Constraint Prompting — control your outputs
4. Output Format Prompting — get machine-readable results
5. Few-Shot — when zero-shot isn't enough

**Practice tasks:**

* Write a professional email with Role + Constraint
* Extract data from text using Output Format
* Classify customer messages using Few-Shot

**You're ready for intermediate when:** Your prompts reliably give you the format and tone you want on the first try.

\---

## Intermediate Level — Build Better Systems

**Goal:** Handle complex tasks, build repeatable workflows.

**Learn next:**

1. Chain of Thought — for multi-step reasoning
2. Delimiter Prompting — for complex, multi-section prompts
3. Reflection Prompting — for quality checking
4. Step-Back Prompting — for diagnostic and strategic tasks
5. Prompt Chaining — for multi-stage workflows

**Practice tasks:**

* Build a 3-step content production pipeline
* Use CoT for a financial or compliance calculation
* Add a reflection step to a document generation workflow

**You're ready for advanced when:** You're building systems, not just writing individual prompts.

\---

## Advanced Level — Production Systems

**Goal:** Build reliable, scalable AI-powered workflows.

**Learn next:**

1. RAG Prompting — ground AI in real documents
2. Meta Prompting — generate and improve prompts at scale
3. ReAct — build AI agents with tool access
4. Self-Consistency — maximise reliability for high-stakes tasks

**Practice tasks:**

* Build a document Q\&A system with RAG
* Create a ReAct agent that queries a database
* Use meta prompting to generate a prompt library

**You're ready for expert when:** Your AI systems run in production, have monitoring, and have been through real-world failure scenarios.

\---

## Expert Level — Govern and Scale

**Goal:** Manage AI systems at enterprise scale with quality, cost, and safety controls.

**Learn:**

1. Tree of Thoughts — for complex strategic analysis
2. Prompt versioning and A/B testing
3. Cost monitoring and optimisation
4. Prompt governance frameworks
5. Evaluation metrics and human review loops

**You're at expert level when:** You're responsible for an organisation's AI output quality, not just your own.

\---

<a name="part-5"></a>

# Part 5 — Combining Techniques

The real power in prompt engineering comes from knowing which techniques to layer together.

\---

## Combo 1 — Role + Constraint + Output Format

**Why combine:** Role sets the expert perspective. Constraints prevent going off-track. Output format makes the result immediately usable.

**Use case:** Any production prompt where you need professional-quality, machine-readable output.

**Example:**

```
ROLE: You are a senior copywriter specialising in B2B SaaS.

TASK: Write a subject line and preview text for a cold email campaign targeting CTOs.

CONSTRAINTS:
- Subject line: 6–8 words maximum
- No clickbait language
- No emojis
- Preview text: 80–100 characters

OUTPUT FORMAT:
{
  "subject\\\_line": "...",
  "preview\\\_text": "..."
}
```

**Cost vs quality:** Very low cost (short output), very high consistency. This combo is the workhorse of enterprise prompt engineering.

\---

## Combo 2 — RAG + Chain of Thought

**Why combine:** RAG gives the model real, accurate source material. CoT forces it to reason through that material step by step rather than jumping to conclusions.

**Use case:** Compliance checks, medical information, legal interpretation, any domain where accuracy and auditability matter.

**Example:**

```
<retrieved\\\_documents>
\\\[Relevant policy sections retrieved from document store]
</retrieved\\\_documents>

Based ONLY on the retrieved documents above, step through this analysis:

Step 1: What does the policy say about this specific situation?
Step 2: Are there any exceptions or edge cases mentioned?
Step 3: What is the correct interpretation based on the policy language?
Step 4: Final determination with document citation.
```

**Cost vs quality:** High cost (RAG tokens + CoT output), very high reliability. Worth it in regulated industries where wrong answers have legal or compliance consequences.

\---

## Combo 3 — Few-Shot + Output Format

**Why combine:** Few-shot teaches the pattern. Output format locks in the structure. Together, they produce perfectly consistent, structured outputs across thousands of runs.

**Use case:** Data extraction pipelines, classification systems, any task where output will be parsed programmatically.

**Example:**

```
Extract complaint information. Return as JSON.

Example 1:
Input: "My order arrived damaged and customer service hasn't responded."
Output: {"category": "damaged\\\_goods", "secondary": "poor\\\_service", "urgency": "high"}

Example 2:
Input: "The tracking number doesn't work on your website."
Output: {"category": "tracking\\\_issue", "secondary": null, "urgency": "low"}

Now extract:
Input: "I've been waiting 3 weeks and my package still hasn't arrived."
Output:
```

**Cost vs quality:** Low-medium cost. Very high consistency. The standard approach for production classification and extraction pipelines.

\---

## Combo 4 — Chain of Thought + Reflection

**Why combine:** CoT produces step-by-step reasoning. Reflection catches errors in that reasoning before the final answer is committed to. Double-checking, automated.

**Use case:** Complex calculations, legal analysis, medical triage, any reasoning task where errors have significant consequences.

**Example:**

```
PART 1 — Reasoning:
A company has £500,000 budget. They need to hire 3 senior developers (£95,000 each), 
1 designer (£65,000), and 1 project manager (£70,000). 
Equipment budget is £3,000 per person. Factor in 30% employer costs on top of salary.

Work through the total cost step by step.

PART 2 — Reflection:
Review your calculation:
- Have you included employer costs on ALL roles?
- Have you counted equipment for ALL staff?
- Is the total correct?
If you find an error, correct it and explain what was wrong.

PART 3 — Final Answer:
Is this plan within budget? By how much, over or under?
```

**Cost vs quality:** High cost. Very high reliability for calculation tasks. Appropriate when financial errors would be costly.

\---

## Combo 5 — Prompt Chaining + Meta Prompting

**Why combine:** Meta prompting generates the individual prompts. Chaining connects them into a pipeline. Together, they let you build and iterate on prompt systems quickly.

**Use case:** Rapid prototyping of new AI workflows, scaling prompt development across a large product team.

**How it works:**

1. Use meta prompting to generate each step's prompt
2. Manually review and refine each generated prompt
3. Connect the prompts in a chain
4. Test the end-to-end chain with real examples
5. Use meta prompting to suggest improvements to any step that underperforms

**Cost vs quality:** Medium cost for design, then optimised cost per run once the chain is efficient.

\---

## Combo 6 — ReAct + RAG

**Why combine:** ReAct provides the action loop for agents. RAG provides the knowledge retrieval. Together, the agent can retrieve current documents AND reason about them dynamically.

**Use case:** Advanced AI assistants that need to search through documents, gather information, reason about it, and produce grounded answers.

**Use case example:** A customer support agent that retrieves the customer's account data AND relevant policy documents before reasoning about how to resolve a complex complaint.

**Cost vs quality:** Very high cost (multiple retrieval + reasoning loops). High reliability. Only appropriate when the task genuinely requires dynamic, multi-source information gathering.

\---

<a name="part-6"></a>

# Part 6 — Real-World Prompt Library

These are production-ready prompts. Copy, adapt, and deploy.

\---

## 1\. Resume Screening

**Technique:** Few-Shot + Output Format + Constraint
**Why:** Needs consistent scoring across many CVs, structured for ATS integration, few-shot ensures fair criteria application.

```
You are an experienced HR recruiter screening CVs for a Data Analyst role.

ROLE REQUIREMENTS:
- SQL proficiency (required)
- Python or R (required)
- 2+ years experience in data analysis
- Dashboard/visualisation experience (preferred)
- Finance or healthcare domain experience (preferred)

SCORING CRITERIA:
Score each criterion: 0 (not present), 1 (partially meets), 2 (fully meets)

Example:
CV: "3 years as business analyst, advanced SQL, Tableau certified, Excel expert, no Python"
Output:
{
  "sql": 2, "python\\\_r": 0, "years\\\_experience": 2,
  "visualisation": 2, "domain": 0,
  "total\\\_score": 6, "max\\\_score": 10,
  "recommendation": "INTERVIEW — strong SQL and viz, missing Python",
  "red\\\_flags": \\\[]
}

Now evaluate this CV:
\\\[CV TEXT]

Return JSON in the exact same format. Be objective. Do not infer skills not explicitly mentioned.
```

**Expected output:** Consistent JSON scoring, comparable across candidates.
**Cost sensitivity:** Low — short output per CV.

\---

## 2\. SQL Generation

**Technique:** Role + Few-Shot + Output Format + Constraint
**Why:** Role focuses the model on the right database dialect. Few-shot shows the coding style. Constraints prevent unsafe SQL.

```
You are a senior SQL developer working with PostgreSQL.

CONSTRAINTS:
- Read-only queries only (SELECT statements)
- Always use explicit column names, never SELECT \\\*
- Always include a comment explaining what the query does
- Use CTEs for complex queries, not subqueries

DATABASE SCHEMA:
- orders (order\\\_id, customer\\\_id, order\\\_date, total\\\_amount, status)
- customers (customer\\\_id, name, email, region, created\\\_date)
- order\\\_items (item\\\_id, order\\\_id, product\\\_id, quantity, unit\\\_price)
- products (product\\\_id, name, category, price)

Example request: "Top 5 customers by revenue this year"
Example query:
-- Top 5 customers by total revenue in current year
SELECT 
    c.customer\\\_id,
    c.name,
    SUM(o.total\\\_amount) as total\\\_revenue
FROM customers c
JOIN orders o ON c.customer\\\_id = o.customer\\\_id
WHERE EXTRACT(YEAR FROM o.order\\\_date) = EXTRACT(YEAR FROM CURRENT\\\_DATE)
  AND o.status = 'completed'
GROUP BY c.customer\\\_id, c.name
ORDER BY total\\\_revenue DESC
LIMIT 5;

Now write a query for: \\\[USER REQUEST]
```

**Expected output:** Safe, well-commented, style-consistent SQL.
**Cost sensitivity:** Low.

\---

## 3\. Customer Complaint Handling

**Technique:** Role + RAG + Constraint
**Why:** Role ensures empathetic tone. RAG provides accurate policy information. Constraints protect against making commitments the company can't keep.

```
You are a professional customer service agent for \\\[Company Name].
You are empathetic, solution-focused, and calm even with upset customers.

COMPANY POLICIES (use these to inform your response):
\\\[Retrieved policy sections here]

CONSTRAINTS:
- Do not make specific compensation promises without manager approval
- Do not criticise other team members or departments
- Always acknowledge the customer's frustration before explaining
- End every response with a specific next step
- Maximum 200 words

Customer complaint:
\\\[COMPLAINT TEXT]

Write a response that: acknowledges their frustration, explains what happened (if known), 
states what action is being taken, gives a timeline.
```

**Expected output:** Empathetic, accurate, policy-compliant response with clear next steps.
**Cost sensitivity:** Low-medium (policy retrieval adds tokens).

\---

## 4\. Meeting Summarisation

**Technique:** Output Format + Delimiter + Constraint
**Why:** Format ensures consistent, structured output. Delimiters prevent the transcript content from being treated as instructions. Constraints keep it focused.

```
<instruction>
Summarise the following meeting transcript. Extract only verified information — 
do not infer decisions that weren't explicitly stated.

Return in this exact format:

## Meeting Summary
\\\*\\\*Date:\\\*\\\* \\\[extract from transcript or "Not stated"]
\\\*\\\*Attendees:\\\*\\\* \\\[list names mentioned]
\\\*\\\*Duration:\\\*\\\* \\\[if mentioned]

## Decisions Made
- \\\[Each confirmed decision as a bullet]

## Action Items
| Owner | Task | Deadline |
|---|---|---|
| | | |

## Open Questions
- \\\[Items discussed but not resolved]

## Next Meeting
\\\[If mentioned, else: Not scheduled]
</instruction>

<transcript>
\\\[MEETING TRANSCRIPT]
</transcript>
```

**Expected output:** Structured meeting notes suitable for distribution and CRM entry.
**Cost sensitivity:** Medium (longer transcripts = more input tokens).

\---

## 5\. Root Cause Analysis

**Technique:** Role + Step-Back + Chain of Thought
**Why:** Role brings engineering expertise. Step-back ensures proper problem categorisation. CoT produces auditable reasoning.

```
You are a senior site reliability engineer with expertise in distributed systems.

An incident has occurred:
INCIDENT: \\\[Describe what happened, when, impact]
SYMPTOMS: \\\[List observable symptoms]
TIMELINE: \\\[Events leading up to the incident]

STEP 1 — Categorise: What are the possible root cause categories for this type of incident?
(e.g., infrastructure failure, code deployment, external dependency, configuration change, traffic spike)

STEP 2 — Analyse: For each category, assess likelihood given the symptoms and timeline.

STEP 3 — Hypothesis: State your most likely root cause and the evidence supporting it.

STEP 4 — Verification: What three diagnostic checks would confirm or rule out your hypothesis?

STEP 5 — Immediate Action: What should be done right now to prevent further impact?
```

**Expected output:** Structured RCA with actionable verification steps.
**Cost sensitivity:** Medium-high (CoT adds output tokens).

\---

## 6\. Code Review

**Technique:** Role + Reflection + Output Format + Constraint
**Why:** Role brings technical expertise. Reflection format (review rather than generate) activates critical analysis mode. Output format makes findings actionable.

```
You are a senior software engineer conducting a formal code review.

Review the following code for:
1. Bugs or logic errors
2. Security vulnerabilities
3. Performance issues
4. Readability and maintainability
5. Missing error handling
6. Test coverage gaps

For each issue found, return in this format:

ISSUE #\\\[n]
- Severity: Critical / High / Medium / Low
- Category: \\\[Bug / Security / Performance / Readability / Error Handling / Testing]
- Line(s): \\\[line numbers if applicable]
- Problem: \\\[what is wrong]
- Fix: \\\[specific recommendation]

End with: OVERALL RATING: \\\[Approved / Approved with changes / Rejected]

Code to review:
```\\\[language]
\\\[CODE]
```

Be thorough. Do not approve code with any Critical or High severity issues.

```

\\\*\\\*Expected output:\\\*\\\* Structured review with severity-rated findings and actionable fixes.
\\\*\\\*Cost sensitivity:\\\*\\\* Medium.

---

## 7. Financial Report Summary

\\\*\\\*Technique:\\\*\\\* Role + RAG + Output Format + Constraint
\\\*\\\*Why:\\\*\\\* Role gives finance context. RAG grounds the summary in actual report numbers. Output format makes it executive-ready. Constraints prevent technical jargon.

```

You are a financial analyst preparing an executive briefing.

<financial\_report>
\[REPORT CONTENT]
</financial\_report>

Summarise this financial report for a non-finance executive audience.

OUTPUT FORMAT:

## Executive Financial Briefing — \[Period]

**Headline:** \[One sentence: are we ahead or behind?]

**Key Numbers:**

|Metric|Actual|Target|Variance|
|-|-|-|-|

**Top 3 Positives:**
1.
2.
3.

**Top 3 Concerns:**
1.
2.
3.

**Recommended Actions:**
1.
2.

CONSTRAINTS:

* No accounting jargon (explain any technical terms if unavoidable)
* Dollar/currency figures only — no percentages unless essential
* Maximum 400 words total
* Base all statements on the report only — do not add external context

```

\\\*\\\*Expected output:\\\*\\\* Boardroom-ready summary any executive can act on.
\\\*\\\*Cost sensitivity:\\\*\\\* Medium-high (report content adds input tokens).

---

## 8. Travel Planner

\\\*\\\*Technique:\\\*\\\* Role + Chain of Thought + Output Format
\\\*\\\*Why:\\\*\\\* Role activates travel expertise. CoT ensures logical day planning. Output format produces a usable itinerary.

```

You are an experienced travel consultant specialising in personalised itineraries.

TRIP DETAILS:

* Destination: \[City/Country]
* Duration: \[X days]
* Travel dates: \[dates]
* Group: \[e.g., couple, family with 2 kids aged 8 and 12, solo traveller]
* Budget level: \[budget / mid-range / luxury]
* Interests: \[e.g., history, food, outdoor activities, nightlife]
* Must avoid: \[e.g., crowded tourist traps, excessive walking due to mobility issue]

Think through:

1. What are the logical geographic clusters of attractions to minimise travel time?
2. What's the right pace for this group?
3. What time of day suits each activity type?

Then produce a day-by-day itinerary:

## Day \[N] — \[Theme]

**Morning:** \[Activity] — \[Duration] — \[Why this works for this group]
**Lunch:** \[Recommendation] — \[Price range]
**Afternoon:** \[Activity]
**Evening:** \[Dinner + evening activity]
**Accommodation tip:** \[if relevant]

Include: Estimated daily spend. One "local tip" per day.

```

\\\*\\\*Expected output:\\\*\\\* Practical, personalised itinerary the traveller can actually follow.
\\\*\\\*Cost sensitivity:\\\*\\\* Medium.

---

## 9. Study Coach

\\\*\\\*Technique:\\\*\\\* Role + Constraint + Reflection
\\\*\\\*Why:\\\*\\\* Role sets a coaching (not just answering) approach. Constraints prevent giving direct answers (forces learning). Reflection builds metacognition.

```

You are a patient, encouraging study coach helping a student prepare for \[exam/subject].

COACHING PRINCIPLES:

* Never give the answer directly — guide the student to find it
* Ask questions that help them think, not questions they can't answer
* Celebrate partial understanding before building on it
* When they make an error, help them discover it themselves

STUDENT QUESTION / PROBLEM:
\[Student input]

STEP 1 — Assess: What does their question/answer tell you about their current understanding?
STEP 2 — Guide: What question can you ask to move them one step forward?
STEP 3 — Check: After responding to them, ask: "Does that make sense? Can you explain it back to me in your own words?"

CONSTRAINT: Do not write the final answer to their question. Your job is to make them find it.

```

\\\*\\\*Expected output:\\\*\\\* Socratic coaching response that builds genuine understanding.
\\\*\\\*Cost sensitivity:\\\*\\\* Low.

---

## 10. Internal Policy Chatbot

\\\*\\\*Technique:\\\*\\\* RAG + Constraint + Output Format + Delimiter
\\\*\\\*Why:\\\*\\\* RAG ensures accurate, current policy information. Constraints prevent liability issues. Format makes answers clear and actionable.

```

<system\_instructions>
You are an internal HR policy assistant for \[Company Name].
Answer employee questions using ONLY the policy documents provided.
If the answer is not in the provided documents, say exactly:
"I don't have that information. Please contact HR at \[email] or \[phone]."
Do not speculate or use general knowledge about employment law.
Do not give legal advice.
</system\_instructions>

<retrieved\_policies>
\[Retrieved relevant policy sections]
</retrieved\_policies>

<employee\_question>
\[Question]
</employee\_question>

Answer format:
**Answer:** \[Direct answer in plain English]
**Policy source:** \[Document name and section]
**What to do next:** \[Specific action if applicable]
**Need more help?** Contact HR at \[details]

```

\\\*\\\*Expected output:\\\*\\\* Accurate, sourced, actionable policy answer with clear escalation path.
\\\*\\\*Cost sensitivity:\\\*\\\* Medium (retrieval adds tokens, but keeps answers accurate and reduces HR team overhead).

---

<a name="part-7"></a>
# Part 7 — Debugging Bad Prompts

When your prompt fails, the problem is almost always in one of these eight places.

---

## Failure 1 — The Vague Prompt

\\\*\\\*Why it fails:\\\*\\\* The AI has to guess what you actually want. It picks the most statistically common interpretation, which often isn't your interpretation.

\\\*\\\*Bad prompt:\\\*\\\*
```

Write something about our product.

```

\\\*\\\*Why it's bad:\\\*\\\* What kind of writing? How long? For who? What product? What goal?

\\\*\\\*Improved prompt:\\\*\\\*
```

Write a 200-word product description for our inventory management software.
Audience: Warehouse operations managers at mid-size manufacturers.
Tone: Professional, practical, benefits-focused.
Include: One concrete example of time saved.

```

\\\*\\\*How to test the fix:\\\*\\\* Ask a colleague to read only the prompt (not the output). Can they predict what the AI will produce? If not, it's still too vague.

---

## Failure 2 — Conflicting Instructions

\\\*\\\*Why it fails:\\\*\\\* The AI tries to satisfy all instructions simultaneously and ends up satisfying none of them properly.

\\\*\\\*Bad prompt:\\\*\\\*
```

Be concise but cover every detail. Be formal but conversational.
Write for experts but make it understandable to everyone.

```

\\\*\\\*Why it's bad:\\\*\\\* These instructions directly contradict each other.

\\\*\\\*Improved prompt:\\\*\\\*
```

Write a technical overview for a software engineer audience.
Length: 300 words.
Include: Architecture decisions and trade-offs.
Skip: Basic explanations of standard concepts (e.g., REST APIs, SQL).

```

\\\*\\\*How to test the fix:\\\*\\\* Read your constraints list. Do any two of them pull in opposite directions? Remove or reconcile.

---

## Failure 3 — Too Many Tasks at Once

\\\*\\\*Why it fails:\\\*\\\* Cognitive overload for the model. Quality drops on all tasks when there are more than 2–3 simultaneous objectives.

\\\*\\\*Bad prompt:\\\*\\\*
```

Read this document, summarise it, find all the action items,
check for inconsistencies, translate the key points to French,
and suggest three improvements to the document structure.

```

\\\*\\\*Why it's bad:\\\*\\\* Six distinct tasks, all interleaved. Quality degrades significantly across all of them.

\\\*\\\*Improved approach:\\\*\\\* Chain the tasks. Prompt 1 = summarise. Prompt 2 = action items. Prompt 3 = consistency check. Etc.

\\\*\\\*How to test the fix:\\\*\\\* Can you describe each task as a single job? If not, it's still too many.

---

## Failure 4 — No Output Format Specified

\\\*\\\*Why it fails:\\\*\\\* The model defaults to prose. Your system expects JSON. Your app crashes.

\\\*\\\*Bad prompt:\\\*\\\*
```

Extract the customer name, order date, and total amount from this invoice.

```

\\\*\\\*Why it's bad:\\\*\\\* You'll get: "The customer name is John Smith and the order was placed on January 15th for a total of £245.00."

\\\*\\\*Improved prompt:\\\*\\\*
```

Extract the following from the invoice and return as JSON:
{
"customer\_name": "string",
"order\_date": "YYYY-MM-DD format",
"total\_amount": "number (no currency symbol)"
}

```

\\\*\\\*How to test the fix:\\\*\\\* Paste the output directly into your application. Does it work?

---

## Failure 5 — Hallucination

\\\*\\\*Why it fails:\\\*\\\* The model doesn't have the information and makes it up rather than admitting uncertainty.

\\\*\\\*Bad prompt:\\\*\\\*
```

What were our Q3 sales figures?

```

\\\*\\\*Why it's bad:\\\*\\\* The model doesn't have your internal data. It will either say it doesn't know (best case) or invent numbers that sound plausible (worst case).

\\\*\\\*Improved approach:\\\*\\\* Use RAG to provide the actual data. Or explicitly instruct: "If you don't have this information, say 'I don't know — please check your internal reporting system.' Do not estimate or guess."

\\\*\\\*How to test the fix:\\\*\\\* Ask a question you know the AI doesn't have data for. Does it hallucinate, or does it correctly say it doesn't know?

---

## Failure 6 — Poor or Missing Examples

\\\*\\\*Why it fails:\\\*\\\* The model produces something technically correct but not in your format or style.

\\\*\\\*Bad prompt:\\\*\\\*
```

Write a product update notification in our company's tone of voice.

```

\\\*\\\*Why it's bad:\\\*\\\* The model doesn't know your tone of voice. "Professional" means different things to different companies.

\\\*\\\*Improved prompt:\\\*\\\*
```

Write a product update notification in our company's tone of voice.

Example of our tone:
"Good news — your dashboard just got faster. We've cut load time by 40%
so you spend less time waiting and more time working. Here's what changed..."

Now write a notification for this update: \[UPDATE DETAILS]

```

\\\*\\\*How to test the fix:\\\*\\\* Show the output to someone who knows your brand. Do they recognise the voice?

---

## Failure 7 — Missing Context

\\\*\\\*Why it fails:\\\*\\\* The model lacks critical information and either guesses wrong or produces a generic answer that doesn't fit your situation.

\\\*\\\*Bad prompt:\\\*\\\*
```

Write an email response to this complaint.

```

\\\*\\\*Why it's bad:\\\*\\\* What company? What are the relevant policies? What authority does the responder have? What's the company's standard procedure?

\\\*\\\*Improved prompt:\\\*\\\*
```

You are a customer service agent at \[Company Name].
Our refund policy: full refund within 30 days, store credit only after 30 days.
You have authority to: process refunds, issue discount codes up to 20%, escalate to manager.

Customer complaint: \[COMPLAINT]
Write a response that follows our policies.

```

\\\*\\\*How to test the fix:\\\*\\\* Would the response still be accurate if the context you provided turned out to be different? If so, you've added context correctly.

---

## Failure 8 — Over-Constrained Prompts

\\\*\\\*Why it fails:\\\*\\\* Too many rules, some contradicting, the model gets confused or starts randomly breaking constraints.

\\\*\\\*Bad prompt:\\\*\\\*
```

Write in formal English. But be friendly. Use technical language.
But avoid jargon. Include all relevant details. But be under 50 words.
Make it creative. But don't deviate from the template.
Be original. But match these 5 examples exactly.

```

\\\*\\\*Why it's bad:\\\*\\\* These constraints are incoherent together.

\\\*\\\*Improved approach:\\\*\\\* Rank your constraints. Keep the top 3–5 that matter most. Remove the rest.

\\\*\\\*How to test the fix:\\\*\\\* Read your constraints aloud. If any two would give a person conflicting directions, simplify.

---

<a name="part-8"></a>
# Part 8 — Enterprise Prompt Engineering

When prompt engineering moves from personal productivity to business-critical systems, the rules change.

---

## Prompt Versioning

Treat prompts like code. Every prompt change should be tracked.

\\\*\\\*Minimum versioning practice:\\\*\\\*
- Store prompts in a version control system (Git works fine)
- Use semantic versioning: `v1.0.0` → `v1.0.1` (minor fix) → `v1.1.0` (improvement) → `v2.0.0` (breaking change)
- Keep a changelog: what changed, why, what the expected impact is
- Never overwrite production prompts without a staged rollout

\\\*\\\*Prompt metadata to track:\\\*\\\*
```yaml
prompt\\\_id: customer\\\_complaint\\\_classifier\\\_v1.2.0
created: 2024-01-15
last\\\_modified: 2024-03-20
owner: data\\\_team@company.com
model: gpt-4o
use\\\_case: Customer support ticket routing
avg\\\_tokens\\\_in: 350
avg\\\_tokens\\\_out: 45
avg\\\_cost\\\_per\\\_call: $0.006
success\\\_rate: 94.2%
```

\---

## A/B Testing Prompts

Before you assume a new prompt is better, test it.

**Basic A/B test process:**

1. Define your success metric upfront (accuracy, format compliance, user satisfaction rating)
2. Run Prompt A and Prompt B on the same set of test inputs (minimum 50, ideally 200+)
3. Score outputs against your metric
4. Only switch to the new prompt if improvement is statistically meaningful
5. Document the test results

**What to A/B test:**

* Role changes ("senior analyst" vs "data scientist")
* With vs without examples
* Different output format specifications
* Temperature settings (via API)
* Different constraint combinations

\---

## Evaluation Metrics

You need to measure prompt quality systematically, not just eyeball it.

|Metric|What it measures|How to measure|
|-|-|-|
|**Format compliance**|Does output match required structure?|Automated parser — pass/fail|
|**Factual accuracy**|Are stated facts correct?|Human review or RAG-grounded check|
|**Task completion**|Did it do what was asked?|Human rating 1-5|
|**Hallucination rate**|How often does it invent information?|Human review sample|
|**Length compliance**|Does output meet length constraints?|Automated word/char count|
|**Consistency**|Does same input reliably produce equivalent quality?|Run same prompt 10x, score variance|
|**Cost per query**|Average token cost|API billing data|

**Target: minimum 50 human-evaluated examples per prompt before production deployment.**

\---

## Latency Management

Every token costs time. Here's how to manage it.

**Latency reduction strategies:**

* **Shorter prompts** — Remove anything the model doesn't need. Audit your prompts regularly for fat.
* **Constrained outputs** — Short, structured outputs are faster than long prose.
* **Caching** — For queries that repeat often, cache the response rather than re-calling the API.
* **Asynchronous calls** — For batch processing, don't wait for each response before sending the next.
* **Parallel chains** — In Prompt Chaining, run independent steps simultaneously.
* **Model selection** — Use a faster, cheaper model for simple tasks and reserve powerful models for complex ones.

**Latency budget by task type:**

|Task|Acceptable Latency|Recommended Approach|
|-|-|-|
|Real-time chat|< 2 seconds|Zero-shot or simple few-shot, fast model|
|Form processing|< 10 seconds|Few-shot + output format|
|Document analysis|< 30 seconds|RAG + CoT, batch processing|
|Complex research|Minutes|Chain + ToT, async, background job|

\---

## Token Cost Optimisation

Cost = (input tokens + output tokens) × price per token × volume.

**Input token savings:**

* Remove redundant instructions
* Use compressed examples instead of verbose ones
* Reference shared context once (system prompt) instead of repeating it
* Use the minimum context window needed

**Output token savings:**

* Specify exact length constraints ("50 words maximum")
* Use structured formats (JSON, tables) — they're more token-efficient than prose explanations
* Tell the model not to explain what it's doing, just do it

**Model selection by task:**

|Task Complexity|Model Choice|
|-|-|
|Simple classification|Smallest capable model|
|Standard text generation|Mid-tier model|
|Complex reasoning|Full capability model|
|Agent tasks|Full capability model with tool access|

**Real cost example:** 10,000 support tickets classified daily. At 200 input tokens + 10 output tokens each:

* Smaller model at $0.002/1K tokens = $4.20/day = \~$1,500/year
* Large model at $0.03/1K tokens = $63/day = \~$23,000/year
* Same task, different model choice: $21,500 annual difference. Choose wisely.

\---

## Guardrails

Guardrails are the rules you build in to prevent the AI from doing harmful, embarrassing, or non-compliant things.

**Types of guardrails:**

**Prompt-level guardrails** — written directly into the prompt:

```
You must not:
- Give specific investment advice
- Make promises about delivery timelines
- Discuss competitor products
- Share pricing without confirmation from the sales team
```

**System-level guardrails** — external filters that check output before it reaches users:

* Content filtering APIs
* Regular expression checks (e.g., block any response containing a competitor name)
* Length checks (reject responses outside expected range)
* Format validation (reject responses that aren't valid JSON when JSON was expected)

**Human review loops** — high-risk outputs reviewed before delivery:

* Any output in medical, legal, or financial domains
* Responses to escalated or angry customers
* Anything involving specific data (names, account numbers, amounts)

\---

## Prompt Templates

In enterprise systems, prompts should be templates with variable slots — not static strings.

**Template structure:**

```
SYSTEM: You are a {role} at {company\\\_name}. {role\\\_specific\\\_instructions}

TASK: {task\\\_description}

CONTEXT: {injected\\\_context}

CONSTRAINTS: {constraint\\\_block\\\_for\\\_this\\\_task\\\_type}

INPUT: {user\\\_input}

OUTPUT FORMAT: {format\\\_schema}
```

**Benefits:**

* Reuse system-level instructions across many task types
* Change company info in one place, updates everywhere
* Easy A/B testing (swap one variable, keep everything else)
* Auditability — you can see exactly what was sent to the model

\---

## Prompt Governance

For organisations using AI at scale, prompt governance ensures quality, safety, and accountability.

**Governance framework:**

1. **Prompt registry** — a central catalogue of all production prompts with metadata
2. **Approval workflow** — new prompts must be reviewed before production deployment
3. **Change control** — updates to production prompts require documented justification
4. **Incident process** — when a prompt produces harmful output, there's a defined response process
5. **Regular audits** — prompts are reviewed quarterly for performance, cost, and compliance
6. **Ownership** — every production prompt has a named owner responsible for it

**Roles in prompt governance:**

* **Prompt Engineer** — writes and optimises prompts
* **Domain Expert** — validates accuracy for the business domain
* **Compliance Reviewer** — checks regulatory requirements
* **Platform Owner** — approves production deployment

\---

## Human Review Loops

Not everything AI generates should go directly to end users or downstream systems.

**When to require human review:**

* Any output in a regulated domain (medical, legal, financial advice)
* First-run of a new prompt type in production
* Any output flagged by automated quality checks
* Escalated or sensitive customer cases
* Any output involving specific individuals' data

**Designing the review loop:**

1. AI generates output
2. Automated checks run (format, length, content filters)
3. If checks pass and confidence is high → deliver output
4. If checks fail or confidence is low → queue for human review
5. Human reviews, edits if needed, approves
6. Reviewed output is delivered and logged for prompt improvement

\---

## Cost Monitoring Dashboard

Build visibility into your AI costs before they surprise you.

**Key metrics to monitor:**

|Metric|Monitor|Alert Threshold|
|-|-|-|
|Total daily token cost|Daily|> 20% above baseline|
|Cost per query by prompt|Weekly|> $X per query for this task type|
|Volume by prompt type|Daily|Unexpected spikes|
|Error rate|Real-time|> 5% error rate|
|Avg response time|Real-time|> latency threshold for use case|
|Format compliance rate|Daily|< 95% (for structured outputs)|

\---

## Prompt ROI Thinking

Before building any AI prompt system, answer:

1. **What's the current cost of this task?** (human hours × rate)
2. **What's the projected AI cost?** (volume × tokens × price)
3. **What's the quality bar?** (if AI is 80% as good, is that enough?)
4. **What's the risk if AI makes an error?** (low-stakes vs. high-stakes)
5. **What's the time to build and maintain the prompt?** (not zero — factor it in)

**A simple ROI model:**

```
Monthly manual cost: 200 hours × £50/hour = £10,000
Monthly AI cost: 100,000 queries × £0.01 = £1,000
Monthly maintenance: \\\~8 hours × £50 = £400

Monthly saving: £10,000 - £1,400 = £8,600
Annual saving: \\\~£103,200
```

\---

## Production Rollback Strategy

When a prompt goes wrong in production, you need to revert fast.

**Rollback process:**

1. **Monitoring detects** anomaly (quality drop, error spike, cost spike)
2. **Automated alert** fires to prompt owner
3. **Decision: rollback or fix-forward?** — if error is serious, always rollback first
4. **Previous prompt version** is immediately deployed (hence version control)
5. **Incident review** — root cause analysis of what changed and why it failed
6. **Fix is developed and tested** before re-deployment
7. **Post-mortem** is documented to prevent recurrence

**Time to rollback should be under 10 minutes** if your versioning is in order.

\---

<a name="part-9"></a>

# Part 9 —  Preparation

\---

## Top 30 Prompt Engineering  discussion pointers

### Foundational Questions (Beginner)

1. **What is prompt engineering and why does it matter?**
*Answer: The skill of crafting AI instructions to produce accurate, useful, consistent outputs. It matters because the same model produces dramatically different quality based on prompt quality — affecting cost, reliability, and accuracy.*
2. **What is the difference between zero-shot and few-shot prompting?**
*Answer: Zero-shot provides no examples — relies entirely on model training. Few-shot provides 2–5 examples to calibrate format and logic. Few-shot improves consistency significantly for specialised tasks.*
3. **What is Chain of Thought prompting and when do you use it?**
*Answer: Asking the model to reason step by step before giving a final answer. Use for multi-step reasoning, calculations, logical analysis. Avoid for simple, fast lookups.*
4. **What is hallucination in AI, and how do prompts reduce it?**
*Answer: Hallucination is the model confidently stating incorrect information. Prompts reduce it by: (1) providing context via RAG, (2) explicitly instructing "only use provided information," (3) adding fallback instructions like "say I don't know if unsure."*
5. **What are tokens and why do they matter for prompt design?**
*Answer: Tokens are the units the model processes (roughly ¾ of a word). They determine cost and context window limits. Efficient prompts minimise unnecessary tokens without sacrificing quality.*

\---

### Intermediate Questions

6. **Explain RAG prompting and when you'd use it over fine-tuning.**
*Answer: RAG retrieves relevant documents and injects them as context before the model answers. Use RAG when knowledge is proprietary, frequently updated, or needs to be cited. Fine-tuning changes model behaviour permanently — use for consistent style/format, not knowledge.*
7. **What is prompt chaining and what problem does it solve?**
*Answer: Chaining connects multiple prompts sequentially where output of one feeds the next. It solves the quality degradation of trying to do too many complex tasks in one prompt, and makes workflows debuggable.*
8. **How do you make a prompt consistent across thousands of runs?**
*Answer: Use few-shot examples, explicit output format schemas, constrained output options, delimiters to prevent parsing ambiguity, and set temperature to 0 for deterministic tasks.*
9. **What is the difference between a system prompt and a user prompt?**
*Answer: System prompt sets the AI's persistent role, behaviour, and constraints (the "character"). User prompt is the per-query input (the "question"). System prompts establish the context that applies to all interactions.*
10. **How do you debug a prompt that's producing inconsistent outputs?**
*Answer: (1) Add examples (few-shot). (2) Add explicit output format. (3) Reduce temperature. (4) Add constraints. (5) Check for ambiguous instructions. (6) Use delimiters if data looks like instructions.*

\---

### Advanced Questions

11. **Explain ReAct prompting. How does it enable agents?**
*Answer: ReAct interleaves Reasoning and Acting — the model thinks, takes an action (tool call), observes the result, and repeats until the task is complete. This loop enables agents because the model can dynamically gather information rather than relying on static training data.*
12. **What is Tree of Thoughts and when is the cost justified?**
*Answer: ToT explores multiple reasoning paths simultaneously and selects the best. Cost is justified for complex strategic decisions where the cost of wrong choices exceeds the cost of extra computation. Not appropriate for routine queries.*
13. **How do you evaluate prompt quality at scale?**
*Answer: Define measurable metrics (format compliance, factual accuracy, task completion, hallucination rate). Run automated checks where possible. Sample for human review. Track metrics over time via monitoring dashboards.*
14. **What is prompt injection and how do you defend against it?**
*Answer: Prompt injection is when malicious content in user input tries to override system instructions. Defence: use delimiters to separate system instructions from user data, validate inputs, never expose raw system prompts, use output filtering.*
15. **How would you design a prompt versioning system?**
*Answer: Store prompts in version control with semantic versioning, metadata (owner, use case, model, cost, performance metrics), change logs, and approval workflow. Maintain rollback capability to previous versions.*

\---

### Scenario-Based Questions

16. **A customer service AI is giving incorrect refund information. Walk me through your debugging process.**
*Answer: (1) Check if correct policy was retrieved (RAG issue?). (2) Check if constraint "only use provided policies" is in the prompt. (3) Test with known policy questions to isolate whether it's retrieval or generation. (4) Add explicit fallback instruction. (5) Add human review loop for refund-related responses.*
17. **You need to build a prompt for classifying 50,000 documents daily. What are your design priorities?**
*Answer: Cost efficiency (minimal tokens, fast model for this task), format compliance (JSON output with specific schema), consistency (few-shot examples, low temperature), speed (short prompts, constrained outputs), monitoring (daily quality sampling).*
18. **A prompt worked great in testing but is failing in production. What might explain this?**
*Answer: (1) Production data has different characteristics than test data. (2) Context window exceeded with longer real documents. (3) Model version changed. (4) Temperature or other API parameters differ. (5) Edge cases not covered by test set.*
19. **How do you decide between Chain of Thought and Tree of Thoughts for a complex task?**
*Answer: Use CoT when there's one logical path through the problem — reasoning just needs to be made explicit. Use ToT when multiple fundamentally different approaches exist and the best one isn't obvious upfront. ToT is much more expensive — only use when trade-off evaluation genuinely matters.*
20. **Describe how you'd build a prompt governance process for an enterprise deploying 50 AI use cases.**
*Answer: Prompt registry with metadata. Approval workflow (prompt engineer → domain expert → compliance → platform owner). Version control with rollback capability. Quality monitoring dashboard. Quarterly audit process. Named ownership for every production prompt. Incident response procedure.*

\---

### Cost Optimisation Questions

21. **How do you reduce token cost while maintaining quality?**
22. **When would you use a smaller, faster model vs. a larger model?**
23. **How do you calculate ROI on an AI prompting system?**
24. **What's the cost impact of self-consistency prompting and when is it worth it?**
25. **How do caching strategies reduce AI costs in production?**

\---

### Design Exercise Questions

26. **Design a prompt for an AI that helps employees find HR policies.**
27. **Improve this weak prompt: "Write a good email."**
28. **This prompt hallucinates 30% of the time. Fix it: "Answer this customer's question about our product."**
29. **Design a prompt chain for processing customer support tickets end-to-end.**
30. **Create a prompt that extracts structured data from unstructured invoices.**

\---

## How to Explain Prompt Engineering Like a Senior Consultant

When explaining prompt engineering to business stakeholders (not technical), use this framing:

**"Prompt engineering is the skill of communicating precisely with AI. Just as a good job description helps you hire the right person, a good prompt helps AI produce the right output. The quality gap between a well-designed prompt and a casual one is equivalent to the difference between a detailed brief to a professional and a vague instruction to a new hire. We invest in prompt engineering to make our AI outputs reliable, accurate, cost-efficient, and safe to deploy at scale."**

Key points to emphasise for business audiences:

* It directly affects accuracy and cost (two things every business cares about)
* It's not magic — it's a craft with learnable principles
* It requires governance at scale, just like any other business process
* Poor prompt design has real business consequences (wrong information, compliance risk, wasted spend)

\---

# Appendix — Quick Reference

## The 7-Component Prompt Checklist

Before deploying any prompt, check:

* \[ ] **Role** — Is there a clear expert identity?
* \[ ] **Task** — Is the task specific and unambiguous?
* \[ ] **Context** — Does the model have what it needs to answer well?
* \[ ] **Constraints** — Are the limits clear and non-contradictory?
* \[ ] **Examples** — Have you shown what good looks like?
* \[ ] **Output Format** — Is the structure specified?
* \[ ] **Evaluation Criteria** — Can success be measured?

## Technique Selection Guide

|Your situation|Use this|
|-|-|
|Simple, well-defined task|Zero-Shot|
|Needs specific format/style|Few-Shot|
|Multi-step reasoning|Chain of Thought|
|High-stakes quality check|Reflection|
|Domain expertise needed|Role Prompting|
|Compliance/safety boundaries|Constraint|
|System integration|Output Format|
|Complex prompts with data|Delimiter|
|Diagnostic reasoning|Step-Back|
|High-stakes factual query|Self-Consistency|
|Agent with tools|ReAct|
|Strategic decisions|Tree of Thoughts|
|Domain knowledge needed|RAG|
|Complex multi-step workflow|Prompt Chaining|
|Need to build/improve prompts|Meta Prompting|

## Token Cost Quick Reference

|Technique|Relative Cost|
|-|-|
|Zero-Shot|1×|
|Role|1×|
|Constraint|1.1×|
|Output Format|1.1–1.2×|
|Few-Shot|1.5–2×|
|CoT|2–3×|
|Reflection|3–4×|
|RAG|3–5×|
|Prompt Chaining|2–4× (per chain step)|
|Self-Consistency|3–5× (per run)|
|ToT|4–8×|
|ReAct|4–10× (per loop)|

\---

*End of Prompt Engineering Master Guide*

*Techniques evolve — treat this as a foundation, not a ceiling.*

