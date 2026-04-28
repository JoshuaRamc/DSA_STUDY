# Day 8: Heap + Cost Optimization

Date: April 27, 2026
Label: day8_heap_cost_optimization
Focus: Heap and priority queue patterns, top K selection, kth largest reasoning, and reducing the cost of production LLM systems without destroying quality.

## Goal For Today

By the end of Day 8, you should be able to:

1. Recognize when a heap is the right tool instead of sorting everything.
2. Explain the difference between a min heap and a max heap conceptually.
3. Solve top K and kth largest style problems with the right time complexity.
4. Talk through how to reduce the cost of a production LLM system in a structured, practical way.

This day matters because heaps test whether you can maintain only the most important items efficiently, and cost optimization questions test whether you can think like a production engineer balancing quality, latency, and spend.

## Coding Concepts

### Heap

A heap is a data structure that gives fast access to the smallest or largest item.

In Python, the built-in `heapq` module implements a min heap.

That means:

- the smallest item is always at the top
- `heappush` inserts an item
- `heappop` removes the smallest item

Example:

```python
import heapq

heap = []
heapq.heappush(heap, 5)
heapq.heappush(heap, 2)
heapq.heappush(heap, 8)

heapq.heappop(heap)
# 2
```

### Why Heap Works

Use a heap when:

- you need the top K items
- you need the kth largest or kth smallest item
- you need repeated access to the current best item
- sorting the full input would do more work than necessary

### Min Heap vs Max Heap

In Python:

- `heapq` is a min heap
- there is no built-in max heap

To simulate a max heap, you often push negative values:

```python
import heapq

nums = [3, 1, 5]
heap = []

for num in nums:
    heapq.heappush(heap, -num)

largest = -heapq.heappop(heap)
```

### Heap Intuition

The key idea:

```text
Keep only what matters most, and throw away the rest early.
```

For top K problems, this is often much better than sorting everything.

### When Not To Use A Heap

Do not force a heap if:

- you only need one pass maximum or minimum
- sorting is simpler and the input is tiny
- a hashmap or stack pattern matches the problem better

You want the simplest correct tool.

## Pattern 1: Fixed-Size Min Heap For Top K

Use this when:

- you need the K largest items
- you do not need the full sorted order of all elements
- you want to keep only the best K seen so far

Template:

```python
import heapq

heap = []

for value in values:
    heapq.heappush(heap, value)

    if len(heap) > k:
        heapq.heappop(heap)
```

Why it works:

- the heap stores at most `k` items
- the smallest of those `k` stays at the top
- anything smaller than the kept top K gets removed

## Pattern 2: Heap By Frequency

Use this when:

- items have counts
- you need the most frequent K items
- a hashmap gives the counts and a heap keeps the top K

Template:

```python
import heapq
from collections import Counter

counts = Counter(nums)
heap = []

for num, freq in counts.items():
    heapq.heappush(heap, (freq, num))
    if len(heap) > k:
        heapq.heappop(heap)
```

## Pattern 3: Kth Largest With Min Heap

Use this when:

- you need the kth largest item
- you do not need all values fully sorted

Template:

```python
import heapq

heap = []

for num in nums:
    heapq.heappush(heap, num)
    if len(heap) > k:
        heapq.heappop(heap)

return heap[0]
```

Why it works:

```text
The heap keeps the k largest values seen so far.
The smallest among them is exactly the kth largest overall.
```

## Problem 1: Top K Frequent Elements

LeetCode: 347. Top K Frequent Elements
Difficulty: Medium
Pattern: Hashmap + fixed-size min heap
Core structure: Frequency count + keep best K

## Status

[ ] COMPLETED TOP K FREQUENT ELEMENTS SUCCESSFULLY

### Problem Statement

Given an integer array `nums` and an integer `k`, return the `k` most frequent elements.

Example:

```python
nums = [1, 1, 1, 2, 2, 3]
k = 2
# return [1, 2]
```

### Key Insight

This problem has two parts:

1. Count how often each number appears.
2. Keep only the top `k` frequencies.

A hashmap solves the counting.

A min heap of size `k` solves the top K part efficiently.

### Code

```python
import heapq
from collections import Counter


class Solution:
    def topKFrequent(self, nums: list[int], k: int) -> list[int]:
        counts = Counter(nums)
        heap = []

        for num, freq in counts.items():
            heapq.heappush(heap, (freq, num))

            if len(heap) > k:
                heapq.heappop(heap)

        return [num for freq, num in heap]
```

### Walkthrough

Input:

```python
nums = [1, 1, 1, 2, 2, 3]
k = 2
```

Trace:

```text
counts = {1: 3, 2: 2, 3: 1}

push (3, 1)
heap = [(3, 1)]

push (2, 2)
heap = [(2, 2), (3, 1)]

push (1, 3)
heap = [(1, 3), (3, 1), (2, 2)]
size > 2, pop smallest frequency
remove (1, 3)

heap now contains the top 2 frequent elements
```

### Complexity

Time: O(n log k)

Why:

- counting takes O(n)
- each unique value may be pushed into a heap of size at most `k`

Space: O(n)

Why:

- the frequency map can store up to all unique values
- the heap stores up to `k` items

### Interview Explanation

"I first count frequencies with a hashmap because that gives me how often each value appears in O(n) time. Then I maintain a min heap of size k storing `(frequency, value)` pairs. If the heap grows past size k, I pop the smallest frequency. That way I keep only the k most frequent elements instead of sorting everything."

## Problem 2: Kth Largest Element In An Array

LeetCode: 215. Kth Largest Element in an Array
Difficulty: Medium
Pattern: Fixed-size min heap
Core structure: Keep the k largest values seen so far

## Status

[ ] COMPLETED KTH LARGEST ELEMENT IN AN ARRAY SUCCESSFULLY

### Problem Statement

Given an integer array `nums` and an integer `k`, return the kth largest element in the array.

Example:

```python
nums = [3, 2, 1, 5, 6, 4]
k = 2
# return 5
```

### Key Insight

You do not need the whole array sorted.

You only need to keep track of the `k` largest values seen so far.

If you maintain a min heap of size `k`:

- the heap contains the current `k` largest values
- the smallest value inside that heap is the kth largest overall

### Code

```python
import heapq


class Solution:
    def findKthLargest(self, nums: list[int], k: int) -> int:
        heap = []

        for num in nums:
            heapq.heappush(heap, num)

            if len(heap) > k:
                heapq.heappop(heap)

        return heap[0]
```

### Walkthrough

Input:

```python
nums = [3, 2, 1, 5, 6, 4]
k = 2
```

Trace:

```text
push 3 -> heap = [3]
push 2 -> heap = [2, 3]
push 1 -> heap = [1, 3, 2]
size > 2, pop 1 -> heap = [2, 3]

push 5 -> heap = [2, 3, 5]
size > 2, pop 2 -> heap = [3, 5]

push 6 -> heap = [3, 5, 6]
size > 2, pop 3 -> heap = [5, 6]

push 4 -> heap = [4, 6, 5]
size > 2, pop 4 -> heap = [5, 6]

return heap[0] = 5
```

### Complexity

Time: O(n log k)

Why: each element may be pushed and possibly popped from a heap of size at most `k`.

Space: O(k)

Why: the heap never stores more than `k` elements.

### Interview Explanation

"I use a min heap of size k. As I scan the array, I keep only the k largest values seen so far. Whenever the heap grows beyond size k, I remove the smallest. At the end, the smallest value remaining in that heap is the kth largest element overall."

## Coding Patterns Side By Side

### Top K Frequent Elements

Question:

```text
Which k items have the highest frequency?
```

Main pattern:

```python
counts = Counter(nums)
```

and

```python
if len(heap) > k:
    heapq.heappop(heap)
```

### Kth Largest Element In An Array

Question:

```text
What value would be in position k if the array were sorted descending?
```

Main pattern:

```python
return heap[0]
```

## RRK: Cost Optimization For Production LLM Systems

A production LLM system can be technically correct and still fail because it is too expensive to operate.

For Day 8, your answer should sound like someone who knows cost is a system design dimension, not just a finance issue.

## Core Vocabulary

### Token Usage

LLM cost usually scales with tokens.

That includes:

- input prompt tokens
- retrieved context tokens
- output tokens

Longer prompts usually cost more and often increase latency too.

### Caching

Caching means reusing previous work instead of recomputing it.

Examples:

- exact response cache
- retrieval result cache
- embedding cache
- tool result cache

### Batching

Batching means grouping work together so the system does fewer total operations.

Examples:

- batch embeddings
- batch offline evaluations
- batch asynchronous summarization jobs

### Model Routing

Model routing means sending easy tasks to cheaper models and harder tasks to stronger models.

Example:

```text
FAQ question -> small model
complex reasoning or tool-heavy task -> larger model
```

### Cost Per Request

A useful metric:

```text
total infrastructure and model cost / number of successful requests
```

This helps you measure whether optimizations are actually working.

## RRK Practice Prompt

Practice this out loud:

```text
How do you reduce the cost of a production LLM system?
```

## Reducing LLM Cost

### High-Level Principle

Do not start by blindly downgrading the model.

Instead, reduce unnecessary work first.

Main levers:

1. Cache repeated work.
2. Reduce prompt and retrieval size.
3. Route simple requests to cheaper models.
4. Use async processing where real-time response is not required.
5. Measure cost by workflow and by request type.

## First Response Structure

Use this order:

1. Clarify workload and traffic shape.
2. Identify major cost drivers.
3. Reduce unnecessary tokens.
4. Improve retrieval efficiency.
5. Introduce model routing.
6. Cache repeated work.
7. Move non-urgent tasks async.
8. Monitor quality so cost cuts do not break the product.

## Google-Level RRK Structure

For Google-style RRK answers, use this structure:

1. Clarify the use case.
2. State assumptions.
3. Identify cost drivers.
4. Describe the serving path.
5. Optimize prompt and retrieval efficiency.
6. Optimize model choice and routing.
7. Add caching and async workflows.
8. Discuss quality guardrails and tradeoffs.
9. Define cost and latency metrics.
10. Summarize the design.

Strong interview phrase:

"I would reduce cost first by eliminating unnecessary work, then by matching model size to task complexity, and I would verify that each optimization preserves user-visible quality."

## Quantitative Thinking For This Prompt

Use numbers so your answer feels real.

Example assumptions:

- 100,000 daily requests
- average prompt plus context size of 4,000 input tokens
- average answer size of 500 output tokens
- peak traffic of 20 to 50 QPS
- some requests are simple retrieval Q&A, while a smaller fraction are complex multi-step agent tasks

Useful sentence:

"Even small token reductions matter at scale, because cutting 1,000 tokens from a high-volume workflow can materially reduce both cost and latency."

## Interview-Ready Answer

"To reduce the cost of a production LLM system, I would first break down where spend is coming from, such as prompt size, retrieved context size, model choice, repeated requests, and expensive tool-heavy workflows. Then I would remove unnecessary work before changing user-visible quality. That means caching repeated queries and embeddings, retrieving fewer but higher-quality chunks, shortening prompts, and moving non-urgent jobs to asynchronous paths. After that, I would introduce model routing so simple tasks use cheaper models and only complex tasks escalate to larger models. I would monitor cost per request, token usage, latency, and answer quality together, because a cheaper system is not actually better if it noticeably harms user outcomes."

## Clarify The Problem Like Google

Before optimizing, ask:

1. What part of the system costs the most: model calls, retrieval, tool calls, or infrastructure?
2. Which workflows are highest volume?
3. Which workflows require the strongest model, and which are simpler?
4. Is the system real-time, batch, or mixed?
5. What quality metrics must stay stable while reducing cost?

## Cost Driver Thinking

Break the request into parts:

```text
user request
-> retrieval
-> prompt assembly
-> model call
-> tool calls
-> output validation
```

Potential cost drivers:

- too many retrieved chunks
- repeated identical or similar requests
- overuse of a large model
- multi-step agent loops
- expensive synchronous tasks that could be async
- long outputs where short outputs would suffice

## Component Deep Dive

### Prompt Size

Questions to ask:

- Is the prompt longer than it needs to be?
- Are we repeating instructions on every turn?
- Are we including too much retrieved context?

Ways to reduce cost:

- shorten system and task instructions
- compress or summarize context
- remove redundant examples
- cap output length when appropriate

### Retrieval

Questions to ask:

- Are we retrieving too many chunks?
- Are the chunks low quality or redundant?
- Is reranking helping us send fewer, better documents?

Ways to reduce cost:

- retrieve fewer chunks
- improve chunking quality
- rerank before prompt assembly
- filter by metadata or permissions earlier

### Model Selection

Questions to ask:

- Are all requests going to the largest model?
- Can simple tasks use a smaller model?
- Can some tasks avoid generation entirely?

Ways to reduce cost:

- route by complexity
- use a small model for classification, extraction, or rewriting
- reserve expensive models for ambiguous or high-value cases

### Caching

Questions to ask:

- Do users ask repeated questions?
- Do identical retrieval queries repeat?
- Are embeddings recomputed unnecessarily?

Ways to reduce cost:

- response cache
- retrieval cache
- embedding cache
- tool result cache

### Async Processing

Questions to ask:

- Which tasks really need real-time completion?
- Which tasks can run in the background?

Ways to reduce cost:

- async document ingestion
- async summarization
- batch offline analysis
- delayed non-urgent agent tasks

## Quality And Reliability Guardrails

Do not present cost optimization as just cutting corners.

Mention:

- maintain answer quality metrics
- watch hallucination and groundedness
- test routing rules carefully
- use fallbacks if smaller models fail
- monitor whether shorter prompts hurt quality

Strong phrase:

"I would treat quality regression as a real failure mode of cost optimization, so I would pair every cost change with evaluation and monitoring."

## Tradeoffs

Make these explicit:

- cost vs quality:
  smaller models and shorter prompts may reduce quality if pushed too far
- cost vs latency:
  some optimizations help both, but extra routing or reranking can add latency
- simplicity vs optimization:
  a heavily optimized routing system may save money but be harder to maintain
- caching vs freshness:
  cached answers save money but may become stale if data changes often

## Metrics And Monitoring

Cost metrics:

- cost per request
- cost per successful workflow
- total token usage by workflow
- average input tokens
- average output tokens
- cache hit rate

Quality metrics:

- answer quality
- groundedness
- retrieval precision
- fallback rate
- user satisfaction

Operational metrics:

- latency
- QPS
- error rate
- async queue depth

## Follow-Up Questions

### What is usually the biggest LLM cost driver?

Answer:

"Often it is a combination of large prompt size, too much retrieved context, and overuse of a large model on requests that do not actually need it."

### Why not just switch to the cheapest model?

Answer:

"Because a cheaper model that causes lower answer quality, more retries, or more escalations can increase overall system cost and reduce user trust."

### How does retrieval affect cost?

Answer:

"Retrieved documents become prompt tokens, so weak retrieval can raise both cost and latency by sending too much low-value context to the model."

### When is caching risky?

Answer:

"Caching is risky when freshness matters or when responses are user-specific and permissions are not handled carefully."

### What does good model routing look like?

Answer:

"Good model routing sends easy, repetitive, or narrow tasks to smaller models and escalates only ambiguous, high-value, or reasoning-heavy tasks to stronger models."

## RRK Drill Section

Say these out loud without notes:

1. Answer "How do you reduce the cost of a production LLM system?" in under 2 minutes.
2. Name the main cost drivers in an LLM serving path.
3. Explain how caching helps.
4. Explain why retrieval quality affects cost.
5. Give one example of a good model routing rule.
6. Explain when async processing helps.
7. Give one quality risk caused by over-optimization.
8. Name the metrics you would monitor after a cost optimization rollout.

## RRK Interview Checklist

When answering LLM cost optimization prompts, cover these:

1. Cost drivers
2. Token usage
3. Retrieval efficiency
4. Model selection
5. Caching
6. Batching
7. Async processing
8. Quality guardrails
9. Cost metrics
10. Tradeoffs

## Common Mistakes

### Mistake 1: Sorting Everything When You Only Need K Items

Heaps are valuable because they help you avoid unnecessary full sorting.

### Mistake 2: Forgetting That Python `heapq` Is A Min Heap

Do not accidentally reason about it like a max heap.

### Mistake 3: Using A Heap Without Knowing What It Stores

Be clear whether the heap contains:

- raw values
- indexes
- `(frequency, value)` pairs

### Mistake 4: Optimizing Cost Without Measuring It

Do not say:

```text
We should use a smaller model to save money.
```

Better:

```text
I would identify the highest-cost workflows first, reduce unnecessary tokens, and then test whether smaller models preserve quality for those request types.
```

### Mistake 5: Cutting Prompt Size Without Checking Quality

A smaller prompt is not automatically better if it removes important context.

### Mistake 6: Forgetting That Caching Can Serve Stale Or Unauthorized Results

Caching needs freshness and permission awareness.

## Python Syntax You Must Be Comfortable With

### `heapq`

```python
import heapq
```

### `heappush`

```python
heapq.heappush(heap, value)
```

### `heappop`

```python
smallest = heapq.heappop(heap)
```

### Frequency Counting

```python
from collections import Counter
counts = Counter(nums)
```

## Manual Practice Routine

For coding:

1. Ask whether the problem needs top K or kth selection.
2. Decide what should be stored in the heap.
3. Decide whether the heap should stay size `k`.
4. Dry run one example.
5. State time and space complexity.

For RRK:

1. Clarify where the cost comes from.
2. Identify high-volume workflows.
3. Reduce unnecessary tokens.
4. Improve retrieval efficiency.
5. Add model routing.
6. Add caching or async processing.
7. Protect quality with evaluation.

## Dry Run Template

Use this for heap problems:

```text
Input:

Current value:
Heap before:
Push?
Pop?
Heap after:
```

Use this for LLM cost optimization:

```text
Workflow:
Main cost driver:
Token reduction idea:
Retrieval improvement:
Model routing plan:
Cache opportunity:
Quality risk:
Metric to watch:
```

## Practice Prompts

### Top K Frequent Elements

Trace this:

```python
nums = [1, 1, 2, 2, 2, 3]
k = 2
```

Expected:

```python
[1, 2]
```

### Kth Largest Element In An Array

Trace this:

```python
nums = [3, 2, 3, 1, 2, 4, 5, 5, 6]
k = 4
```

Expected:

```python
4
```

### RRK

Practice out loud:

```text
How do you reduce the cost of a production LLM system?
```

Then make sure you cover:

1. Prompt size
2. Retrieval size
3. Model routing
4. Caching
5. Batching
6. Async processing
7. Quality monitoring
8. Cost per request

## Final Day 8 Code To Memorize

### Top K Frequent Elements

```python
import heapq
from collections import Counter


class Solution:
    def topKFrequent(self, nums: list[int], k: int) -> list[int]:
        counts = Counter(nums)
        heap = []

        for num, freq in counts.items():
            heapq.heappush(heap, (freq, num))

            if len(heap) > k:
                heapq.heappop(heap)

        return [num for freq, num in heap]
```

### Kth Largest Element In An Array

```python
import heapq


class Solution:
    def findKthLargest(self, nums: list[int], k: int) -> int:
        heap = []

        for num in nums:
            heapq.heappush(heap, num)

            if len(heap) > k:
                heapq.heappop(heap)

        return heap[0]
```

## Day 8 Self-Test

Answer these without looking:

1. When is a heap better than sorting the full array?
2. Why does Python `heapq` naturally fit fixed-size top K with a min heap?
3. Why does Top K Frequent Elements need both a hashmap and a heap?
4. Why does `heap[0]` become the kth largest element in a size-`k` min heap?
5. What are the most common cost drivers in a production LLM system?
6. Why can better retrieval reduce both cost and latency?
7. Why should model routing be paired with evaluation?
8. What is one case where async processing saves money?
9. Why can caching be risky?
10. What cost metric would you track after deploying an optimization?

## Day 8 Answers

1. When you only need the top K or kth item and do not need a full sorted result.
2. Because the smallest of the kept K items sits at the top, which makes it easy to remove weaker candidates.
3. The hashmap counts frequency, and the heap keeps only the best K frequencies.
4. Because the heap contains the k largest values seen so far, and the smallest among them is the kth largest overall.
5. Prompt tokens, retrieved context, model choice, repeated requests, and expensive multi-step workflows.
6. Because fewer, higher-quality chunks reduce prompt size and reduce wasted model work.
7. Because smaller or cheaper models can hurt answer quality if you do not verify performance.
8. Background summarization, ingestion, or offline analysis that does not need an immediate response.
9. Because cached data can become stale or leak user-specific information if permissions are not handled correctly.
10. Cost per request or cost per successful workflow.

## Day 8 Interview Phrases

Use these phrases until they become natural:

- "I use a heap when I need to keep only the most important K items efficiently."
- "In Python, `heapq` is a min heap, so for top K I often keep a size-k min heap and remove the smallest when needed."
- "For Top K Frequent Elements, the hashmap solves counting and the heap solves selection."
- "For Kth Largest, I do not need a full descending sort. I only need the best k values seen so far."
- "I would reduce LLM cost first by eliminating unnecessary work, not by blindly downgrading the model."
- "Prompt size, retrieval size, and model choice are usually the biggest cost levers."
- "Better retrieval can improve quality while also reducing token cost."
- "Model routing should send simple requests to smaller models and reserve large models for harder cases."
- "Caching and async workflows can save cost, but they need freshness and permission awareness."
- "I would always pair cost optimization with quality monitoring so savings do not create a worse product."

## Day 8 Completion Criteria

You are done with Day 8 when you can:

1. Explain min heap and max heap intuition in plain English.
2. Implement Top K Frequent Elements without looking.
3. Implement Kth Largest Element in an Array without looking.
4. State time and space complexity for both heap problems.
5. Explain why heaps help avoid unnecessary full sorting.
6. Verbally answer "How do you reduce the cost of a production LLM system?"
7. Cover caching, batching, model routing, retrieval efficiency, prompt size, and async processing without being prompted.
8. Explain how to reduce cost without silently damaging quality.
