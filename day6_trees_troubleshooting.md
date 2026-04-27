# Day 6: Trees + Troubleshooting

Date: April 26, 2026
Label: day6_trees_troubleshooting
Focus: Binary trees, DFS recursion, BFS with a queue, base cases, and troubleshooting slow or failing systems with a structured debugging approach.

## Goal For Today

By the end of Day 6, you should be able to:

1. Recognize when a problem is asking you to traverse a tree.
2. Explain the difference between DFS and BFS and when each one fits naturally.
3. Write clean recursive tree solutions with correct base cases.
4. Talk through how to troubleshoot a slow or failing production system without guessing.

This day matters because tree problems show whether you can reason cleanly with recursive structure, and troubleshooting questions show whether you can stay calm and methodical when systems misbehave.

## Coding Concepts

### Binary Tree

A binary tree is a structure where each node can have:

- a left child
- a right child

Typical interview node shape:

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

### Why Trees Feel Different

Arrays and strings are linear.

Trees branch.

That means your code often has to make the same decision on:

- the current node
- the left subtree
- the right subtree

That is why recursion appears so often in tree problems.

### DFS

DFS means depth-first search.

You fully explore one path before switching to another path.

In tree interviews, DFS is often written recursively.

Common DFS pattern:

```python
def dfs(node):
    if not node:
        return

    dfs(node.left)
    dfs(node.right)
```

### BFS

BFS means breadth-first search.

You explore level by level.

In tree interviews, BFS usually uses a queue.

Common BFS pattern:

```python
from collections import deque

queue = deque([root])

while queue:
    node = queue.popleft()

    if node.left:
        queue.append(node.left)
    if node.right:
        queue.append(node.right)
```

### Base Case

The base case is what stops recursion.

In tree problems, the most common base case is:

```python
if not node:
    return ...
```

This is one of the most important habits for Day 6.

If your base case is wrong, the whole solution usually breaks.

## Pattern 1: DFS Recursion

Use this when:

- The problem naturally asks about a subtree.
- You want to compute something from children.
- You want a short and clean recursive solution.

Template:

```python
def dfs(node):
    if not node:
        return base_value

    left_result = dfs(node.left)
    right_result = dfs(node.right)

    return combine(left_result, right_result, node)
```

## Pattern 2: BFS Level Order

Use this when:

- The problem asks about levels.
- You want shortest-step style exploration.
- You need to process nodes layer by layer.

Template:

```python
from collections import deque

queue = deque([root])

while queue:
    level_size = len(queue)

    for _ in range(level_size):
        node = queue.popleft()

        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
```

## Pattern 3: Compare Two Treeskm fv 

Use this when:

- You are checking equality or structural similarity.
- The problem gives two roots.
- You need to verify both value and shape.

Template:

```python
def same(a, b):
    if not a and not b:
        return True
    if not a or not b:
        return False
    if a.val != b.val:
        return False

    return same(a.left, b.left) and same(a.right, b.right)
```

## Problem 1: Maximum Depth Of Binary Tree

LeetCode: 104. Maximum Depth of Binary Tree
Difficulty: Easy
Pattern: DFS recursion
Core structure: Tree + recursive height

## Status

[ X ] COMPLETED MAXIMUM DEPTH OF BINARY TREE SUCCESSFULLY

### Problem Statement

Given the `root` of a binary tree, return its maximum depth.

The maximum depth is the number of nodes along the longest path from the root down to a leaf node.

Example:

```python
root = [3, 9, 20, null, null, 15, 7]
# return 3
```

### Key Insight

The depth of a node is:

```text
1 + max(depth of left subtree, depth of right subtree)
```

If the node is missing, its depth is `0`.

That gives a very natural recursive solution.

### Code

```python
class Solution:
    def maxDepth(self, root: TreeNode | None) -> int:
        if not root:
            return 0

        left_depth = self.maxDepth(root.left)
        right_depth = self.maxDepth(root.right)

        return 1 + max(left_depth, right_depth)
```

### Walkthrough

Input:

```text
        3
       / \
      9  20
         / \
        15  7
```

Trace:

```text
maxDepth(3)
= 1 + max(maxDepth(9), maxDepth(20))

maxDepth(9)
= 1 + max(0, 0)
= 1

maxDepth(20)
= 1 + max(maxDepth(15), maxDepth(7))
= 1 + max(1, 1)
= 2

maxDepth(3)
= 1 + max(1, 2)
= 3
```

### Complexity

Time: O(n)

Why: every node is visited once.

Space: O(h)

Why: recursion stack depth is the height of the tree.

In the worst case of a skewed tree, that can become O(n).

### Interview Explanation

"I use DFS recursion because the depth of a tree is naturally defined in terms of the depths of its left and right subtrees. My base case is that an empty node has depth 0. Then for each real node, I return 1 plus the larger of the two subtree depths."

## Problem 2: Binary Tree Level Order Traversal

LeetCode: 102. Binary Tree Level Order Traversal
Difficulty: Medium
Pattern: BFS with queue
Core structure: Tree + queue + level grouping

## Status

[ X ] COMPLETED BINARY TREE LEVEL ORDER TRAVERSAL SUCCESSFULLY

### Problem Statement

Given the `root` of a binary tree, return the level order traversal of its nodes' values.

Example:

```python
root = [3, 9, 20, null, null, 15, 7]
# return [[3], [9, 20], [15, 7]]
```

### Key Insight

The phrase "level order" is a strong hint for BFS.

You process all nodes at one level before moving to the next.

To do that cleanly:

1. Use a queue.
2. At each loop, record the current queue length.
3. That length is the number of nodes in the current level.

### Code

```python
from collections import deque


class Solution:
    def levelOrder(self, root: TreeNode | None) -> list[list[int]]:
        if not root:
            return []

        result = []
        queue = deque([root])

        while queue:
            level = []
            level_size = len(queue)

            for _ in range(level_size):
                node = queue.popleft()
                level.append(node.val)

                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)

            result.append(level)

        return result
```

### Walkthrough

Input:

```text
        3
       / \
      9  20
         / \
        15  7
```

Trace:

```text
queue = [3]
level_size = 1
process 3
append [3]
queue becomes [9, 20]

level_size = 2
process 9, 20
append [9, 20]
queue becomes [15, 7]

level_size = 2
process 15, 7
append [15, 7]
```

### Complexity

Time: O(n)

Space: O(n)

Why: the queue may hold many nodes from one level.

### Interview Explanation

"I use BFS because the problem asks for level order traversal. A queue lets me process nodes in the same order they are discovered. I use the queue length at the start of each round to know how many nodes belong to the current level."

## Problem 3: Same Tree

LeetCode: 100. Same Tree
Difficulty: Easy
Pattern: Recursive tree comparison
Core structure: Two trees + DFS comparison

## Status

[  ] COMPLETED SAME TREE SUCCESSFULLY

### Problem Statement

Given the roots of two binary trees `p` and `q`, write a function to check if they are the same.

Two trees are the same if they are structurally identical and the nodes have the same values.

Example:

```python
p = [1, 2, 3]
q = [1, 2, 3]
# return True
```

### Key Insight

Two trees are the same only if all three conditions hold:

1. Both nodes are missing, or both nodes exist.
2. Their values are equal.
3. Their left subtrees are the same and their right subtrees are the same.

This is another natural recursive pattern.

### Code

```python
class Solution:
    def isSameTree(self, p: TreeNode | None, q: TreeNode | None) -> bool:
        if not p and not q:
            return True

        if not p or not q:
            return False

        if p.val != q.val:
            return False

        return self.isSameTree(p.left, q.left) and self.isSameTree(p.right, q.right)
```

### Walkthrough

Input:

```text
p:      1
       / \
      2   3

q:      1
       / \
      2   3
```

Trace:

```text
compare 1 and 1
values match

compare left children 2 and 2
values match
their children are both null

compare right children 3 and 3
values match
their children are both null

return True
```

### Complexity

Time: O(n)

Why: each pair of corresponding nodes is checked once.

Space: O(h)

Why: recursion stack follows tree height.

### Interview Explanation

"I compare the two trees recursively. If both nodes are missing, they match. If only one is missing, they do not match. If both exist, their values must match and both left subtrees and right subtrees must also match."

## Coding Patterns Side By Side

### Maximum Depth Of Binary Tree

Question:

```text
How tall is this tree?
```

Main pattern:

```python
return 1 + max(left_depth, right_depth)
```

### Binary Tree Level Order Traversal

Question:

```text
What values appear at each level?
```

Main pattern:

```python
level_size = len(queue)
```

### Same Tree

Question:

```text
Do these two trees have the same shape and values?
```

Main pattern:

```python
return same(lefts) and same(rights)
```

## RRK: Troubleshooting A Slow Or Failing System

A good engineer does not jump straight to random fixes.

They narrow the problem, measure what is happening, form hypotheses, and isolate the failing component.

For Day 6, your troubleshooting answer should feel calm, structured, and production-minded.

## Core Vocabulary

### Scope

Scope means:

- who is affected
- what is affected
- when it started
- whether the issue is broad or isolated

### Latency

Latency is how long something takes.

Examples:

- page load time
- API response time
- database query time
- external dependency time

### Dependency

A dependency is something your system relies on.

Examples:

- database
- cache
- third-party API
- CDN
- authentication service

### Hypothesis

A hypothesis is a testable guess about the root cause.

Example:

```text
The website is slow because a new backend query is causing database latency.
```

### Mitigation

Mitigation means reducing user impact before the full root cause fix is complete.

Examples:

- rollback a bad deploy
- disable a broken feature flag
- route traffic away from a failing dependency
- serve a degraded but usable experience

## RRK Practice Prompt

Practice this out loud:

```text
Your marketing manager says the new company website is slow. What do you do?
```

## Troubleshooting A Slow Website

### High-Level Principle

Do not start by assuming the frontend is the problem.

Slow websites can come from:

1. frontend rendering
2. backend APIs
3. database queries
4. cache misses
5. network issues
6. CDN or static asset problems
7. third-party scripts

### First Response Structure

Use this order:

1. Clarify the symptom.
2. Narrow the scope.
3. Check recent changes.
4. Measure latency by component.
5. Form hypotheses.
6. Test and isolate.
7. Mitigate user impact.
8. Fix root cause.
9. Prevent recurrence.

## Google-Level RRK Structure

For Google-style RRK answers, use this structure:

1. Clarify the problem.
2. State assumptions.
3. Describe the request path.
4. Identify likely failure points.
5. Explain what you would measure first.
6. Explain how you would isolate the issue.
7. Explain mitigation options.
8. Explain root cause fix.
9. Explain prevention and monitoring improvements.
10. Summarize the approach.

Strong interview phrase:

"I would troubleshoot this systematically by first narrowing scope, then measuring latency across the request path, and only after that deciding whether the problem is frontend, backend, database, network, or a third-party dependency."

## Quantitative Thinking For This Prompt

Use numbers so your answer feels concrete.

Example assumptions:

- 500,000 daily page views
- average traffic around `500,000 / 86,400`, or about 6 QPS
- peak traffic may be much higher during campaigns, for example 50 to 200 QPS
- current homepage P95 load time might have jumped from 2 seconds to 8 seconds
- backend API target might be under 300 ms
- database query target might be under 100 ms for common paths

Useful sentence:

"I would compare current latency against a baseline, because a page that feels slow is easier to debug when I know which metric regressed and by how much."

## Interview-Ready Answer

"If a marketing manager says the new company website is slow, I would first clarify the symptom and scope. I'd ask whether the slowness affects all users or only certain regions, devices, or pages, and whether it started after a recent deployment, content change, or traffic spike. Then I'd inspect the request path end to end, including frontend performance, backend API latency, database query time, cache behavior, network timing, and third-party scripts. I'd use logs, metrics, traces, browser performance tools, and synthetic monitoring to see where latency is being added. Once I isolate the slow component, I'd mitigate impact quickly, for example by rolling back a bad deploy, disabling a heavy third-party script, or re-enabling caching. After that I'd fix the root cause and add monitoring, regression checks, and performance budgets so the issue is easier to catch next time."

## Clarify The Problem Like Google

Before trying to fix it, ask:

1. Is the whole site slow or only specific pages?
2. Is it affecting all users or only some users, devices, browsers, or regions?
3. Did the issue begin after a release, marketing campaign, config change, or infrastructure event?
4. Is the problem page-load latency, API latency, timeouts, or rendering jank?
5. What is the user-visible severity and business impact?

## Request Path Thinking

Walk the system in order:

```text
browser
-> DNS and CDN
-> static assets
-> frontend rendering
-> API gateway or backend
-> application service
-> database or cache
-> third-party dependencies
```

This structure keeps you from guessing.

## Component Deep Dive

### Frontend

Questions to ask:

- Are JavaScript bundles too large?
- Did a new page introduce expensive rendering?
- Are images or fonts too heavy?
- Is a third-party script blocking render?

### Backend

Questions to ask:

- Did an API get slower after a deploy?
- Are requests waiting on synchronous work that should be async?
- Are there spikes in error rate or timeout rate?

### Database

Questions to ask:

- Did a query plan regress?
- Is an index missing?
- Are there lock or connection pool issues?
- Did a new feature create an N+1 query pattern?

### Cache

Questions to ask:

- Did cache hit rate drop?
- Are expensive reads bypassing cache?
- Is invalidation too aggressive?

### Network And CDN

Questions to ask:

- Are requests slower in one region?
- Is CDN caching working?
- Are static assets being served from the wrong location?

### Third-Party Dependencies

Questions to ask:

- Did an analytics, chat, or ad script become slow?
- Is a partner API timing out?
- Are retries causing cascading delays?

## Reliability And Failure Handling

### Failure Modes

1. Frontend asset bloat
   Why it breaks:
   new bundles, images, or scripts increase load and render time
   How to handle it:
   bundle analysis, code splitting, compression, lazy loading
2. Slow backend API
   Why it breaks:
   expensive synchronous work or inefficient service logic
   How to handle it:
   profiling, caching, async workflows, optimized code paths
3. Database regression
   Why it breaks:
   slow queries, missing indexes, lock contention
   How to handle it:
   query analysis, indexes, replicas, schema or query redesign
4. Cache failure
   Why it breaks:
   cache misses force expensive backend or database reads
   How to handle it:
   restore caching, tune TTLs, fix invalidation strategy
5. Third-party slowdown
   Why it breaks:
   external scripts or APIs add latency or block rendering
   How to handle it:
   isolate, defer, timeout, or temporarily disable the dependency
6. Traffic spike
   Why it breaks:
   sudden demand overloads application or data tiers
   How to handle it:
   autoscaling, load shedding, caching, queues, rate limits

## Security And Operational Discipline

Even for a performance issue, do not ignore safe operations.

Mention:

- roll back carefully
- use feature flags when possible
- avoid exposing internal debug data to users
- confirm monitoring and logs do not leak sensitive information
- document what changed during mitigation

Strong phrase:

"Even while moving quickly, I would make mitigation changes in a controlled way so I do not trade a latency incident for a security or reliability incident."

## Tradeoffs

Make these explicit:

- speed of mitigation vs confidence:
  fast rollback reduces impact quickly but may hide the exact root cause if not investigated carefully
- latency vs cost:
  more caching, replicas, and CDN capacity improve performance but increase spend
- frontend richness vs performance:
  richer pages can hurt load time if assets and rendering are not controlled
- aggressive retries vs overload:
  retries can help transient failures but can also amplify latency and load

## Metrics And Monitoring

System and product metrics:

- page load time
- largest contentful paint
- time to first byte
- API latency
- database latency
- cache hit rate
- error rate
- timeout rate
- traffic volume by region

Operational metrics:

- mean time to detect
- mean time to mitigate
- mean time to recovery

## Follow-Up Questions

### What would you check first?

Answer:

"I'd first narrow scope and check what changed recently. Then I'd look at a latency breakdown so I can see whether the time is being spent in the browser, network, backend, database, or a third-party dependency."

### How do you know whether it is frontend or backend?

Answer:

"I'd compare browser performance data with backend API metrics. If frontend render time is high but APIs are normal, the issue is likely client-side. If the browser is mostly waiting on APIs, the problem is probably backend or downstream."

### What if only some users are affected?

Answer:

"That points me toward region-specific networking, browser-specific rendering, device performance differences, tenant-specific data size, or a partial rollout. I'd compare healthy and affected cohorts side by side."

### What if the issue started right after a deploy?

Answer:

"That makes the new deploy my strongest initial hypothesis. I'd compare metrics before and after the release and be ready to roll back or disable the change quickly while I investigate root cause."

### How do you prevent it from happening again?

Answer:

"I'd add better monitoring, performance regression checks, clearer dashboards, and possibly performance budgets in CI or pre-release testing so we catch similar regressions earlier."

## RRK Drill Section

Say these out loud without notes:

1. Answer "The website is slow" using a clear troubleshooting structure.
2. Add realistic assumptions for traffic, latency, and business impact.
3. Walk the request path from browser to backend and database.
4. Explain how to distinguish frontend latency from backend latency.
5. Name one likely database issue and one likely third-party issue.
6. Give one fast mitigation and one long-term fix.
7. Explain what metrics you would monitor after recovery.
8. Summarize your answer in under 2 minutes.

## RRK Interview Checklist

When answering troubleshooting prompts, cover these:

1. Symptom clarification
2. Scope
3. Recent changes
4. Request path
5. Measurement
6. Hypothesis
7. Isolation
8. Mitigation
9. Root cause
10. Prevention

## Common Mistakes

### Mistake 1: Skipping The Base Case

In tree problems, missing or weak base cases cause most errors.

Always decide first:

```python
if not node:
    return ...
```

### Mistake 2: Mixing Up DFS And BFS

DFS usually means:

- recursion or explicit stack
- path-focused exploration

BFS usually means:

- queue
- level-by-level exploration

### Mistake 3: Forgetting That Level Order Means BFS

When the problem says:

```text
level order
```

that is a very strong BFS signal.

### Mistake 4: Comparing Tree Values Without Comparing Structure

Two trees are not the same just because they contain the same values.

The structure must match too.

### Mistake 5: Guessing At Performance Problems

Do not say:

```text
I think it is probably the database.
```

Better:

```text
I would measure latency across the request path and isolate the slow component before deciding on the fix.
```

### Mistake 6: Fixing Without Mitigating

When users are impacted, do not wait for the perfect root cause analysis before reducing damage.

Mitigate first when appropriate.

## Python Syntax You Must Be Comfortable With

### Recursive Calls

```python
left_depth = self.maxDepth(root.left)
right_depth = self.maxDepth(root.right)
```

### Queue With `deque`

```python
from collections import deque
queue = deque([root])
```

### `popleft`

```python
node = queue.popleft()
```

### Empty Node Checks

```python
if not root:
    return []
```

## Manual Practice Routine

For coding:

1. State whether the problem fits DFS or BFS.
2. Say the base case out loud.
3. Explain what each recursive call or queue level represents.
4. Dry run one example.
5. State time and space complexity.

For RRK:

1. Clarify the symptom.
2. Narrow the scope.
3. Walk the request path.
4. Measure by component.
5. Form hypotheses.
6. Isolate the issue.
7. Mitigate user impact.
8. Prevent recurrence.

## Dry Run Template

Use this for tree recursion:

```text
Input tree:

Current node:
Base case?
Left result:
Right result:
Return value:
```

Use this for BFS:

```text
Queue at start of level:
Level size:
Nodes processed:
Children added:
Result after level:
```

Use this for troubleshooting:

```text
Symptom:
Scope:
Recent changes:
Latency breakdown:
Hypothesis:
Isolation step:
Mitigation:
Root cause:
Prevention:
```

## Practice Prompts

### Maximum Depth Of Binary Tree

Trace this:

```text
        1
       / \
      2   3
     /
    4
```

Expected:

```python
3
```

### Binary Tree Level Order Traversal

Trace this:

```text
        1
       / \
      2   3
```

Expected:

```python
[[1], [2, 3]]
```

### Same Tree

Trace this:

```text
p = [1, 2, 1]
q = [1, 1, 2]
```

Expected:

```python
False
```

### RRK

Practice out loud:

```text
Your marketing manager says the new company website is slow. What do you do?
```

Then make sure you cover:

1. Scope
2. Frontend
3. Backend
4. Database
5. Network
6. Third-party dependency
7. Mitigation
8. Prevention

## Final Day 6 Code To Memorize

### Maximum Depth Of Binary Tree

```python
class Solution:
    def maxDepth(self, root: TreeNode | None) -> int:
        if not root:
            return 0

        left_depth = self.maxDepth(root.left)
        right_depth = self.maxDepth(root.right)

        return 1 + max(left_depth, right_depth)
```

### Binary Tree Level Order Traversal

```python
from collections import deque


class Solution:
    def levelOrder(self, root: TreeNode | None) -> list[list[int]]:
        if not root:
            return []

        result = []
        queue = deque([root])

        while queue:
            level = []
            level_size = len(queue)

            for _ in range(level_size):
                node = queue.popleft()
                level.append(node.val)

                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)

            result.append(level)

        return result
```

### Same Tree

```python
class Solution:
    def isSameTree(self, p: TreeNode | None, q: TreeNode | None) -> bool:
        if not p and not q:
            return True

        if not p or not q:
            return False

        if p.val != q.val:
            return False

        return self.isSameTree(p.left, q.left) and self.isSameTree(p.right, q.right)
```

## Day 6 Self-Test

Answer these without looking:

1. What is the difference between DFS and BFS?
2. Why is the base case so important in tree recursion?
3. Why does Maximum Depth return `1 + max(left, right)`?
4. Why is level order traversal a BFS problem?
5. What makes two trees the same?
6. Why should you clarify scope before troubleshooting?
7. What is the difference between mitigation and root cause fix?
8. Why is latency breakdown by component useful?
9. Why can third-party scripts slow a website?
10. Why should you add prevention after the incident is fixed?

## Day 6 Answers

1. DFS explores one path deeply before switching, while BFS explores level by level.
2. The base case stops recursion and defines what happens at empty nodes.
3. Because the height of a node is one for itself plus the larger of its two subtree heights.
4. Because level order means processing nodes level by level, which is exactly what BFS does.
5. They must have the same structure and the same values at corresponding nodes.
6. Because you need to know who is affected, what is affected, and how broad the issue is before choosing a fix.
7. Mitigation reduces immediate user impact, while the root cause fix addresses why the issue happened.
8. It tells you where time is actually being spent instead of forcing you to guess.
9. They can block rendering, add network waits, or fail in ways that slow the page.
10. Because a good engineer improves monitoring and guardrails so the same class of problem is easier to catch next time.

## Day 6 Interview Phrases

Use these phrases until they become natural:

- "This is a tree traversal problem, so I first decide whether DFS or BFS is the cleaner fit."
- "My base case is what happens when the node is null."
- "The depth of a node is one plus the maximum depth of its children."
- "Level order is a strong signal for BFS with a queue."
- "Two trees are only the same if both structure and values match."
- "I would first clarify whether the slowdown affects all users or only certain pages, regions, or devices."
- "I would measure latency across the full request path before guessing at the root cause."
- "I would mitigate user impact quickly, then continue root cause analysis."
- "A slow website could be frontend, backend, database, network, cache, or a third-party dependency."
- "After recovery, I would add monitoring and regression checks so the issue is easier to catch next time."

## Day 6 Completion Criteria

You are done with Day 6 when you can:

1. Explain DFS and BFS in plain English.
2. Implement Maximum Depth of Binary Tree without looking.
3. Implement Binary Tree Level Order Traversal without looking.
4. Implement Same Tree without looking.
5. State time and space complexity for all three tree problems.
6. Explain how to troubleshoot a slow website in a structured way.
7. Distinguish frontend, backend, database, network, cache, and third-party causes of latency.
8. Explain mitigation, root cause analysis, and prevention without being prompted.
