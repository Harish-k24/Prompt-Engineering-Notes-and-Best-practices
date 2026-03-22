# 🛠️ Practical Prompt Engineering: Code Examples \& Patterns

Real-world, copy-paste-ready code for implementing prompt engineering techniques.

\---

## Part 1: Setup \& Utilities

### Basic LLM Wrapper (Any Provider)

```python
import json                 # (optional here) used for structured payloads / responses
import time                 # used to measure latency of LLM calls
from typing import Optional, Dict, Any   # type hints for better readability & contracts


class PromptEngine:
    """Encapsulates prompt logic with validation & monitoring
    → Acts as a wrapper over LLM APIs
    → Adds metrics, error handling, and configuration control
    """
    
    def __init__(self, model_name: str = "claude-3-sonnet", temperature: float = 0):
        # model identifier (can switch between providers/models)
        self.model = model_name
        
        # controls randomness (0 = deterministic, good for pipelines)
        self.temperature = temperature
        
        # observability metrics
        self.call_count = 0          # total API calls made
        self.total_tokens = 0        # cumulative token usage
        self.errors = []             # store error messages
    
    
    def call(self, prompt: str, system: Optional[str] = None, 
             max_tokens: int = 1000) -> Dict[str, Any]:
        """Call LLM with error handling and monitoring
        
        Inputs:
        - prompt: user input / task
        - system: system instruction (behavior control)
        - max_tokens: response size limit
        
        Output:
        - structured response with success flag, tokens, latency
        """
        try:
            start_time = time.time()   # start latency timer
            
            # actual LLM call (delegated to internal method)
            response = self._call_api(
                system=system,
                prompt=prompt,
                temperature=self.temperature,
                max_tokens=max_tokens
            )
            
            # compute latency
            latency = time.time() - start_time
            
            # update metrics
            self.call_count += 1
            self.total_tokens += response.get('tokens_used', 0)
            
            # standard response format (important for pipelines)
            return {
                'success': True,
                'response': response['text'],                 # actual LLM output
                'tokens': response.get('tokens_used', 0),     # usage tracking
                'latency_ms': latency * 1000                  # performance metric
            }
        
        except Exception as e:
            # capture error for observability
            self.errors.append(str(e))
            
            # return failure-safe response (no crash)
            return {
                'success': False,
                'error': str(e),
                'response': None
            }
    
    
    def _call_api(self, system, prompt, temperature, max_tokens):
        """Replace with your actual API call
        
        → This is the integration point:
           - Claude / OpenAI / Azure OpenAI
           - Can add retry, rate limit handling, etc.
        """
        # Placeholder/mock response
        return {
            'text': 'Mock response',
            'tokens_used': len(prompt.split())   # naive token estimation
        }
    
    
    def get_metrics(self) -> Dict:
        """Return usage metrics
        
        → Useful for:
           - cost tracking
           - performance monitoring
           - error analysis
        """
        return {
            'calls': self.call_count,
            'total_tokens': self.total_tokens,
            
            # average tokens per request (cost efficiency metric)
            'avg_tokens_per_call': self.total_tokens / max(self.call_count, 1),
            
            'error_count': len(self.errors),
            
            # error rate (reliability metric)
            'error_rate': len(self.errors) / max(self.call_count, 1)
        }


# Usage:
engine = PromptEngine(temperature=0)  # deterministic → consistent outputs
result = engine.call(prompt="Your prompt here")

print(engine.get_metrics())  # observability dashboard
```

\---

## Part 2: Technique Implementations

### 1\. Zero-Shot Classification (Production-Ready)

```python
class ZeroShotClassifier:
    """Reliable zero-shot classification with constraints"""
    
    def \_\_init\_\_(self, categories: list, engine: PromptEngine):
        self.categories = categories
        self.engine = engine
        self.category\_str = " | ".join(categories)
    
    def build\_prompt(self, text: str, task\_description: str = "") -> str:
        """Build constrained zero-shot prompt"""
        return f"""You are a text classifier.

Task: {task\_description if task\_description else 'Classify the following text'}
Categories: {self.category\_str}

Rules:
1. Return ONLY the category name, nothing else
2. Choose from: {self.category\_str}
3. If ambiguous, pick the best match
4. No explanation, no justification

Text to classify:
"{text}"

Category:"""
    
    def classify(self, text: str, task\_description: str = "") -> Dict:
        """Classify and validate output"""
        prompt = self.build\_prompt(text, task\_description)
        
        result = self.engine.call(
            system="You are a precise text classifier.",
            prompt=prompt,
            max\_tokens=10  # Minimal tokens for single word
        )
        
        if not result\['success']:
            return {'category': None, 'confidence': 0, 'error': result\['error']}
        
        # Extract and validate category
        response = result\['response'].strip().strip('"').strip()
        
        if response in self.categories:
            return {
                'category': response,
                'confidence': 0.95,  # High confidence for exact match
                'tokens': result\['tokens']
            }
        else:
            # Fallback: fuzzy match
            closest = self.\_find\_closest(response)
            return {
                'category': closest,
                'confidence': 0.5,  # Low confidence for fuzzy match
                'tokens': result\['tokens'],
                'warning': f'Response "{response}" not in categories'
            }
    
    def \_find\_closest(self, text: str) -> str:
        """Find closest category match"""
        for cat in self.categories:
            if cat.lower() in text.lower():
                return cat
        return self.categories\[0]  # Default to first

# Usage:
classifier = ZeroShotClassifier(
    categories=\['Positive', 'Negative', 'Neutral'],
    engine=engine
)

result = classifier.classify("I absolutely love this product!")
print(result)  # {'category': 'Positive', 'confidence': 0.95, ...}
```

\---

### 2\. Few-Shot Learning (With Examples)

```python
class FewShotLearner:
    """Few-shot learning with example curation"""
    
    def \_\_init\_\_(self, task\_name: str, engine: PromptEngine):
        self.task\_name = task\_name
        self.engine = engine
        self.examples = \[]
    
    def add\_example(self, input\_text: str, output\_text: str, importance: float = 1.0):
        """Add a training example"""
        self.examples.append({
            'input': input\_text,
            'output': output\_text,
            'importance': importance
        })
    
    def get\_relevant\_examples(self, query: str, k: int = 3) -> list:
        """Select most relevant examples (basic: just take first k)"""
        # In production: use similarity scoring
        return self.examples\[:k]
    
    def build\_prompt(self, query: str, system: str = "", k: int = 3) -> str:
        """Build few-shot prompt with examples"""
        examples = self.get\_relevant\_examples(query, k)
        
        prompt = f"{system}\\n\\n" if system else ""
        prompt += f"Task: {self.task\_name}\\n\\n"
        prompt += "Examples:\\n"
        prompt += "=" \* 50 + "\\n"
        
        for i, ex in enumerate(examples, 1):
            prompt += f"Example {i}:\\n"
            prompt += f"Input: {ex\['input']}\\n"
            prompt += f"Output: {ex\['output']}\\n\\n"
        
        prompt += "=" \* 50 + "\\n"
        prompt += f"Now solve:\\nInput: {query}\\nOutput:"
        
        return prompt
    
    def run(self, query: str, system: str = "", k: int = 3) -> Dict:
        """Run few-shot learning"""
        prompt = self.build\_prompt(query, system, k)
        
        result = self.engine.call(
            system=system or f"You are an expert at {self.task\_name}",
            prompt=prompt
        )
        
        return {
            'response': result\['response'],
            'success': result\['success'],
            'examples\_used': len(self.get\_relevant\_examples(query, k)),
            'tokens': result.get('tokens', 0)
        }

# Usage:
learner = FewShotLearner(
    task\_name="Sentiment Analysis",
    engine=engine
)

# Add examples
learner.add\_example(
    input\_text="I love this!",
    output\_text="Positive"
)
learner.add\_example(
    input\_text="This is terrible",
    output\_text="Negative"
)
learner.add\_example(
    input\_text="It's okay",
    output\_text="Neutral"
)

# Run
result = learner.run("Not bad, pretty good")
print(result)
```

\---

### 3\. Chain-of-Thought (With Reasoning)

```python
class ChainOfThoughtReasoner:
    """CoT with structured reasoning output"""
    
    def \_\_init\_\_(self, engine: PromptEngine):
        self.engine = engine
    
    def build\_cot\_prompt(self, query: str, task\_description: str = "") -> str:
        """Build CoT prompt forcing step-by-step reasoning"""
        return f"""{task\_description}

Solve this step by step:

Step 1: Identify the key information
Step 2: Break down the problem
Step 3: Work through each part
Step 4: Synthesize the answer

Query: {query}

Let me think through this:"""
    
    def extract\_reasoning(self, response: str) -> Dict:
        """Parse response into reasoning steps and final answer"""
        lines = response.split('\\n')
        steps = \[]
        
        for line in lines:
            if 'step' in line.lower() or any(x in line for x in \['1:', '2:', '3:', '4:']):
                steps.append(line.strip())
        
        return {
            'reasoning\_steps': steps,
            'full\_response': response,
            'step\_count': len(steps)
        }
    
    def run(self, query: str, task\_description: str = "") -> Dict:
        """Run CoT reasoning"""
        prompt = self.build\_cot\_prompt(query, task\_description)
        
        result = self.engine.call(
            system="You are a careful thinker. Always show your work step by step.",
            prompt=prompt
        )
        
        if not result\['success']:
            return {'success': False, 'error': result\['error']}
        
        reasoning = self.extract\_reasoning(result\['response'])
        
        return {
            'success': True,
            'response': result\['response'],
            'reasoning': reasoning,
            'tokens': result\['tokens'],
            'confidence': min(reasoning\['step\_count'] / 4, 1.0)  # Confidence based on steps
        }

# Usage:
reasoner = ChainOfThoughtReasoner(engine)
result = reasoner.run(
    query="If 3 people have 2 apples each, how many apples total?",
    task\_description="Math problem solving"
)
print(result\['reasoning'])
```

\---

### 4\. Self-Consistency (Multiple Paths)

```python
from collections import Counter
import asyncio

class SelfConsistencyVoter:
    """Generate multiple solutions and vote"""
    
    def \_\_init\_\_(self, engine: PromptEngine, num\_paths: int = 3):
        self.engine = engine
        self.num\_paths = num\_paths
    
    def build\_prompt\_variant(self, query: str, variant\_id: int) -> str:
        """Build prompt with slight variation to encourage different paths"""
        instructions = \[
            "Think step by step.",
            "Consider all angles.",
            "Work through this carefully.",
            "Take your time with this.",
        ]
        
        instr = instructions\[variant\_id % len(instructions)]
        return f"""{instr}

Query: {query}

Answer:"""
    
    def run(self, query: str) -> Dict:
        """Generate multiple solutions and vote"""
        responses = \[]
        tokens\_used = 0
        
        for i in range(self.num\_paths):
            prompt = self.build\_prompt\_variant(query, i)
            result = self.engine.call(
                system="You are a helpful AI assistant.",
                prompt=prompt,
                max\_tokens=500
            )
            
            if result\['success']:
                response = result\['response'].strip()
                responses.append(response)
                tokens\_used += result\['tokens']
        
        # Vote on responses
        if not responses:
            return {'success': False, 'error': 'All attempts failed'}
        
        # Simple voting: find most common response
        response\_counts = Counter(responses)
        most\_common = response\_counts.most\_common(1)\[0]
        
        return {
            'success': True,
            'best\_response': most\_common\[0],
            'vote\_count': most\_common\[1],
            'confidence': most\_common\[1] / len(responses),
            'all\_responses': responses,
            'total\_tokens': tokens\_used,
            'paths': len(responses)
        }

# Usage:
voter = SelfConsistencyVoter(engine, num\_paths=3)
result = voter.run("What is 15 + 27?")
print(f"Answer: {result\['best\_response']}")
print(f"Confidence: {result\['confidence']:.2%}")
```

\---

### 5\. Prompt Chaining (Pipeline)

```python
class PromptChain:
    """Chain multiple prompts sequentially"""
    
    def \_\_init\_\_(self, engine: PromptEngine):
        self.engine = engine
        self.steps = \[]
        self.execution\_log = \[]
    
    def add\_step(self, name: str, system: str, prompt\_template: str, 
                 output\_key: str = "output", validate\_fn=None):
        """Add a step to the chain"""
        self.steps.append({
            'name': name,
            'system': system,
            'prompt\_template': prompt\_template,
            'output\_key': output\_key,
            'validate': validate\_fn
        })
    
    def execute(self, initial\_input: Dict) -> Dict:
        """Execute chain step by step"""
        current\_state = initial\_input.copy()
        
        for step in self.steps:
            try:
                # Build prompt using current state
                prompt = step\['prompt\_template'].format(\*\*current\_state)
                
                # Execute step
                result = self.engine.call(
                    system=step\['system'],
                    prompt=prompt
                )
                
                if not result\['success']:
                    return {
                        'success': False,
                        'failed\_step': step\['name'],
                        'error': result\['error'],
                        'log': self.execution\_log
                    }
                
                # Validate output if needed
                if step\['validate']:
                    validation = step\['validate'](result\['response'])
                    if not validation\['valid']:
                        return {
                            'success': False,
                            'failed\_step': step\['name'],
                            'error': f"Validation failed: {validation\['reason']}",
                            'log': self.execution\_log
                        }
                
                # Store output
                current\_state\[step\['output\_key']] = result\['response']
                
                # Log step
                self.execution\_log.append({
                    'step': step\['name'],
                    'input\_keys': list(current\_state.keys()),
                    'tokens': result\['tokens'],
                    'success': True
                })
            
            except Exception as e:
                return {
                    'success': False,
                    'failed\_step': step\['name'],
                    'error': str(e),
                    'log': self.execution\_log
                }
        
        return {
            'success': True,
            'final\_output': current\_state,
            'log': self.execution\_log
        }

# Usage:
chain = PromptChain(engine)

# Step 1: Extract entities
chain.add\_step(
    name="Extract Entities",
    system="Extract named entities.",
    prompt\_template='Extract people and companies from: "{text}"'
)

# Step 2: Validate
def validate\_json(response):
    try:
        json.loads(response)
        return {'valid': True}
    except:
        return {'valid': False, 'reason': 'Invalid JSON'}

chain.add\_step(
    name="Validate",
    system="Validate the extraction.",
    prompt\_template='Is this valid? {output}',
    validate\_fn=validate\_json
)

# Run chain
result = chain.execute({'text': 'Elon Musk founded SpaceX'})
print(result\['log'])
```

\---

### 6\. Tree of Thoughts (Multi-Path Reasoning)

```python
class TreeOfThoughts:
    """Explore multiple reasoning paths"""
    
    def \_\_init\_\_(self, engine: PromptEngine, depth: int = 2, branching: int = 2):
        self.engine = engine
        self.depth = depth
        self.branching = branching
        self.tree = {}
    
    def generate\_branches(self, query: str, depth: int, parent\_id: str = "root") -> list:
        """Generate multiple reasoning branches"""
        branches = \[]
        
        for i in range(self.branching):
            branch\_id = f"{parent\_id}\_{i}"
            prompt = f"""Generate a different approach to this problem:

Problem: {query}

Approach {i+1}: Provide a unique reasoning path (be creative):"""
            
            result = self.engine.call(prompt=prompt, max\_tokens=200)
            
            if result\['success']:
                branches.append({
                    'id': branch\_id,
                    'reasoning': result\['response'],
                    'tokens': result\['tokens'],
                    'depth': depth
                })
        
        return branches
    
    def evaluate\_branches(self, branches: list, query: str) -> list:
        """Score and rank branches"""
        scored = \[]
        
        for branch in branches:
            eval\_prompt = f"""Evaluate this reasoning path for the problem:
            
Problem: {query}
Reasoning: {branch\['reasoning']}

Score 1-10 based on:
1. Logical correctness
2. Completeness
3. Practicality

Return ONLY a number 1-10:"""
            
            result = self.engine.call(prompt=eval\_prompt, max\_tokens=5)
            
            try:
                score = int(result\['response'].strip())
                scored.append({\*\*branch, 'score': score})
            except:
                scored.append({\*\*branch, 'score': 5})
        
        return sorted(scored, key=lambda x: x\['score'], reverse=True)
    
    def run(self, query: str) -> Dict:
        """Run Tree of Thoughts"""
        # Generate initial branches
        branches = self.generate\_branches(query, 0)
        
        # Evaluate and select top branches
        evaluated = self.evaluate\_branches(branches, query)
        top\_branch = evaluated\[0] if evaluated else None
        
        return {
            'success': True if top\_branch else False,
            'best\_solution': top\_branch\['reasoning'] if top\_branch else None,
            'score': top\_branch\['score'] if top\_branch else 0,
            'all\_branches': evaluated,
            'tokens\_used': sum(b\['tokens'] for b in evaluated)
        }

# Usage:
tot = TreeOfThoughts(engine, depth=2, branching=3)
result = tot.run("How to improve code quality?")
print(f"Best solution: {result\['best\_solution']}")
print(f"Score: {result\['score']}")
```

\---

## Part 3: Production Patterns

### Error Handling \& Retry Logic

```python
class RobustPromptEngine:
    """Engine with retry, fallback, and caching"""
    
    def \_\_init\_\_(self, engine: PromptEngine, max\_retries: int = 3):
        self.engine = engine
        self.max\_retries = max\_retries
        self.cache = {}
    
    def call\_with\_retry(self, prompt: str, validator=None, fallback\_response=None) -> Dict:
        """Call with retry and validation"""
        
        # Check cache
        cache\_key = hash(prompt)
        if cache\_key in self.cache:
            return {'response': self.cache\[cache\_key], 'cached': True}
        
        for attempt in range(self.max\_retries):
            try:
                result = self.engine.call(prompt=prompt)
                
                if not result\['success']:
                    if attempt < self.max\_retries - 1:
                        continue
                    else:
                        return {'success': False, 'fallback': fallback\_response}
                
                # Validate if needed
                if validator:
                    is\_valid = validator(result\['response'])
                    if is\_valid:
                        self.cache\[cache\_key] = result\['response']
                        return result
                    elif attempt < self.max\_retries - 1:
                        continue
                else:
                    self.cache\[cache\_key] = result\['response']
                    return result
            
            except Exception as e:
                if attempt == self.max\_retries - 1:
                    return {
                        'success': False,
                        'error': str(e),
                        'fallback': fallback\_response
                    }
        
        return {'success': False, 'fallback': fallback\_response}

# Usage:
robust\_engine = RobustPromptEngine(engine, max\_retries=3)

def validate\_json(response):
    try:
        json.loads(response)
        return True
    except:
        return False

result = robust\_engine.call\_with\_retry(
    prompt='Generate JSON: {"key": "value"}',
    validator=validate\_json,
    fallback\_response='{"key": "value"}'
)
```

\---

### Monitoring \& Metrics

```python
class PromptMonitor:
    """Track performance metrics"""
    
    def \_\_init\_\_(self):
        self.metrics = {
            'latencies': \[],
            'token\_usage': \[],
            'success\_count': 0,
            'failure\_count': 0,
            'by\_task': {}
        }
    
    def record(self, task\_name: str, latency\_ms: float, tokens: int, success: bool):
        """Record a prompt execution"""
        self.metrics\['latencies'].append(latency\_ms)
        self.metrics\['token\_usage'].append(tokens)
        
        if success:
            self.metrics\['success\_count'] += 1
        else:
            self.metrics\['failure\_count'] += 1
        
        if task\_name not in self.metrics\['by\_task']:
            self.metrics\['by\_task']\[task\_name] = {
                'count': 0,
                'avg\_latency': 0,
                'total\_tokens': 0
            }
        
        self.metrics\['by\_task']\[task\_name]\['count'] += 1
        self.metrics\['by\_task']\[task\_name]\['total\_tokens'] += tokens
    
    def report(self) -> Dict:
        """Generate metrics report"""
        import statistics
        
        if not self.metrics\['latencies']:
            return {}
        
        return {
            'p50\_latency\_ms': statistics.median(self.metrics\['latencies']),
            'p95\_latency\_ms': statistics.quantiles(self.metrics\['latencies'], n=20)\[18] if len(self.metrics\['latencies']) > 20 else max(self.metrics\['latencies']),
            'avg\_tokens': statistics.mean(self.metrics\['token\_usage']),
            'success\_rate': self.metrics\['success\_count'] / (self.metrics\['success\_count'] + self.metrics\['failure\_count']),
            'by\_task': self.metrics\['by\_task']
        }

# Usage:
monitor = PromptMonitor()
monitor.record('sentiment\_classification', latency\_ms=245, tokens=50, success=True)
monitor.record('sentiment\_classification', latency\_ms=198, tokens=48, success=True)
print(monitor.report())
```

\---

## Part 4: Real-World Examples

### Complete: Sentiment Analysis Pipeline

```python
class SentimentAnalysisPipeline:
    """Production sentiment analysis with chaining"""
    
    def \_\_init\_\_(self, engine: PromptEngine):
        self.engine = engine
        self.monitor = PromptMonitor()
    
    def analyze(self, text: str) -> Dict:
        """Complete pipeline: extract → classify → explain"""
        start = time.time()
        
        # Step 1: Clean \& extract key phrases
        extract\_result = self.\_extract\_key\_phrases(text)
        if not extract\_result\['success']:
            return {'success': False, 'error': 'Extraction failed'}
        
        # Step 2: Classify sentiment
        classify\_result = self.\_classify\_sentiment(extract\_result\['phrases'])
        if not classify\_result\['success']:
            return {'success': False, 'error': 'Classification failed'}
        
        # Step 3: Explain reasoning
        explain\_result = self.\_explain\_decision(
            text,
            classify\_result\['sentiment'],
            extract\_result\['phrases']
        )
        
        latency = time.time() - start
        self.monitor.record('sentiment\_pipeline', latency \* 1000, 
                          classify\_result.get('tokens', 0), True)
        
        return {
            'success': True,
            'text': text,
            'sentiment': classify\_result\['sentiment'],
            'confidence': classify\_result\['confidence'],
            'explanation': explain\_result\['explanation'],
            'latency\_ms': latency \* 1000
        }
    
    def \_extract\_key\_phrases(self, text: str) -> Dict:
        """Extract key phrases"""
        prompt = f'Extract 3 key phrases from: "{text}"\\nPhrases (comma-separated):'
        result = self.engine.call(prompt=prompt, max\_tokens=50)
        return {
            'success': result\['success'],
            'phrases': result\['response'].split(',') if result\['success'] else \[]
        }
    
    def \_classify\_sentiment(self, phrases: list) -> Dict:
        """Classify sentiment"""
        phrases\_str = ', '.join(phrases\[:3])
        prompt = f'Sentiment of: {phrases\_str}\\nReturn: Positive, Negative, or Neutral'
        result = self.engine.call(prompt=prompt, max\_tokens=10)
        
        sentiment = result\['response'].strip() if result\['success'] else 'Unknown'
        return {
            'success': result\['success'],
            'sentiment': sentiment,
            'confidence': 0.9,
            'tokens': result.get('tokens', 0)
        }
    
    def \_explain\_decision(self, text: str, sentiment: str, phrases: list) -> Dict:
        """Explain the classification"""
        prompt = f"""Why is this text {sentiment}?

Text: "{text}"
Key phrases: {', '.join(phrases\[:3])}

Explanation:"""
        result = self.engine.call(prompt=prompt, max\_tokens=100)
        return {
            'explanation': result\['response'] if result\['success'] else 'Unable to explain'
        }

# Usage:
pipeline = SentimentAnalysisPipeline(engine)
result = pipeline.analyze("This product exceeded my expectations! Absolutely love it.")
print(result)
```

\---

## Key Takeaways for Implementation

### ✅ DO:

```python
# ✅ Use temperature=0 for production pipelines
engine = PromptEngine(temperature=0)

# ✅ Validate outputs
if output in expected\_categories:
    proceed()

# ✅ Cache results
cache\[prompt\_hash] = result

# ✅ Monitor metrics
monitor.record(task, latency, tokens, success)

# ✅ Have fallbacks
if fails: use\_fallback\_or\_escalate()
```

### ❌ DON'T:

```python
# ❌ Assume model will follow format
# ✅ Instead: Enforce with strict constraints + validation

# ❌ Use default temperature
# ✅ Instead: temperature=0 for determinism

# ❌ Hardcode prompts
# ✅ Instead: Template them

# ❌ Skip error handling
# ✅ Instead: Retry, validate, fallback

# ❌ Ignore costs
# ✅ Instead: Count tokens, monitor budget
```

\---

## Cost Optimization Checklist

```python
# 1. Count tokens before deploying
estimated\_tokens = len(prompt.split()) \* 1.3  # \~30% overhead
estimated\_cost = estimated\_tokens \* 0.003 / 1000  # Sonnet pricing

# 2. Use simpler models for simple tasks
# Claude Haiku for classification
# Claude Sonnet for reasoning
# Claude Opus only for hardest tasks

# 3. Batch requests when possible
results = \[engine.call(p) for p in prompts]  # Sequential
# Better: Use batch API if available

# 4. Cache reusable context
if context\_hash in cache:
    context = cache\[context\_hash]

# 5. Monitor cost per operation
total\_cost = sum(result\['tokens'] for result in results) \* 0.003 / 1000
cost\_per\_item = total\_cost / len(results)
print(f"Cost per item: ${cost\_per\_item:.6f}")
```

\---

