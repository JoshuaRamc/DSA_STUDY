# Google Forward Deployed Engineer, GenAI: High-Level Study Plan

**Interview Date:** Monday, May 4, 2026  
**Role:** Forward Deployed Engineer, GenAI  
**Interview Rounds:** Role Related Knowledge, Coding  
**Strategy:** 70% Coding, 30% RRK/System Design

---

## Overall Goal

Prepare to perform well in two areas:

1. **Coding / DSA**
   - Write clean Python in a shared interview document.
   - Recognize common patterns quickly.
   - Explain brute force, optimized approach, time complexity, and space complexity.

2. **Role Related Knowledge / RRK**
   - Design production-grade AI systems.
   - Explain tradeoffs around scale, latency, reliability, cost, security, and evaluation.
   - Communicate clearly through vague and open-ended problems.

---

## Daily Study Split

- **70% Coding**
- **30% RRK / AI System Design**

Each day:

1. Learn the core pattern.
2. Solve the assigned problems traditionally.
3. Recreate at least one solution in plain text without autocomplete.
4. Practice one RRK answer out loud.
5. Write down what confused you.

---

# Day 3: Sliding Window + Intro to Production AI Systems

## Coding Focus

Learn:

- Sliding window
- Expand/shrink pattern
- Contiguous substring/subarray logic
- One-pass running best patterns

Problems:

- Longest Substring Without Repeating Characters
- Best Time to Buy and Sell Stock
- Maximum Subarray
- Optional: Maximum Average Subarray I or Minimum Size Subarray Sum

Key ideas:

- Sliding window uses `left` and `right` pointers.
- Use a set or hashmap to track current window state.
- Shrink when the window becomes invalid.
- Not every one-pass problem is sliding window.

## RRK Focus

Learn:

- What makes an AI system production-grade
- RAG basics
- LLM generation
- Evaluation
- Fallback handling
- Permissions

Practice prompt:

> Design an AI agent for internal company Q&A.

Must cover:

- Users and use case
- Data sources
- Ingestion
- Chunking and embeddings
- Vector retrieval
- Permission-aware retrieval
- LLM answer generation
- Citations
- Evaluation
- Fallbacks
- Monitoring

---

# Day 4: Two Pointers + System Design Basics

## Coding Focus

Learn:

- Two pointers
- Moving inward from both ends
- Sorted input patterns
- Pair/triplet search

Problems:

- Container With Most Water
- Two Sum II
- 3Sum for exposure

Key ideas:

- Use two pointers when moving one pointer changes the search space predictably.
- Explain why moving left or right is valid.
- Be comfortable with sorted arrays.

## RRK Focus

Learn:

- Basic system design structure
- Scale assumptions
- Latency vs cost
- Throughput
- Bottlenecks

Practice prompt:

> How would you scale your EY agentic AI platform from 50 users to 1 million users?

Must cover:

- API layer
- Load balancing
- Async workflows
- Queueing
- Caching
- Rate limits
- Horizontal scaling
- Observability
- Cost controls

---

# Day 5: Stack + Observability and Reliability

## Coding Focus

Learn:

- Stack pattern
- Last-in-first-out logic
- Matching and validation problems

Problems:

- Valid Parentheses
- Min Stack
- Daily Temperatures as stretch

Key ideas:

- Use a stack when the most recent item matters first.
- For parentheses, push opening brackets and match closing brackets.
- Explain edge cases clearly.

## RRK Focus

Learn:

- Logging
- Monitoring
- Tracing
- Reliability
- Retries
- Fallbacks
- Quality gates

Practice prompt:

> How do you debug a failing AI system?

Must cover:

- Identify where the failure occurs
- Input issue
- Retrieval issue
- Tool issue
- Model issue
- Prompt issue
- Permission issue
- Latency or infrastructure issue
- Use LangSmith-style tracing
- Add test cases and regression checks

---

# Day 6: Trees + Troubleshooting

## Coding Focus

Learn:

- Binary trees
- DFS recursion
- BFS with queue
- Base cases

Problems:

- Maximum Depth of Binary Tree
- Binary Tree Level Order Traversal
- Same Tree

Key ideas:

- DFS usually uses recursion.
- BFS usually uses a queue.
- Always define your base case.

## RRK Focus

Learn:

- Troubleshooting frameworks
- Latency debugging
- Distributed systems basics

Practice prompt:

> Your marketing manager says the new company website is slow. What do you do?

Must cover:

- Clarify scope
- Is it all users or some users?
- Is it frontend, backend, database, network, or third-party dependency?
- Check logs and metrics
- Measure latency by component
- Form hypotheses
- Test and isolate
- Roll back or mitigate
- Prevent recurrence

---

# Day 7: Graphs + Security

## Coding Focus

Learn:

- Graph representation
- DFS/BFS on grids
- Visited sets
- Avoiding cycles

Problems:

- Number of Islands
- Clone Graph

Key ideas:

- Graph problems require tracking visited nodes.
- Grid DFS/BFS is a common pattern.
- Be comfortable with boundary checks.

## RRK Focus

Learn:

- PII protection
- Access control
- Permission-aware retrieval
- Prompt injection
- Data leakage prevention

Practice prompt:

> How do you prevent an LLM system from leaking sensitive data?

Must cover:

- Authentication
- Authorization
- Document ACLs
- Permission-aware retrieval
- Secret redaction
- Tool access controls
- Prompt injection detection
- Audit logs
- Human review for high-risk actions

---

# Day 8: Heap + Cost Optimization

## Coding Focus

Learn:

- Heap / priority queue
- Min heap vs max heap
- Top K pattern

Problems:

- Top K Frequent Elements
- Kth Largest Element in an Array

Key ideas:

- Use heaps when you need top K, smallest K, or priority ordering.
- Python uses `heapq` as a min heap.
- Know the time complexity.

## RRK Focus

Learn:

- LLM cost optimization
- Caching
- Batching
- Model selection
- Token usage

Practice prompt:

> How do you reduce the cost of a production LLM system?

Must cover:

- Cache repeated queries
- Use smaller models when possible
- Route only complex tasks to larger models
- Reduce prompt size
- Retrieve fewer but higher-quality chunks
- Batch where possible
- Use async processing
- Monitor cost per request

---

# Day 9: Advanced Arrays + Cloud Basics

## Coding Focus

Learn:

- Prefix sums
- Precomputation
- Array products
- Subarray sums

Problems:

- Product of Array Except Self
- Subarray Sum Equals K

Key ideas:

- Prefix sums help answer range or subarray questions efficiently.
- Hashmaps can track previous prefix sums.
- Product Except Self requires avoiding division and using prefix/suffix products.

## RRK Focus

Learn high-level GCP basics:

- Vertex AI
- Gemini
- BigQuery
- Cloud Run
- Cloud Storage
- Pub/Sub
- Cloud Functions

Practice prompt:

> How would you map an AWS or Azure AI system you built to Google Cloud services?

Must cover:

- Compute layer
- Storage layer
- Data warehouse
- Model serving
- Orchestration
- Monitoring
- Security

---

# Day 10: Graphs Advanced + Full AI System Design

## Coding Focus

Learn:

- Dependency graphs
- Topological sort intuition
- Cycle detection

Problems:

- Course Schedule

Key ideas:

- Model prerequisites as a graph.
- Detect cycles to know if completion is impossible.
- Use DFS or BFS/Kahn's algorithm.

## RRK Focus

Practice full system design:

> Design an AI customer support system.

Must cover:

- User query flow
- Data ingestion
- Knowledge base
- RAG
- Tool calling
- Escalation to human
- Evaluation
- Latency targets
- Security
- Cost
- Monitoring
- Failure modes

Add numeric estimates:

- Number of users
- Requests per second
- Latency target
- Storage estimate
- Cost drivers

---

# Day 11: Review + Your Personal Architecture Story

## Coding Focus

Redo:

- Longest Substring Without Repeating Characters
- Number of Islands
- Top K Frequent Elements
- Product of Array Except Self

Goal:

- Recognize the pattern quickly.
- Code without looking.
- Explain time and space complexity.

## RRK Focus

Practice prompt:

> Walk me through your EY system architecture.

Must cover:

- Fortune 500 payments platform context
- 50+ bank integrations
- Azure AI Foundry
- LangGraph
- Temporal
- Human-in-the-loop review
- LangSmith observability
- Evaluation gates
- Production safety
- Business impact

Prepare a 2-minute version and a 5-minute version.

---

# Day 12: Mock Interview Day

## Coding Focus

Do two timed problems:

- 30 minutes each
- Plain text or Google Doc only
- No running code
- Explain out loud

For each problem:

1. Clarify requirements.
2. Give brute force.
3. Optimize.
4. Code.
5. Dry run.
6. State complexity.

## RRK Focus

Do one full mock RRK system design:

- 60 minutes
- Ask clarifying questions
- Draw high-level design
- Deep dive components
- Discuss scale
- Discuss failures
- Discuss cost
- Discuss security
- Discuss evaluation

Practice prompt options:

- Design an internal Q&A agent.
- Design an AI customer support system.
- Design an agent for bank data mapping.
- Design a document intelligence system for payments rules.

---

# Day 13: Light Review and Confidence Day

## Coding Focus

Light review only:

- Hashmaps
- Sliding window
- Two pointers
- Stack
- BFS/DFS
- Heap
- Prefix sums

Do at most 1 or 2 easy problems.

Do not burn out.

## RRK Focus

Review your core system design checklist:

1. Users and requirements
2. Scale assumptions
3. High-level architecture
4. Data model
5. APIs and components
6. AI/ML integration
7. Evaluation
8. Monitoring
9. Security
10. Cost
11. Tradeoffs
12. Failure handling

Practice your 60-second intro.

---

# Day 14: Interview Day

## Before the Interview

Review only:

- Pattern cheat sheet
- Complexity cheat sheet
- RRK checklist
- Your EY architecture story

Do not cram new topics.

## Coding Round Execution

Use this structure:

1. Clarify the problem.
2. Restate the goal.
3. Give brute force.
4. Explain optimized pattern.
5. Write clean Python.
6. Dry run manually.
7. State time and space complexity.

## RRK Round Execution

Use this structure:

1. Clarify users and requirements.
2. State assumptions.
3. Draw high-level architecture.
4. Explain data flow.
5. Deep dive important components.
6. Discuss scale, bottlenecks, and tradeoffs.
7. Discuss reliability and failures.
8. Discuss security and compliance.
9. Discuss evaluation and monitoring.
10. Summarize the design.

---

# Must-Know Coding Patterns

## Hashmap

Used for:

- Fast lookup
- Counting frequency
- Complement search

Key problem:

- Two Sum

## Sliding Window

Used for:

- Contiguous substring/subarray
- Longest or shortest valid window
- Expand/shrink rules

Key problem:

- Longest Substring Without Repeating Characters

## Two Pointers

Used for:

- Sorted arrays
- Pair search
- Shrinking search space

Key problems:

- Two Sum II
- Container With Most Water

## Stack

Used for:

- Matching
- Nested structure
- Most recent item matters

Key problem:

- Valid Parentheses

## BFS/DFS

Used for:

- Trees
- Graphs
- Grids
- Connected components

Key problem:

- Number of Islands

## Heap

Used for:

- Top K
- Priority
- Smallest/largest items

Key problem:

- Top K Frequent Elements

## Prefix Sum

Used for:

- Subarray sums
- Running totals
- Efficient range reasoning

Key problem:

- Subarray Sum Equals K

---

# Must-Know RRK Framework

When designing any AI system, cover:

1. Use case and users
2. Requirements
3. Scale assumptions
4. Data sources
5. Ingestion
6. Retrieval or tool use
7. LLM/model layer
8. Orchestration
9. APIs
10. Evaluation
11. Monitoring
12. Security
13. Cost
14. Failure handling
15. Tradeoffs

---

# Common RRK Phrases To Use

- A production AI system is more than an LLM call.
- I would start by clarifying the users, scale, and success criteria.
- I would make retrieval permission-aware to avoid data leakage.
- I would evaluate both retrieval quality and answer quality.
- I would use tracing to understand where an agent failed.
- I would add fallback behavior when confidence is low.
- I would balance latency, cost, and quality based on the user experience.
- I would treat this as a production service with observability and quality gates.
- I would first launch with a constrained scope, collect feedback, and then expand.

---

# Common Mistakes To Avoid

## Coding

- Jumping into code before clarifying.
- Memorizing without explaining.
- Forgetting edge cases.
- Not dry running.
- Not stating complexity.
- Relying on autocomplete.

## RRK

- Designing only an LLM prompt.
- Forgetting security and permissions.
- Forgetting evaluation.
- Forgetting latency and cost.
- Not asking clarifying questions.
- Not making tradeoffs explicit.
- Staying too high-level without deep diving.

---

# Final Mindset

You are not trying to become a competitive programmer in two weeks.

You are trying to show Google that you are:

- A strong engineer
- A clear communicator
- A production-minded AI builder
- Comfortable with ambiguity
- Able to reason through systems, tradeoffs, and failures

That is exactly what this role is looking for.
