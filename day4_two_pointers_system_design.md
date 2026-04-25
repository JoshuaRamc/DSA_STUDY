# Day 4: Two Pointers + System Design Basics

Date: April 24, 2026
Label: day4_two_pointers_system_design
Focus: Two pointers, moving inward from both ends, sorted array patterns, pair and triplet search, and core system design thinking for scale.

## Goal For Today

By the end of Day 4, you should be able to:

1. Recognize when two pointers are better than nested loops.
2. Explain why moving the left or right pointer is logically valid.
3. Use sorted input to reduce search space.
4. Talk through basic system design tradeoffs like scale, latency, throughput, bottlenecks, and cost.

This day matters because interviewers often want both algorithmic reasoning and systems reasoning in the same loop.

## Coding Concepts

### Two Pointers

Two pointers means using two positions in an array or string and moving them according to rules.

Common setup:

```python
left = 0
right = len(nums) - 1
```

You then decide which pointer to move based on the current values.

### Why Two Pointers Works

Two pointers is useful when:

- The input is sorted.
- You want a pair or triplet.
- You compare values from both ends.
- Moving one pointer changes the search space in a predictable way.

This is the key interview sentence:

"I can use two pointers because the input structure lets me eliminate part of the search space after each comparison."

### Moving Inward From Both Ends

In some problems, the useful information sits at the ends.

Example setup:

```python
left = 0
right = len(height) - 1
```

At each step:

1. Evaluate the pair at `left` and `right`.
2. Update the best answer.
3. Move the pointer that cannot lead to a better result unless it changes.

### Sorted Input Patterns

Sorted arrays are powerful because order gives you information.

Example:

```python
nums = [2, 7, 11, 15]
target = 9
```

If:

```python
nums[left] + nums[right] > target
```

then moving `right` leftward may reduce the sum.

If:

```python
nums[left] + nums[right] < target
```

then moving `left` rightward may increase the sum.

That is what makes pointer movement valid instead of random.

### Pair Search

Use pair search when:

- You need two values.
- The array is sorted or can be reasoned about with order.
- One comparison tells you how to move.

### Triplet Search

Triplet search often means:

1. Fix one number.
2. Solve a two-pointer pair problem on the rest of the array.

This is a common pattern for `3Sum`.

## Pattern 1: Opposite Ends

Use this when:

- You start from both ends.
- The pair at the ends tells you which side to move.
- You want O(n) instead of O(n^2).

Template:

```python
left = 0
right = len(nums) - 1

while left < right:
    current = some_function(nums[left], nums[right])

    if should_move_left:
        left += 1
    else:
        right -= 1
```

## Pattern 2: Fixed Index + Two Pointers

Use this when:

- You need triplets.
- You can sort the array first.
- For each fixed index, the remainder becomes a two-pointer search.

Template:

```python
nums.sort()

for i in range(len(nums)):
    left = i + 1
    right = len(nums) - 1

    while left < right:
        total = nums[i] + nums[left] + nums[right]
```

## Problem 1: Container With Most Water

LeetCode: 11. Container With Most Water
Difficulty: Medium
Pattern: Two pointers from both ends
Core structure: Array + area calculation

## Status

[X ] COMPLETED CONTAINER WITH MOST WATER SUCCESSFULLY

### Problem Statement

You are given an integer array `height` where each element is a vertical line.

Find two lines that together with the x-axis form a container that holds the most water.

Return the maximum amount of water.

Example:

```python
height = [1, 8, 6, 2, 5, 4, 8, 3, 7]
# return 49
```

### Key Insight

The area is determined by:

```python
width = right - left
height_of_container = min(height[left], height[right])
area = width * height_of_container
```

If one side is shorter, that shorter side limits the area.

So after checking the current area, move the shorter side inward.

Why?

Because keeping the shorter side while shrinking width cannot improve the answer. The only hope is to find a taller shorter side.

### Code

```python
class Solution:
    def maxArea(self, height: list[int]) -> int:
        left = 0
        right = len(height) - 1
        best = 0

        while left < right:
            width = right - left
            current_height = min(height[left], height[right])
            area = width * current_height
            best = max(best, area)

            if height[left] < height[right]:
                left += 1
            else:
                right -= 1

        return best
```

### Walkthrough

Input:

```python
height = [1, 8, 6, 2, 5, 4, 8, 3, 7]
```

Start:

```python
left = 0
right = 8
```

Trace:

```text
left = 0, right = 8
heights are 1 and 7
width = 8
area = 8 * min(1, 7) = 8
best = 8
move left because 1 is the shorter side

left = 1, right = 8
heights are 8 and 7
width = 7
area = 7 * min(8, 7) = 49
best = 49
move right because 7 is the shorter side
```

### Complexity

Time: O(n)

Space: O(1)

### Interview Explanation

"I use two pointers starting at both ends. The area depends on width times the shorter of the two heights. After computing the current area, I move the pointer at the shorter height, because that side is the bottleneck. Moving the taller side would only reduce width without increasing the limiting height."

## Problem 2: Two Sum II

LeetCode: 167. Two Sum II - Input Array Is Sorted
Difficulty: Medium
Pattern: Two pointers on sorted array
Core structure: Sorted array + pair search

## Status

[X ] COMPLETED TWO SUM II SUCCESSFULLY

### Problem Statement

Given a 1-indexed array of integers `numbers` that is sorted in non-decreasing order, find two numbers such that they add up to a specific `target`.

Return the indices of the two numbers, added by one.

Example:

```python
numbers = [2, 7, 11, 15]
target = 9
# return [1, 2]
```

### Key Insight

Because the array is sorted:

- If the sum is too small, move `left` rightward to increase it.
- If the sum is too large, move `right` leftward to decrease it.

Each move eliminates impossible pairs.

### Code

```python
class Solution:
    def twoSum(self, numbers: list[int], target: int) -> list[int]:
        left = 0
        right = len(numbers) - 1

        while left < right:
            total = numbers[left] + numbers[right]

            if total == target:
                return [left + 1, right + 1]

            if total < target:
                left += 1
            else:
                right -= 1
```

### Walkthrough

Input:

```python
numbers = [2, 7, 11, 15]
target = 9
```

Trace:

```text
left = 0, right = 3
2 + 15 = 17
too large
move right leftward

left = 0, right = 2
2 + 11 = 13
too large
move right leftward

left = 0, right = 1
2 + 7 = 9
match
return [1, 2]
```

### Complexity

Time: O(n)

Space: O(1)

### Interview Explanation

"The sorted order lets me use two pointers. I start at both ends and compute the sum. If the sum is too small, I move the left pointer to increase it. If the sum is too large, I move the right pointer to decrease it. Because the array is sorted, these moves are valid and let me eliminate parts of the search space."

## Problem 3: 3Sum

LeetCode: 15. 3Sum
Difficulty: Medium
Pattern: Sort + fixed index + two pointers
Core structure: Array + triplet search

## Status

[ X] COMPLETED 3SUM FOR EXPOSURE

### Problem Statement

Given an integer array `nums`, return all unique triplets `[nums[i], nums[j], nums[k]]` such that:

```python
i != j
j != k
i != k
```

and:

```python
nums[i] + nums[j] + nums[k] == 0
```

### Key Insight

Sort the array first.

Then:

1. Fix one number at index `i`.
2. Use two pointers on the rest of the array to find pairs that sum to `-nums[i]`.
3. Skip duplicates so the result only contains unique triplets.

### Code

```python
class Solution:
    def threeSum(self, nums: list[int]) -> list[list[int]]:
        nums.sort()
        result = []

        for i in range(len(nums)):
            if i > 0 and nums[i] == nums[i - 1]:
                continue

            left = i + 1
            right = len(nums) - 1

            while left < right:
                total = nums[i] + nums[left] + nums[right]

                if total < 0:
                    left += 1
                elif total > 0:
                    right -= 1
                else:
                    result.append([nums[i], nums[left], nums[right]])
                    left += 1
                    right -= 1

                    while left < right and nums[left] == nums[left - 1]:
                        left += 1

                    while left < right and nums[right] == nums[right + 1]:
                        right -= 1

        return result
```

### Walkthrough

Input:

```python
nums = [-1, 0, 1, 2, -1, -4]
```

After sorting:

```python
[-4, -1, -1, 0, 1, 2]
```

Trace:

```text
i = 0, nums[i] = -4
Need pair sum 4
No valid pair found

i = 1, nums[i] = -1
Need pair sum 1
left = 2, right = 5
-1 + -1 + 2 = 0
append [-1, -1, 2]

move inward
left = 3, right = 4
-1 + 0 + 1 = 0
append [-1, 0, 1]
```

### Complexity

Time: O(n^2)

Why: sorting is O(n log n), but the dominant part is the outer loop plus inner two-pointer scan.

Space: O(1) extra space ignoring output

### Interview Explanation

"I sort the array so I can use two pointers. For each index `i`, I treat `nums[i]` as the fixed first number and search for two numbers to the right that bring the total to zero. I skip duplicates at the fixed index and after finding a valid triplet so the output stays unique."

## Coding Patterns Side By Side

### Container With Most Water

Question:

```text
Which two ends create the largest area?
```

Main pattern:

```python
if height[left] < height[right]:
    left += 1
else:
    right -= 1
```

### Two Sum II

Question:

```text
Which pair in this sorted array makes the target?
```

Main pattern:

```python
if total < target:
    left += 1
else:
    right -= 1
```

### 3Sum

Question:

```text
Can I fix one value and solve the remaining pair search with two pointers?
```

Main pattern:

```python
nums.sort()
```

then:

```python
left = i + 1
right = len(nums) - 1
```

## RRK: System Design Basics

System design interviews are often less about perfect architecture and more about showing that you can reason clearly under scale, uncertainty, and tradeoffs.

For Day 4, your goal is not to become a distributed systems expert in one sitting. Your goal is to build a stable structure for answering system design prompts.

## Core System Design Structure

A simple structure for most system design answers:

1. Clarify the use case.
2. State assumptions about scale.
3. Sketch the high-level architecture.
4. Walk through the request path.
5. Identify bottlenecks and scaling strategies.
6. Discuss observability, reliability, and cost.

This sentence helps in interviews:

"I'll start with assumptions and a simple baseline architecture, then scale it step by step based on traffic, latency, and cost constraints."

## Scale Assumptions

Before designing, define what scale means.

Good assumption categories:

- Number of users
- Requests per second
- Peak traffic patterns
- Data volume
- Latency requirements
- Availability requirements
- Budget or cost sensitivity

Example:

```text
50 users is a small internal deployment with light concurrency.
1 million users implies much higher concurrency, stricter reliability, and stronger cost controls.
```

## Latency Vs Cost

This is a core tradeoff.

Lower latency usually costs more because you may need:

- More replicas
- More caching
- More precomputation
- Faster but more expensive infrastructure
- More model calls or larger models

Lower cost may increase:

- Wait times
- Use of queues
- Batch processing
- Smaller models
- More aggressive caching

Interview phrase:

"I would separate user-facing synchronous paths from slower asynchronous workflows so I can protect latency without letting cost grow uncontrollably."

## Throughput

Throughput means how much work the system can handle over time.

Examples:

- Requests per second
- Jobs per minute
- Documents processed per hour

A system can have:

- Good latency for one request
- But poor throughput under heavy concurrency

That distinction matters.

## Bottlenecks

A bottleneck is the part of the system that limits total performance.

Common bottlenecks:

- API servers
- Databases
- LLM inference
- Vector search
- Queue consumers
- Network bandwidth
- Third-party APIs

Good interview habit:

Name the likely bottlenecks early, then explain how you would relieve them.

## Practice Prompt

Practice this out loud:

```text
How would you scale your EY agentic AI platform from 50 users to 1 million users?
```

## Scaling The EY Agentic AI Platform

### Problem Framing

Start with a calm framing:

```text
I would assume this is an agentic AI platform that serves internal or client-facing workflows through APIs and asynchronous job execution. Going from 50 users to 1 million users changes the problem from simple functionality to scale, reliability, observability, and cost control.
```

### Baseline At 50 Users

At 50 users, the platform can be relatively simple:

- One API layer
- One primary database
- A small worker pool
- Direct model calls
- Basic logging and dashboards

This is enough for early product iteration, but it will not hold for 1 million users.

### High-Level Scaled Architecture

```text
Clients
-> API gateway
-> load balancer
-> stateless API services
-> auth and rate limiting
-> orchestration service
-> cache
-> databases and storage
-> async queues
-> worker services
-> model providers and internal tools
-> observability stack
```

## Must-Cover Areas

### API Layer

The API layer should be stateless so it can scale horizontally.

Responsibilities:

- Authentication
- Authorization
- Request validation
- Rate limiting
- Request routing
- Returning fast acknowledgements for async jobs

Why stateless?

Because stateless services are easier to replicate behind a load balancer.

### Load Balancing

Load balancing distributes traffic across many API instances.

Benefits:

- Prevents a single server from being overloaded
- Improves availability
- Supports horizontal scaling

For scale growth:

- Start with a managed load balancer
- Add health checks
- Route traffic to healthy instances only

### Async Workflows

Agentic AI systems often do slow work:

- Multi-step reasoning
- Tool usage
- Document retrieval
- Long-running generation
- External API calls

Do not force every workflow into a synchronous request.

Better pattern:

1. User sends request.
2. API validates and creates a job.
3. Job is pushed to a queue.
4. Worker processes it asynchronously.
5. User polls or gets notified when it completes.

This protects the API layer from long-lived requests.

### Queueing

Queues help absorb spikes and decouple services.

Benefits:

- Smooth bursty traffic
- Retry failed jobs
- Separate fast ingestion from slow processing
- Control concurrency

Examples of queue use:

- Agent task execution
- Document ingestion
- Evaluation jobs
- Email or webhook notifications

### Caching

Caching reduces both latency and cost.

Things you might cache:

- Authentication results
- Frequent read queries
- Document metadata
- Popular retrieval results
- Repeated prompt outputs when safe
- Feature flags and configuration

Important note:

Do not cache blindly for AI outputs if freshness or permissions matter.

Interview phrase:

"I would cache aggressively at safe boundaries, especially metadata and repeated reads, but I would be careful with user-specific and permission-sensitive responses."

### Rate Limits

Rate limits protect the platform from abuse and accidental overload.

They also control downstream cost.

Rate limit dimensions:

- Per user
- Per API key
- Per tenant
- Per minute
- Per expensive action like model invocation

This matters a lot for AI systems because model calls can be costly.

### Horizontal Scaling

Horizontal scaling means adding more instances instead of just using a bigger machine.

Good candidates for horizontal scaling:

- API servers
- Worker fleets
- Retrieval services
- Stateless orchestrators

This works best when services are stateless and coordination is handled through shared storage or queues.

### Observability

You need to know what the system is doing in production.

Observability includes:

- Logs
- Metrics
- Traces
- Alerts
- Dashboards

Key metrics:

- Request latency
- Error rates
- Queue depth
- Worker backlog
- Token usage
- Model cost per request
- Cache hit rate
- Retrieval latency
- Job success rate

Interview phrase:

"I would instrument both the synchronous request path and the asynchronous job path so I can see latency, failures, queue buildup, and cost in real time."

### Cost Controls

At 1 million users, cost can become the real bottleneck.

Cost controls include:

- Caching
- Smaller models for simpler tasks
- Routing only hard tasks to expensive models
- Timeouts on slow tools
- Token limits
- Budget alerts
- Batch processing where possible
- Separate service tiers

This is a strong interview sentence:

"I would not send every request to the most expensive model. I would use model routing and cost-aware orchestration so the platform stays economically viable at scale."

## Request Flow Example

A good verbal walkthrough:

```text
A client request first hits the API gateway and load balancer, then lands on a stateless API service. The API authenticates the request, applies rate limits, and decides whether the work is synchronous or asynchronous. If the task is lightweight, it can return immediately. If the task triggers a long-running agent workflow, the API stores job metadata and pushes a task onto a queue. Worker services consume jobs from the queue, call retrieval systems, internal tools, and model providers, then persist results. The client either polls for results or receives a callback. Throughout the flow, metrics, logs, and traces are emitted for observability.
```

## Bottlenecks At 1 Million Users

Likely bottlenecks:

1. API saturation under burst traffic
2. Queue backlog from slow agent jobs
3. LLM cost and latency
4. Retrieval latency and vector database load
5. Database hot spots
6. Third-party dependency failures

How to respond:

- Add more stateless API replicas
- Partition worker pools by job type
- Use separate queues for fast and slow jobs
- Cache repeated reads
- Add circuit breakers and retries
- Use model routing
- Scale databases with replicas, partitioning, or workload separation

## Spoken System Design Answer

Practice this:

"I would scale the EY agentic AI platform by separating the system into stateless API services, asynchronous workflow processing, and shared storage layers. At the front door, I would put an API gateway and load balancer so traffic can be distributed across many API instances. The API layer should stay stateless and handle auth, validation, rate limiting, and request routing. For long-running agent tasks, I would avoid blocking the request thread and instead enqueue jobs for worker services. Queueing lets us absorb traffic spikes and scale worker fleets independently. I would add caching for repeated reads and safe reusable results to reduce both latency and infrastructure cost. As traffic grows, I would horizontally scale the API and worker layers, and I would instrument the whole platform with logs, metrics, traces, and alerts so we can detect queue buildup, latency regressions, and rising model cost. I would also add cost controls like model routing, token budgets, and tighter rate limits so the platform remains reliable and economically sustainable at 1 million users."

## Google-Level System Design Structure

For Google-style RRK answers, force yourself through this order:

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

"I'll start with assumptions and a simple architecture, then I'll go deeper on the components most likely to become bottlenecks as the platform grows from dozens of users to millions."

## Quantitative Thinking For This Prompt

Put real numbers on the problem.

Example assumptions:

- 1 million registered users
- 100,000 daily active users
- 5 requests per active user per day
- 500,000 requests per day
- Average QPS is about `500,000 / 86,400`, or around 6 QPS
- Peak traffic may be 10 to 20 times the average, so design for 60 to 120 QPS at minimum
- If this is enterprise or multi-tenant and bursty, a safer peak target might be 500 QPS
- Latency target for lightweight API calls: under 500 ms
- Latency target for synchronous AI responses: under 2 seconds at P50, under 5 seconds at P95
- Long-running workflows should be pushed to async paths
- If each workflow generates 200 KB of logs and artifacts, then 500,000 daily requests can produce about 100 GB per day of operational data

Useful sentence:

"I would explicitly design for peak concurrency, not just average QPS, because the platform can look healthy on paper but still fail during burst traffic."

## Interview-Ready Answer

"I would scale the platform by separating synchronous user-facing traffic from asynchronous agent workflows. I'd start with a stateless API layer behind a load balancer, then move long-running agent tasks onto queues and distributed workers so the request path stays responsive. I'd assume something like 100,000 daily active users and plan for peak traffic in the hundreds of QPS, with a sub-500 ms target for lightweight API responses and a few seconds for synchronous AI responses. From there I'd focus on the likely bottlenecks: model latency and cost, queue backlog, retrieval latency, and database hot spots. I'd scale stateless services horizontally, add caching where it is safe, enforce rate limits per user and tenant, and instrument the whole platform with logs, metrics, traces, and budget alerts."

## Clarify The Problem Like Google

Before designing, ask:

1. Is this platform internal, client-facing, or both?
2. Are workloads synchronous chat requests, batch jobs, or both?
3. Is the platform multi-tenant?
4. What is the target latency for user-facing operations?
5. Are there compliance or data residency constraints?
6. What are the highest-cost operations today?

If the interviewer does not answer, state assumptions and continue.

## High-Level Architecture With Cloud-Abstraction First

Abstract components:

```text
Clients
-> stateless API layer
-> auth, tenant isolation, and rate limiting
-> orchestration layer
-> cache
-> message queues
-> distributed workers
-> storage systems
-> retrieval and model-serving dependencies
-> observability and evaluation pipeline
```

Optional AWS and Azure mappings:

- Stateless API layer behind a load balancer
  AWS: `ALB` or `API Gateway` with `ECS`, `EKS`, or `Lambda`
  Azure: `Application Gateway` or `API Management` with `AKS`, `Container Apps`, or `Functions`
- Message queue for async workflows
  AWS: `SQS` or `EventBridge`
  Azure: `Service Bus` or `Event Grid`
- Distributed worker fleet
  AWS: `ECS`, `EKS`, or `Lambda`
  Azure: `AKS`, `Container Apps`, or `Functions`
- Cache layer
  AWS: `ElastiCache`
  Azure: `Azure Cache for Redis`
- Object and operational storage
  AWS: `S3`, `RDS`, `DynamoDB`
  Azure: `Blob Storage`, `Azure SQL`, `Cosmos DB`

Keep the explanation system-first. The cloud mapping is a bonus, not the main answer.

## Component Deep Dive

### API Layer

Responsibilities:

- Authentication
- Authorization
- Input validation
- Tenant routing
- Rate limiting
- Fast acknowledgment for async jobs

This layer should be stateless so it can scale horizontally.

### Orchestration Layer

Responsibilities:

- Decide whether work is synchronous or asynchronous
- Coordinate tool calls
- Apply policy checks
- Track workflow state

For agentic systems, orchestration logic should be observable and replayable.

### Queue And Worker Layer

Responsibilities:

- Absorb bursts
- Decouple services
- Retry transient failures
- Run long workflows without tying up API threads

### Storage Systems

Split storage by access pattern:

- Fast metadata lookups
- Workflow state
- Logs and artifacts
- Analytics and evaluation data

This is usually better than forcing everything into one database.

## Scalability Discussion

At 50 users:

- One API tier may be enough
- A small worker pool is enough
- Minimal caching may be acceptable

At 1 million users:

- API servers must scale horizontally
- Worker pools likely need to be partitioned by workload
- Queues need backpressure and monitoring
- Data stores may need replicas, partitioning, or workload separation
- Expensive AI paths need stricter admission control and model routing

Good sentence:

"I would not scale every component the same way. I'd scale API, async workers, storage, and AI dependencies independently based on which one becomes the bottleneck."

## Reliability And Failure Handling

### Failure Modes

1. API saturation
   Why it breaks:
   burst traffic overwhelms stateless services
   How to handle it:
   autoscaling, load shedding, rate limits, admission control
2. Queue backlog
   Why it breaks:
   worker throughput is lower than job arrival rate
   How to handle it:
   more workers, workload-specific queues, retry policies, dead-letter queues
3. Database hot spots
   Why it breaks:
   too many reads or writes target the same key or table
   How to handle it:
   caching, read replicas, partitioning, schema redesign
4. LLM or external API failures
   Why it breaks:
   timeout, outage, or rate limit from a dependency
   How to handle it:
   circuit breakers, retries with backoff, fallback models, graceful degradation
5. Stale cached data
   Why it breaks:
   cache is cheaper and faster but may serve outdated state
   How to handle it:
   TTLs, cache invalidation on writes, safe cache boundaries
6. Bad workflow state transitions
   Why it breaks:
   complex agent orchestration can create partial failures
   How to handle it:
   idempotent job handlers, workflow checkpoints, compensating actions

## Security, Compliance, And Permissions

This should be explicit in your answer.

Mention:

- Strong authentication
- Tenant isolation
- Access control at the API and data layer
- Encryption in transit and at rest
- Audit logs
- Secrets management
- PII minimization and redaction
- Safe handling of model prompts and logs

Strong phrase:

"At this scale, security is not a side topic. I would enforce tenant isolation and least privilege throughout the request path, and I would avoid logging sensitive prompt or customer data unless there is a strong operational reason."

## Cost Optimization

At scale, cost becomes a first-class design concern.

Main levers:

- Use async workflows for long jobs
- Cache repeated reads
- Route simple tasks to cheaper models
- Apply strict timeouts and budgets to tool calls
- Use smaller worker shapes for lightweight jobs
- Batch offline evaluation and ingestion work
- Rate limit expensive operations

Strong phrase:

"I would treat model invocation, queue depth, and storage growth as budgeted resources, not just technical resources."

## Tradeoffs

Call these out directly:

- Latency vs cost:
  faster and more replicated systems cost more
- Accuracy vs speed:
  deeper orchestration and more validation improve quality but take longer
- Complexity vs maintainability:
  multi-stage async systems scale better but are harder to debug
- Consistency vs availability:
  strict coordination can reduce availability under failure
- Generality vs optimization:
  a flexible platform is easier to extend, but specialized fast paths may be needed for core workloads

## Metrics And Evaluation

System metrics:

- Request rate
- P50, P95, P99 latency
- Error rate
- Queue depth
- Worker utilization
- Cache hit rate
- Database latency
- Cost per request
- Cost per successful workflow

AI and product metrics:

- Task success rate
- Tool failure rate
- Model latency
- Hallucination rate when applicable
- User satisfaction
- Time saved
- Escalation rate

## Interviewer Follow-Up Questions

### How does this scale to millions of users?

Answer:

"I'd scale the stateless API layer horizontally, decouple slow work through queues, and independently scale the worker fleet and storage systems. I'd also add caching and tighter cost-aware routing so the platform remains both fast and economically sustainable."

### What are the bottlenecks?

Answer:

"The bottlenecks are likely the expensive AI path, queue backlog, and data-store pressure. I'd verify that with metrics rather than guess, then optimize the highest-latency or highest-cost component first."

### What happens if a worker fleet falls behind?

Answer:

"Queue depth will rise, latency will grow, and eventually jobs may miss SLOs. I'd partition queues by workload, scale consumers independently, and add dead-letter handling for poison jobs."

### How do you measure success?

Answer:

"I'd measure user-facing latency and reliability, but also cost per workflow, queue health, and business-level task completion. A system that is fast but too expensive, or cheap but unreliable, is still not successful."

### How do you reduce cost?

Answer:

"I'd reduce cost by caching safe repeated work, routing simple requests to cheaper models or simpler execution paths, and keeping long-running workflows asynchronous so I'm not overprovisioning the synchronous tier."

## Google-Style Deep Dive On The EY Prompt

### Clarify

State assumptions like:

```text
I'll assume this is a multi-tenant agentic AI platform serving enterprise workflows, with both synchronous user interactions and asynchronous background tasks. I'll assume peak traffic in the hundreds of QPS and strong expectations around tenant isolation, latency, and cost control.
```

### State Assumptions

Example:

- 100,000 DAU
- 500 QPS peak
- P95 synchronous latency under 5 seconds
- Async jobs may run for minutes
- 99.9% availability target for core API operations

### High-Level Architecture

Describe it in this order:

```text
client
-> stateless API services
-> auth, tenant policy, and rate limits
-> orchestration layer
-> queue for long-running work
-> distributed workers
-> storage and retrieval systems
-> observability and cost controls
```

### Component Deep Dive

Spend time on:

- queue isolation by job type
- worker autoscaling
- cache strategy
- database separation by workload
- AI provider fallback

### Reliability And Failure Handling

Mention:

- retries
- idempotency
- circuit breakers
- dead-letter queues
- graceful degradation

### Security And Permissions

Mention:

- tenant isolation
- least privilege
- encrypted storage
- audit logs
- prompt and output redaction when needed

### Cost Optimization

Mention:

- model routing
- caching
- token budgeting
- async processing
- usage quotas

### Metrics And Evaluation

Mention:

- latency SLOs
- queue depth
- cost per workflow
- error rate
- task completion

## RRK Drill Section

Say these out loud without notes:

1. Answer the EY scaling prompt using the 10-step Google structure.
2. Add concrete QPS, latency, and storage assumptions.
3. Explain the first three bottlenecks you would expect.
4. Describe one failure in the queue layer and one failure in the data layer.
5. Explain one security risk and how you would prevent it.
6. Give a latency-vs-cost tradeoff.
7. Map your design to both AWS and Azure.
8. Give a 90-second answer and then a 3-minute deeper version.

## RRK Interview Checklist

When asked a system design scaling question, cover these:

1. Assumptions about scale
2. API layer
3. Load balancing
4. Async workflows
5. Queueing
6. Caching
7. Rate limits
8. Horizontal scaling
9. Observability
10. Cost controls

## Common Mistakes

### Mistake 1: Moving A Pointer Without Justification

In two-pointer problems, always explain why pointer movement is valid.

Weak answer:

```text
I move left or right depending on the sum.
```

Better answer:

```text
Because the array is sorted, moving left increases the sum and moving right decreases the sum.
```

### Mistake 2: Forgetting Sorted Input Matters

Two Sum II depends on sorted order.

Without sorted order, this exact pointer logic would not be valid.

### Mistake 3: Forgetting Duplicate Handling In 3Sum

If you do not skip duplicates, you may return repeated triplets.

### Mistake 4: Treating Scale As A Vague Word

Say what scale means:

- users
- requests per second
- concurrency
- data volume
- cost

### Mistake 5: Designing Everything As Synchronous

At large scale, long-running work usually belongs in async workflows with queues and workers.

### Mistake 6: Ignoring Cost In AI Platforms

A technically correct design can still fail if model and infrastructure cost explode.

## Python Syntax You Must Be Comfortable With

### `min`

```python
current_height = min(height[left], height[right])
```

### `sort`

```python
nums.sort()
```

### Duplicate Skip Pattern

```python
while left < right and nums[left] == nums[left - 1]:
    left += 1
```

### 1-Indexed Return

```python
return [left + 1, right + 1]
```

Two Sum II returns 1-indexed positions, not 0-indexed positions.

## Manual Practice Routine

For coding:

1. Say why two pointers is valid.
2. State what each pointer represents.
3. Explain the movement rule.
4. Dry run one example.
5. State time and space complexity.

For system design:

1. Clarify the use case.
2. State scale assumptions.
3. Draw the high-level architecture.
4. Walk the request path.
5. Identify bottlenecks.
6. Explain scaling strategy.
7. Mention observability and cost controls.

## Dry Run Template

Use this for two pointers:

```text
Input:

State before loop:
left:
right:
best or target:

Iteration 1:
values at left and right:
computed result:
why move left or right:
state after:

Return:
```

Use this for system design:

```text
Use case:
Scale assumptions:
API layer:
Traffic distribution:
Async path:
Queueing:
Caching:
Rate limits:
Observability:
Cost controls:
Bottlenecks:
```

## Practice Prompts

### Container With Most Water

Trace this:

```python
height = [1, 8, 6, 2, 5, 4, 8, 3, 7]
```

Expected:

```python
49
```

### Two Sum II

Trace this:

```python
numbers = [2, 3, 4]
target = 6
```

Expected:

```python
[1, 3]
```

### 3Sum

Trace this:

```python
nums = [-1, 0, 1, 2, -1, -4]
```

Expected:

```python
[[-1, -1, 2], [-1, 0, 1]]
```

Order of triplets does not matter.

### RRK

Practice out loud:

```text
How would you scale your EY agentic AI platform from 50 users to 1 million users?
```

Then make sure you cover:

1. API layer
2. Load balancing
3. Async workflows
4. Queueing
5. Caching
6. Rate limits
7. Horizontal scaling
8. Observability
9. Cost controls

## Final Day 4 Code To Memorize

### Container With Most Water

```python
class Solution:
    def maxArea(self, height: list[int]) -> int:
        left = 0
        right = len(height) - 1
        best = 0

        while left < right:
            width = right - left
            current_height = min(height[left], height[right])
            area = width * current_height
            best = max(best, area)

            if height[left] < height[right]:
                left += 1
            else:
                right -= 1

        return best
```

### Two Sum II

```python
class Solution:
    def twoSum(self, numbers: list[int], target: int) -> list[int]:
        left = 0
        right = len(numbers) - 1

        while left < right:
            total = numbers[left] + numbers[right]

            if total == target:
                return [left + 1, right + 1]

            if total < target:
                left += 1
            else:
                right -= 1
```

### 3Sum

```python
class Solution:
    def threeSum(self, nums: list[int]) -> list[list[int]]:
        nums.sort()
        result = []

        for i in range(len(nums)):
            if i > 0 and nums[i] == nums[i - 1]:
                continue

            left = i + 1
            right = len(nums) - 1

            while left < right:
                total = nums[i] + nums[left] + nums[right]

                if total < 0:
                    left += 1
                elif total > 0:
                    right -= 1
                else:
                    result.append([nums[i], nums[left], nums[right]])
                    left += 1
                    right -= 1

                    while left < right and nums[left] == nums[left - 1]:
                        left += 1

                    while left < right and nums[right] == nums[right + 1]:
                        right -= 1

        return result
```

## Day 4 Self-Test

Answer these without looking:

1. When is two pointers better than nested loops?
2. Why is moving the shorter side valid in Container With Most Water?
3. Why does sorted order matter in Two Sum II?
4. Why do we sort before 3Sum?
5. What is throughput?
6. What is a bottleneck?
7. Why do async workflows help at scale?
8. Why do queues help with bursty traffic?
9. Why are rate limits important in AI systems?
10. Why are cost controls part of good system design?

## Day 4 Answers

1. Two pointers is useful when pointer movement can eliminate part of the search space predictably.
2. Because the shorter side limits the current area, and keeping it while reducing width cannot improve the answer.
3. Sorted order lets you know that moving left increases the sum and moving right decreases it.
4. Sorting allows the fixed-index plus two-pointer pattern and makes duplicate handling easier.
5. Throughput is how much work the system can process over time, such as requests per second.
6. A bottleneck is the component that limits overall performance or capacity.
7. Async workflows keep slow work off the synchronous request path.
8. Queues absorb spikes, decouple services, and let workers process jobs at controlled rates.
9. Rate limits protect reliability, prevent abuse, and control downstream model cost.
10. Because a system that works technically but is too expensive to operate is still a bad design.

## Day 4 Interview Phrases

Use these phrases until they become natural:

- "I can use two pointers because each movement reduces the search space in a justified way."
- "Since the array is sorted, moving left increases the sum and moving right decreases it."
- "The shorter side is the bottleneck for the current container area."
- "For 3Sum, I sort first, fix one index, then run a two-pointer search on the remaining range."
- "I would start with assumptions about traffic, concurrency, and latency goals."
- "I would keep the API layer stateless so it can scale horizontally."
- "Long-running agent tasks should move to asynchronous workflows."
- "Queues help smooth spikes and protect downstream systems."
- "I would instrument the platform with logs, metrics, traces, and alerts."
- "Cost controls matter because AI platforms can become expensive before they become technically impossible."

## Day 4 Completion Criteria

You are done with Day 4 when you can:

1. Explain why two pointers works for sorted pair search.
2. Implement Container With Most Water without looking.
3. Implement Two Sum II without looking.
4. Explain 3Sum well enough to show exposure-level understanding.
5. State time and space complexity for all three problems.
6. Explain scale assumptions, throughput, latency, bottlenecks, and cost tradeoffs.
7. Verbally answer the EY platform scaling prompt.
8. Cover API layer, load balancing, async workflows, queueing, caching, rate limits, horizontal scaling, observability, and cost controls without being prompted.
