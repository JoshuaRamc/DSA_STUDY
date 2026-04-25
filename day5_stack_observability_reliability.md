# Day 5: Stack + Observability And Reliability

Date: April 25, 2026
Label: day5_stack_observability_reliability
Focus: Stack pattern, last-in-first-out logic, matching and validation problems, and debugging failing AI systems with strong observability and reliability practices.

## Goal For Today

By the end of Day 5, you should be able to:

1. Recognize when a stack is the right data structure.
2. Explain why last-in-first-out logic solves matching and nested structure problems.
3. Use a stack to validate, track minimums, and solve "next greater" style problems.
4. Talk through how to debug a failing AI system in a structured, production-minded way.

This day matters because a strong engineer is not only someone who can build systems. It is also someone who can understand why a system is failing and restore it calmly.

## Coding Concepts

### Stack

A stack is a last-in-first-out data structure.

The most recent item added is the first one that comes back out.

In Python, a list is often used as a stack:

```python
stack = []
stack.append("a")
stack.append("b")

stack.pop()
# "b"
```

### Why Stack Works

Use a stack when:

- The most recent unfinished thing matters first.
- You need to match open and close symbols.
- You want to remember state while scanning from left to right.
- You need "next greater" reasoning with unresolved items.

### Last-In-First-Out Logic

The key intuition:

```text
Newest unfinished item should be checked first.
```

That is why a stack fits problems like parentheses matching.

### Matching Problems

Examples:

- `(` must match `)`
- `[` must match `]`
- `{` must match `}`

When you see an opening bracket, push it.
When you see a closing bracket, it must match the top of the stack.

### Validation Problems

Validation often means:

- Is the structure balanced?
- Is every close valid?
- Did we finish with anything left unmatched?

These are classic stack questions.

## Pattern 1: Push On Open, Pop On Close

Use this when:

- You have nested structure.
- Open tokens and close tokens must match.
- The latest open token is the one that should close first.

Template:

```python
stack = []

for char in s:
    if char is opening_symbol:
        stack.append(char)
    else:
        if not stack:
            return False

        top = stack.pop()

        if top does not match char:
            return False

return len(stack) == 0
```

## Pattern 2: Stack With Extra State

Use this when:

- You need more than just the top value.
- You want each push to carry derived information.
- You want O(1) access to something like a minimum.

Example:

```python
stack.append((value, current_min))
```

## Pattern 3: Monotonic Stack

Use this when:

- You need the next greater or next warmer value.
- Items stay unresolved until a better future value appears.

A monotonic stack usually stores indexes.

Template:

```python
stack = []

for i, value in enumerate(values):
    while stack and values[stack[-1]] < value:
        prev_index = stack.pop()
        answer[prev_index] = i - prev_index

    stack.append(i)
```

## Problem 1: Valid Parentheses

LeetCode: 20. Valid Parentheses
Difficulty: Easy
Pattern: Matching with stack
Core structure: String + stack

## Status

[ X] COMPLETED VALID PARENTHESES SUCCESSFULLY

### Problem Statement

Given a string `s` containing only the characters:

```text
()[]{}
```

determine if the input string is valid.

A string is valid if:

1. Open brackets are closed by the same type of brackets.
2. Open brackets are closed in the correct order.
3. Every close has a matching earlier open.

Example:

```python
s = "()[]{}"
# return True
```

Example:

```python
s = "(]"
# return False
```

### Key Insight

When you see a closing bracket, the only opening bracket that matters is the most recent unmatched one.

That is exactly stack behavior.

### Code

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        pairs = {
            ")": "(",
            "]": "[",
            "}": "{",
        }

        for char in s:
            if char in "([{":
                stack.append(char)
            else:
                if not stack:
                    return False

                top = stack.pop()

                if top != pairs[char]:
                    return False

        return len(stack) == 0
```

### Walkthrough

Input:

```python
s = "([])"
```

Trace:

```text
char = "("
push "("
stack = ["("]

char = "["
push "["
stack = ["(", "["]

char = "]"
pop "["
it matches "]"
stack = ["("]

char = ")"
pop "("
it matches ")"
stack = []

return True
```

### Complexity

Time: O(n)

Space: O(n)

Why: in the worst case, the stack holds many opening brackets.

### Interview Explanation

"I use a stack because when I see a closing bracket, it must match the most recent unmatched opening bracket. I push opening brackets onto the stack, and for each closing bracket I check whether the top of the stack is the correct match. If not, or if the stack is empty too early, the string is invalid."

## Problem 2: Min Stack

LeetCode: 155. Min Stack
Difficulty: Medium
Pattern: Stack with extra state
Core structure: Stack + running minimum

## Status

[ X ] COMPLETED MIN STACK SUCCESSFULLY

### Problem Statement

Design a stack that supports:

- `push`
- `pop`
- `top`
- `getMin`

All operations should run in O(1) time.

### Key Insight

If you only store raw values, then `getMin` may require scanning the whole stack.

Instead, store each value together with the minimum seen so far at that point.

### Code

```python
class MinStack:

    def __init__(self):
        self.stack = []

    def push(self, val: int) -> None:
        if not self.stack:
            current_min = val
        else:
            current_min = min(val, self.stack[-1][1])

        self.stack.append((val, current_min))

    def pop(self) -> None:
        self.stack.pop()

    def top(self) -> int:
        return self.stack[-1][0]

    def getMin(self) -> int:
        return self.stack[-1][1]
```

### Walkthrough

Trace:

```text
push(5)
stack = [(5, 5)]

push(2)
stack = [(5, 5), (2, 2)]

push(4)
stack = [(5, 5), (2, 2), (4, 2)]

getMin()
return 2

pop()
remove (4, 2)

getMin()
return 2
```

### Complexity

Time:

- `push`: O(1)
- `pop`: O(1)
- `top`: O(1)
- `getMin`: O(1)

Space: O(n)

### Interview Explanation

"To support O(1) minimum lookup, I store more than just the value. Each stack entry stores the value and the minimum of the stack at that moment. Then `getMin` is just the minimum field of the top entry."

## Problem 3: Daily Temperatures

LeetCode: 739. Daily Temperatures
Difficulty: Medium
Pattern: Monotonic stack
Core structure: Array + stack of indexes

## Status

[ X ] COMPLETED DAILY TEMPERATURES FOR STRETCH

### Problem Statement

Given an array `temperatures`, return an array `answer` such that:

```text
answer[i] = number of days until a warmer temperature
```

If there is no future day for which this is possible, keep `0`.

Example:

```python
temperatures = [73, 74, 75, 71, 69, 72, 76, 73]
# return [1, 1, 4, 2, 1, 1, 0, 0]
```

### Key Insight

Some days are waiting for a warmer future day.

Use a stack of unresolved indexes:

- While the current day is warmer than the day at the top of the stack, resolve that earlier day.
- Then push the current index.

### Code

```python
class Solution:
    def dailyTemperatures(self, temperatures: list[int]) -> list[int]:
        answer = [0] * len(temperatures)
        stack = []

        for i, temp in enumerate(temperatures):
            while stack and temperatures[stack[-1]] < temp:
                prev_index = stack.pop()
                answer[prev_index] = i - prev_index

            stack.append(i)

        return answer
```

### Walkthrough

Input:

```python
temperatures = [73, 74, 75, 71, 69, 72, 76, 73]
```

Trace:

```text
i = 0, temp = 73
push index 0

i = 1, temp = 74
74 > 73
pop 0
answer[0] = 1
push 1

i = 2, temp = 75
75 > 74
pop 1
answer[1] = 1
push 2
```

### Complexity

Time: O(n)

Why: each index is pushed once and popped once.

Space: O(n)

### Interview Explanation

"I use a monotonic stack of unresolved indexes. Each index on the stack is waiting for a warmer future day. When I find a temperature that is warmer than the day at the top of the stack, I can resolve that earlier index immediately."

## Coding Patterns Side By Side

### Valid Parentheses

Question:

```text
Does every closing bracket match the most recent unmatched opening bracket?
```

Main pattern:

```python
top = stack.pop()
```

### Min Stack

Question:

```text
How can I support O(1) minimum lookup?
```

Main pattern:

```python
stack.append((value, current_min))
```

### Daily Temperatures

Question:

```text
Which previous indexes are still waiting for a better future value?
```

Main pattern:

```python
while stack and temperatures[stack[-1]] < temp:
```

## RRK: Observability And Reliability

A production AI system will fail sometimes.

The real engineering question is:

```text
How quickly can I detect the failure, isolate it, and fix it without making things worse?
```

For Google-style RRK, debugging is not random experimentation. It should be structured and measurable.

## Core Vocabulary

### Logging

Logs are detailed event records.

Examples:

- request received
- retrieval returned 0 documents
- tool call failed with timeout
- model output was blocked by safety policy

Logs help answer:

```text
What happened?
```

### Monitoring

Monitoring tracks system behavior over time.

Examples:

- request count
- error rate
- latency
- queue depth
- token usage

Monitoring helps answer:

```text
Is the system healthy?
```

### Tracing

Tracing follows one request through many system components.

Example path:

```text
user request
-> auth
-> retrieval
-> reranker
-> tool call
-> LLM
-> output validation
```

Tracing helps answer:

```text
Where exactly did this request fail or slow down?
```

### Reliability

Reliability means the system behaves correctly and consistently over time.

This includes:

- availability
- correctness
- resilience under failure
- graceful degradation

### Retries

Retries help when failures are transient.

Use them carefully.

Good for:

- temporary network errors
- rate-limit backoff
- flaky downstream services

Bad for:

- invalid inputs
- broken permissions
- deterministic prompt bugs

### Fallbacks

Fallbacks are safer backup behaviors when the ideal path fails.

Examples:

- use a simpler model
- skip an optional tool
- return source documents instead of a generated answer
- route to a human reviewer

### Quality Gates

A quality gate is a check before output reaches the user.

Examples:

- confidence threshold
- citation presence check
- PII redaction check
- policy validation
- regression evaluation before deployment

## RRK Practice Prompt

Practice this out loud:

```text
How do you debug a failing AI system?
```

## Debugging A Failing AI System

### High-Level Principle

Do not start by blaming the model.

Start by locating the failure stage.

Possible failure layers:

1. Input issue
2. Retrieval issue
3. Tool issue
4. Model issue
5. Prompt issue
6. Permission issue
7. Latency or infrastructure issue

### First Response Structure

Use this order:

1. Confirm the failing behavior.
2. Check scope.
3. Trace a single failing request.
4. Identify the failing stage.
5. Mitigate user impact.
6. Fix root cause.
7. Add regression coverage.

## Google-Level System Design Structure

For Google-style RRK answers, use this structure:

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

"I would debug this systematically by first narrowing scope, then following the request path with traces and metrics, and only after that deciding whether the issue is in data, retrieval, orchestration, model behavior, or infrastructure."

## Quantitative Thinking For This Prompt

Use realistic numbers so your answer feels grounded.

Example assumptions:

- 50,000 daily active users
- 3 AI requests per active user per day
- 150,000 requests per day
- Average QPS is about `150,000 / 86,400`, or roughly 2 QPS
- Peak traffic might still be 20 to 40 QPS during business hours
- P95 latency target for user-facing responses could be under 4 seconds
- Retrieval latency target could be under 300 ms
- Model latency target could be under 2 seconds for the common path
- Error budget might target 99.9% successful responses

Useful sentence:

"Even if the average traffic is small, a debugging strategy has to work at peak load, because some failures only show up when the system is under pressure."

## Interview-Ready Answer

"I would debug a failing AI system by first narrowing the scope of the failure. I’d ask whether it affects all users or just some users, whether it is a correctness issue, latency issue, or access issue, and whether it started after a recent deployment or data change. Then I’d trace one failing request end to end across the input layer, retrieval layer, tool calls, prompt construction, model invocation, and output validation. That helps me identify whether the problem is in data, retrieval, permissions, prompt logic, model behavior, or infrastructure. Once I know the failing stage, I’d mitigate impact quickly, for example by rolling back a prompt change, disabling a bad tool, or routing to a simpler fallback path. After that I’d fix the root cause and add regression tests, quality gates, and monitoring so the same failure is easier to catch next time."

## Clarify The Problem Like Google

Before jumping into fixes, ask:

1. What exactly is failing: wrong answers, no answers, slow answers, or unauthorized answers?
2. Is the issue global or isolated to one tenant, one region, or one document set?
3. Did the issue start after a deploy, data refresh, prompt change, or provider change?
4. Is the failure reproducible?
5. What is the user-visible severity?

## High-Level Debugging Architecture

Abstract components:

```text
Client
-> stateless API layer
-> auth and policy checks
-> retrieval and document services
-> tools and orchestration
-> model-serving layer
-> output validation and safety checks
-> logging, tracing, metrics, and evaluation systems
```

Optional cloud mappings:

- Stateless API layer behind a load balancer
  AWS: `API Gateway` or `ALB` with `ECS`, `EKS`, or `Lambda`
  Azure: `API Management` or `Application Gateway` with `AKS`, `Container Apps`, or `Functions`
- Centralized logging and monitoring
  AWS: `CloudWatch`, `X-Ray`, `OpenSearch`
  Azure: `Azure Monitor`, `Application Insights`, `Log Analytics`
- Queues and async jobs for retries or replay
  AWS: `SQS`, `EventBridge`
  Azure: `Service Bus`, `Event Grid`

## Component Deep Dive

### Input Layer

Questions to ask:

- Is the user input malformed?
- Did a client-side change break formatting?
- Are required fields missing?

### Retrieval Layer

Questions to ask:

- Did retrieval return the wrong documents?
- Did it return no documents?
- Did metadata filters or ACL filters exclude the right content?
- Did chunking or embeddings change?

### Tool Layer

Questions to ask:

- Did a downstream tool timeout?
- Did the tool schema change?
- Is the tool response invalid or incomplete?

### Model And Prompt Layer

Questions to ask:

- Did the prompt change recently?
- Is the context too long or too noisy?
- Is the model hallucinating or refusing unexpectedly?

### Permission Layer

Questions to ask:

- Is the user missing access they should have?
- Is the system showing data it should not?
- Did ACL propagation break?

### Infrastructure Layer

Questions to ask:

- Are latency spikes coming from retrieval, model, or network?
- Is there queue buildup?
- Are we hitting provider rate limits?

## LangSmith-Style Tracing Mindset

For agentic systems, tracing is one of the most useful tools.

A good trace should show:

- user input
- rewritten query
- retrieved documents
- tool calls
- prompt payload
- model output
- output validation result
- latency by stage

Good interview phrase:

"I would use trace-level inspection to compare a healthy request and a failing request side by side, because that often shows whether the break is in retrieval, orchestration, prompt assembly, or the model response."

## Reliability And Failure Handling

### Failure Modes

1. Bad input
   Why it breaks:
   malformed or unexpected user request
   How to handle it:
   validation, input normalization, client-side checks
2. Retrieval failure
   Why it breaks:
   stale index, bad chunking, weak embeddings, wrong metadata filter
   How to handle it:
   retrieval evals, index freshness checks, reranking, query rewrite
3. Tool failure
   Why it breaks:
   timeout, schema mismatch, downstream outage
   How to handle it:
   retries for transient errors, circuit breakers, graceful fallback
4. Model failure
   Why it breaks:
   hallucination, refusal, unexpected format, latency spike
   How to handle it:
   output validation, structured generation, fallback model, stricter prompts
5. Prompt failure
   Why it breaks:
   prompt regression after change or conflicting instructions
   How to handle it:
   prompt versioning, rollback, regression test suite
6. Permission failure
   Why it breaks:
   missing ACL propagation or overbroad access
   How to handle it:
   permission-aware retrieval, audit logs, deny-by-default checks
7. Infrastructure failure
   Why it breaks:
   overload, queue growth, network issues, provider rate limits
   How to handle it:
   autoscaling, rate limits, retries with backoff, load shedding

## Security And Permissions

Do not skip this in debugging answers.

A failing AI system can also be a security incident.

Mention:

- verify whether sensitive data was exposed
- confirm access controls still hold
- audit recent permission or policy changes
- redact sensitive content from logs when needed
- use least privilege for tools and services

Strong interview phrase:

"When debugging, I would treat permission issues differently from normal quality issues, because a wrong answer is one problem, but leaked data is a security event."

## Cost Optimization While Debugging

Debugging can create extra cost if you replay requests carelessly.

Be smart about:

- sampling traces instead of replaying everything
- using cheaper models in test environments
- replaying only representative failures
- limiting long prompt comparisons
- batching offline eval runs

## Tradeoffs

Make these explicit:

- latency vs cost:
  deeper debugging instrumentation and fallback paths may cost more
- accuracy vs speed:
  more validation checks can slow the response path
- complexity vs maintainability:
  richer orchestration gives more power but more places to fail
- observability depth vs privacy:
  more logs and traces help debugging but increase sensitive-data risk
- retries vs overload:
  retries can help transient issues but can also amplify failures

## Metrics And Evaluation

System metrics:

- request volume
- error rate
- timeout rate
- P50, P95 latency
- queue depth
- tool failure rate
- cache hit rate

AI quality metrics:

- retrieval recall at top K
- groundedness
- hallucination rate
- answer format success rate
- escalation rate

Operational metrics:

- mean time to detect
- mean time to mitigate
- mean time to recovery
- regression recurrence rate

## Follow-Up Questions

### How do you know whether the issue is retrieval or model quality?

Answer:

"I would inspect traces and compare the retrieved context against the final answer. If the retrieved documents are good but the answer is wrong, the issue is likely in prompt or model behavior. If the context is poor or missing, the issue is more likely retrieval quality, filtering, or freshness."

### What if the system is failing only for one customer or one tenant?

Answer:

"That usually points me toward tenant-specific permissions, data quality, configuration drift, or region-specific infrastructure. I’d compare a working tenant and a failing tenant side by side."

### What happens if tracing itself is incomplete?

Answer:

"Then observability is part of the incident. I’d use whatever logs and metrics exist to localize the gap, restore the missing instrumentation, and treat better tracing as part of the fix so the same class of issue is easier to debug next time."

### How do you reduce cost while improving reliability?

Answer:

"I’d focus first on the highest-cost failure modes, like repeated failed model calls or noisy retries. Better caching, rate limiting, and tighter fallback logic often improve both reliability and cost."

### How do you prevent the same bug from coming back?

Answer:

"I’d add regression tests, trace-based replay, monitoring alerts, and deployment quality gates. The goal is not just to fix the incident once, but to make that class of failure easier to catch before users see it again."

## Quality Gates And Regression Checks

Add these after incidents:

- test cases from real failing inputs
- retrieval regression set
- prompt regression checks
- tool contract tests
- permission test cases
- latency alert thresholds

## RRK Drill Section

Say these out loud without notes:

1. Answer "How do you debug a failing AI system?" in under 2 minutes.
2. Add realistic assumptions for users, QPS, and latency.
3. Walk one request through the system step by step.
4. Explain how to distinguish retrieval failure from prompt failure.
5. Explain one security failure and how you would respond.
6. Give one example of a good retry and one example of a bad retry.
7. Explain how tracing, logging, and monitoring each help differently.
8. Describe what regression checks you would add after the incident.

## RRK Interview Checklist

When answering debugging and reliability prompts, cover these:

1. Scope of failure
2. Reproduction
3. Request tracing
4. Failing stage
5. Immediate mitigation
6. Root cause
7. Reliability improvements
8. Security implications
9. Cost implications
10. Regression prevention

## Common Mistakes

### Mistake 1: Blaming The Model Too Early

Many failures are not model failures.

They can come from:

- bad input
- wrong retrieval
- broken tools
- stale permissions
- infrastructure issues

### Mistake 2: Using Retries For Everything

Retries help transient failures, not deterministic ones.

Retrying a broken prompt or invalid permission state just wastes time and money.

### Mistake 3: Debugging Without Traces

In AI systems, the request path is too long to guess reliably.

Use traces to see what actually happened.

### Mistake 4: Fixing The Incident But Not Adding Regression Coverage

A good engineer does not stop at recovery.

They make the same failure less likely in the future.

## Python Syntax You Must Be Comfortable With

### `append`

```python
stack.append(value)
```

### `pop`

```python
top = stack.pop()
```

### Top Of Stack

```python
stack[-1]
```

### Tuple Storage

```python
stack.append((value, current_min))
```

## Manual Practice Routine

For coding:

1. Say why a stack fits the problem.
2. State what goes on the stack.
3. Explain when you push and pop.
4. Dry run one example.
5. State time and space complexity.

For RRK:

1. Clarify the failing behavior.
2. Narrow scope.
3. Walk the request path.
4. Identify the failing stage.
5. Mitigate.
6. Find root cause.
7. Add regression checks.

## Dry Run Template

Use this for stack problems:

```text
Input:

State before loop:
stack:

Iteration 1:
current value:
push or pop:
stack after:

Return:
```

Use this for debugging an AI system:

```text
Symptom:
Scope:
Recent changes:
Trace path:
Failing stage:
Immediate mitigation:
Root cause:
Regression checks:
```

## Practice Prompts

### Valid Parentheses

Trace this:

```python
s = "([{}])"
```

Expected:

```python
True
```

Trace this:

```python
s = "([)]"
```

Expected:

```python
False
```

### Min Stack

Trace:

```text
push(5)
push(2)
push(4)
getMin()
pop()
getMin()
```

Expected:

```text
2
2
```

### Daily Temperatures

Trace this:

```python
temperatures = [30, 40, 50, 60]
```

Expected:

```python
[1, 1, 1, 0]
```

### RRK

Practice out loud:

```text
How do you debug a failing AI system?
```

Then make sure you cover:

1. Input issue
2. Retrieval issue
3. Tool issue
4. Model issue
5. Prompt issue
6. Permission issue
7. Latency or infrastructure issue
8. Tracing
9. Regression checks

## Final Day 5 Code To Memorize

### Valid Parentheses

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        pairs = {
            ")": "(",
            "]": "[",
            "}": "{",
        }

        for char in s:
            if char in "([{":
                stack.append(char)
            else:
                if not stack:
                    return False

                top = stack.pop()

                if top != pairs[char]:
                    return False

        return len(stack) == 0
```

### Min Stack

```python
class MinStack:

    def __init__(self):
        self.stack = []

    def push(self, val: int) -> None:
        if not self.stack:
            current_min = val
        else:
            current_min = min(val, self.stack[-1][1])

        self.stack.append((val, current_min))

    def pop(self) -> None:
        self.stack.pop()

    def top(self) -> int:
        return self.stack[-1][0]

    def getMin(self) -> int:
        return self.stack[-1][1]
```

### Daily Temperatures

```python
class Solution:
    def dailyTemperatures(self, temperatures: list[int]) -> list[int]:
        answer = [0] * len(temperatures)
        stack = []

        for i, temp in enumerate(temperatures):
            while stack and temperatures[stack[-1]] < temp:
                prev_index = stack.pop()
                answer[prev_index] = i - prev_index

            stack.append(i)

        return answer
```

## Day 5 Self-Test

Answer these without looking:

1. When is a stack better than a queue?
2. Why does Valid Parentheses use last-in-first-out logic?
3. Why is `getMin` O(1) in Min Stack?
4. Why does Daily Temperatures store indexes instead of values?
5. What is the difference between logging, monitoring, and tracing?
6. Why should you not blame the model first?
7. When are retries useful?
8. What is a quality gate?
9. How do you distinguish retrieval failure from model failure?
10. Why do regression checks matter after an incident?

## Day 5 Answers

1. A stack is better when the most recent unfinished item matters first.
2. A closing bracket must match the most recent unmatched opening bracket.
3. Each stack entry stores the minimum so far, so the top entry already knows the current minimum.
4. Because the answer depends on distance to a future index, not just the temperature value.
5. Logging records events, monitoring tracks system health over time, and tracing follows one request through many components.
6. Because many failures come from data, retrieval, tools, permissions, or infrastructure instead.
7. Retries are useful for transient failures like timeouts or temporary rate limits.
8. A quality gate is a check that prevents risky or low-quality output from reaching users.
9. Inspect the retrieved context and compare it with the final answer in a request trace.
10. Because the goal is not only to fix the bug once, but to make it easier to catch next time.

## Day 5 Interview Phrases

Use these phrases until they become natural:

- "I use a stack because the most recent unmatched item is the one that matters next."
- "For matching problems, I push on open and validate on close."
- "For Min Stack, I store extra state so minimum lookup stays O(1)."
- "For Daily Temperatures, unresolved indexes wait on the stack until a warmer future day appears."
- "I would debug the AI system by first locating the failing stage instead of blaming the model immediately."
- "Tracing helps me compare a healthy request and a failing request step by step."
- "A production incident should end with regression checks, not just a rollback."
- "Retries help transient failures, but they are the wrong tool for deterministic failures."
- "I would separate correctness issues, latency issues, and permission issues early."
- "Observability is how I turn a vague failure into a specific, fixable problem."

## Day 5 Completion Criteria

You are done with Day 5 when you can:

1. Explain stack in plain English.
2. Implement Valid Parentheses without looking.
3. Implement Min Stack without looking.
4. Explain Daily Temperatures at an exposure level.
5. State time and space complexity for all three coding problems.
6. Explain the difference between logging, monitoring, and tracing.
7. Verbally answer "How do you debug a failing AI system?"
8. Distinguish input, retrieval, tool, model, prompt, permission, and infrastructure failures.
9. Explain how tracing and regression checks improve reliability.
