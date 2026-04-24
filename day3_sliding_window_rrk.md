# Day 3: Sliding Window + Intro To RRK

Date: April 23, 2026
Label: day3_sliding_window_rrk
Focus: Sliding window, expand/shrink patterns, array scanning, and intro AI system design for production AI agents.

## Goal For Today

By the end of Day 3, you should be able to do two things:

1. Recognize when a coding problem uses a window over a string or array.
2. Explain the basic architecture of a production AI system using RAG, an LLM, evaluation, and fallback handling.

This day matters because your interview is not only LeetCode. You are also being tested on whether you can reason about AI systems like an engineer.

## Coding Concepts

### Sliding Window

Sliding window is a pattern where you track a section of an array or string using two boundaries.

Most of the time, those boundaries are:

```python
left = 0
right = 0
```

The window is the part between `left` and `right`.

```text
s = "abcabcbb"

left = 0
right = 2

window = "abc"
```

### Why Sliding Window Works

Sliding window is useful when you need to inspect contiguous parts of an input.

Examples:

- A substring
- A subarray
- A streak of valid characters
- A range of prices
- A running sum

Instead of rebuilding every possible substring or subarray, you move the window boundaries.

### Expand And Shrink Pattern

The most common sliding window pattern is:

1. Expand the window by moving `right`.
2. Add the new item into the window state.
3. If the window becomes invalid, shrink it by moving `left`.
4. Update the best answer when the window is valid.

Template:

```python
left = 0
window = {}
best = 0

for right in range(len(s)):
    char = s[right]

    # Add char to the window state.

    while window_is_invalid:
        left_char = s[left]

        # Remove left_char from the window state.

        left += 1

    best = max(best, right - left + 1)
```

How to think:

- `right` explores new territory.
- `left` fixes the window when it breaks a rule.
- The window state remembers what is currently inside the window.

## Pattern 1: Sliding Window With A Set

Use this when:

- You need a substring with no duplicates.
- You only need to know whether something is inside the current window.
- The window becomes invalid when a duplicate appears.

Template:

```python
seen = set()
left = 0
best = 0

for right in range(len(s)):
    while s[right] in seen:
        seen.remove(s[left])
        left += 1

    seen.add(s[right])
    best = max(best, right - left + 1)

return best
```

## Pattern 2: Running Best Value

Use this when:

- You only need the best result seen so far.
- The answer can be updated in one pass.
- You do not need to store every previous result.

Example:

```python
best = 0

for value in values:
    best = max(best, value)
```

This appears in Best Time to Buy/Sell Stock and Maximum Subarray.

## Problem 1: Longest Substring Without Repeating Characters

LeetCode: 3. Longest Substring Without Repeating Characters
Difficulty: Medium
Pattern: Sliding window with set
Core structure: String + set + two pointers

## Status

[X]  COMPLETED LONGEST SUBSTRING WITHOUT REPEATING CHARACTERS SUCCESSFULLY

### Problem Statement

Given a string `s`, return the length of the longest substring without repeating characters.

Example:

```python
s = "abcabcbb"
# return 3
```

The answer is `"abc"`, with length `3`.

Example:

```python
s = "bbbbb"
# return 1
```

The answer is `"b"`, with length `1`.

### Key Insight

The current substring is valid only if it has no repeated characters.

So we keep a window with unique characters:

- Expand right when reading a new character.
- If the new character already exists, shrink from the left until it can fit.
- Track the longest valid window length.

### Algorithm

1. Create an empty set called `seen`.
2. Set `left = 0`.
3. Loop `right` across the string.
4. While `s[right]` is already in `seen`, remove `s[left]` and move `left`.
5. Add `s[right]` to `seen`.
6. Update the best length with `right - left + 1`.

### Code

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        seen = set()
        left = 0
        best = 0

        for right in range(len(s)):
            while s[right] in seen:
                seen.remove(s[left])
                left += 1

            seen.add(s[right])
            best = max(best, right - left + 1)

        return best
```

### Walkthrough

Input:

```python
s = "abcabcbb"
```

Trace:

```text
right = 0, char = "a"
window = "a"
best = 1

right = 1, char = "b"
window = "ab"
best = 2

right = 2, char = "c"
window = "abc"
best = 3

right = 3, char = "a"
"a" is already in the window.
Remove from the left until the old "a" is gone.
window becomes "bca"
best stays 3
```

### Complexity

Time: O(n)

Why: each character is added once and removed at most once.

Space: O(n)

Why: the set can store characters from the current window.

### Interview Explanation

"I can use a sliding window because I need the longest contiguous substring that satisfies a rule: no repeated characters. I keep a set of characters in the current window. As I move the right pointer, if the new character is already in the set, I shrink from the left until the duplicate is removed. Then I update the maximum window length."

## Problem 2: Best Time To Buy And Sell Stock

LeetCode: 121. Best Time to Buy and Sell Stock
Difficulty: Easy
Pattern: One pass running minimum
Core structure: Array + running best

## Status

[ X ] COMPLETED BEST TIME TO BUY AND SELL STOCK SUCCESSFULLY

### Problem Statement

Given an array `prices`, where `prices[i]` is the price of a stock on day `i`, return the maximum profit you can achieve by choosing one day to buy and a later day to sell.

If no profit is possible, return `0`.

Example:

```python
prices = [7, 1, 5, 3, 6, 4]
# return 5
```

Buy at `1`, sell at `6`, profit is `5`.

### Key Insight

To maximize profit on a sell day, you want the lowest buy price before that day.

Track:

- `min_price`: cheapest price seen so far
- `best_profit`: best profit seen so far

### Code

```python
class Solution:
    def maxProfit(self, prices: list[int]) -> int:
        min_price = float("inf")
        best_profit = 0

        for price in prices:
            min_price = min(min_price, price)
            profit = price - min_price
            best_profit = max(best_profit, profit)

        return best_profit
```

### Walkthrough

Input:

```python
prices = [7, 1, 5, 3, 6, 4]
```

Trace:

```text
price = 7
min_price = 7
profit = 0
best_profit = 0

price = 1
min_price = 1
profit = 0
best_profit = 0

price = 5
min_price = 1
profit = 4
best_profit = 4

price = 6
min_price = 1
profit = 5
best_profit = 5
```

### Complexity

Time: O(n)

Space: O(1)

### Interview Explanation

"I only need one pass. For each price, I track the lowest price seen before or at that day. Then I calculate what profit I would make if I sold today. I update the best profit as I go. This gives O(n) time and O(1) space."

## Problem 3: Maximum Subarray

LeetCode: 53. Maximum Subarray
Difficulty: Medium
Pattern: Kadane's algorithm
Core structure: Array + running sum

## Status

[ X ] I COMPLETED MAXIMUM SUBARRAY SUCCESSFULLY

### Problem Statement

Given an integer array `nums`, find the contiguous subarray with the largest sum and return its sum.

Example:

```python
nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
# return 6
```

The best subarray is:

```python
[4, -1, 2, 1]
# sum = 6
```

### Key Insight

At each number, ask:

```text
Is it better to extend the previous subarray, or start fresh here?
```

If the previous running sum hurts you, drop it and start over.

### Code

```python
class Solution:
    def maxSubArray(self, nums: list[int]) -> int:
        current_sum = nums[0]
        best_sum = nums[0]

        for num in nums[1:]:
            current_sum = max(num, current_sum + num)
            best_sum = max(best_sum, current_sum)

        return best_sum
```

### Walkthrough

Input:

```python
nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
```

Trace:

```text
start:
current_sum = -2
best_sum = -2

num = 1
max(1, -2 + 1) = 1
current_sum = 1
best_sum = 1

num = -3
max(-3, 1 + -3) = -2
current_sum = -2
best_sum = 1

num = 4
max(4, -2 + 4) = 4
current_sum = 4
best_sum = 4

num = -1
max(-1, 4 + -1) = 3
current_sum = 3
best_sum = 4

num = 2
current_sum = 5
best_sum = 5

num = 1
current_sum = 6
best_sum = 6
```

### Complexity

Time: O(n)

Space: O(1)

### Interview Explanation

"I use Kadane's algorithm. At each index, I decide whether to extend the previous subarray or start a new subarray at the current number. I track the best sum seen so far. This works because a negative or weak previous sum should not be carried forward if starting fresh is better."

## Coding Patterns Side By Side

### Longest Substring Without Repeating Characters

Question:

```text
What is the longest valid substring with no duplicates?
```

Main pattern:

```python
while s[right] in seen:
    seen.remove(s[left])
    left += 1
```

### Best Time To Buy And Sell Stock

Question:

```text
What is the best profit if I sell today using the cheapest earlier buy price?
```

Main pattern:

```python
min_price = min(min_price, price)
best_profit = max(best_profit, price - min_price)
```

### Maximum Subarray

Question:

```text
Should I extend the current subarray or start over here?
```

Main pattern:

```python
current_sum = max(num, current_sum + num)
```

## RRK: What Is An AI System In Production?

An AI system in production is more than a prompt sent to a model.

It is a full software system that:

1. Accepts user input.
2. Finds relevant context or data.
3. Calls an LLM.
4. Returns a useful answer.
5. Handles uncertainty and failure.
6. Measures quality over time.
7. Protects users, data, and the business.

For interviews, say this clearly:

"A production AI system is not just the model. It includes retrieval, orchestration, prompts, tools, evaluation, monitoring, fallback paths, and human or product-level safeguards."

## RRK Core Vocabulary

### LLM

An LLM is the language model that generates or reasons over text.

Examples of what the LLM can do:

- Answer questions
- Summarize documents
- Extract structured information
- Decide which tool to call
- Draft responses

The LLM is powerful, but it does not automatically know private company data unless you provide that data.

### RAG

RAG means retrieval-augmented generation.

The system retrieves relevant documents first, then gives those documents to the LLM as context.

Basic flow:

```text
User question
-> retrieve relevant documents
-> pass question and documents to LLM
-> generate grounded answer
-> return answer with sources
```

RAG helps when the answer depends on private, changing, or company-specific knowledge.

### Embeddings

Embeddings turn text into vectors so similar meanings can be searched.

Example:

```text
"How do I reset my password?"
```

should be close to:

```text
"Password reset instructions"
```

even if the words are not exactly the same.

### Vector Database

A vector database stores document chunks and their embeddings.

The system searches it to find chunks that are semantically similar to the user's question.

### Evaluation

Evaluation means measuring whether the AI system is doing a good job.

You can evaluate:

- Answer correctness
- Whether the answer used the retrieved context
- Whether the system refused when it should
- Latency
- User satisfaction
- Hallucination rate

### Fallback Handling

Fallback handling means deciding what the system should do when confidence is low or something fails.

Examples:

- Ask a clarifying question.
- Say it does not know.
- Show relevant documents instead of a generated answer.
- Route to a human.
- Use a safer canned response.

## RRK Practice Prompt

Practice this out loud:

```text
Design an AI agent for internal company Q&A.
```

## AI Agent For Internal Company Q&A

### Problem Clarification

Start by asking or stating assumptions:

```text
I would design an internal Q&A agent that helps employees answer questions from company documents like policies, onboarding docs, engineering runbooks, HR docs, and product documentation.
```

Possible clarifying questions:

1. Who are the users: all employees, support agents, engineers, or HR?
2. What documents can it access?
3. Does it need to cite sources?
4. Are there permission boundaries by role or team?
5. What should happen when the answer is uncertain?

If the interviewer wants you to proceed, state assumptions and keep moving.

### High-Level Architecture

```text
User
-> chat UI
-> backend API
-> auth and permissions
-> query rewriting or classification
-> retrieval from company knowledge base
-> LLM response generation
-> safety and quality checks
-> answer with citations
-> logging and evaluation
```

### Data Ingestion

Company documents need to be prepared before retrieval.

Steps:

1. Connect to sources like Google Drive, Confluence, Notion, Slack, GitHub, or internal wikis.
2. Extract text from documents.
3. Split documents into chunks.
4. Create embeddings for each chunk.
5. Store chunks, metadata, permissions, and embeddings.
6. Re-index documents when they change.

Important metadata:

- Document title
- URL
- Owner
- Last updated date
- Department
- Permission rules

### Retrieval Flow

When a user asks a question:

1. Check who the user is and what they can access.
2. Convert the question into an embedding.
3. Search the vector database for relevant chunks.
4. Filter retrieved chunks by permissions.
5. Optionally rerank the chunks.
6. Send the best chunks to the LLM.

### Generation Flow

The LLM receives:

- The user's question
- Retrieved document chunks
- Instructions to answer only from provided context
- Instructions to cite sources
- Instructions to say when the answer is not found

Prompt shape:

```text
You are an internal company Q&A assistant.
Answer using only the provided context.
If the context does not contain the answer, say you do not know and suggest the next best step.
Include citations to the documents used.
```

### Evaluation

You should evaluate before and after launch.

Offline evaluation:

- Create a test set of real employee questions.
- Add expected answers or expected source documents.
- Measure retrieval quality: did the right document appear in the top results?
- Measure answer quality: was the answer correct and grounded?

Online evaluation:

- Track thumbs up/down.
- Track whether users click citations.
- Track repeated questions.
- Track escalation to human support.
- Sample logs for quality review.

Core metrics:

- Retrieval precision
- Retrieval recall
- Answer correctness
- Groundedness
- Latency
- Deflection rate
- Escalation rate

### Fallback Handling

The system needs a plan for weak or risky answers.

Fallback cases:

1. No relevant documents found.
2. Retrieved documents conflict.
3. User lacks permission.
4. The question asks for sensitive information.
5. The model is uncertain.
6. The LLM or retrieval service fails.

Fallback responses:

```text
I could not find a reliable answer in the available company documents.
Here are the closest documents I found, or I can route this to the right team.
```

For permission issues:

```text
I cannot access documents that your account is not permitted to view.
```

For conflicting information:

```text
I found conflicting information across these sources. I would show both sources and recommend contacting the document owner.
```

### Security And Permissions

This is important in system design interviews.

The agent should not leak internal data.

Design choices:

- Use company SSO.
- Apply permissions before showing retrieved chunks.
- Store document ACLs with each chunk.
- Log access safely.
- Avoid sending restricted data to tools that should not see it.
- Redact secrets when needed.

Interview phrase:

"Retrieval must be permission-aware. The system should only retrieve and generate from documents the user is allowed to access."

### Production Concerns

A production AI agent needs:

- Monitoring
- Logging
- Rate limits
- Latency targets
- Cost controls
- Versioned prompts
- Model fallback
- Human escalation
- Data freshness checks
- Incident response

Good interview phrase:

"I would treat the AI agent like a production service, with observability, evaluation, and fallback behavior, not as a one-off prompt."

## Spoken RRK Answer

Practice saying this:

"I would design the internal Q&A agent as a RAG-based system. The user asks a question in a chat UI, and the backend authenticates the user and checks permissions. The system retrieves relevant chunks from company documents using embeddings and a vector database, filters those chunks based on what the user is allowed to see, and passes the best context to an LLM. The LLM is instructed to answer only from the provided context and cite sources. If the system cannot find enough evidence, it should say it does not know, show the closest useful documents, ask a clarifying question, or route the user to a human owner. I would evaluate it with a test set of real questions, measuring retrieval quality, answer correctness, groundedness, latency, and user feedback. In production, I would monitor failures, track cost, version prompts, and make sure permission-aware retrieval prevents data leakage."

## Google-Level RRK Structure

For Google-style RRK answers, use this exact structure every time:

1. Clarify the problem.
2. State assumptions.
3. High-level architecture.
4. Component deep dive.
5. Scalability discussion.
6. Reliability and failure handling.
7. Security and permissions.
8. Cost optimization.
9. Tradeoffs.
10. Metrics and evaluation.

Strong interview phrase:

"I’ll start by clarifying the user, scale, and constraints, then I’ll walk from high-level architecture into the parts that usually break first: retrieval quality, permissions, latency, and cost."

## Quantitative Thinking For This Prompt

Do not keep this answer vague. Put numbers on it.

Example assumptions for an internal company Q&A agent:

- 100,000 employees globally
- 20,000 daily active users
- 2 questions per active user per day
- 40,000 questions per day
- Average QPS is about `40,000 / 86,400`, which is under 1 QPS on average
- Peak traffic can still be much higher, for example 20 to 50 QPS during working hours
- Latency target for a user-facing answer is under 2 seconds for common questions
- 95th percentile latency target might be under 4 seconds
- Corpus size could be 10 million document chunks
- If each chunk plus metadata averages 2 KB, that is about 20 GB before indexing overhead

Useful interview sentence:

"Even if average QPS is low, I would design for burstiness, because internal systems often see sharp peaks during business hours or after company-wide announcements."

## Interview-Ready Answer

"I would design the internal Q&A system as a permission-aware RAG platform. First I would clarify who the users are, what knowledge sources are in scope, and what the latency target is. I’d assume something like 20,000 daily active users, peak traffic around a few dozen QPS, and a target of under 2 seconds for common answers. At a high level, I’d use a chat client, a stateless API layer, an authentication and authorization layer, a retrieval service over chunked company documents, and an LLM generation layer with citations. Then I’d go deeper on the parts that matter most: permission-aware retrieval, prompt quality, fallback behavior when context is weak, and evaluation pipelines that measure both retrieval quality and answer groundedness. I’d also call out cost controls, because retrieval plus generation plus reranking can get expensive quickly at scale."

## Clarify The Problem Like Google

Before jumping to architecture, ask:

1. Who are the users: employees, support agents, engineers, or executives?
2. What knowledge sources are in scope?
3. Does the system need citations?
4. What are the latency expectations?
5. What data is sensitive or access-controlled?
6. Is the system answering only, or can it also take actions?

If the interviewer wants you to move faster, state assumptions explicitly and continue.

## High-Level Architecture With Cloud-Abstraction First

Abstract system components:

```text
Client
-> stateless API layer
-> auth and access control service
-> retrieval orchestration service
-> vector search and metadata store
-> reranking layer
-> LLM generation service
-> response validation and policy layer
-> logging, metrics, and evaluation pipeline
```

Optional cloud mappings:

- Stateless API layer behind a load balancer
  AWS: `API Gateway` or `ALB` with `ECS`, `EKS`, or `Lambda`
  Azure: `API Management` or `Application Gateway` with `AKS`, `Container Apps`, or `Functions`
- Distributed workers for ingestion and async jobs
  AWS: `SQS` + `Lambda`, `ECS`, or `EKS`
  Azure: `Service Bus` + `Functions`, `Container Apps`, or `AKS`
- Storage systems for document metadata and results
  AWS: `S3`, `RDS`, `DynamoDB`, `OpenSearch`
  Azure: `Blob Storage`, `Azure SQL`, `Cosmos DB`, `Azure AI Search`

## Component Deep Dive

### API Layer

Responsibilities:

- Authenticate the caller
- Enforce rate limits
- Route the request
- Return synchronous answers for fast flows
- Hand off to async workflows for slow flows

The API layer should be stateless so it can scale horizontally.

### Retrieval Layer

Responsibilities:

- Rewrite the query if needed
- Embed the query
- Search the vector index
- Filter by metadata and permissions
- Rerank top candidates
- Pass top chunks to the LLM

For this prompt, retrieval quality is often the real product.

### LLM Layer

Responsibilities:

- Follow prompt instructions
- Use retrieved context only
- Cite sources
- Refuse or degrade safely when evidence is weak

### Evaluation Pipeline

This should not be an afterthought.

Use:

- Offline eval datasets
- Shadow testing for prompt changes
- Sampled production reviews
- Human review for ambiguous or high-risk cases

## AI System Depth

### Prompt Design

Prompt requirements:

- Answer only from provided context
- Cite sources
- State uncertainty when context is weak
- Do not reveal restricted data
- Keep the answer concise and useful

### Tool Usage And Orchestration

If the agent needs tools beyond retrieval, say so clearly:

- Search tools
- Document lookup tools
- Ticketing or workflow tools
- Directory tools for ownership lookup

Good interview phrase:

"I would keep the first version narrow and reliable, then add tool use only where it clearly improves user outcomes without creating too much failure surface."

### Human-In-The-Loop

Use human escalation when:

- The answer confidence is low
- Documents conflict
- The question is sensitive
- The user is asking for an action with business impact

## Scalability Discussion

Start small, then scale.

At 10,000 users:

- A single retrieval cluster may be enough
- Re-indexing can be mostly batch-based
- One main model path may be acceptable

At 1 million users:

- Retrieval traffic and embedding workloads become large
- You likely need separate ingestion and serving pipelines
- You may need caching for repeated queries
- You may need regional deployment for latency and residency
- Model routing becomes important for cost control

Useful sentence:

"I would separate ingestion scale from query-serving scale, because indexing millions of documents and answering live user questions are very different workloads."

## Reliability And Failure Handling

### Failure Modes

1. Bad data ingestion
   Why it breaks:
   malformed docs, wrong parsing, missing ACLs
   How to handle it:
   validation, schema checks, quarantine bad documents, retry logic
2. Retrieval misses relevant context
   Why it breaks:
   weak chunking, poor embeddings, metadata filters too strict
   How to handle it:
   better chunking, reranking, query rewriting, eval-driven tuning
3. LLM hallucination
   Why it breaks:
   model answers beyond evidence
   How to handle it:
   grounded prompts, citation checks, confidence thresholds, fallback to source links
4. API or provider failure
   Why it breaks:
   upstream dependency outage or timeout
   How to handle it:
   retries with backoff, circuit breakers, graceful degradation, cached fallback responses
5. System overload
   Why it breaks:
   peak traffic exceeds capacity
   How to handle it:
   autoscaling, queues, rate limits, admission control
6. Stale or inconsistent data
   Why it breaks:
   docs changed but index not refreshed
   How to handle it:
   freshness metadata, re-index pipelines, source-of-truth links, last-updated timestamps

## Security, Compliance, And Permissions

This is a must-cover area for Google-style answers.

You should mention:

- SSO and identity-based access
- Row-level or document-level ACL enforcement
- PII detection and redaction
- Encryption in transit and at rest
- Audit logging
- Least-privilege service accounts
- Separation between retrieval permissions and model permissions

Strong interview phrase:

"I would enforce permissions before retrieval results are ever shown to the model response path, because once restricted content enters the prompt, you have already lost the security boundary."

## Cost Optimization

Main levers:

- Cache repeated retrieval results
- Use smaller models for easy questions
- Use expensive models only for hard or high-value cases
- Limit prompt size and retrieval depth
- Batch ingestion and embedding jobs
- Set token budgets and timeout budgets

Retrieval-depth tradeoff:

- More chunks may improve answer quality
- But more chunks increase token cost and latency

## Tradeoffs

Make these explicit:

- Latency vs cost:
  more reranking and larger models improve quality but cost more and respond slower
- Accuracy vs speed:
  deeper retrieval and more validation help accuracy but increase latency
- Complexity vs maintainability:
  multi-stage orchestration is powerful but harder to operate
- Consistency vs availability:
  strict freshness guarantees can slow ingestion or increase serving complexity
- Retrieval depth vs performance:
  larger context windows may help but can hurt speed and raise hallucination risk if noisy

## Metrics And Evaluation

For Google-style RRK, always mention both product and system metrics.

System metrics:

- P50, P95 latency
- Error rate
- Timeout rate
- Cache hit rate
- Retrieval latency
- Queue depth
- Cost per request

AI quality metrics:

- Retrieval recall at top K
- Groundedness
- Citation correctness
- Hallucination rate
- Helpfulness rating
- Escalation rate

Business metrics:

- Question deflection rate
- Time saved per employee
- Repeat-question rate

## Interviewer Follow-Up Questions

### How does this scale to millions of users?

Answer:

"I would keep the API layer stateless and scale it horizontally, separate ingestion from serving, and use distributed retrieval infrastructure so search load can scale independently from generation load. I’d also use caching and model routing, because at that point cost is as important as pure technical scalability."

### What are the bottlenecks?

Answer:

"The likely bottlenecks are vector retrieval latency, LLM latency and cost, and permission filtering if ACL logic is expensive. I’d measure those explicitly, then scale and optimize the highest-latency or highest-cost stage first."

### What happens if retrieval finds the wrong documents?

Answer:

"That becomes a quality and trust issue. I’d improve chunking, retrieval tuning, and reranking, and I’d also make the model refuse when the evidence is weak rather than answering confidently from poor context."

### How do you measure success?

Answer:

"I’d measure both system health and answer quality. On the system side I care about latency, failure rate, and cost per request. On the AI side I care about groundedness, citation correctness, retrieval recall, and user satisfaction."

### How do you reduce cost?

Answer:

"I’d reduce cost by caching safe repeated work, routing simple tasks to cheaper models, limiting retrieval depth, and batching offline workloads like embedding generation and evaluation."

## RRK Drill Section

Say these out loud without notes:

1. Design an internal company Q&A agent using the 10-step Google structure.
2. Add realistic numbers for users, QPS, latency, and storage.
3. Explain the biggest bottleneck first.
4. Describe one security failure and how you would prevent it.
5. Explain one latency-vs-cost tradeoff.
6. Map your abstract design to both AWS and Azure.
7. Explain what happens when retrieval quality is poor.
8. Give a concise interview-ready answer in under 2 minutes.

## RRK Interview Checklist

When asked to design an AI system, cover these:

1. User and use case
2. Data sources
3. Retrieval
4. LLM generation
5. Evaluation
6. Fallbacks
7. Security and permissions
8. Monitoring and production readiness

## Common Mistakes

### Mistake 1: Calling Everything Sliding Window

Not every one-pass problem is sliding window.

Sliding window usually means a contiguous range with a `left` and `right` boundary.

Best Time to Buy/Sell Stock is one pass, but it is better described as tracking a running minimum.

Maximum Subarray is one pass, but it is Kadane's algorithm.

### Mistake 2: Forgetting To Shrink The Window

For Longest Substring Without Repeating Characters, this is incomplete:

```python
if s[right] in seen:
    return best
```

You should shrink until the duplicate is removed.

### Mistake 3: Letting Stock Sell Before Buy

For stock profit, the buy must happen before the sell.

Tracking `min_price` as you scan left to right enforces that order.

### Mistake 4: Maximum Subarray Starting Best At 0

Wrong for all-negative arrays:

```python
best_sum = 0
```

Example:

```python
nums = [-3, -2, -5]
```

The correct answer is `-2`, not `0`.

Use:

```python
current_sum = nums[0]
best_sum = nums[0]
```

### Mistake 5: Describing An AI System As Just An LLM Call

In production, the LLM is only one component.

You also need retrieval, data permissions, evaluation, logging, fallback handling, and monitoring.

## Python Syntax You Must Be Comfortable With

### `range(len(s))`

Use this when you need indexes.

```python
for right in range(len(s)):
    char = s[right]
```

### `set.remove`

Removes an item from a set.

```python
seen.remove("a")
```

Only remove when you know the item exists.

### `float("inf")`

Useful starting value when you want to track a minimum.

```python
min_price = float("inf")
```

### `max`

Useful for running best values.

```python
best = max(best, current)
```

## Manual Practice Routine

For coding:

1. Say the pattern name.
2. Identify what state you need.
3. Write the loop.
4. Update the state.
5. Update the best answer.
6. State time and space complexity.

For RRK:

1. Clarify the use case.
2. Draw or describe the architecture.
3. Explain data ingestion.
4. Explain retrieval.
5. Explain generation.
6. Explain evaluation.
7. Explain fallback handling.
8. Mention security and permissions.

## Dry Run Template

Use this for sliding window:

```text
Input:

State before loop:
left:
right:
window state:
best:

Iteration 1:
current character:
is window valid?
do we shrink?
state after:
best:

Return:
```

Use this for AI system design:

```text
User asks:

Step 1: authentication and permissions
Step 2: retrieval
Step 3: context filtering
Step 4: LLM generation
Step 5: citation and quality check
Step 6: fallback if needed
Step 7: logging and evaluation
```

## Practice Prompts

### Longest Substring Without Repeating Characters

Trace this:

```python
s = "pwwkew"
```

Expected:

```python
3
```

Trace this:

```python
s = "dvdf"
```

Expected:

```python
3
```

### Best Time To Buy And Sell Stock

Trace this:

```python
prices = [7, 1, 5, 3, 6, 4]
```

Expected:

```python
5
```

Trace this:

```python
prices = [7, 6, 4, 3, 1]
```

Expected:

```python
0
```

### Maximum Subarray

Trace this:

```python
nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
```

Expected:

```python
6
```

Trace this:

```python
nums = [-3, -2, -5]
```

Expected:

```python
-2
```

### RRK

Practice out loud:

```text
Design an AI agent for internal company Q&A.
```

Then answer:

1. What data sources does it use?
2. How does retrieval work?
3. How do you prevent data leakage?
4. How do you evaluate answer quality?
5. What happens if the system is uncertain?

## Final Day 3 Code To Memorize

### Longest Substring Without Repeating Characters

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        seen = set()
        left = 0
        best = 0

        for right in range(len(s)):
            while s[right] in seen:
                seen.remove(s[left])
                left += 1

            seen.add(s[right])
            best = max(best, right - left + 1)

        return best
```

### Best Time To Buy And Sell Stock

```python
class Solution:
    def maxProfit(self, prices: list[int]) -> int:
        min_price = float("inf")
        best_profit = 0

        for price in prices:
            min_price = min(min_price, price)
            profit = price - min_price
            best_profit = max(best_profit, profit)

        return best_profit
```

### Maximum Subarray

```python
class Solution:
    def maxSubArray(self, nums: list[int]) -> int:
        current_sum = nums[0]
        best_sum = nums[0]

        for num in nums[1:]:
            current_sum = max(num, current_sum + num)
            best_sum = max(best_sum, current_sum)

        return best_sum
```

## Day 3 Self-Test

Answer these without looking:

1. What does sliding window mean?
2. What do `left` and `right` represent?
3. Why does Longest Substring use a set?
4. When do you shrink the window?
5. Why does Best Time to Buy/Sell Stock track `min_price`?
6. Why is Maximum Subarray initialized with `nums[0]`?
7. What does RAG stand for?
8. Why does an internal Q&A agent need retrieval?
9. What should happen when no reliable context is found?
10. Why must retrieval be permission-aware?

## Day 3 Answers

1. Sliding window means tracking a contiguous section of an array or string with moving boundaries.
2. `left` is the start of the current window, and `right` is the end being expanded.
3. The set tracks which characters are currently inside the window.
4. You shrink when the window violates the rule, such as containing a duplicate.
5. The best sell profit depends on the cheapest earlier buy price.
6. Starting with `nums[0]` handles all-negative arrays correctly.
7. RAG stands for retrieval-augmented generation.
8. The LLM needs company-specific context that is not built into the model.
9. The system should say it does not know, show closest sources, ask a clarifying question, or escalate.
10. Permission-aware retrieval prevents users from seeing information they are not allowed to access.

## Day 3 Interview Phrases

Use these phrases until they become natural:

- "This is a sliding window problem because I need the longest contiguous substring that satisfies a rule."
- "The right pointer expands the window, and the left pointer shrinks it when the rule is broken."
- "I will track the current window state in a set."
- "For stock profit, I track the minimum price seen so far and the best profit."
- "For Maximum Subarray, I decide whether to extend the current subarray or start fresh."
- "A production AI system is more than an LLM call."
- "I would use RAG so the model can answer from current company documents."
- "Retrieval must be permission-aware."
- "If the system lacks reliable context, it should fail gracefully instead of hallucinating."
- "I would evaluate retrieval quality, answer groundedness, latency, and user feedback."

## Day 3 Completion Criteria

You are done with Day 3 when you can:

1. Explain sliding window in plain English.
2. Implement Longest Substring Without Repeating Characters without looking.
3. Implement Best Time to Buy/Sell Stock without looking.
4. Implement Maximum Subarray without looking.
5. State time and space complexity for all three coding problems.
6. Explain what a production AI system includes besides the model.
7. Verbally design an internal company Q&A agent using RAG.
8. Mention evaluation, fallback handling, and permissions without being prompted.
