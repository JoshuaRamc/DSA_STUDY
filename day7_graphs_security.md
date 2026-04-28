# Day 7: Graphs + Security

Date: April 27, 2026
Label: day7_graphs_security
Focus: Graph representation, DFS and BFS on graphs and grids, visited tracking, avoiding cycles, and designing GenAI systems that do not leak sensitive data.

## Goal For Today

By the end of Day 7, you should be able to:

1. Recognize when a problem is really a graph even if it looks like a grid or a network of objects.
2. Explain why visited tracking is required in graph problems.
3. Traverse connected components cleanly with DFS or BFS.
4. Talk through how to secure an LLM system against data leakage, permission mistakes, and prompt injection.

This day matters because graph problems test whether you can reason about connected structure without getting lost, and security questions test whether you think like a production engineer instead of just a model user.

## Coding Concepts

### What Is A Graph?

A graph is a set of nodes connected by edges.

Examples:

- people connected by friendships
- services connected by network calls
- web pages connected by links
- cells in a grid connected by up, down, left, and right movement

In interviews, a graph may be shown as:

- an adjacency list
- a matrix
- a grid
- object references like `node.neighbors`

### Why Graphs Feel Different

Graphs are not always linear.

From one node, you can branch to many other nodes.

That creates two main issues:

1. You need a traversal strategy.
2. You need to avoid revisiting the same node forever.

### DFS

DFS means depth-first search.

You keep going down one path before backing up.

Typical recursive shape:

```python
def dfs(node):
    if node in visited:
        return

    visited.add(node)

    for neighbor in graph[node]:
        dfs(neighbor)
```

### BFS

BFS means breadth-first search.

You explore outward layer by layer.

Typical queue shape:

```python
from collections import deque

queue = deque([start])
visited = {start}

while queue:
    node = queue.popleft()

    for neighbor in graph[node]:
        if neighbor not in visited:
            visited.add(neighbor)
            queue.append(neighbor)
```

### Visited Set

The visited set prevents:

- infinite loops on cycles
- repeated work
- double counting connected components

This is one of the most important Day 7 habits.

If you forget `visited`, many graph solutions break.

### Connected Component

A connected component is a group of nodes that can all reach one another.

For grid problems like Number of Islands:

- each island is one connected component
- water separates components

### Grid As A Graph

A grid is often secretly a graph.

Each cell is a node.
Edges connect neighboring cells.

Common directions:

```python
directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]
```

### Boundary Checks

Grid traversal must protect against invalid coordinates.

Common check:

```python
if row < 0 or row >= rows or col < 0 or col >= cols:
    return
```

## Pattern 1: DFS For Connected Components

Use this when:

- you need to fully explore one component
- recursion is allowed and natural
- the problem asks how many groups exist

Template:

```python
def dfs(node):
    if node in visited:
        return

    visited.add(node)

    for neighbor in neighbors(node):
        dfs(neighbor)
```

## Pattern 2: Grid DFS Or BFS

Use this when:

- the input is a matrix or board
- movement rules define adjacency
- you need to flood-fill or count regions

Template:

```python
def dfs(r, c):
    if r < 0 or r >= rows or c < 0 or c >= cols:
        return
    if grid[r][c] != "1" or (r, c) in visited:
        return

    visited.add((r, c))

    dfs(r + 1, c)
    dfs(r - 1, c)
    dfs(r, c + 1)
    dfs(r, c - 1)
```

## Pattern 3: Clone While Traversing

Use this when:

- the graph nodes are objects
- you need a deep copy
- the graph may contain cycles

Template:

```python
old_to_new = {}

def dfs(node):
    if node in old_to_new:
        return old_to_new[node]

    copy = Node(node.val)
    old_to_new[node] = copy

    for neighbor in node.neighbors:
        copy.neighbors.append(dfs(neighbor))

    return copy
```

## Problem 1: Number Of Islands

LeetCode: 200. Number of Islands
Difficulty: Medium
Pattern: Grid DFS or BFS
Core structure: Matrix + visited + connected component counting

## Status

[ X ] COMPLETED NUMBER OF ISLANDS SUCCESSFULLY

### Problem Statement

Given a 2D grid of `"1"`s and `"0"`s:

- `"1"` means land
- `"0"` means water

Return the number of islands.

An island is formed by connecting adjacent land cells horizontally or vertically.

Example:

```python
grid = [
    ["1", "1", "0", "0", "0"],
    ["1", "1", "0", "0", "0"],
    ["0", "0", "1", "0", "0"],
    ["0", "0", "0", "1", "1"],
]

# return 3
```

### Key Insight

Every time you find land that has not been visited yet, you have found a new island.

Then you should fully explore that island so you do not count it again.

That means:

1. scan every cell
2. when you find unvisited land, increment island count
3. run DFS or BFS to mark the whole connected landmass

### Code

```python
class Solution:
    def numIslands(self, grid: list[list[str]]) -> int:
        if not grid:
            return 0

        rows = len(grid)
        cols = len(grid[0])
        visited = set()
        islands = 0

        def dfs(r: int, c: int) -> None:
            if r < 0 or r >= rows or c < 0 or c >= cols:
                return
            if grid[r][c] != "1" or (r, c) in visited:
                return

            visited.add((r, c))

            dfs(r + 1, c)
            dfs(r - 1, c)
            dfs(r, c + 1)
            dfs(r, c - 1)

        for r in range(rows):
            for c in range(cols):
                if grid[r][c] == "1" and (r, c) not in visited:
                    islands += 1
                    dfs(r, c)

        return islands
```

### Walkthrough

Input:

```python
grid = [
    ["1", "1", "0"],
    ["1", "0", "0"],
    ["0", "1", "1"],
]
```

Trace:

```text
r = 0, c = 0
land and not visited
islands = 1
DFS marks (0,0), (0,1), (1,0)

continue scanning

r = 2, c = 1
land and not visited
islands = 2
DFS marks (2,1), (2,2)

return 2
```

### Complexity

Time: O(rows * cols)

Why: each cell is visited at most once.

Space: O(rows * cols)

Why: the visited set and recursion stack can grow with the grid.

### Interview Explanation

"I treat the grid like a graph where each land cell connects to its up, down, left, and right neighbors. I scan every cell, and whenever I find land that has not been visited, I increment the island count and run DFS to mark the full connected component. That prevents double counting."

## Problem 2: Clone Graph

LeetCode: 133. Clone Graph
Difficulty: Medium
Pattern: Graph DFS with hashmap
Core structure: Node objects + cycle-safe deep copy

## Status

[ X ] COMPLETED CLONE GRAPH SUCCESSFULLY

### Problem Statement

Given a reference to a node in a connected undirected graph, return a deep copy of the graph.

Each node contains:

- a value
- a list of neighbors

### Key Insight

This graph may contain cycles.

So if you try to recursively copy neighbors without remembering what you already copied, you can recurse forever or create duplicate node copies.

You need a mapping:

- key = original node
- value = cloned node

### Code

```python
class Solution:
    def cloneGraph(self, node: "Node | None") -> "Node | None":
        if not node:
            return None

        old_to_new = {}

        def dfs(current: "Node") -> "Node":
            if current in old_to_new:
                return old_to_new[current]

            copy = Node(current.val)
            old_to_new[current] = copy

            for neighbor in current.neighbors:
                copy.neighbors.append(dfs(neighbor))

            return copy

        return dfs(node)
```

### Walkthrough

Suppose:

```text
1 -- 2
|    |
4 -- 3
```

Trace:

```text
start dfs(1)
create copy of 1
store old_to_new[1] = copy1

visit neighbor 2
create copy of 2
store old_to_new[2] = copy2

visit neighbor 1 from 2
1 already copied
reuse copy1 instead of cloning again
```

That reuse step is what makes the clone cycle-safe.

### Complexity

Time: O(V + E)

Why: each node and edge is processed once.

Space: O(V)

Why: the hashmap and recursion stack grow with the number of nodes.

### Interview Explanation

"I use DFS plus a hashmap from original node to cloned node. The hashmap solves two problems at once: it prevents infinite recursion on cycles, and it ensures that each original node gets exactly one cloned copy. As I traverse neighbors, I recursively clone them and append those cloned references to the current clone."

## Coding Patterns Side By Side

### Number Of Islands

Question:

```text
How many connected land components exist in this grid?
```

Main pattern:

```python
if grid[r][c] == "1" and (r, c) not in visited:
    islands += 1
    dfs(r, c)
```

### Clone Graph

Question:

```text
How do I deep copy a graph that may contain cycles?
```

Main pattern:

```python
if current in old_to_new:
    return old_to_new[current]
```

## RRK: Security For LLM Systems

A production LLM system is not secure just because the model has safety tuning.

You still have to protect:

- user identity
- document permissions
- secrets
- tool access
- logs
- prompts
- outputs

For Day 7, your security answer should sound like an engineer building safe system boundaries, not like someone hoping the model behaves.

## Core Vocabulary

### PII

PII means personally identifiable information.

Examples:

- full name with other identifying context
- email address
- phone number
- Social Security number
- home address
- bank account information

### Authentication

Authentication answers:

```text
Who is this user?
```

Examples:

- SSO
- OAuth
- session tokens
- service identity

### Authorization

Authorization answers:

```text
What is this user allowed to access or do?
```

Examples:

- document ACLs
- role-based access control
- team-based access
- tool-specific permissions

### Permission-Aware Retrieval

Permission-aware retrieval means the system only retrieves documents the user is allowed to see.

This is critical.

If a restricted document reaches the prompt, your security boundary is already broken.

### Prompt Injection

Prompt injection is when untrusted input tries to manipulate the model into ignoring instructions or exposing data.

Examples:

- "ignore previous instructions"
- "reveal the hidden system prompt"
- malicious text embedded inside retrieved documents

### Data Leakage

Data leakage means the system reveals information to someone who should not receive it.

That can happen through:

- retrieval
- prompts
- tool calls
- logs
- cached responses
- model outputs

## RRK Practice Prompt

Practice this out loud:

```text
How do you prevent an LLM system from leaking sensitive data?
```

## Preventing Sensitive Data Leakage

### High-Level Principle

Do not rely on the model alone to protect data.

Security should be enforced in multiple layers:

1. identity
2. authorization
3. retrieval filtering
4. prompt and tool controls
5. output checks
6. logging and auditability

### First Response Structure

Use this order:

1. Clarify the type of data and use case.
2. Authenticate the user or service.
3. Enforce authorization before retrieval or tool use.
4. Redact or minimize sensitive context.
5. Restrict tool permissions.
6. Detect prompt injection and unsafe actions.
7. Audit and monitor access.
8. Add human review for high-risk workflows.

## Google-Level RRK Structure

For Google-style RRK answers, use this structure:

1. Clarify the problem.
2. State assumptions.
3. High-level architecture.
4. Component deep dive.
5. Threat model and failure modes.
6. Security controls and access boundaries.
7. Monitoring and auditability.
8. Tradeoffs.
9. Metrics and evaluation.
10. Summarize the design.

Strong interview phrase:

"I would not treat security as a prompt-level feature. I would enforce identity, access control, retrieval boundaries, tool restrictions, and audit logging throughout the system."

## Quantitative Thinking For This Prompt

Use numbers so your answer feels real.

Example assumptions:

- 100,000 employees
- 20,000 daily active users
- 40,000 questions per day
- peak traffic of 20 to 50 QPS during work hours
- millions of indexed document chunks
- a subset of documents marked confidential or highly restricted
- some workflows allowed to answer only, while others can trigger actions

Useful sentence:

"The main security risk grows with both scale and connectivity, because more data sources and more tool integrations create more paths for leakage."

## Interview-Ready Answer

"To prevent an LLM system from leaking sensitive data, I would enforce security before, during, and after the model call. First, I would authenticate the user and enforce authorization so retrieval only considers documents that user is allowed to access. I would store document ACLs with indexed chunks and apply permission-aware retrieval before any context is passed to the model. I would minimize and redact sensitive content where possible, restrict tool access with least privilege, and treat untrusted user input and retrieved text as possible prompt injection sources. On the output side, I would add policy checks, redaction checks, and stronger review paths for high-risk actions. Finally, I would keep audit logs, alerts, and periodic access reviews so we can detect misuse and prove the controls are working."

## Clarify The Problem Like Google

Before designing, ask:

1. What kind of sensitive data are we protecting: PII, financial data, source code, internal docs, or customer records?
2. Is the system read-only, or can it take actions through tools?
3. Are users internal employees, external customers, or both?
4. Does the system need citations or access explanations?
5. What are the highest-risk failure modes: overexposure, prompt injection, unsafe tool calls, or logging leakage?

## High-Level Secure Architecture

Think in this order:

```text
user
-> authentication
-> authorization and policy checks
-> permission-aware retrieval
-> prompt assembly with minimal context
-> model call
-> output validation and redaction
-> response
-> audit logs and monitoring
```

If tools are involved:

```text
model
-> tool policy layer
-> least-privilege tool execution
-> result validation
```

## Component Deep Dive

### Authentication

Controls:

- SSO
- strong session validation
- service identity for backend components

Why it matters:

If identity is weak, every later security control is weaker too.

### Authorization

Controls:

- document ACLs
- RBAC or ABAC
- deny-by-default policy
- tenant isolation

Why it matters:

The user should only access what their role and document permissions allow.

### Retrieval Layer

Controls:

- store permissions with document chunks
- filter retrieval before results reach the prompt
- avoid merging results across tenants
- attach source metadata for auditing

Strong interview phrase:

"I would enforce permissions at retrieval time, not just at response formatting time."

### Prompt And Context Layer

Controls:

- include only the minimum necessary context
- redact secrets when possible
- do not include hidden system data unless required
- separate trusted instructions from untrusted document text

Why it matters:

The more sensitive content you send to the model, the larger the blast radius if something goes wrong.

### Tool Access Layer

Controls:

- least-privilege tool credentials
- allowlists for which tools a workflow can call
- step-up approval for high-risk actions
- argument validation before execution

Examples of high-risk actions:

- sending email
- issuing refunds
- modifying records
- downloading large datasets

### Output Layer

Controls:

- PII detection
- secret scanning
- policy filters
- refusal logic for unsafe requests
- human review for high-risk outputs

### Logging And Auditability

Controls:

- audit logs for access and tool usage
- masked or redacted logs
- anomaly detection on unusual access patterns
- periodic review of privileged workflows

## Threat Model And Failure Modes

### Failure Modes

1. Unauthorized retrieval
   Why it breaks:
   ACL filtering is missing, stale, or incorrect
   How to handle it:
   permission-aware retrieval, deny-by-default checks, ACL sync validation
2. Prompt injection from user input
   Why it breaks:
   malicious text tries to override trusted instructions
   How to handle it:
   trusted/untrusted prompt separation, policy checks, constrained tool use
3. Prompt injection from documents
   Why it breaks:
   retrieved content contains adversarial instructions
   How to handle it:
   treat retrieved text as untrusted, never allow documents to redefine policy
4. Over-privileged tools
   Why it breaks:
   the model can call tools that expose or modify too much
   How to handle it:
   least privilege, tool allowlists, approval gates
5. Leakage through logs or caches
   Why it breaks:
   sensitive prompts or outputs are stored too broadly
   How to handle it:
   redacted logs, scoped caches, short retention, access controls on observability systems
6. Unsafe output
   Why it breaks:
   the model returns confidential information even if the request looked harmless
   How to handle it:
   output policy checks, redaction, user-specific access review, human escalation

## Monitoring And Auditability

Security metrics to mention:

- unauthorized access attempts
- retrieval denials by policy
- prompt injection detection rate
- tool-call approval rate
- sensitive output blocks
- audit log coverage
- unusual access spikes by user or tenant

Operational security metrics:

- mean time to detect security incidents
- mean time to revoke unsafe access
- percentage of indexed documents with valid ACL metadata

## Tradeoffs

Make these explicit:

- security vs latency:
  more policy checks and redaction steps may add latency
- security vs usability:
  stricter access boundaries may reduce convenience for users
- observability vs privacy:
  richer logs help debugging but can themselves become a leakage risk
- model flexibility vs control:
  broad tool freedom is powerful but increases blast radius

## Follow-Up Questions

### How do you handle prompt injection?

Answer:

"I treat both user input and retrieved content as untrusted. I keep trusted system instructions separate, restrict what the model is allowed to do, and never let document text redefine tool policy or access rules."

### Why is permission-aware retrieval so important?

Answer:

"Because once restricted content is retrieved into the prompt, you have already crossed the security boundary. Filtering only at the final response is too late."

### What about logs?

Answer:

"Logs are part of the threat surface. I would avoid storing raw sensitive prompts and outputs unless necessary, and I would redact, scope access, and set retention limits."

### What if the model can take actions?

Answer:

"Then I would add stronger controls than for read-only answers, including least-privilege tools, argument validation, approval gates for high-risk actions, and a full audit trail."

### How do you prove the system is secure?

Answer:

"I would combine design controls with testing and monitoring: permission tests, red-team prompt injection tests, audit reviews, output policy evals, and alerts for unusual access behavior."

## RRK Drill Section

Say these out loud without notes:

1. Answer "How do you prevent an LLM system from leaking sensitive data?" in under 2 minutes.
2. Explain the difference between authentication and authorization.
3. Explain why permission-aware retrieval must happen before the model call.
4. Name one prompt injection risk from a user and one from a document.
5. Explain how tool access should be restricted.
6. Explain one security risk in logs and how to reduce it.
7. Give one tradeoff between security and latency.
8. Describe when you would require human review.

## RRK Interview Checklist

When answering LLM security prompts, cover these:

1. Authentication
2. Authorization
3. Document ACLs
4. Permission-aware retrieval
5. Secret and PII redaction
6. Tool access controls
7. Prompt injection handling
8. Output validation
9. Audit logs
10. Human review for high-risk actions

## Common Mistakes

### Mistake 1: Forgetting `visited`

Without visited tracking, graph traversal can:

- loop forever
- revisit work
- overcount components

### Mistake 2: Treating A Grid As Something Totally Different

Many grid problems are graph problems with implicit neighbors.

### Mistake 3: Cloning Before Memoizing

In Clone Graph, if you do not store the clone immediately after creating it, cycles will break the solution.

### Mistake 4: Enforcing Permissions Too Late

Wrong idea:

```text
Let the model see everything, then filter the answer.
```

That is already a security failure.

### Mistake 5: Trusting The Model To Enforce Security By Itself

The model is one layer, not the security boundary.

### Mistake 6: Forgetting Logs And Caches Can Leak Data Too

The response path is not the only place where secrets can escape.

## Python Syntax You Must Be Comfortable With

### Set Membership

```python
if (r, c) in visited:
    return
```

### Nested Loops Over A Grid

```python
for r in range(rows):
    for c in range(cols):
        ...
```

### `deque`

```python
from collections import deque
queue = deque([start])
```

### Hashmap Memoization

```python
old_to_new[current] = copy
```

## Manual Practice Routine

For coding:

1. Say what the nodes are.
2. Say what makes two nodes adjacent.
3. Decide between DFS and BFS.
4. State what goes in `visited`.
5. Dry run one example.
6. State time and space complexity.

For RRK:

1. Clarify the sensitive data and user model.
2. Explain identity and authorization.
3. Explain permission-aware retrieval.
4. Explain prompt injection risks.
5. Explain tool restrictions.
6. Explain output checks and audit logs.

## Dry Run Template

Use this for graph traversal:

```text
Input:

Current node or cell:
Visited before?
Neighbors:
Next recursive calls or queue additions:
Visited after:
```

Use this for LLM security:

```text
Sensitive data:
User identity:
Allowed data:
Retrieval control:
Prompt control:
Tool control:
Output control:
Audit trail:
```

## Practice Prompts

### Number Of Islands

Trace this:

```python
grid = [
    ["1", "1", "0", "0"],
    ["0", "1", "0", "1"],
    ["0", "0", "1", "1"],
]
```

Expected:

```python
2
```

### Clone Graph

Practice explaining:

```text
Why do we need a hashmap from old node to new node?
```

Expected answer:

```text
To avoid infinite recursion on cycles and to ensure each original node maps to exactly one clone.
```

### RRK

Practice out loud:

```text
How do you prevent an LLM system from leaking sensitive data?
```

Then make sure you cover:

1. Authentication
2. Authorization
3. Document ACLs
4. Permission-aware retrieval
5. Prompt injection
6. Tool controls
7. Audit logs
8. Human review

## Final Day 7 Code To Memorize

### Number Of Islands

```python
class Solution:
    def numIslands(self, grid: list[list[str]]) -> int:
        if not grid:
            return 0

        rows = len(grid)
        cols = len(grid[0])
        visited = set()
        islands = 0

        def dfs(r: int, c: int) -> None:
            if r < 0 or r >= rows or c < 0 or c >= cols:
                return
            if grid[r][c] != "1" or (r, c) in visited:
                return

            visited.add((r, c))

            dfs(r + 1, c)
            dfs(r - 1, c)
            dfs(r, c + 1)
            dfs(r, c - 1)

        for r in range(rows):
            for c in range(cols):
                if grid[r][c] == "1" and (r, c) not in visited:
                    islands += 1
                    dfs(r, c)

        return islands
```

### Clone Graph

```python
class Solution:
    def cloneGraph(self, node: "Node | None") -> "Node | None":
        if not node:
            return None

        old_to_new = {}

        def dfs(current: "Node") -> "Node":
            if current in old_to_new:
                return old_to_new[current]

            copy = Node(current.val)
            old_to_new[current] = copy

            for neighbor in current.neighbors:
                copy.neighbors.append(dfs(neighbor))

            return copy

        return dfs(node)
```

## Day 7 Self-Test

Answer these without looking:

1. Why does a graph problem usually need a visited set?
2. How can a grid be treated like a graph?
3. What is a connected component?
4. Why does Number of Islands increment only when it finds unvisited land?
5. Why does Clone Graph need a hashmap?
6. What is the difference between authentication and authorization?
7. Why must retrieval be permission-aware?
8. What is prompt injection?
9. Why are logs part of the security surface?
10. When should a high-risk LLM workflow involve human review?

## Day 7 Answers

1. To avoid infinite loops, repeated work, and double counting.
2. Each cell is a node, and valid neighboring cells are connected by edges.
3. A connected component is a set of nodes that can reach one another.
4. Because that is the start of a new island that has not already been explored.
5. To reuse already-cloned nodes and prevent infinite recursion on cycles.
6. Authentication verifies who the user is, and authorization decides what they may access or do.
7. Because a restricted document should never enter the model context for an unauthorized user.
8. Prompt injection is when untrusted text tries to manipulate the model into ignoring instructions or exposing data.
9. Because sensitive prompts, outputs, or tool results can leak through logging if logs are not controlled.
10. When the workflow exposes sensitive data, makes business-impacting changes, or has low confidence.

## Day 7 Interview Phrases

Use these phrases until they become natural:

- "I treat the grid as a graph where adjacent land cells form connected components."
- "Visited tracking prevents both infinite loops and double counting."
- "Every time I find unvisited land, I have found a new island."
- "In Clone Graph, the old-to-new mapping is the key structure because it handles cycles and guarantees one clone per node."
- "I would not rely on the model alone for security."
- "Authentication tells me who the user is, and authorization tells me what they may access."
- "Retrieval must be permission-aware so restricted content never reaches the prompt."
- "I treat user input and retrieved documents as untrusted when thinking about prompt injection."
- "Tool access should follow least privilege and require extra review for high-risk actions."
- "Audit logs and output checks are part of the security design, not optional extras."

## Day 7 Completion Criteria

You are done with Day 7 when you can:

1. Explain graph traversal in plain English.
2. Implement Number of Islands without looking.
3. Explain Clone Graph clearly enough to code it from memory.
4. State time and space complexity for both graph problems.
5. Explain why visited sets and memo maps are essential.
6. Verbally answer "How do you prevent an LLM system from leaking sensitive data?"
7. Cover authentication, authorization, ACLs, permission-aware retrieval, prompt injection, tool controls, and audit logging without being prompted.
